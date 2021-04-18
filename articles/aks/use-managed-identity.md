---
title: Beheerde identiteiten gebruiken in Azure Kubernetes Service
description: Meer informatie over het gebruik van beheerde identiteiten in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 58813504c5de057e06433b2e955931b37560d825
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600655"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Beheerde identiteiten gebruiken in Azure Kubernetes Service

Momenteel is voor een Azure Kubernetes Service-cluster (AKS) (met name de Kubernetes-cloudprovider) een identiteit vereist voor het maken van extra resources, zoals load balancers en beheerde schijven in Azure. Deze identiteit kan een *beheerde* identiteit of een *service-principal zijn.* Als u een [service-principal gebruikt,](kubernetes-service-principal.md)moet u ervoor zorgen dat er een wordt gemaakt of dat AKS er namens u een maakt. Als u een beheerde identiteit gebruikt, wordt deze automatisch voor u gemaakt door AKS. Clusters die service-principals gebruiken, bereiken uiteindelijk een status waarin de service-principal moet worden vernieuwd om het cluster te laten werken. Het beheren van service-principals voegt complexiteit toe. Daarom is het eenvoudiger om beheerde identiteiten te gebruiken. Dezelfde machtigingsvereisten gelden voor zowel service-principals als beheerde identiteiten.

*Beheerde identiteiten* zijn in feite een wrapper rond service-principals en maken het beheer eenvoudiger. Roulatie van referenties voor MI vindt automatisch elke 46 dagen plaats volgens Azure Active Directory standaardinstelling. AKS maakt gebruik van door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteitstypen. Deze identiteiten zijn momenteel onveranderbaar. Lees meer over beheerde [identiteiten voor Azure-resources voor meer informatie.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende resource hebben geïnstalleerd:

- De Azure CLI, versie 2.15.1 of hoger

## <a name="limitations"></a>Beperkingen

* Tenants verplaatsen/migreren van clusters met beheerde identiteit wordt niet ondersteund.
* Als het cluster is ingeschakeld, wijzigen Node-Managed Identity(NMI)-pods de iptables van de knooppunten om aanroepen naar het `aad-pod-identity` Azure Instance Metadata-eindpunt te onderscheppen. Deze configuratie betekent dat elke aanvraag bij het eindpunt voor metagegevens wordt onderschept door NMI, zelfs als de pod geen `aad-pod-identity` gebruikt. AzurePodIdentityException CRD kan worden geconfigureerd om te informeren dat aanvragen naar het eindpunt voor metagegevens die afkomstig zijn van een pod die overeenkomt met labels die zijn gedefinieerd in CRD, geproxied moeten worden zonder verwerking `aad-pod-identity` in NMI. De systeempods met `kubernetes.azure.com/managedby: aks` een label in de _naamruimte kube-system_ moeten worden uitgesloten door de `aad-pod-identity` CRD AzurePodIdentityException te configureren. Zie [Aad-pod-identity uitschakelen](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)voor een specifieke pod of toepassing voor meer informatie.
  Als u een uitzondering wilt configureren, installeert [u de YAML met de microfoon-uitzondering](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Samenvatting van beheerde identiteiten

AKS maakt gebruik van verschillende beheerde identiteiten voor ingebouwde services en invoegtoepassingen.

| Identiteit                       | Name    | Gebruiksvoorbeeld | Standaardmachtigingen | Bring Your Own Identity
|----------------------------|-----------|----------|
| Besturingsvlak | niet zichtbaar | Wordt gebruikt door AKS-onderdelen van het besturingsvlak voor het beheren van clusterresources, waaronder load balancers voor binnendijen en door AKS beheerde openbare IP's, en bewerkingen voor automatische schaalverschalen van clusters | Rol inzender voor knooppuntresourcegroep | Ondersteund
| Kubelet | AKS-clusternaam-agentpool | Verificatie met Azure Container Registry (ACR) | NA (voor kubernetes v1.15+) | Momenteel niet ondersteund
| Add-on | AzureNPM | Er is geen identiteit vereist | NA | No
| Add-on | AzureCNI-netwerkbewaking | Er is geen identiteit vereist | NA | No
| Add-on | azure-policy (gatekeeper) | Er is geen identiteit vereist | NA | No
| Add-on | azure-policy | Er is geen identiteit vereist | NA | No
| Add-on | Calico | Er is geen identiteit vereist | NA | No
| Add-on | Dashboard | Er is geen identiteit vereist | NA | No
| Add-on | HTTPApplicationRouting | De vereiste netwerkbronnen beheren | Lezersrol voor knooppuntresourcegroep, inzendersrol voor DNS-zone | No
| Add-on | Toepassingsgateway voor toegangspoort | De vereiste netwerkbronnen beheren| De rol Inzender voor de knooppuntresourcegroep | No
| Add-on | omsagent | Wordt gebruikt voor het verzenden van metrische AKS-gegevens naar Azure Monitor | De rol Uitgever van metrische gegevens bewaken | No
| Add-on | Virtual-Node (ACIConnector) | Beheert de vereiste netwerkbronnen voor Azure Container Instances (ACI) | De rol Inzender voor de knooppuntresourcegroep | No
| OSS-project | aad-pod-identity | Hiermee kunnen toepassingen veilig toegang krijgen tot cloudbronnen met Azure Active Directory (AAD) | NA | Stappen voor het verlenen van machtigingen op https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Een AKS-cluster met beheerde identiteiten maken

U kunt nu een AKS-cluster met beheerde identiteiten maken met behulp van de volgende CLI-opdrachten.

Maak eerst een Azure-resourcegroep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak vervolgens een AKS-cluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Zodra het cluster is gemaakt, kunt u de workloads van uw toepassing implementeren in het nieuwe cluster en daarmee werken, net zoals u hebt gedaan met AKS-clusters op basis van service-principals.

Haal ten slotte referenties op voor toegang tot het cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Een AKS-cluster bijwerken naar beheerde identiteiten (preview)

U kunt nu een AKS-cluster bijwerken dat momenteel met service-principals werkt om met beheerde identiteiten te werken met behulp van de volgende CLI-opdrachten.

Registreer eerst de functievlag voor door het systeem toegewezen identiteit:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Werk de door het systeem toegewezen identiteit bij:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Registreer de functievlag voor door de gebruiker toegewezen identiteit:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Werk de door de gebruiker toegewezen identiteit bij:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Zodra de door het systeem toegewezen of door de gebruiker toegewezen identiteiten zijn bijgewerkt naar beheerde identiteit, voert u een uit op uw knooppunten om de update naar de beheerde identiteit `az aks nodepool upgrade --node-image-only` te voltooien.

## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>De door het systeem toegewezen beheerde identiteit voor uw AKS-cluster verkrijgen en gebruiken

Controleer met de volgende CLI-opdracht of uw AKS-cluster een beheerde identiteit gebruikt:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

Als het cluster beheerde identiteiten gebruikt, ziet u de `clientId` waarde 'msi'. Een cluster dat in plaats daarvan een service-principal gebruikt, geeft in plaats daarvan de object-id weer. Bijvoorbeeld: 

```output
{
  "clientId": "msi"
}
```

Nadat u hebt gecontroleerd of het cluster beheerde identiteiten gebruikt, kunt u de object-id van de door het systeem toegewezen identiteit van het besturingsvlak vinden met de volgende opdracht:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> Voor het maken en gebruiken van uw eigen VNet, statisch IP-adres of gekoppelde Azure-schijf waar de resources zich buiten de resourcegroep van het werkknooppunt plaatsen, gebruikt u de PrincipalID van de door het systeem toegewezen beheerde identiteit van het cluster om een roltoewijzing uit te voeren. Zie Toegang tot andere Azure-resources delegeren voor meer informatie over [roltoewijzing.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> Het kan 60 minuten duren voordat machtigingen worden verleend aan beheerde identiteiten voor clusters die worden gebruikt door de Azure Cloud-provider.


## <a name="bring-your-own-control-plane-mi"></a>Bring your own control plane MI
Met een aangepaste besturingsvlakidentiteit kan toegang worden verleend tot de bestaande identiteit voordat het cluster wordt gemaakt. Deze functie maakt scenario's mogelijk, zoals het gebruik van een aangepast VNET of uitgaand type UDR met een vooraf gemaakte beheerde identiteit.

U moet de Azure CLI, versie 2.15.1 of hoger, hebben geïnstalleerd.

### <a name="limitations"></a>Beperkingen
* Azure Government wordt momenteel niet ondersteund.
* Azure China 21Vianet wordt momenteel niet ondersteund.

Als u nog geen beheerde identiteit hebt, maakt u er een met [az identity CLI.][az-identity-create]

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Het resultaat moet er als volgende uitzien:

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

Als uw beheerde identiteit deel uitmaakt van uw abonnement, kunt u de [opdracht az identity CLI gebruiken om][az-identity-list] er een query op uit te voeren.  

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

Het maken van een cluster met uw eigen beheerde identiteiten bevat deze profielgegevens voor userAssignedIdentities:

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
* Gebruik [Azure Resource Manager (ARM)-sjablonen om ][aks-arm-template] clusters met beheerde identiteit te maken.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-list]: /cli/azure/identity#az-identity-list
