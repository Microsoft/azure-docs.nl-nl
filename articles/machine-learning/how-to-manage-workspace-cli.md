---
title: Werkruimten maken met Azure CLI
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Azure CLI-extensie voor machine learning om een nieuwe werkruimte Azure Machine Learning maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 8a00f5474fb73677125b85e48fcc2a42f34fdeb0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876399"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Een werkruimte maken voor Azure Machine Learning met Azure CLI


In dit artikel leert u hoe u een werkruimte Azure Machine Learning maken met behulp van de Azure CLI. De Azure CLI biedt opdrachten voor het beheren van Azure-resources. De machine learning-extensie voor de CLI biedt opdrachten voor het werken met Azure Machine Learning resources.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, kunt u de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](/cli/azure/install-azure-cli).

    Als u [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) gebruikt, opent u de CLI via de browser en bevindt deze zich in de cloud.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>De CLI verbinden met uw Azure-abonnement

> [!IMPORTANT]
> Als u de Azure Cloud Shell gebruikt, kunt u deze sectie overslaan. De Cloud Shell verifieert u automatisch met behulp van het account dat u bij uw Azure-abonnement aanmeldt.

Er zijn verschillende manieren waarop u zich kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De eenvoudigste methode is interactieve verificatie met behulp van een browser. Voor interactieve verificatie opent u een opdrachtregel of terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de aanwijzingen op de opdrachtregel volgen. Dit omvat het bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en het invoeren van een autorisatiecode.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Zie Aanmelden met Azure CLI voor andere methoden [voor authenticatie.](/cli/azure/authenticate-azure-cli)

## <a name="create-a-workspace"></a>Een werkruimte maken

De Azure Machine Learning is afhankelijk van de volgende Azure-services of -entiteiten:

> [!IMPORTANT]
> Als u geen bestaande Azure-service opgeeft, wordt er automatisch een gemaakt tijdens het maken van de werkruimte. U moet altijd een resourcegroep opgeven. Wanneer u uw eigen opslagaccount koppelt, moet u ervoor zorgen dat het aan de volgende criteria voldoet:
>
> * Het opslagaccount is _geen_ Premium-account (Premium_LRS en Premium_GRS)
> * Zowel De mogelijkheden van Azure Blob als Azure File zijn ingeschakeld
> * Hiërarchische naamruimte (ADLS Gen 2) is uitgeschakeld
>
> Deze vereisten gelden alleen voor het _standaardopslagaccount_ dat door de werkruimte wordt gebruikt.

| Service | Parameter om een bestaand exemplaar op te geven |
| ---- | ---- |
| **Azure-resourcegroep** | `-g <resource-group-name>`
| **Azure Storage-account** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) biedt momenteel geen ondersteuning voor unicode-tekens in resourcegroepnamen. U kunt dit probleem oplossen door een resourcegroep te gebruiken die deze tekens niet bevat.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De Azure Machine Learning werkruimte moet worden gemaakt binnen een resourcegroep. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken. Met de volgende opdracht kunt u __een nieuwe resourcegroep maken__. Vervang `<resource-group-name>` door de naam die u voor deze resourcegroep wilt gebruiken. Vervang `<location>` door de Azure-regio die u voor deze resourcegroep wilt gebruiken:

> [!TIP]
> Selecteer een regio waar Azure Machine Learning beschikbaar is. Zie [Producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) voor informatie.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

De respons van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Zie [az group](/cli/azure/group) voor meer informatie over het werken met resourcegroepen.

### <a name="automatically-create-required-resources"></a>Automatisch vereiste resources maken

Gebruik de volgende opdracht om een nieuwe werkruimte te maken waarin de __services__ automatisch worden gemaakt:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> De naam van de werkruimte is niet-gevoelig.

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>Virtueel netwerk en privé-eindpunt

> [!IMPORTANT]
> Het gebruik van Azure Machine Learning werkruimte met private link is niet beschikbaar in de Azure Government regio's.

Als u de toegang tot uw werkruimte tot een virtueel netwerk wilt beperken, kunt u de volgende parameters gebruiken:

* `--pe-name`: de naam van het privé-eindpunt dat wordt gemaakt.
* `--pe-auto-approval`: Of privé-eindpuntverbindingen met de werkruimte automatisch moeten worden goedgekeurd.
* `--pe-resource-group`: de resourcegroep waarin u het privé-eindpunt wilt maken. Moet dezelfde groep zijn die het virtuele netwerk bevat.
* `--pe-vnet-name`: Het bestaande virtuele netwerk waarin u het privé-eindpunt wilt maken.
* `--pe-subnet-name`: de naam van het subnet waarin het privé-eindpunt moet worden maken. De standaardwaarde is `default`.

Zie Virtueel netwerkisolatie en privacyoverzicht voor meer informatie over het gebruik van een privé-eindpunt en virtueel [netwerk met uw werkruimte.](how-to-network-security-overview.md)

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Door de klant beheerde sleutel en werkruimte met grote bedrijfsimpact

Standaard worden metagegevens voor de werkruimte opgeslagen in een Azure Cosmos DB exemplaar dat door Microsoft wordt onderhouden. Deze gegevens worden versleuteld met door Microsoft beheerde sleutels.

> [!NOTE]
> Azure Cosmos DB wordt __niet gebruikt__ voor het opslaan van gegevens zoals modelprestaties, gegevens die zijn vastgelegd door experimenten of gegevens die zijn vastgelegd in uw modelimplementaties. Zie de sectie Bewaking en [](concept-azure-machine-learning-architecture.md) logboekregistratie van het artikel architectuur en concepten voor meer informatie over het bewaken van deze items.

In plaats van de door Microsoft beheerde sleutel te gebruiken, kunt u uw eigen sleutel verstrekken. Hiermee maakt u het Azure Cosmos DB dat metagegevens opgeslagen in uw Azure-abonnement. Gebruik de parameter om de Azure Key Vault sleutel op te geven en om de URL van de sleutel in de `--cmk-keyvault` `--resource-cmk-uri` kluis op te geven.

Voordat u de `--cmk-keyvault` `--resource-cmk-uri` parameters en gebruikt, moet u eerst de volgende acties uitvoeren:

1. Machtig de __Machine Learning-app__ (in Identiteits- en toegangsbeheer) met inzendermachtigingen voor uw abonnement.
1. Volg de stappen in [Door de klant beheerde sleutels configureren](../cosmos-db/how-to-setup-cmk.md) om:
    * De provider Azure Cosmos DB registreren
    * Een Azure Key Vault en configureren
    * Een sleutel genereren

U hoeft de instantie van de werkruimte niet handmatig Azure Cosmos DB maken. Er wordt er een voor u gemaakt tijdens het maken van de werkruimte. Deze Azure Cosmos DB wordt in een afzonderlijke resourcegroep gemaakt met behulp van een naam op basis van dit patroon: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Gebruik de parameter om de gegevens te beperken die Door Microsoft in uw werkruimte worden `--hbi-workspace` verzameld. 

> [!IMPORTANT]
> Het selecteren van een grote bedrijfsimpact kan alleen worden uitgevoerd bij het maken van een werkruimte. U kunt deze instelling niet wijzigen nadat de werkruimte is gemaakt.

Zie Enterprise security for Azure Machine Learning voor meer informatie over door de klant beheerde sleutels en werkruimte [met een hoge bedrijfsimpact.](concept-data-encryption.md#encryption-at-rest)

### <a name="use-existing-resources"></a>Bestaande resources gebruiken

Als u een werkruimte wilt maken die gebruikmaakt van bestaande resources, moet u de id voor de resources verstrekken. Gebruik de volgende opdrachten om de id voor de services op te halen:

> [!IMPORTANT]
> U hoeft niet alle bestaande resources op te geven. U kunt een of meer opgeven. U kunt bijvoorbeeld een bestaand opslagaccount opgeven en de werkruimte maakt de andere resources.

+ **Azure Storage account**: `az storage account show --name <storage-account-name> --query "id"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de id voor uw opslagaccount:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Als u een bestaand account Azure Storage gebruiken, kan dit geen Premium-account zijn (Premium_LRS en Premium_GRS). Het kan ook geen hiërarchische naamruimte hebben (gebruikt met Azure Data Lake Storage Gen2). Premium-opslag of hiërarchische naamruimte worden niet ondersteund met het _standaardopslagaccount_ van de werkruimte. U kunt Premium Storage of hiërarchische naamruimte gebruiken met _niet-standaardopslagaccounts._

+ **Azure-toepassing Insights**:

    1. Installeer de Application Insights-extensie:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Haal de id van uw Application Insight-service op:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de id voor uw Application Insights-service:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault:**`az keyvault show --name <key-vault-name> --query "ID"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de id voor uw sleutelkluis:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de id voor het containerregister:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > In het containerregister moet het [beheerdersaccount](../container-registry/container-registry-authentication.md#admin-account) zijn ingeschakeld voordat het kan worden gebruikt met een Azure Machine Learning werkruimte.

Zodra u de id's hebt voor de resource(s) die u wilt gebruiken met de werkruimte, gebruikt u de basisopdracht en voegt u de `az workspace create -w <workspace-name> -g <resource-group-name>` parameter(s) en id('s) toe voor de bestaande resources. Met de volgende opdracht maakt u bijvoorbeeld een werkruimte die gebruikmaakt van een bestaand containerregister:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Werkruimten op een lijst zetten

Gebruik de volgende opdracht om alle werkruimten voor uw Azure-abonnement weer te geven:

```azurecli-interactive
az ml workspace list
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Zie de documentatie over [az ml workspace list voor meer](/cli/azure/ml/workspace#az_ml_workspace_list) informatie.

## <a name="get-workspace-information"></a>Werkruimtegegevens verkrijgen

Gebruik de volgende opdracht om informatie over een werkruimte op te halen:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Zie de documentatie az [ml workspace show voor meer](/cli/azure/ml/workspace#az_ml_workspace_show) informatie.

## <a name="update-a-workspace"></a>Een werkruimte bijwerken

Gebruik de volgende opdracht om een werkruimte bij te werken:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Zie de documentatie over [az ml workspace update voor meer](/cli/azure/ml/workspace#az_ml_workspace_update) informatie.

## <a name="share-a-workspace-with-another-user"></a>Een werkruimte delen met een andere gebruiker

Gebruik de volgende opdracht om een werkruimte te delen met een andere gebruiker in uw abonnement:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Zie Gebruikers en rollen beheren voor meer informatie over op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) met [Azure Machine Learning.](how-to-assign-roles.md)

Zie de documentatie over [az ml workspace share voor meer](/cli/azure/ml/workspace#az_ml_workspace_share) informatie.

## <a name="sync-keys-for-dependent-resources"></a>Sleutels synchroniseren voor afhankelijke resources

Als u de toegangssleutels wijzigt voor een van de resources die door uw werkruimte worden gebruikt, duurt het ongeveer een uur voordat de werkruimte wordt gesynchroniseerd met de nieuwe sleutel. Gebruik de volgende opdracht om af te dwingen dat de nieuwe sleutels onmiddellijk worden gesynchroniseerd in de werkruimte:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Zie Toegangssleutels voor opslag opnieuw maken voor meer informatie over het wijzigen [van sleutels.](how-to-change-storage-access-key.md)

Zie de documentatie az [ml workspace sync-keys voor meer](/cli/azure/ml/workspace#az_ml_workspace_sync-keys) informatie.

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Als u een werkruimte wilt verwijderen nadat deze niet meer nodig is, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Als u een werkruimte verwijdert, worden de Application Insight, het opslagaccount, de sleutelkluis of het containerregister die door de werkruimte worden gebruikt, niet verwijderd.

U kunt ook de resourcegroep verwijderen, waardoor de werkruimte en alle andere Azure-resources in de resourcegroep worden verwijderd. Gebruik de volgende opdracht om de resourcegroep te verwijderen:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Zie de documentatie over [az ml workspace delete voor meer](/cli/azure/ml/workspace#az_ml_workspace_delete) informatie.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten met resourceproviders

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>De werkruimte verplaatsen

> [!WARNING]
> Het verplaatsen Azure Machine Learning werkruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe tenant, wordt niet ondersteund. Als u dit doet, kunnen er fouten optreden.

### <a name="deleting-the-azure-container-registry"></a>De Azure Container Registry

De Azure Machine Learning werkruimte gebruikt Azure Container Registry (ACR) voor sommige bewerkingen. Er wordt automatisch een ACR-exemplaar aan het maken wanneer er voor het eerst een nodig is.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de az [ml-documentatie](/cli/azure/ml) voor meer informatie over machine learning Azure CLI-extensie voor uw omgeving.