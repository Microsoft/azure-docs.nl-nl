---
title: Azure RBAC in Kubernetes beheren vanuit Azure
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het gebruik van Azure RBAC voor Kubernetes-autorisatie met Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c708a577a1c2e4bb8f7ddff90f458afd0d9e566f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782995"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC gebruiken voor Kubernetes-autorisatie (preview)

Vandaag de dag kunt u al [gebruikmaken van geïntegreerde verificatie tussen Azure Active Directory (Azure AD) en AKS.](managed-aad.md) Wanneer deze integratie is ingeschakeld, kunnen klanten Azure AD-gebruikers, -groepen of -service-principals gebruiken als onderwerp in Kubernetes RBAC. Meer informatie vindt u [hier.](azure-ad-rbac.md)
Met deze functie kunt u gebruikersidentiteiten en -referenties voor Kubernetes afzonderlijk beheren. U moet Azure RBAC en Kubernetes RBAC echter nog steeds afzonderlijk instellen en beheren. Zie hier voor meer informatie over verificatie en autorisatie met RBAC [in](concepts-identity.md)AKS.

Dit document bevat een nieuwe benadering waarmee geïntegreerd beheer en toegangsbeheer mogelijk is voor Azure-resources, AKS en Kubernetes-resources.

## <a name="before-you-begin"></a>Voordat u begint

De mogelijkheid om RBAC voor Kubernetes-resources vanuit Azure te beheren, biedt u de keuze om RBAC voor de clusterbronnen te beheren met behulp van Azure of systeemeigen Kubernetes-mechanismen. Wanneer deze functie is ingeschakeld, worden Azure AD-principals uitsluitend gevalideerd door Azure RBAC, terwijl gewone Kubernetes-gebruikers en -serviceaccounts uitsluitend worden gevalideerd door Kubernetes RBAC. Zie hier voor meer informatie over verificatie en autorisatie met RBAC [in](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Vereisten 
- Zorg ervoor dat u azure CLI versie 2.9.0 of hoger hebt
- Zorg ervoor dat de `EnableAzureRBACPreview` functievlag is ingeschakeld.
- Zorg ervoor dat u de `aks-preview` [CLI-extensie][az-extension-add] v0.4.55 of hoger hebt geïnstalleerd
- Zorg ervoor dat u [kubectl v1.18.3+ hebt geïnstalleerd.][az-aks-install-cli]

#### <a name="register-enableazurerbacpreview-preview-feature"></a>`EnableAzureRBACPreview`Preview-functie registreren

Als u een AKS-cluster wilt maken dat gebruikmaakt van Azure RBAC voor Kubernetes-autorisatie, moet u de `EnableAzureRBACPreview` functievlag inschakelen voor uw abonnement.

Registreer de `EnableAzureRBACPreview` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 U kunt de registratiestatus controleren met behulp van de opdracht [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van [de opdracht az provider][az-provider-register] register:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster wilt maken dat gebruikmaakt van Azure RBAC, hebt u de *CLI-extensie aks-preview* versie 0.4.55 of hoger nodig. Installeer de *Azure CLI-extensie aks-preview* met behulp van de [opdracht az extension add][az-extension-add] of installeer beschikbare updates met de opdracht az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Beperkingen

- Vereist [beheerde Azure AD-integratie.](managed-aad.md)
- U kunt Azure RBAC voor Kubernetes-autorisatie niet integreren in bestaande clusters tijdens de preview, maar u kunt dit wel op algemene beschikbaarheid (GA).
- Gebruik [kubectl v1.18.3+][az-aks-install-cli].
- Als u CRD's hebt en aangepaste roldefinities maakt, is de enige manier om crd's op dit moment te behandelen. `Microsoft.ContainerService/managedClusters/*/read` AKS werkt aan meer gedetailleerde machtigingen voor CRD's. Voor de resterende objecten kunt u de specifieke API-groepen gebruiken, bijvoorbeeld: `Microsoft.ContainerService/apps/deployments/read` .
- Het kan 5 minuten duren voor nieuwe roltoewijzingen zijn doorgegeven en bijgewerkt door de autorisatieserver.
- Vereist dat de Azure AD-tenant die is geconfigureerd voor verificatie hetzelfde is als de tenant voor het abonnement dat het AKS-cluster bevat. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Een nieuw cluster maken met behulp van Azure RBAC en beheerde Azure AD-integratie

Maak een AKS-cluster met behulp van de volgende CLI-opdrachten.

Een Azure-resourcegroep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak het AKS-cluster met beheerde Azure AD-integratie en Azure RBAC voor Kubernetes-autorisatie.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Het maken van een cluster met Azure AD-integratie en Azure RBAC voor Kubernetes-autorisatie heeft de volgende sectie in de antwoordtekst:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Roltoewijzingen maken voor gebruikers voor toegang tot het cluster

AKS biedt de volgende vier ingebouwde rollen:


| Rol                                | Beschrijving  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC-lezer  | Staat alleen-lezentoegang toe om de meeste objecten in een naamruimte te zien. Het weergeven van rollen of rolbindingen is niet toegestaan. Deze rol staat geen weergave toe, omdat het lezen van de inhoud van Geheimen toegang biedt tot ServiceAccount-referenties in de naamruimte, waardoor API-toegang mogelijk is als elk ServiceAccount in de naamruimte (een vorm van escalatie van `Secrets` bevoegdheden)  |
| Azure Kubernetes Service RBAC Writer | Hiermee staat u lees-/schrijftoegang toe tot de meeste objecten in een naamruimte. Deze rol staat het weergeven of wijzigen van rollen of rolbindingen niet toe. Met deze rol kunt u echter pods openen en uitvoeren als elk serviceaccount in de naamruimte, zodat deze kan worden gebruikt om de API-toegangsniveaus van elk ServiceAccount in de naamruimte `Secrets` te verkrijgen. |
| Azure Kubernetes Service RBAC-beheerder  | Hiermee staat u beheerderstoegang toe, bedoeld om te worden verleend binnen een naamruimte. Hiermee staat u lees-/schrijftoegang toe tot de meeste resources in een naamruimte (of clusterbereik), met inbegrip van de mogelijkheid om rollen en rolbindingen binnen de naamruimte te maken. Deze rol staat geen schrijftoegang toe tot resourcequota of tot de naamruimte zelf. |
| Azure Kubernetes Service RBAC-clusterbeheerder  | Hiermee staat u supergebruikerstoegang toe om acties uit te voeren op elke resource. Het biedt volledige controle over elke resource in het cluster en in alle naamruimten. |


Roltoewijzingen die zijn beperkt tot het hele **AKS-cluster,** kunnen worden uitgevoerd op de blade Access Control (IAM) van de clusterresource op Azure Portal of met behulp van Azure CLI-opdrachten, zoals hieronder wordt weergegeven:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

waarbij een gebruikersnaam (bijvoorbeeld ) of zelfs de `<AAD-ENTITY-ID>` user@contoso.com ClientID van een service-principal kan zijn.

U kunt ook roltoewijzingen maken die zijn gericht op een specifieke **naamruimte** in het cluster:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Op dit moment moeten roltoewijzingen die zijn beperkt tot naamruimten, worden geconfigureerd via Azure CLI.


### <a name="create-custom-roles-definitions"></a>Aangepaste roldefinities maken

Desgewenst kunt u ervoor kiezen om uw eigen roldefinitie te maken en vervolgens toe te wijzen zoals hierboven.

Hieronder vindt u een voorbeeld van een roldefinitie waarmee een gebruiker alleen implementaties en niets anders kan lezen. U kunt hier de volledige lijst met mogelijke [acties controleren.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


Kopieer de onderstaande json naar een bestand met de naam `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

Vervang `<YOUR SUBSCRIPTION ID>` door de id van uw abonnement, die u kunt krijgen door het volgende uit te laten uitvoeren:

```azurecli-interactive
az account show --query id -o tsv
```


Nu kunnen we de roldefinitie maken door de onderstaande opdracht uit te voeren vanuit de map waarin u hebt `deploy-view.json` opgeslagen:

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Nu u de roldefinitie hebt, kunt u deze toewijzen aan een gebruiker of een andere identiteit door het volgende uit te uitvoeren:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Azure RBAC gebruiken voor Kubernetes-autorisatie met `kubectl`

> [!NOTE]
> Zorg ervoor dat u de meest recente kubectl hebt door de onderstaande opdracht uit te voeren:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Mogelijk moet u deze uitvoeren met `sudo` bevoegdheden. 

Nu u de gewenste rol en machtigingen hebt toegewezen. U kunt beginnen met het aanroepen van de Kubernetes-API, bijvoorbeeld vanuit `kubectl` .

Voor dit doel gaan we eerst de kubeconfig van het cluster op halen met behulp van de onderstaande opdracht:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> U hebt de ingebouwde [Azure Kubernetes Service clustergebruiker](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) nodig om de bovenstaande stap uit te voeren.

Nu kunt u kubectl gebruiken om bijvoorbeeld de knooppunten in het cluster weer te geven. De eerste keer dat u de opdracht hebt uitgevoerd, moet u zich aanmelden en voor volgende opdrachten wordt het respectieve toegangsken gebruikt.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Azure RBAC gebruiken voor Kubernetes-autorisatie met `kubelogin`

Als u aanvullende scenario's wilt deblokkeren, zoals niet-interactieve aanmeldingen, oudere versies of het gebruik van eenmalige aanmelding voor meerdere clusters zonder dat u zich hoeft aan te melden bij een nieuw cluster, verleend dat uw token nog geldig is, heeft AKS een exec-invoegcode met de naam `kubectl` [`kubelogin`](https://github.com/Azure/kubelogin) gemaakt.

U kunt deze gebruiken door het volgende uit te uitvoeren:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

De eerste keer moet u zich interactief aanmelden, net als bij gewone kubectl, maar daarna is dat niet meer nodig, zelfs niet voor nieuwe Azure AD-clusters (zolang uw token nog geldig is).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Opschonen

### <a name="clean-role-assignment"></a>Roltoewijzing ops schonen

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Kopieer de id of id's van alle toewijzingen die u hebt gedaan en vervolgens.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Roldefinitie ops schonen

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Cluster en resourcegroep verwijderen

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over AKS-verificatie, autorisatie, Kubernetes RBAC en Azure RBAC vindt [u hier.](concepts-identity.md)
- Lees hier meer over [](../role-based-access-control/overview.md)Azure RBAC.
- Lees hier meer over de acties die u kunt gebruiken om aangepaste Azure-rollen voor Kubernetes-autorisatie gedetailleerd te [definiëren.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
