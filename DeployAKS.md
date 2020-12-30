# Deploy AKS
## Remarks
- The official documentation is [here](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough-portal).
- I, [fukunagt](https://github.com/fukunagt), has deployed AKS cluster using Visual Studio Subscription account.

<!--
### Prerequisite
- Create Azure account.
- Create a resource group.
- Create a cloud shell resource.
-->

## Steps
1. Login [Azure Portal](https://portal.azure.com/#home).
1. Click AKS icon.
1. I have set the following parameters. I have set the defalut value for the other parameters that are not described here.
   - Resource Group: fukuRG
   - Kubernetes cluster name: aks-test
   - Region: Japan East
   - Node count: 1
   - Authentication method: System-assigned managed identity
     - When I chose Service principal, I have got the error. I have no idea to fix it.
1. Create a cluster.
1. Open Cloud Shell.
1. Run the following command to get the credentials for kubectl command.
   ```sh
   $ az aks get-credentials --resource-group fukuRG --name aks-test
   ```
1. Run the following command.
   ```sh
   $ kubectl get nodes -o wide
   NAME                                STATUS   ROLES   AGE   VERSION    INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
   aks-agentpool-30099824-vmss000000   Ready    agent   10h   v1.18.10   10.240.0.4    <none>        Ubuntu 18.04.5 LTS   5.4.0-1034-azure   docker://19.3.12
   ```
