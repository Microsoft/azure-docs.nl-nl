---
title: Resource groepen beheren-Azure CLI
description: Gebruik Azure CLI voor het beheren van uw resource groepen via Azure Resource Manager. Laat zien hoe u resource groepen kunt maken, weer geven en verwijderen.
author: mumian
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e28b66844eaa0b73c2654175dea2e31d3cd75f5d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172093"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure Resource Manager-resource groepen beheren met behulp van Azure CLI

Meer informatie over het gebruik van Azure CLI met [Azure Resource Manager](overview.md) voor het beheren van uw Azure-resource groepen. Zie [Azure-resources beheren met Azure cli](manage-resources-cli.md)voor meer informatie over het beheren van Azure-resources.

Andere artikelen over het beheren van resource groepen:

- [Azure-resource groepen beheren met behulp van de Azure Portal](manage-resources-portal.md)
- [Azure-resource groepen beheren met behulp van Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Wat is een resource groep?

Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. U bepaalt hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is. Over het algemeen voegt u resources die dezelfde levens cyclus delen, toe aan dezelfde resource groep, zodat u deze eenvoudig kunt implementeren, bijwerken en verwijderen als groep.

De resourcegroep slaat metagegevens op over de resources. Dat is de reden waarom u moet aangeven waar die metagegevens moeten worden opgeslagen als u een locatie voor de resourcegroep opgeeft. In verband met nalevingsvereisten moet u er mogelijk voor zorgen dat uw gegevens worden opgeslagen in een bepaalde regio.

De resourcegroep slaat metagegevens op over de resources. Als u een locatie voor de resourcegroep opgeeft, geeft u op waar deze metagegevens worden opgeslagen.

## <a name="create-resource-groups"></a>Resource groepen maken

Met de volgende CLI-opdracht maakt u een resource groep.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Resource groepen weer geven

Het volgende CLI-script bevat de resource groepen onder uw abonnement.

```azurecli-interactive
az group list
```

Een resource groep ophalen:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Resourcegroepen verwijderen

Met het volgende CLI-script wordt een resource groep verwijderd:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Zie [Azure Resource Manager resource groep verwijderen](delete-resource-group.md)voor meer informatie over de manier waarop Azure Resource Manager het verwijderen van resources ordent.

## <a name="deploy-resources-to-an-existing-resource-group"></a>Resources implementeren voor een bestaande resource groep

Zie [resources implementeren voor een bestaande resource groep](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Een resource groep en-resources implementeren

U kunt een resource groep maken en resources voor de groep implementeren met behulp van een resource manager-sjabloon. Zie [resource groep maken en resources implementeren](../templates/deploy-to-subscription.md#resource-groups)voor meer informatie.

## <a name="redeploy-when-deployment-fails"></a>Opnieuw implementeren wanneer de implementatie mislukt

Deze functie wordt ook wel bekend als *Rollback bij fout*. Zie opnieuw [implementeren wanneer de implementatie mislukt](../templates/rollback-on-error.md)voor meer informatie.

## <a name="move-to-another-resource-group-or-subscription"></a>Verplaatsen naar een andere resource groep of een ander abonnement

U kunt de resources in de groep verplaatsen naar een andere resource groep. Zie [resources verplaatsen](manage-resources-cli.md#move-resources)voor meer informatie.

## <a name="lock-resource-groups"></a>Resource groepen vergren delen

Vergren delen voor komt dat andere gebruikers in uw organisatie per ongeluk essentiële resources verwijderen of wijzigen, zoals een Azure-abonnement, resource groep of resource.

Met het volgende script wordt een resource groep vergrendeld zodat de resource groep niet kan worden verwijderd.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName
```

Met het volgende script worden alle vergren delingen voor een resource groep opgehaald:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName
```

Met het volgende script wordt een vergren deling verwijderd:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Label resource groepen

U kunt Tags Toep assen op resource groepen en resources om uw assets logisch te organiseren. Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md#azure-cli)voor meer informatie.

## <a name="export-resource-groups-to-templates"></a>Resource groepen exporteren naar sjablonen

Nadat u de resource groep hebt ingesteld, kunt u de Resource Manager-sjabloon voor de resource groep weer geven. Het exporteren van de sjabloon biedt twee voor delen:

- Automatiseer toekomstige implementaties van de oplossing omdat de sjabloon alle volledige infra structuur bevat.
- De syntaxis van de sjabloon leren door te kijken naar de JavaScript Object Notation (JSON) die uw oplossing vertegenwoordigt.

Als u alle resources in een resource groep wilt exporteren, gebruikt u [AZ Group export](/cli/azure/group#az_group_export) en geeft u de naam van de resource groep op.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName
```

Met het script wordt de sjabloon op de-console weer gegeven. Kopieer de JSON en sla deze op als een bestand.

In plaats van alle resources in de resource groep te exporteren, kunt u selecteren welke resources u wilt exporteren.

Als u één resource wilt exporteren, geeft u deze resource-ID door.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $resourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az group export --resource-group $resourceGroupName --resource-ids $storageAccount
```

Als u meer dan één resource wilt exporteren, geeft u de resource-Id's die door spaties zijn gescheiden door. Als u alle resources wilt exporteren, geeft u dit argument niet op of levert u ' * ' op.

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccount1 $storageAccount2
```

Wanneer u de sjabloon exporteert, kunt u opgeven of de para meters in de sjabloon moeten worden gebruikt. Standaard worden para meters voor resource namen opgenomen, maar ze hebben geen standaard waarde. U moet deze parameter waarde door geven tijdens de implementatie.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

In de resource wordt de para meter gebruikt voor de naam.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Als u de `--include-parameter-default-value` para meter gebruikt bij het exporteren van de sjabloon, bevat de sjabloon parameter een standaard waarde die is ingesteld op de huidige waarde. U kunt deze standaard waarde gebruiken of de standaard waarde overschrijven door door te geven in een andere waarde.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Als u de- `--skip-resource-name-params` para meter gebruikt bij het exporteren van de sjabloon, worden para meters voor resource namen niet opgenomen in de sjabloon. In plaats daarvan wordt de resource naam rechtstreeks op de resource ingesteld op de huidige waarde. U kunt de naam niet aanpassen tijdens de implementatie.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

De functie sjabloon exporteren biedt geen ondersteuning voor het exporteren van Azure Data Factory-resources. Zie [een Data Factory in azure Data Factory kopiëren of klonen](../../data-factory/copy-clone-data-factory.md)voor meer informatie over het exporteren van Data Factory-resources.

Als u resources wilt exporteren die zijn gemaakt via het klassieke implementatie model, moet u [deze migreren naar het Resource Manager-implementatie model](../../virtual-machines/migration-classic-resource-manager-overview.md).

Zie voor meer informatie [het exporteren van één en meerdere resources naar sjabloon in azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Toegang tot resource groepen beheren

[Toegangs beheer op basis van rollen (Azure RBAC) van Azure](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot resources in azure beheert. Zie [Azure-roltoewijzingen toevoegen of verwijderen met Azure cli](../../role-based-access-control/role-assignments-cli.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Resource Manager](overview.md)voor meer informatie Azure Resource Manager.
- Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager sjablonen](../templates/template-syntax.md)voor meer informatie over de syntaxis van de Resource Manager-sjabloon.
- Zie [Stapsgewijze zelf studies](../index.yml)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloon verwijzing](/azure/templates/)voor het weer geven van de Azure Resource Manager sjabloon schema's.