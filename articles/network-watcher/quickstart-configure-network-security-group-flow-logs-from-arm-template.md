---
title: 'Quickstart: Stroomlogboeken voor netwerkbeveiligingsgroepen configureren met behulp van een ARM-sjabloon (Azure Resource Manager-sjabloon)'
description: Ontdek hoe u NSG-stroomlogboeken programmatisch kunt inschakelen met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon) en Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 01/07/2021
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: ded7b24461fdcdbc3d020a487cafc20620633097
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019717"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Quickstart: Stroomlogboeken voor netwerkbeveiligingsgroepen configureren met behulp van een ARM-sjabloon

In deze quickstart leest u hoe u [NSG-stroomlogboeken](network-watcher-nsg-flow-logging-overview.md) kunt inschakelen met behulp van een [Azure Resource Manager](../azure-resource-manager/management/overview.md)-sjabloon (ARM-sjabloon) en Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

We beginnen met een overzicht van de eigenschappen van het object NSG-stroomlogboek. Wij zorgen voor voorbeeldsjablonen. Vervolgens gebruiken we een lokale Azure PowerShell-instantie om de sjabloon te implementeren.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Deze resources worden in de sjabloon gedefinieerd:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Object NSG-stroomlogboeken

De volgende code toont een object NSG-stroomlogboeken en de bijbehorende parameters. Voeg deze code toe aan de sectie Resources van uw sjabloon om een `Microsoft.Network/networkWatchers/flowLogs`-resource te maken:

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

Zie [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs) voor een volledig overzicht van de eigenschappen van het object NSG-stroomlogboeken.

## <a name="create-your-template"></a>Uw sjabloon maken

Raadpleeg, als u voor de eerste keer ARM-sjablonen gebruikt, de volgende artikelen voor meer informatie over ARM-sjablonen:

- [Resources implementeren met ARM-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Zelfstudie: uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Het volgende voorbeeld is een volledige sjabloon. Het is ook de eenvoudigste versie van de sjabloon. Het voorbeeld bevat de minimale parameters die worden doorgegeven voor het instellen van NSG-stroomlogboeken. Zie het overzichtsartikel [NSG-stroomlogboeken vanuit een Azure Resource Manager-sjabloon configureren](network-watcher-nsg-flow-logging-azure-resource-manager.md) voor meer voorbeelden.

### <a name="example"></a>Voorbeeld

De volgende sjabloon maakt stroomlogboeken voor een NSG mogelijk, waarna de logboeken vervolgens in een specifiek opslagaccount worden opgeslagen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - Voor de resourcenaam wordt de indeling _ParentResource_ChildResource_ gebruikt. In ons voorbeeld is de bovenliggende resource het regionale Azure Network Watcher-exemplaar:
>    - **Indeling**: NetworkWatcher_RegionName
>    - **Voorbeeld**: NetworkWatcher_centraluseuap
> - `targetResourceId` is de resource-id van de doel-NSG.
> - `storageId` is de resource-id van het doelopslagaccount.

## <a name="deploy-the-template"></a>De sjabloon implementeren

In deze zelfstudie wordt ervan uitgegaan dat u een bestaande resourcegroep hebt en een NSG waarvoor u stroomlogboekregistratie kunt inschakelen.

U kunt de voorbeeldsjablonen die in dit artikel worden weergegeven, lokaal opslaan als *azuredeploy.json*. Werk de eigenschapswaarden bij, zodat ze naar geldige resources in uw abonnement wijzen.

Voer de volgende opdracht in Azure PowerShell uit om de sjabloon te implementeren:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Met de bovenstaande opdrachten wordt een resource geïmplementeerd in de voorbeeldresourcegroep NetworkWatcherRG en niet in de resourcegroep die de NSG bevat.

## <a name="validate-the-deployment"></a>De implementatie valideren

U hebt twee opties om te controleren of uw implementatie is geslaagd:

- In uw PowerShell-console wordt `ProvisioningState` weergeven als `Succeeded`.
- Ga naar de [portalpagina voor NSG-stroomlogboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) om uw wijzigingen te bevestigen. 

Als er problemen zijn met de implementatie, raadpleegt u [Veelvoorkomende fouten in Azure-implementaties met Azure Resource Manager oplossen](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure-resources verwijderen via de implementatiemodus Volledig. Geef een implementatie in de modus Volledig op zonder de resource op te nemen die u wilt verwijderen om een stroomlogboeken-resource te verwijderen. Lees meer over de [implementatiemodus Volledig](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

U kunt een NSG-stroomlogboek ook uitschakelen in Azure Portal:

1. Meld u aan bij de Azure-portal.
1. Selecteer **Alle services**. Typ **Network Watcher** in het vak **Filteren**. Selecteer **Network Watcher** in de zoekresultaten.
1. Selecteer onder **Logboeken** de optie **NSG-stroomlogboeken**.
1. Selecteer in de lijst met NSG’s de NSG waarvoor u stroomlogboeken wilt uitschakelen.
1. Selecteer onder **Instellingen voor stroomlogboeken** de optie **Uit**.
1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u NSG-stroomlogboeken kunt inschakelen met behulp van een ARM-sjabloon. Vervolgens leert u hoe u uw NSG-stroomgegevens kunt visualiseren met een van de volgende opties:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Opensource-hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
