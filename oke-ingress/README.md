# Oracle Kubernetes [Nginx Ingress](https://kubernetes.github.io/ingress-nginx/) Installation
The steps described in this page are based on [this OCI page](https://docs.cloud.oracle.com/iaas/Content/ContEng/Tasks/contengsettingupingresscontroller.htm)

1. Switch to default context.

First determine the name of the default context, run:

```bash
kubectl config view
```

Then take note of the *name*, *user* and *cluster* values under section *"- contexts"*. The run the following command to switch to it.

```bash
kubectl config use-context context-c3wczrxmftd
```

2. Grant the *Kubernetes RBAC cluster-admin clusterrole* to a OCI user based on
the user's *Oracle Cloud Identifier (OCID)*.

> To obtain the OICD open the OCI Console and from there
> click on the menu option *Identity > Users*.
> Then click on *show* under the username and take note of the OID.

```bash
kubectl create clusterrolebinding sttc_admin --clusterrole=cluster-admin --user=ocid1.user.oc1..aaaaaaaazhciwyt5kooopvnovupyao7v7a73imsvxoqrb2omojbcvcxpgvrq
```

3. Create the *NGINX ingress controller* along with the Kubernetes RBAC roles and bindings:

First get the latest manifest file:

```bash
curl -O https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/mandatory.yaml
```

Then create the RBAC roles and Nginx ingress controller by running:

```bash
kubectl create -f mandatory.yaml
```

4. Now that the *NGINX ingress controller* has been created,
run the following command to apply a *Load Balancer* service.

```bash
kubectl apply -f cloud-generic.yaml
```

- Verify that all services are running

```bash
kubectl get svc -n ingress-nginx
```

> Repeat the above process until an *EXTERNAL-IP* is assigned.
> Note that this may take a few seconds as the controller is
> basically creating an OCI load balancer also visible from the
> OCI console itself under *Networking > Load Balancers*.

- To delete the Ingress Controller, all configurations and start over you can run:

```bash
kubectl delete -n ingress-nginx  configmap nginx-configuration
kubectl delete -n ingress-nginx  configmap tcp-services
kubectl delete -n ingress-nginx  configmap udp-services
kubectl delete -n ingress-nginx serviceaccount nginx-ingress-serviceaccount
kubectl delete -n default clusterrole nginx-ingress-clusterrole
kubectl delete -n ingress-nginx role nginx-ingress-role
kubectl delete -n ingress-nginx rolebinding nginx-ingress-role-nisa-binding
kubectl delete -n default clusterrolebinding nginx-ingress-clusterrole-nisa-binding
kubectl delete -n ingress-nginx deployment nginx-ingress-controller
kubectl delete -n ingress-nginx service nginx-ingress-controller
```

5. Now that the ingress is installed we can create deploy a sample and then create an ingress.

> As in the sample we also implement TLS security therefore the first step is to crate the certificates.

- Using the [openssl](https://www.openssl.org/) utility [generate a TLS certificate](https://www.linode.com/docs/security/ssl/create-a-self-signed-tls-certificate/) as following:

```bash
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out httpbin.sample.crt -keyout httpbin.sample.key
```

When prompted enter further details as desired, for example:

```
Country Name (2 letter code) []:GB
State or Province Name (full name) []:Warwickshire
Locality Name (eg, city) []:Leamington
Organization Name (eg, company) []:HTTPBIN
Organizational Unit Name (eg, section) []:Sample
Common Name (eg, fully qualified host name) []:httpbin.sample
Email Address []:me@httpbin.sample
```

Once completed this should generate *httpbin.sample.key* and *httpbin.sample.crt*.

- The secret must be created in the target namespace where an ingress service will be created. If default, then the following steps are not required. If something other than default then ensure that the

> note that in the below example we're creating a target namespace called *orders-ms*

```bash
kubectl create namespace httpbin-nginx
kubectl config set-context httpbin-nginx --user=user-c3wczrxmftd --cluster=cluster-c3wczrxmftd --namespace=httpbin-nginx
kubectl config use-context httpbin-nginx
```

Then run create the secret on the target name space:

```bash
kubectl create secret tls httpbin-secret --key httpbin.sample.key --cert httpbin.sample.crt
```

- Deploy a sample service:

First get the latest manifest file:

```bash
curl -O https://raw.githubusercontent.com/istio/istio/release-1.0/samples/httpbin/httpbin.yaml
```

Then apply the manifest:

```bash
kubectl apply -f httpbin.yaml
```

Verify that pods were created:

```bash
kubectl get pods
```

- create the Ingress as following:

```bash
kubectl create -f httpbin-ingress.yaml
```

- Try it out as following:

```bash
export INGRESS_HOST=$(kubectl -n ingress-nginx get service ingress-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n ingress-nginx get service ingress-nginx -o jsonpath='{.spec.ports[?(@.name=="http")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n ingress-nginx get service ingress-nginx -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
```

Check that the env variables were set correctly:

```bash
echo $INGRESS_HOST $INGRESS_PORT $SECURE_INGRESS_PORT
```

Then make a simple test to verify that the ingress works for HTTP traffic:

> As the certificate was created against domain *httpbin.sample*
> and the service also expects this domain then we need to inform
> curl to resolve $INGRESS_HOST against this domain.

```bash
curl -I -HHost:httpbin.sample \
--resolve httpbin.sample:$INGRESS_PORT:$INGRESS_HOST \
http://httpbin.sample:$INGRESS_PORT/headers
```

If successful result should be similar to:

```bash
HTTP/1.1 200 OK
Server: nginx/1.15.9
Date: Tue, 19 Mar 2019 15:37:08 GMT
Content-Type: application/json
Content-Length: 270
Connection: keep-alive
Vary: Accept-Encoding
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

Now using HTTPS:

```bash
curl -I --insecure \
-HHost:httpbin.sample \
--resolve httpbin.sample:$SECURE_INGRESS_PORT:$INGRESS_HOST \
https://httpbin.sample:$SECURE_INGRESS_PORT/headers
```

- To delete the sample run:

```bash
kubectl delete namespace httpbin-nginx
```

#### Following a tip to troubleshoot the configuration by inspecting the Nginx config:

First find the ingress controller pod:

```bash
kubectl get pod -n ingress-nginx
```

Then use the pod to *kubectl exec* into it:

```bash
kubectl -n ingress-nginx exec -it nginx-ingress-controller-56c5c48c4d-fstg5 -- cat /etc/nginx/nginx.conf > nginx.output
```
