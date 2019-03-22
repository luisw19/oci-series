# [Oracle Kubernetes Engine](https://docs.cloud.oracle.com/iaas/Content/ContEng/Concepts/contengoverview.htm) installation using the OCI CLI
To provision an [OKE](https://cloud.oracle.com/containers/kubernetes-engine) instance using the **OCI CLI** execute the following steps:

> The following steps assume you're using either MacOS, Linux or Unix.

> This recipe assumes you have an OCI account. If this is not the case, [click here](https://docs.oracle.com/en/cloud/get-started/subscriptions-cloud/csgsg/how-do-i-sign.html) for options on how to create one.

1) [Install the OCI CLI](https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/cliinstall.htm) by executing:

```bash
bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
```

Then follow the steps as prompted.

2) Create a Kubernetes Cluster

oci ce cluster create --name oke-cluster \
  --kubernetes-version <preferred version> \
  --vcn-id <vcn-ocid> \
  --service-lb-subnet-ids [] \
  ..

> Sometimes (specially if when using a promotion account) it might not be possible
> to provision an OKE instance because your tenancy doesn't have enough [service limits](https://docs.cloud.oracle.com/iaas/Content/General/Concepts/servicelimits.htm). If this is the case, check this link for steps on how to
> [request a service limit increase](https://docs.cloud.oracle.com/iaas/Content/General/Concepts/servicelimits.htm).

3) 
