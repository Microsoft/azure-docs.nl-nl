---
title: Resource Manager-voorbeeldsjablonen voor Azure Monitor
description: Azure Monitor-functies implementeren en configureren met Resource Manager-sjablonen
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 9218886ded7827d4b7a1e2413f1470ee5cd1563d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733955"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Resource Manager-voorbeeldsjablonen voor Azure Monitor

Azure Monitor kan op schaal worden geïmplementeerd en geconfigureerd met behulp van [Azure Resource Manager-sjabloon](../azure-resource-manager/templates/template-syntax.md). De volgende artikelen bevatten voorbeeldsjablonen voor verschillende Azure Monitor-functies. Deze voorbeelden kunnen worden gewijzigd voor uw specifieke vereisten en worden geïmplementeerd met behulp van een standaardmethode voor het implementeren van Resource Manager-sjablonen. 

## <a name="deploying-the-sample-templates"></a>De voorbeeldsjablonen implementeren
De basisstappen voor het gebruik van de voorbeelden zijn:

1. De sjabloon kopiëren en deze opslaan als een JSON-bestand.
2. De parameters wijzigen voor uw omgeving en deze opslaan als een JSON-bestand.
4. De sjabloon implementeren met behulp van [een implementatiemethode voor Resource Manager-sjablonen](../azure-resource-manager/templates/deploy-powershell.md). 

Gebruik bijvoorbeeld de volgende opdrachten voor het implementeren van de sjabloon en het parameterbestand voor een resourcegroep met behulp van PowerShell of Azure CLI.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Lijst met voorbeeldsjablonen

- [Agenten](agents/resource-manager-agent.md): Log Analytics-agent en diagnostische extensie implementeren en configureren.
- Waarschuwingen
  - [Waarschuwingsregels van logboek](alerts/resource-manager-alerts-log.md): waarschuwingen van logboekquery's en Azure-activiteitenlogboek.
  - [Metrische waarschuwingsregels](alerts/resource-manager-alerts-metric.md): waarschuwingen van metrische gegevens die gebruikmaken van verschillende soorten logica.
- [Application Insights](app/resource-manager-app-resource.md)
- [Diagnostische instellingen](essentials/resource-manager-diagnostic-settings.md): diagnostische instellingen maken om logboeken en metrische gegevens van verschillende resourcetypen door te sturen.
- [Logboekquery's](logs/resource-manager-log-queries.md): opgeslagen logboekquery's maken in een Log Analytics-werkruimte.
- [Log Analytics-werkruimte](logs/resource-manager-workspace.md): Log Analytics-werkruimte maken en verzameling van verschillende gegevensbronnen van Log Analytics-agent configureren.
- [Workbooks](visualize/resource-manager-workbooks.md): werkmappen maken.
- [Container Insights](containers/resource-manager-container-insights.md) -onboarding-clusters naar container Insights.
- [Azure Monitor voor VM's](vm/resource-manager-vminsights.md): virtuele machines onboarden naar Azure Monitor voor VM's.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md)