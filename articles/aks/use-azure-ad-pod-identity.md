---
title: Door pods Azure Active Directory identiteiten gebruiken in Azure Kubernetes Service (preview)
description: Meer informatie over het gebruik van door een AAD-pod beheerde identiteiten in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: f090f5e11688f35ce090bb07ec0d23530bf9d90e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777847"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Door pods Azure Active Directory identiteiten gebruiken in Azure Kubernetes Service (preview)

Azure Active Directory door pods beheerde identiteiten maakt gebruik van Kubernetes-primitieven om beheerde identiteiten voor [Azure-resources][az-managed-identities] en -identiteiten in Azure Active Directory (AAD) te koppelen aan pods. Beheerders maken identiteiten en bindingen als Kubernetes-primitieven waarmee pods toegang hebben tot Azure-resources die afhankelijk zijn van AAD als id-provider.

> [!NOTE]
> Als u een bestaande installatie van AADPODIDENTITY hebt, moet u de bestaande installatie verwijderen. Als u deze functie inschakelen, betekent dit dat het ONDERDEEL MIC niet nodig is.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende resource hebben geïnstalleerd:

* De Azure CLI, versie 2.20.0 of hoger
* De `azure-preview` extensieversie 0.5.5 of hoger

### <a name="limitations"></a>Beperkingen

* Er zijn maximaal 200 pod-identiteiten toegestaan voor een cluster.
* Er zijn maximaal 200 pod-id-uitzonderingen toegestaan voor een cluster.
* Door pods beheerde identiteiten zijn alleen beschikbaar in Linux-knooppuntgroepen.

### <a name="register-the-enablepodidentitypreview"></a>Registreer de `EnablePodIdentityPreview`

Registreer de `EnablePodIdentityPreview` functie:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>De `aks-preview` Azure CLI installeren

U hebt ook de Azure *CLI-extensie aks-preview* versie 0.4.64 of hoger nodig. Installeer de *Azure CLI-extensie aks-preview* met behulp van de [opdracht az extension add.][az-extension-add] U kunt ook beschikbare updates installeren met behulp van [de opdracht az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-cni"></a>Een AKS-cluster maken met Azure CNI

> [!NOTE]
> Dit is de standaard aanbevolen configuratie

Maak een AKS-cluster Azure CNI een door pod beheerde identiteit is ingeschakeld. De volgende opdrachten gebruiken [az group create][az-group-create] om een resourcegroep te maken met de naam *myResourceGroup* en de opdracht [az aks create][az-aks-create] om een AKS-cluster met de naam *myAKSCluster* te maken in de resourcegroep *myResourceGroup.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Gebruik [az aks get-credentials om][az-aks-get-credentials] u aan te melden bij uw AKS-cluster. Met deze opdracht wordt ook het clientcertificaat op `kubectl` uw ontwikkelcomputer gedownload en geconfigureerd.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Een bestaand AKS-cluster bijwerken met Azure CNI

Werk een bestaand AKS-cluster bij met Azure CNI om een door pod beheerde identiteit op te nemen.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --network-plugin azure
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Kubenet-netwerkinvoegsel gebruiken met Azure Active Directory door pods beheerde identiteiten 

> [!IMPORTANT]
> Het uitvoeren van aad-pod-identity in een cluster met Kubenet is vanwege de beveiligings implicatie geen aanbevolen configuratie. Volg de oplossingsstappen en configureer beleidsregels voordat u aad-pod-identity in een cluster met Kubenet inschakelen.

## <a name="mitigation"></a>Oplossing

Als u het beveiligingsprobleem op clusterniveau wilt verhelpen, kunt u de toegangscontroller OpenPolicyAgent gebruiken samen met gatekeeper die de webhook valideert. Op voorwaarde dat Gatekeeper al in uw cluster is geïnstalleerd, voegt u de ConstraintTemplate van het type K8sPSPCapabilities toe:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Voeg een sjabloon toe om het paaien van pods te beperken met de NET_RAW mogelijkheid:

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

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Een AKS-cluster maken met een Kubenet-netwerkinvoegsel

Maak een AKS-cluster met een Kubenet-netwerkinvoegsel en een door pods beheerde identiteit ingeschakeld.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Een bestaand AKS-cluster bijwerken met kubenet-netwerkinvoegsel

Werk een bestaand AKS-cluster bij met de Kubnet-netwerkinvoegsel om een door pod beheerde identiteit op te nemen.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Een identiteit maken

Maak een identiteit met [az identity create][az-identity-create] en stel de *IDENTITY_CLIENT_ID* en *IDENTITY_RESOURCE_ID* in.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Machtigingen toewijzen voor de beheerde identiteit

De *IDENTITY_CLIENT_ID* beheerde identiteit moet lezersmachtigingen hebben in de resourcegroep die de virtuele-machineschaalset van uw AKS-cluster bevat.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Een pod-identiteit maken

Maak een pod-identiteit voor het cluster met behulp van `az aks pod-identity add` .

> [!IMPORTANT]
> U moet de juiste machtigingen, zoals , voor uw abonnement hebben `Owner` om de identiteit en rolbinding te maken.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Wanneer u een door pods beheerde identiteit in uw AKS-cluster inschakelen, wordt een AzurePodIdentityException met de naam *aks-addon-exception* toegevoegd aan de *kube-system-naamruimte.* Met Een AzurePodIdentityException kunnen pods met bepaalde labels toegang krijgen tot het Azure Instance Metadata Service-eindpunt (IMDS) zonder dat ze worden onderschept door de NMI-server (Door knooppunt beheerde identiteit). Met *de aks-addon-exception* kunnen AKS eigen addons, zoals een door een AAD-pod beheerde identiteit, werken zonder dat u handmatig een AzurePodIdentityException moet configureren. U kunt eventueel een AzurePodIdentityException toevoegen, verwijderen en bijwerken met `az aks pod-identity exception add` behulp van , , of `az aks pod-identity exception delete` `az aks pod-identity exception update` `kubectl` .

## <a name="run-a-sample-application"></a>Een voorbeeldtoepassing uitvoeren

Een pod kan alleen gebruikmaken van een door een AAD-pod beheerde identiteit als de pod een *label aadpodidbinding* heeft met een waarde die overeenkomt met een selector uit *een AzureIdentityBinding.* Als u een voorbeeldtoepassing wilt uitvoeren met behulp van een door een AAD-pod beheerde identiteit, maakt u een `demo.yaml` bestand met de volgende inhoud. Vervang *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* en *IDENTITY_RESOURCE_GROUP* door de waarden uit de vorige stappen. Vervang *SUBSCRIPTION_ID* door uw abonnements-id.

> [!NOTE]
> In de vorige stappen hebt u de *variabelen POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* en *IDENTITY_RESOURCE_GROUP* gemaakt. U kunt een opdracht zoals gebruiken om `echo` de waarde weer te geven die u voor variabelen hebt ingesteld, bijvoorbeeld `echo $IDENTITY_NAME` .

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

U ziet dat de poddefinitie een *label aadpodidbinding* heeft met een waarde die overeenkomt met de naam van de pod-identiteit die u `az aks pod-identity add` in de vorige stap hebt genomen.

Implementeer `demo.yaml` in dezelfde naamruimte als uw pod-identiteit met behulp van `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Controleer of de voorbeeldtoepassing wordt uitgevoerd met `kubectl logs` behulp van .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Controleer in de logboeken of het token is verkregen en of de *GET-bewerking* is geslaagd.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Opschonen

Als u een door een AAD-pod beheerde identiteit uit uw cluster wilt verwijderen, verwijdert u de voorbeeldtoepassing en de podidentiteit uit het cluster. Verwijder vervolgens de identiteit.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Volgende stappen

Zie Beheerde identiteiten voor Azure-resources voor meer informatie [over beheerde identiteiten.][az-managed-identities]

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
