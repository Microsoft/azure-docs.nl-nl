---
title: Azure Active Directory door Pod beheerde identiteiten gebruiken in azure Kubernetes service (preview)
description: Meer informatie over het gebruik van met AAD pod beheerde beheerde identiteiten in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: f3d0db5b085fcdb9a24310cb2fe310d390b1790a
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574370"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Azure Active Directory door Pod beheerde identiteiten gebruiken in azure Kubernetes service (preview)

Azure Active Directory door Pod beheerde identiteiten gebruikt Kubernetes primitieven om [beheerde identiteiten te koppelen voor Azure-resources][az-managed-identities] en-identiteiten in azure Active Directory (Aad) met een Peul. Beheerders maken identiteiten en bindingen als Kubernetes primitieven die een Peule toegang bieden tot Azure-bronnen die afhankelijk zijn van AAD als een id-provider.

> [!NOTE]
> Als u een bestaande installatie van AADPODIDENTITY hebt, moet u de bestaande installatie verwijderen. Als u deze functie inschakelt, betekent dit dat het microfoon onderdeel niet nodig is.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende bron hebben geïnstalleerd:

* De Azure CLI, versie 2.20.0 of hoger
* De `azure-preview` extensie versie 0.5.5 of hoger

### <a name="limitations"></a>Beperkingen

* Er zijn Maxi maal 200 pod-identiteiten toegestaan voor een cluster.
* Er zijn Maxi maal 200 pod-identiteits uitzonderingen toegestaan voor een cluster.
* Pod-beheerde identiteiten zijn alleen beschikbaar in Linux-knooppunt groepen.

### <a name="register-the-enablepodidentitypreview"></a>Registreer de `EnablePodIdentityPreview`

De `EnablePodIdentityPreview` functie registreren:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>De `aks-preview` Azure cli installeren

U hebt ook de *AKS-preview* Azure cli-extensie versie 0.4.64 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] . Of installeer alle beschik bare updates met behulp van de opdracht [AZ extension update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-cni"></a>Een AKS-cluster maken met Azure CNI

> [!NOTE]
> Dit is de aanbevolen standaard configuratie

Maak een AKS-cluster met Azure CNI en pod-Managed Identity ingeschakeld. De volgende opdrachten gebruiken [AZ Group Create][az-group-create] om een resource groep te maken met de naam *myResourceGroup* en de opdracht [AZ AKS Create][az-aks-create] om een AKS-cluster te maken met de naam *myAKSCluster* in de *myResourceGroup* -resource groep.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Gebruik [AZ AKS Get-referenties][az-aks-get-credentials] om u aan te melden bij uw AKS-cluster. Met deze opdracht wordt ook het `kubectl` client certificaat op uw ontwikkel computer gedownload en geconfigureerd.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Een bestaand AKS-cluster bijwerken met Azure CNI

Werk een bestaand AKS-cluster bij met Azure CNI om pod-beheerde identiteit op te neemt.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --network-plugin azure
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>De Kubenet-netwerk-invoeg toepassing met Azure Active Directory door Pod beheerde identiteiten gebruiken 

> [!IMPORTANT]
> Het uitvoeren van Aad-pod-Identity in een cluster met Kubenet is geen aanbevolen configuratie vanwege de beveiligings implicaties. Volg de stappen voor risico beperking en configureer beleid voordat u Aad-pod-Identity in een cluster met Kubenet inschakelt.

## <a name="mitigation"></a>Oplossing

Om het beveiligings probleem op het cluster niveau te beperken, kunt u OpenPolicyAgent Admission controller samen met gate keeper voor het valideren van webhooks gebruiken. Als er al een gate keeper is geïnstalleerd in uw cluster, voegt u de ConstraintTemplate van het type K8sPSPCapabilities toe:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Voeg een sjabloon toe om het produceren van peulen te beperken met de NET_RAW mogelijkheid:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Een AKS-cluster maken met de Kubenet-netwerk-invoeg toepassing

Een AKS-cluster maken met Kubenet-netwerk-invoeg toepassing en pod-beheerde identiteit ingeschakeld.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Een bestaand AKS-cluster bijwerken met Kubenet-netwerk-invoeg toepassing

Werk een bestaand AKS-cluster bij met Kubnet-netwerk-invoeg toepassing voor het toevoegen van een door Pod beheerde identiteit.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Een identiteit maken

Maak een identiteit met [AZ Identity Create][az-identity-create] en stel de *IDENTITY_CLIENT_ID* en *IDENTITY_RESOURCE_ID* variabelen in.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Machtigingen toewijzen voor de beheerde identiteit

De beheerde identiteit *IDENTITY_CLIENT_ID* moet Lees machtigingen hebben in de resource groep die de schaalset voor virtuele machines van uw AKS-cluster bevat.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Een pod-identiteit maken

Maak een pod-identiteit voor het cluster met behulp van `az aks pod-identity add` .

> [!IMPORTANT]
> U moet de juiste machtigingen hebben, zoals `Owner` , op uw abonnement om de identiteits-en functie binding te maken.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Wanneer u pod-beheerde identiteit op uw AKS-cluster inschakelt, wordt er een AzurePodIdentityException met de naam *AKS-addon-Exception* toegevoegd aan de *uitvoeren-systeem* naam ruimte. Een AzurePodIdentityException maakt het mogelijk dat bepaalde labels toegang hebben tot het Azure Instance Metadata Service (IMDS)-eind punt zonder te worden onderschept door de NMI-server (node-Managed Identity). Met de *AKS-invoeg toepassing-Exception* kunnen AKS van de eerste partij, zoals Aad pod-beheerde identiteit, worden gebruikt zonder dat er hand matig een AzurePodIdentityException hoeft te worden geconfigureerd. U kunt desgewenst een AzurePodIdentityException toevoegen, verwijderen en bijwerken met `az aks pod-identity exception add` , `az aks pod-identity exception delete` , `az aks pod-identity exception update` of `kubectl` .

## <a name="run-a-sample-application"></a>Een voorbeeld toepassing uitvoeren

Voor een pod voor het gebruik van met AAD pod beheerde identiteit, heeft de pod een *aadpodidbinding* -label nodig met een waarde die overeenkomt met een selector van een *AzureIdentityBinding*. Als u een voorbeeld toepassing wilt uitvoeren met behulp van met AAD pod beheerde identiteit, maakt u een `demo.yaml` bestand met de volgende inhoud. Vervang *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* en *IDENTITY_RESOURCE_GROUP* door de waarden uit de vorige stappen. Vervang *SUBSCRIPTION_ID* door uw abonnements-id.

> [!NOTE]
> In de vorige stappen hebt u de *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* en *IDENTITY_RESOURCE_GROUP* variabelen gemaakt. U kunt een opdracht gebruiken `echo` om bijvoorbeeld de waarde weer te geven die u voor variabelen hebt ingesteld `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

U ziet dat de definitie van de pod een *aadpodidbinding* -label heeft met een waarde die overeenkomt met de naam van de pod-identiteit die u `az aks pod-identity add` in de vorige stap hebt uitgevoerd.

Implementeer `demo.yaml` in dezelfde naam ruimte als uw Pod-identiteit met `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Controleer of de voorbeeld toepassing is uitgevoerd met `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Controleer de logboeken geeft aan dat het token is opgehaald en dat de *Get* -bewerking is gelukt.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Opschonen

Als u met AAD pod beheerde identiteit uit uw cluster wilt verwijderen, verwijdert u de voorbeeld toepassing en de pod-identiteit uit het cluster. Verwijder vervolgens de identiteit.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Volgende stappen

Zie [beheerde identiteiten voor Azure-resources][az-managed-identities]voor meer informatie over beheerde identiteiten.

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
