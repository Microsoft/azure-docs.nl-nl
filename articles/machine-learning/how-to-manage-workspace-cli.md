---
title: Werk ruimten maken met Azure CLI
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Azure CLI-extensie voor machine learning om een nieuwe Azure Machine Learning-werk ruimte te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: b6b23e792aaef4d70e9ffc9be3667f0abef49e81
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489545"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Een werk ruimte maken voor Azure Machine Learning met Azure CLI


In dit artikel leert u hoe u een Azure Machine Learning-werk ruimte maakt met behulp van de Azure CLI. De Azure CLI bevat opdrachten voor het beheer van Azure-resources. De machine learning-extensie voor de CLI bevat opdrachten voor het werken met Azure Machine Learning-resources.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](/cli/azure/install-azure-cli).

    Als u [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) gebruikt, opent u de CLI via de browser en bevindt deze zich in de cloud.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>De CLI koppelen aan uw Azure-abonnement

> [!IMPORTANT]
> Als u de Azure Cloud Shell gebruikt, kunt u deze sectie overs Laan. De Cloud shell verifieert u automatisch met het account dat u hebt aangemeld bij uw Azure-abonnement.

Er zijn verschillende manieren waarop u zich kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De eenvoudigste methode is interactieve verificatie met behulp van een browser. Voor interactieve verificatie opent u een opdrachtregel of terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de aanwijzingen op de opdrachtregel volgen. Dit omvat het bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en het invoeren van een autorisatiecode.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Zie [Aanmelden met Azure cli](/cli/azure/authenticate-azure-cli)voor andere verificatie methoden.

## <a name="install-the-machine-learning-extension"></a>De machine learning-extensie installeren

Installeer de machine learning-extensie met de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Een werkruimte maken

De Azure Machine Learning-werk ruimte is afhankelijk van de volgende Azure-Services of-entiteiten:

> [!IMPORTANT]
> Als u geen bestaande Azure-service opgeeft, wordt er automatisch een gemaakt tijdens het maken van de werk ruimte. U moet altijd een resource groep opgeven. Wanneer u uw eigen opslag account koppelt, moet u ervoor zorgen dat het voldoet aan de volgende criteria:
>
> * Het opslag account is _geen_ Premium-account (Premium_LRS en Premium_GRS)
> * Zowel Azure Blob-als Azure-bestands mogelijkheden zijn ingeschakeld
> * Hiërarchische naam ruimte (ADLS gen 2) is uitgeschakeld
>
> Deze vereisten gelden alleen voor het _standaard_ opslag account dat wordt gebruikt door de werk ruimte.

| Service | Para meter om een bestaand exemplaar op te geven |
| ---- | ---- |
| **Azure-resourcegroep** | `-g <resource-group-name>`
| **Azure Storage-account** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) biedt momenteel geen ondersteuning voor Unicode-tekens in namen van resource groepen. Gebruik een resource groep die deze tekens niet bevat om dit probleem te verhelpen.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De Azure Machine Learning-werk ruimte moet binnen een resource groep worden gemaakt. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken. Met de volgende opdracht kunt u __een nieuwe resourcegroep maken__. Vervang `<resource-group-name>` door de naam die u voor deze resourcegroep wilt gebruiken. Vervang `<location>` door de Azure-regio die u voor deze resourcegroep wilt gebruiken:

> [!TIP]
> U moet een regio selecteren waar Azure Machine Learning beschikbaar is. Zie [Producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) voor informatie.

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

Als u een nieuwe werk ruimte wilt maken waar de __Services automatisch worden gemaakt__, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> De naam van de werk ruimte is niet hoofdletter gevoelig.

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

### <a name="virtual-network-and-private-endpoint"></a>Virtueel netwerk en persoonlijk eind punt

> [!IMPORTANT]
> Het gebruik van een Azure Machine Learning werk ruimte met een persoonlijke koppeling is niet beschikbaar in de Azure Government regio's.

Als u de toegang tot uw werk ruimte wilt beperken tot een virtueel netwerk, kunt u de volgende para meters gebruiken:

* `--pe-name`: De naam van het persoonlijke eind punt dat is gemaakt.
* `--pe-auto-approval`: Of privé-eindpunt verbindingen met de werk ruimte automatisch moeten worden goedgekeurd.
* `--pe-resource-group`: De resource groep waarin het persoonlijke eind punt moet worden gemaakt. Moet dezelfde groep zijn die het virtuele netwerk bevat.
* `--pe-vnet-name`: Het bestaande virtuele netwerk voor het maken van het persoonlijke eind punt in.
* `--pe-subnet-name`: De naam van het subnet waarin het persoonlijke eind punt moet worden gemaakt. De standaardwaarde is `default`.

Zie [Virtual Network-isolatie en privacy overview](how-to-network-security-overview.md)voor meer informatie over het gebruik van een persoonlijk eind punt en een virtueel netwerk met uw werk ruimte.

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Door de klant beheerde sleutel en een werk ruimte met hoge bedrijfs impact

Meta gegevens voor de werk ruimte worden standaard opgeslagen in een Azure Cosmos DB exemplaar dat door micro soft wordt beheerd. Deze gegevens zijn versleuteld met door micro soft beheerde sleutels.

> [!NOTE]
> Azure Cosmos DB wordt __niet__ gebruikt voor het opslaan van gegevens zoals model prestaties, informatie die is vastgelegd door experimenten of informatie die is vastgelegd in uw model implementaties. Zie de sectie [bewaking en logboek registratie](concept-azure-machine-learning-architecture.md) in het artikel architectuur en concepten voor meer informatie over het bewaken van deze items.

In plaats van de door micro soft beheerde sleutel te gebruiken, kunt u de zelf-sleutel opgeven. Dit maakt het Azure Cosmos DB-exemplaar dat meta gegevens opslaat in uw Azure-abonnement. Gebruik de `--cmk-keyvault` para meter om de Azure Key Vault op te geven die de sleutel bevat en `--resource-cmk-uri` om de URL op te geven van de sleutel in de kluis.

Voordat u de `--cmk-keyvault` `--resource-cmk-uri` para meters en gebruikt, moet u eerst de volgende acties uitvoeren:

1. Machtig de __machine learning-app__ (in identiteits-en toegangs beheer) met Inzender machtigingen voor uw abonnement.
1. Volg de stappen in door de [klant beheerde sleutels configureren](../cosmos-db/how-to-setup-cmk.md) voor:
    * De Azure Cosmos DB provider registreren
    * Een Azure Key Vault maken en configureren
    * Een sleutel genereren

U hoeft het Azure Cosmos DB exemplaar niet hand matig te maken, er wordt een voor u gemaakt tijdens het maken van de werk ruimte. Dit Azure Cosmos DB exemplaar wordt in een afzonderlijke resource groep gemaakt met behulp van een naam op basis van dit patroon: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Gebruik de para meter om de gegevens te beperken die door micro soft worden verzameld op uw werk ruimte `--hbi-workspace` . 

> [!IMPORTANT]
> Het selecteren van belang rijke bedrijfs impact kan alleen worden uitgevoerd bij het maken van een werk ruimte. U kunt deze instelling niet wijzigen nadat de werk ruimte is gemaakt.

Zie [Enter prise Security for Azure machine learning](concept-data-encryption.md#encryption-at-rest)voor meer informatie over door de klant beheerde sleutels en een werk ruimte met een grote bedrijfs impact.

### <a name="use-existing-resources"></a>Bestaande resources gebruiken

Als u een werk ruimte wilt maken waarin bestaande resources worden gebruikt, moet u de ID voor de resources opgeven. Gebruik de volgende opdrachten om de ID voor de services op te halen:

> [!IMPORTANT]
> U hoeft niet alle bestaande resources op te geven. U kunt een of meer opgeven. U kunt bijvoorbeeld een bestaand opslag account opgeven en de andere resources worden gemaakt door de werk ruimte.

+ **Azure Storage account**: `az storage account show --name <storage-account-name> --query "id"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de ID voor uw opslag account:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Als u een bestaand Azure Storage account wilt gebruiken, kan het geen Premium-account zijn (Premium_LRS en Premium_GRS). Het kan ook geen hiërarchische naam ruimte hebben (gebruikt met Azure Data Lake Storage Gen2). Geen enkele Premium-opslag of hiërarchische naam ruimte wordt ondersteund met het _standaard_ opslag account van de werk ruimte. U kunt Premium-opslag of een hiërarchische naam ruimte gebruiken met _niet-standaard_ opslag accounts.

+ **Azure-toepassing Insights**:

    1. Installeer de Application Insights-extensie:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. De ID ophalen van uw Application Insight-service:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de ID voor uw Application Insights-service:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de ID voor uw sleutel kluis:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de ID voor het container register:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Het [beheerders account](../container-registry/container-registry-authentication.md#admin-account) moet zijn ingeschakeld voor het container register voordat het kan worden gebruikt met een Azure machine learning-werk ruimte.

Wanneer u de Id's hebt voor de resource (s) die u wilt gebruiken voor de werk ruimte, gebruikt u de basis `az workspace create -w <workspace-name> -g <resource-group-name>` opdracht en voegt u de para meter (s) en de id ('s) toe voor de bestaande resources. Met de volgende opdracht maakt u bijvoorbeeld een werk ruimte die gebruikmaakt van een bestaand container register:

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

## <a name="list-workspaces"></a>Werk ruimten weer geven

Als u alle werk ruimten voor uw Azure-abonnement wilt weer geven, gebruikt u de volgende opdracht:

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

Zie de documentatie voor de [lijst AZ ml-werk ruimte](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-list) voor meer informatie.

## <a name="get-workspace-information"></a>Werkruimte gegevens ophalen

Gebruik de volgende opdracht om informatie over een werk ruimte op te halen:

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

Zie de documentatie van [AZ ml Workspace show](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-show) voor meer informatie.

## <a name="update-a-workspace"></a>Een werk ruimte bijwerken

Als u een werk ruimte wilt bijwerken, gebruikt u de volgende opdracht:

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

Zie voor meer informatie de documentatie van [AZ ml Workspace update](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Een werk ruimte delen met een andere gebruiker

Als u een werk ruimte wilt delen met een andere gebruiker in uw abonnement, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Zie [gebruikers en rollen beheren](how-to-assign-roles.md)voor meer informatie over toegangs beheer op basis van rollen (Azure RBAC) met Azure machine learning.

Zie voor meer informatie de documentatie bij [AZ ml-werkruimte share](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-share) .

## <a name="sync-keys-for-dependent-resources"></a>Sleutels voor afhankelijke Resources synchroniseren

Als u de toegangs sleutels voor een van de resources die worden gebruikt door uw werk ruimte wijzigt, duurt het ongeveer een uur voordat de werk ruimte wordt gesynchroniseerd met de nieuwe sleutel. Gebruik de volgende opdracht om ervoor te zorgen dat de werk ruimte onmiddellijk de nieuwe sleutels synchroniseert:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Zie [toegangs sleutels voor opslag opnieuw genereren](how-to-change-storage-access-key.md)voor meer informatie over het wijzigen van sleutels.

Zie voor meer informatie de documentatie van de [werk ruimte synchronisatie van AZ ml-sleutels](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Als u een werk ruimte wilt verwijderen nadat deze niet meer nodig is, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Als u een werk ruimte verwijdert, worden de toepassings inzichten, het opslag account, de sleutel kluis of het container register dat door de werk ruimte wordt gebruikt, niet verwijderd.

U kunt ook de resource groep verwijderen, waarmee de werk ruimte en alle andere Azure-resources in de resource groep worden verwijderd. Als u de resource groep wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Zie voor meer informatie de documentatie voor het verwijderen van de [werk ruimte AZ ml](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-delete) .

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten van de resource provider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>De werk ruimte verplaatsen

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werk ruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe Tenant, wordt niet ondersteund. Dit kan fouten veroorzaken.

### <a name="deleting-the-azure-container-registry"></a>De Azure Container Registry verwijderen

In de Azure Machine Learning-werk ruimte wordt Azure Container Registry (ACR) gebruikt voor bepaalde bewerkingen. Er wordt automatisch een ACR-exemplaar gemaakt wanneer dit voor het eerst nodig is.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de documentatie van [AZ ml](/cli/azure/ext/azure-cli-ml/ml) voor meer informatie over de Azure cli-extensie voor machine learning.