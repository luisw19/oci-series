# Quick install of Oracle Kubernetes Engine
Following a summary of the steps to provision an [OKE](https://cloud.oracle.com/containers/kubernetes-engine) cluster based on the **quick create** option.

> This recipe assumes that have an OCI account. If this is not the case, [click here](https://docs.oracle.com/en/cloud/get-started/subscriptions-cloud/csgsg/how-do-i-sign.html) for options on how to create one.

1) [Install and configure the OCI CLI](https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/cliinstall.htm):

```bash
bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
```

Follow the steps as prompted. Then run:

```bash
oci setup config
```
> Note that during the configuration you'll have to use the OCI web console to [obtain your user and tenancy OCID](https://docs.cloud.oracle.com/Content/API/Concepts/apisigningkey.htm#Other)
> and also [add the generated public to your OCI user](https://docs.cloud.oracle.com/Content/API/Concepts/apisigningkey.htm#How2).

2) Grant Container Engine access to all resources in the tenancy. [Steps here on how to do this](https://docs.cloud.oracle.com/iaas/Content/ContEng/Concepts/contengpolicyconfig.htm#PolicyPrerequisitesService).

3) Create a **Kubernete Cluster** using the **quick cluster** option. [Steps here](https://docs.cloud.oracle.com/iaas/Content/ContEng/Tasks/contengcreatingclusterusingoke.htm).

>Note that in the above example I've used version 1.12.6

> If you hit a [service limits](https://docs.cloud.oracle.com/iaas/Content/General/Concepts/servicelimits.htm) error,
> [create a service request to increase the limits](https://docs.cloud.oracle.com/iaas/Content/General/Concepts/servicelimits.htm).

4) Download the
