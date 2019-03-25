# Oracle Kubernetes Ingress Set up
The steps described in this page are based on: https://docs.cloud.oracle.com/iaas/Content/ContEng/Tasks/contengsettingupingresscontroller.htm

1) Switch to default context (can be found by looking a KUBECONFIG file)

	```bash
	kubectl config use-context context-crwmnlcmzsw
	```

1) Grant the Kubernetes RBAC cluster-admin clusterrole to the OCID user

	```bash
	kubectl create clusterrolebinding sttc_admin --clusterrole=cluster-admin --user=ocid1.user.oc1..aaaaaaaazhci..............cxpgvrq
	```
2) Grant access by executing

	```bash
	kubectl create -f ingress-controller-rbac.yaml
	```

3) Create the default backend deployment and service by executing these commands

	```bash
	kubectl create -f nginx-default-backend-deployment.yaml
	kubectl create -f nginx-default-backend-service.yaml
	```

4) Create the Nginx ingress controller deployment and service

	```bash
	kubectl create -f nginx-ingress-controller-deployment.yaml
	kubectl create -f nginx-ingress-controller-service.yaml
	```

5) Verify that all services are running

	```bash
	kubectl get svc
	```

6) Create TLS secret to be used when creating ingress.yml

	```bash
	openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=nginxsvc/O=nginxsvc"
	```

	Switch context to the one to be used for deploying

	```bash
	kubectl config use-context orders
	```

	Then run the following to create the secret

	```bash
	kubectl create secret tls tls-secret --key tls.key --cert tls.crt
	```

6) In case you need to start over, below commands to delete the objects created

	Delete cluster role bindings and service accounts

	```bash
	kubectl delete clusterrolebinding nginx-ingress-clusterrole-nisa-binding
	kubectl delete clusterrolebinding sttc_admin
	kubectl delete clusterrole nginx-ingress-clusterrole
	kubectl delete serviceaccount nginx-ingress-serviceaccount
	```

	Delete nginx default backends

	```bash
	kubectl delete deploy default-http-backend
	kubectl delete service default-http-backend
	```

	Delete nginx controller deployment and service

	```bash
	kubectl delete deploy nginx-ingress-controller
	kubectl delete service nginx-ingress-controller
	```
