# Quick install of Oracle Kubernetes Engine
Following a summary of the steps to provision an [OKE](https://cloud.oracle.com/containers/kubernetes-engine) cluster based on the **quick create** option.

> The steps assume that there is an OCI account available.
> If this is not the case, [click here](https://docs.oracle.com/en/cloud/get-started/subscriptions-cloud/csgsg/how-do-i-sign.html)
> for options on creating one.

1) [Install](https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/cliinstall.htm) and [configure](https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/cliconfigure.htm) the OCI CLI.

For Unix/Linux based OS this can be done quickly by running:

```bash
bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
```

Follow the steps as prompted. Then to configure it:

```bash
oci setup config
```

> Note that during the configuration you'll have to use the OCI web console to [obtain your user and tenancy OCID](https://docs.cloud.oracle.com/Content/API/Concepts/apisigningkey.htm#Other)
> and also [add the generated public to your OCI user](https://docs.cloud.oracle.com/Content/API/Concepts/apisigningkey.htm#How2).

2) [Click here for the steps](https://docs.cloud.oracle.com/iaas/Content/ContEng/Concepts/contengpolicyconfig.htm#PolicyPrerequisitesService) to grant the **Container Engine** access to all resources in the tenancy.

3) [Click here for the steps](https://docs.cloud.oracle.com/iaas/Content/ContEng/Tasks/contengcreatingclusterusingoke.htm) on creating the **Kubernetes Cluster** using the **quick cluster** option.

> If you hit a [service limits](https://docs.cloud.oracle.com/iaas/Content/General/Concepts/servicelimits.htm) error,
> [create a service request to increase the limits](https://docs.cloud.oracle.com/iaas/Content/General/Concepts/servicelimits.htm).

4) From the Container Cluster **main page**, click on the cluster recently created and **copy** the **cluster id**.

```bash
mkdir -p $HOME/.kube
oci ce cluster create-kubeconfig --cluster-id <cluster ID> --file $HOME/.kube/config --region us-ashburn-1
```

5) Export the **KUBECONFIG** environment variable:

```bash
export KUBECONFIG=$HOME/.kube/config
```

6) Verify that the configuration works by running a **kubectl** command such as:

```bash
kubectl get nodes
```

7) Refer to the [kubectl cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
for tips on how to use the command line. For example:

- Setting up an **alias** with **autocomplete**:

First create the alias, e.g. **k**

```bash
alias k=kubectl
```

- Install [bash completion](https://github.com/scop/bash-completion).
In Mac this can be done easily with **brew** as following:

```bash
brew install bash-completion
```

Then add the following to the `$HOME/.bash_profile`:

```bash
if [ -f $(brew --prefix)/etc/bash_completion ]; then
    . $(brew --prefix)/etc/bash_completion
fi

source <(kubectl completion bash)
alias k=kubectl
complete -F __start_kubectl k
```

Re-source:

```bash
source $HOME/.bash_profile
```

Then try it out:

```bash
k get nodes
```
