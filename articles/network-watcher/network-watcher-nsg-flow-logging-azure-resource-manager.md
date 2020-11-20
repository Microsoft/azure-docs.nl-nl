---
title: Network Watcher-NSG-stroom logboeken maken met behulp van een Azure Resource Manager sjabloon
description: Een Azure Resource Manager sjabloon en Power shell gebruiken om eenvoudig NSG-stroom Logboeken in te stellen.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 8db4d4ae56a5a0ee0c92de5d9822614a3270b1c5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948677"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>NSG-stroom logboeken van een Azure Resource Manager sjabloon configureren

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) is de systeem eigen en krachtige manier waarop u uw [infra structuur kunt beheren als code](/azure/devops/learn/what-is-infrastructure-as-code).

Dit artikel laat u zien hoe u [NSG-stroom logboeken](./network-watcher-nsg-flow-logging-overview.md) programmatisch kunt inschakelen met behulp van een Azure Resource Manager sjabloon en Azure PowerShell. We beginnen met het bieden van een overzicht van de eigenschappen van het NSG-flow logboek object, gevolgd door een paar voorbeeld sjablonen. Vervolgens implementeren we de sjabloon met behulp van een lokaal PowerShell-exemplaar.


## <a name="nsg-flow-logs-object"></a>Object NSG-stroomlogboeken

Hieronder wordt het object NSG-stroomlogboeken met alle parameters weergegeven.
Voor een volledig overzicht van de eigenschappen kunt u de [Sjabloonreferentie NSG-stroomlogboeken](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters) lezen.

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
Om een Microsoft.Network/networkWatchers/flowLogs-resource te maken, voegt u de bovenstaande JSON toe aan de sectie Resources van uw sjabloon.


## <a name="creating-your-template"></a>Uw sjabloon maken

Als u voor het eerst Azure Resource Manager-sjablonen gebruikt, kunt u er meer informatie over vinden via de onderstaande koppelingen.

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
* [Zelfstudie: Uw eerste Azure Resource Manager-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Hieronder vindt u twee voor beelden van volledige sjablonen voor het instellen van NSG-stroom Logboeken.

**Voor beeld 1**: de eenvoudigste versie van de bovenstaande met de minimale para meters die worden door gegeven. Met de onderstaande sjabloon schakelt u NSG-stroom Logboeken in op een doel-NSG en slaat u deze op in een opgegeven opslag account.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> * De naam van de resource heeft de indeling ‘Bovenliggende Resource_Onderliggende resource’. Hier is de bovenliggende resource het regionale Network Watcher-exemplaar (Indeling: NetworkWatcher_Regionaam. Voorbeeld: NetworkWatcher_vscentraaleuap)
> * targetResourceId is de resource-id van de doel-NSG
> * storageId is de resource-id van het doelopslagaccount

**Voor beeld 2**: met de volgende sjablonen worden NSG-stroom Logboeken (versie 2) ingeschakeld met een Bewaar periode van vijf dagen. Het inschakelen van Traffic Analytics met een verwerkings interval van 10 minuten.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Uw Azure Resource Manager-sjabloon implementeren

In deze zelfstudie wordt ervan uitgegaan dat u een bestaande resourcegroep hebt en een NSG waarvoor u stroomlogboekregistratie kunt inschakelen.
U kunt elk van de bovenstaande voorbeeldsjablonen lokaal opslaan als `azuredeploy.json`. Werk de eigenschapswaarden bij zodat ze naar geldige resources in uw abonnement wijzen.

Voer de volgende opdracht in PowerShell uit om de sjabloon te implementeren.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Met de bovenstaande opdrachten wordt een resource geïmplementeerd in de resourcegroep NetworkWatcherRG en niet in de resourcegroep die de NSG bevat


## <a name="verifying-your-deployment"></a>Uw implementatie verifiëren

Er zijn een paar manieren om te controleren of uw implementatie is geslaagd. Uw PowerShell-console zou ‘Geslaagd’ moeten weergegeven bij ‘ProvisioningState’. Daarnaast kunt u de [portalpagina NSG-stroomlogboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) bezoeken om uw wijzigingen te bevestigen. Als er problemen zijn met de implementatie, raadpleegt u [Veelvoorkomende fouten in Azure-implementaties met Azure Resource Manager oplossen](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="deleting-your-resource"></a>Uw resource verwijderen
Azure maakt resourceverwijdering mogelijk via de implementatiemodus ‘Volledig’. Om een Stroomlogboeken-resource te verwijderen, specificeert u een implementatie in de modus Volledig zonder de resource op te nemen die u wilt verwijderen. Lees meer over de [implementatiemodus Volledig](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het visualiseren van uw NSG-stroom gegevens met behulp van:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Opensource-hulpprogramma’s](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](./traffic-analytics.md)