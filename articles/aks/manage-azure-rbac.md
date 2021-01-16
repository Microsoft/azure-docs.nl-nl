---
title: Azure RBAC in Kubernetes beheren vanuit Azure
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het gebruik van Azure RBAC voor Kubernetes-autorisatie met Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: af8d0300b533d9f25cddf225f4ffbe78ca6bf2cb
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249632"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC gebruiken voor Kubernetes-autorisatie (preview)

U kunt nu al gebruikmaken [van geïntegreerde verificatie tussen Azure Active Directory (Azure AD) en AKS](managed-aad.md). Als deze integratie is ingeschakeld, kunnen klanten Azure AD-gebruikers,-groepen of-service-principals gebruiken als onderwerpen in Kubernetes RBAC. Zie [hier](azure-ad-rbac.md)meer informatie.
Met deze functie kunt u gebruikers identiteiten en referenties voor Kubernetes niet afzonderlijk beheren. U moet echter nog steeds Azure RBAC en Kubernetes RBAC afzonderlijk instellen en beheren. Meer informatie over verificatie en autorisatie met RBAC op AKS vindt u [hier](concepts-identity.md).

In dit document wordt een nieuwe aanpak behandeld waarmee u het uniforme beheer en toegangs beheer kunt gebruiken voor Azure-resources, AKS en Kubernetes-resources.

## <a name="before-you-begin"></a>Voordat u begint

De mogelijkheid om RBAC voor Kubernetes-resources van Azure te beheren biedt u de keuze voor het beheren van RBAC voor de cluster bronnen met behulp van Azure-of native Kubernetes-mechanismen. Wanneer deze functie is ingeschakeld, worden Azure AD-principals alleen gevalideerd door Azure RBAC, terwijl reguliere Kubernetes-gebruikers en-service accounts uitsluitend worden gevalideerd door Kubernetes RBAC. Meer informatie over verificatie en autorisatie met RBAC op AKS vindt u [hier](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Vereisten 
- Zorg ervoor dat u de Azure CLI-versie 2.9.0 of hoger hebt
- Zorg ervoor dat de `EnableAzureRBACPreview` functie vlag is ingeschakeld.
- Controleer of u de `aks-preview` [cli-extensie][az-extension-add] v 0.4.55 of hoger hebt geïnstalleerd
- Zorg ervoor dat u [kubectl v 1.18.3 +][az-aks-install-cli]hebt geïnstalleerd.

#### <a name="register-enableazurerbacpreview-preview-feature"></a>`EnableAzureRBACPreview`Preview-functie registreren

Als u een AKS-cluster wilt maken dat gebruikmaakt van Azure RBAC voor Kubernetes-autorisatie, moet u de `EnableAzureRBACPreview` functie vlag inschakelen voor uw abonnement.

Registreer de `EnableAzureRBACPreview` functie vlag met de opdracht [AZ feature REGI ster][az-feature-register] , zoals weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 U kunt de registratiestatus controleren met behulp van de opdracht [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster] [AZ-provider-REGI ster]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster wilt maken dat gebruikmaakt van Azure RBAC, hebt u de *AKS-preview cli-* extensie versie 0.4.55 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] of installeer beschik bare updates met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Beperkingen

- Hiervoor is [beheerde Azure AD-integratie](managed-aad.md)vereist.
- U kunt Azure RBAC voor Kubernetes-autorisatie niet integreren in bestaande clusters tijdens de preview-periode, maar u kunt ook op algemene Beschik baarheid (GA).
- Gebruik [kubectl v 1.18.3 +][az-aks-install-cli].
- Als u CRDs hebt en aangepaste roldefinities maakt, is de enige manier om CRDs vandaag te bieden `Microsoft.ContainerService/managedClusters/*/read` . AKS is bezig met het bieden van meer gedetailleerde machtigingen voor CRDs. Voor de resterende objecten kunt u de specifieke API-groepen gebruiken, bijvoorbeeld: `Microsoft.ContainerService/apps/deployments/read` .
- Nieuwe roltoewijzingen kunnen tot 5min duren om door te geven en te worden bijgewerkt door de autorisatie server.
- Vereist dat de Azure AD-Tenant die is geconfigureerd voor verificatie hetzelfde is als de Tenant voor het abonnement dat het AKS-cluster bevat. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Een nieuw cluster maken met Azure RBAC en beheerde Azure AD-integratie

Maak een AKS-cluster met behulp van de volgende CLI-opdrachten.

Een Azure-resource groep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak het AKS-cluster met beheerde Azure AD-integratie en Azure RBAC voor Kubernetes-autorisatie.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Een geslaagde het maken van een cluster met Azure AD-integratie en Azure RBAC voor Kubernetes-autorisatie heeft de volgende sectie in de antwoord tekst:

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

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Roltoewijzingen voor gebruikers maken voor toegang tot het cluster

AKS biedt de volgende vier ingebouwde rollen:


| Rol                                | Beschrijving  |
|-------------------------------------|--------------|
| RBAC-lezer van de Azure Kubernetes service  | Hiermee staat u alleen-lezen toegang toe om de meeste objecten in een naam ruimte weer te geven. Het weer geven van functies of functie bindingen is niet toegestaan. Deze rol staat weer gave niet toe `Secrets` , omdat het lezen van de inhoud van geheimen toegang biedt tot serviceaccount-referenties in de naam ruimte, waardoor API-toegang zou worden toegestaan als serviceaccount in de naam ruimte (een vorm van bevoegdheden escalatie)  |
| RBAC-schrijver van Azure Kubernetes service | Hiermee wordt lees-/schrijftoegang tot de meeste objecten in een naam ruimte toegestaan. Deze rol staat het weer geven of wijzigen van rollen of rollen bindingen niet toe. Met deze rol is het echter mogelijk `Secrets` om de serviceaccount in de naam ruimte te benaderen en uit te voeren, zodat deze kan worden gebruikt om de API-toegangs niveaus van een wille keurige serviceaccount in de naam ruimte te verkrijgen. |
| RBAC-beheerder voor Azure Kubernetes service  | Hiermee kan beheerders toegang worden verleend binnen een naam ruimte. Hiermee staat u lees-/schrijftoegang toe voor de meeste bronnen in een naam ruimte (of cluster bereik), inclusief de mogelijkheid om rollen en rollen bindingen te maken binnen de naam ruimte. Deze rol staat geen schrijf toegang tot resource quota of de naam ruimte zelf toe. |
| De Azure Kubernetes service RBAC-cluster beheerder  | Hiermee kan toegang van Super gebruikers elke actie op elke resource uitvoeren. Hiermee krijgt u volledige controle over elke resource in het cluster en in alle naam ruimten. |


Rollen toewijzingen binnen bereik van het **hele AKS-cluster** kunnen worden uitgevoerd op de blade Access Control (IAM) van de cluster bron op Azure portal of met behulp van Azure cli-opdrachten zoals hieronder wordt weer gegeven:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

Dit `<AAD-ENTITY-ID>` kan een gebruikers naam zijn (bijvoorbeeld user@contoso.com ) of zelfs de ClientID van een service-principal.

U kunt ook roltoewijzingen maken die zijn gericht op een specifieke **naam ruimte** binnen het cluster:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Momenteel moeten roltoewijzingen in het bereik van naam ruimten worden geconfigureerd via Azure CLI.


### <a name="create-custom-roles-definitions"></a>Definities voor aangepaste rollen maken

Optioneel kunt u ervoor kiezen om uw eigen roldefinitie te maken en vervolgens als hierboven toe te wijzen.

Hieronder ziet u een voor beeld van een roldefinitie waarmee een gebruiker alleen implementaties en niets kan lezen. U kunt [hier](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)de volledige lijst met mogelijke acties controleren.


Kopieer de onderstaande JSON naar een bestand met de naam `deploy-view.json` .

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

Vervang `<YOUR SUBSCRIPTION ID>` door de id van uw abonnement, dat u kunt bereiken door de volgende handelingen uit te voeren:

```azurecli-interactive
az account show --query id -o tsv
```


Nu kunnen we de roldefinitie maken door de onderstaande opdracht uit te voeren vanuit de map waarin u hebt opgeslagen `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Nu u de roldefinitie hebt, kunt u deze toewijzen aan een gebruiker of een andere identiteit door het volgende uit te voeren:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Gebruik Azure RBAC voor Kubernetes-autorisatie met `kubectl`

> [!NOTE]
> Zorg ervoor dat u over de meest recente kubectl beschikt door de onderstaande opdracht uit te voeren:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Mogelijk moet u deze uitvoeren met `sudo` bevoegdheden. 

Nu u de gewenste rol en machtigingen hebt toegewezen. U kunt beginnen met het aanroepen van de Kubernetes-API, bijvoorbeeld van `kubectl` .

Laten we voor dit doel eerst de kubeconfig van het cluster ophalen met behulp van de onderstaande opdracht:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> U hebt de [Azure Kubernetes service-cluster gebruiker](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) ingebouwde rol nodig om de bovenstaande stap uit te voeren.

Nu kunt u kubectl gebruiken om bijvoorbeeld de knoop punten in het cluster weer te geven. De eerste keer dat u deze uitvoert, moet u zich aanmelden en volgende opdrachten gebruiken het respectieve toegangs token.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Gebruik Azure RBAC voor Kubernetes-autorisatie met `kubelogin`

Voor het deblokkeren van aanvullende scenario's, zoals niet-interactieve aanmeldingen, oudere `kubectl` versies of het gebruiken van SSO op meerdere clusters zonder dat u zich hoeft aan te melden bij een nieuw cluster, kunt u aan de hand van aks een exec-invoeg toepassing met de naam maken [`kubelogin`](https://github.com/Azure/kubelogin) .

U kunt deze gebruiken door het volgende uit te voeren:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

De eerste keer moet u zich interactief aanmelden, net als bij gewone kubectl, maar u hoeft niet meer te doen, zelfs voor nieuwe Azure AD-clusters (zolang uw token nog geldig is).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Opschonen

### <a name="clean-role-assignment"></a>Roltoewijzing opschonen

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Kopieer de ID of Id's uit alle toewijzingen die u hebt gedaan en klik vervolgens.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Roldefinitie opschonen

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Cluster en resource groep verwijderen

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

- Lees [hier](concepts-identity.md)meer over AKS-verificatie, autorisatie, Kubernetes RBAC en Azure RBAC.
- Lees [hier](../role-based-access-control/overview.md)meer over Azure RBAC.
- Lees [hier](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)meer over de acties die u kunt gebruiken om aangepaste Azure-rollen voor Kubernetes-autorisatie te definiëren.


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
