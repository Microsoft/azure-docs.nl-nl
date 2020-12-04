---
title: Beheerde identiteiten gebruiken in azure Kubernetes service
description: Meer informatie over het gebruik van beheerde identiteiten in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 96a1eebbdcbf269b06d2ece77987ce7813f1d5f5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571059"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Beheerde identiteiten gebruiken in azure Kubernetes service

Op dit moment vereist een Azure Kubernetes service (AKS)-cluster (met name de Kubernetes-Cloud provider) een identiteit voor het maken van extra resources, zoals load balancers en beheerde schijven in Azure. Deze identiteit kan een *beheerde identiteit* of een service- *Principal* zijn. Als u een [Service-Principal](kubernetes-service-principal.md)gebruikt, moet u één of AKS in uw naam maken. Als u een beheerde identiteit gebruikt, wordt deze automatisch voor u gemaakt door AKS. Clusters die service-principals gebruiken, bereiken uiteindelijk een status waarin de Service-Principal moet worden vernieuwd om het cluster te laten functioneren. Het beheren van service-principals voegt complexiteit toe. Daarom is het eenvoudiger om beheerde identiteiten te gebruiken. Dezelfde machtigings vereisten gelden voor service-principals en beheerde identiteiten.

*Beheerde identiteiten* zijn in feite een wrapper rond service-principals en maken hun beheer eenvoudiger. De rotatie van de referenties voor MI gebeurt elke 46 dagen automatisch volgens Azure Active Directory standaard. AKS maakt gebruik van door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteits typen. Deze identiteiten zijn momenteel onveranderbaar. Meer informatie over [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)vindt u hier.

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende bron hebben geïnstalleerd:

- De Azure CLI, versie 2.8.0 of hoger

## <a name="limitations"></a>Beperkingen

* AKS-clusters met beheerde identiteiten kunnen alleen worden ingeschakeld tijdens het maken van het cluster.
* Tijdens de **upgrade** bewerkingen van het cluster is de beheerde identiteit tijdelijk niet beschikbaar.
* Tenants verplaatsen/migreren van beheerde identiteits clusters worden niet ondersteund.
* Als het cluster is `aad-pod-identity` ingeschakeld, wijzigt NMI (node Managed Identity) het knoop punt ' iptables ' om aanroepen naar het eind punt van de meta gegevens van het Azure-exemplaar te onderscheppen. Deze configuratie houdt in dat elke aanvraag voor het eind punt van de meta gegevens wordt onderschept door NMI, zelfs als de pod niet wordt gebruikt `aad-pod-identity` . AzurePodIdentityException CRD kan zodanig worden geconfigureerd `aad-pod-identity` dat alle aanvragen voor het eind punt van de meta gegevens die afkomstig zijn van een pod die overeenkomt met de labels die zijn gedefinieerd in CRD, via een proxy moeten worden gewaarschuwd zonder enige verwerking in NMI. Het systeem van het `kubernetes.azure.com/managedby: aks` label in de naam ruimte _uitvoeren_ moet worden uitgesloten in `aad-pod-identity` door de AzurePodIdentityException CRD te configureren. Zie [Aad-pod-Identity voor een specifieke Pod of toepassing uitschakelen](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)voor meer informatie.
  Als u een uitzonde ring wilt configureren, installeert u de YAML van de [Mic-uitzonde ring](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Samen vatting van beheerde identiteiten

AKS maakt gebruik van verschillende beheerde identiteiten voor ingebouwde services en invoeg toepassingen.

| Identiteit                       | Naam    | Toepassing | Standaard machtigingen | Uw eigen identiteit meenemen
|----------------------------|-----------|----------|
| Besturingsvlak | niet zichtbaar | Gebruikt door AKS voor beheerde netwerk bronnen, waaronder binnenloads voor binnenkomend verkeer en AKS beheerde open bare Ip's | Rol Inzender voor knooppunt resource groep | Preview
| Kubelet | AKS-cluster naam-agent pool | Verificatie met Azure Container Registry (ACR) | N.V.T. (voor kubernetes v 1.15 +) | Momenteel niet ondersteund
| Invoeg toepassing | AzureNPM | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | AzureCNI netwerk bewaking | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | azurepolicy (gate keeper) | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | azurepolicy | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | Calico | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | Dashboard | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | HTTPApplicationRouting | Hiermee worden de vereiste netwerk bronnen beheerd | Rol van lezer voor knooppunt resource groep, rol Inzender voor DNS-zone | Nee
| Invoeg toepassing | Ingangs toepassings gateway | Hiermee worden de vereiste netwerk bronnen beheerd| Rol Inzender voor knooppunt resource groep | Nee
| Invoeg toepassing | omsagent | Wordt gebruikt om AKS-metrische gegevens naar Azure Monitor te verzenden | Rol van uitgever voor metrische gegevens controleren | Nee
| Invoeg toepassing | Virtual-Node (ACIConnector) | Beheert vereiste netwerk bronnen voor Azure Container Instances (ACI) | Rol Inzender voor knooppunt resource groep | Nee
| OSS-project | Aad-pod-identiteit | Hiermee kunnen toepassingen veilig toegang krijgen tot Cloud bronnen met Azure Active Directory (AAD) | NA | Stappen voor het verlenen van machtigingen op https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Een AKS-cluster maken met beheerde identiteiten

U kunt nu een AKS-cluster met beheerde identiteiten maken met behulp van de volgende CLI-opdrachten.

Maak eerst een Azure-resource groep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak vervolgens een AKS-cluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Een succes volle cluster maken met behulp van beheerde identiteiten bevat deze service-principal-profiel informatie:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Gebruik de volgende opdracht om een query op te vragen van het object objectid van de beheerde identiteit van het beheer vlak:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Het resultaat moet er als volgt uitzien:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Zodra het cluster is gemaakt, kunt u de werk belasting van uw toepassing implementeren naar het nieuwe cluster en ermee werken, net zoals u dat hebt gedaan met AKS-clusters op basis van de Service-Principal.

> [!NOTE]
> Voor het maken en gebruiken van uw eigen VNet, een statisch IP-adres of een gekoppelde Azure-schijf waar de resources zich buiten de resource groep van het worker-knoop punt bevinden, gebruikt u de PrincipalID van het cluster systeem toegewezen beheerde identiteit voor het uitvoeren van een roltoewijzing. Zie [toegang tot andere Azure-resources delegeren](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)voor meer informatie over het toewijzen van rollen.
>
> Machtigings verlening voor door de cluster beheerde identiteit die wordt gebruikt door de Azure-Cloud provider, kan 60 minuten duren om in te vullen.

Haal tot slot referenties op voor toegang tot het cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Een AKS-cluster bijwerken naar beheerde identiteiten (preview-versie)

U kunt nu een AKS-cluster met Service-principals bijwerken om met beheerde identiteiten te werken met behulp van de volgende CLI-opdrachten.

Registreer eerst de functie vlag voor door het systeem toegewezen identiteit:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

De door het systeem toegewezen identiteit bijwerken:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

De door de gebruiker toegewezen identiteit bijwerken:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

De door de gebruiker toegewezen identiteit bijwerken:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Zodra de door het systeem toegewezen of door de gebruiker toegewezen identiteiten zijn bijgewerkt naar een beheerde identiteit, voert u een `az nodepool upgrade --node-image-only` op uw knoop punten uit om de update te volt ooien voor de beheerde identiteit.

## <a name="bring-your-own-control-plane-mi-preview"></a>Uw eigen besturings vlak instellen MI (preview-versie)
De identiteit van een aangepast besturings element biedt toegang tot de bestaande identiteit voordat het cluster wordt gemaakt. Dit maakt scenario's mogelijk, zoals het gebruik van een aangepast VNET of outboundType van UDR met een beheerde identiteit.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

U moet de volgende resources hebben geïnstalleerd:
- De Azure CLI, versie 2.9.0 of hoger
- De 0.4.57-uitbrei ding AKS-preview

Beperkingen voor het maken van uw eigen besturings vlak MI (preview):
* Azure Government wordt momenteel niet ondersteund.
* Azure China 21Vianet wordt momenteel niet ondersteund.

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status als **geregistreerd** wordt weer gegeven. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true):

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weer gegeven als geregistreerd, vernieuwt u de registratie van de `Microsoft.ContainerService` resource provider met behulp van de opdracht [AZ provider REGI ster](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Als u nog geen beheerde identiteit hebt, moet u er een voor beeld maken met behulp van [AZ Identity cli][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Het resultaat moet er als volgt uitzien:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Als uw beheerde identiteit deel uitmaakt van uw abonnement, kunt u de [opdracht AZ Identity cli][az-identity-list] gebruiken om een query uit te maken.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

U kunt nu de volgende opdracht gebruiken om uw cluster te maken met uw bestaande identiteit:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Een succes volle cluster maken met uw eigen beheerde identiteiten bevat deze userAssignedIdentities-profiel informatie:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>Volgende stappen
* Gebruik [Azure Resource Manager (arm)-Sjablonen ][aks-arm-template] voor het maken van beheerde identiteits clusters.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true
