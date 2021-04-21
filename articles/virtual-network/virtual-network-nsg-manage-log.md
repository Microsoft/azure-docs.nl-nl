---
title: Logboekregistratie van diagnostische resources voor een netwerkbeveiligingsgroep
titlesuffix: Azure Virtual Network
description: Meer informatie over het inschakelen van diagnostische resourcelogboeken voor gebeurtenis- en regeltellers voor een Azure-netwerkbeveiligingsgroep.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 0d171dee87a391c5e1d66db10363e6823ef387c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774157"
---
# <a name="resource-logging-for-a-network-security-group"></a>Logboekregistratie van resources voor een netwerkbeveiligingsgroep

Een netwerkbeveiligingsgroep (NSG) bevat regels die verkeer naar een subnet, netwerkinterface of beide toestaan of weigeren. 

Wanneer u logboekregistratie voor een NSG inschakelen, kunt u de volgende typen informatie over resourcelogboek verzamelen:

* **Gebeurtenis:** Vermeldingen worden geregistreerd waarvoor NSG-regels worden toegepast op VM's, op basis van het MAC-adres.
* **Regelteller:** Bevat vermeldingen voor het aantal keren dat elke NSG-regel wordt toegepast om verkeer te weigeren of toe te staan. De status voor deze regels wordt elke 300 seconden verzameld.

Resourcelogboeken zijn alleen beschikbaar voor NSG's die zijn geïmplementeerd via het Azure Resource Manager implementatiemodel. U kunt logboekregistratie van resources niet inschakelen voor NSG's die zijn geïmplementeerd via het klassieke implementatiemodel. Zie Inzicht in Azure-implementatiemodellen voor een beter begrip [van de twee modellen.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Resourcelogboekregistratie wordt afzonderlijk ingeschakeld voor *elke* NSG voor wie u diagnostische gegevens wilt verzamelen. Zie Logboekregistratie van Azure-activiteiten als u geïnteresseerd bent in activiteitenlogboeken [(operationele logboeken).](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Zie Azure Network Watcher [NSG-stroomlogboeken](../network-watcher/network-watcher-nsg-flow-logging-overview.md) als u geïnteresseerd bent in IP-verkeer dat via NSG's stroomt 

## <a name="enable-logging"></a>Logboekregistratie inschakelen

U kunt Azure [Portal,](#azure-portal) [PowerShell](#powershell)of de [Azure CLI](#azure-cli) gebruiken om logboekregistratie van resources in teschakelen.

### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [portal](https://portal.azure.com).
2. Selecteer **Alle services en** typ vervolgens *netwerkbeveiligingsgroepen.* Wanneer **Netwerkbeveiligingsgroepen** worden weergegeven in de zoekresultaten, selecteert u deze.
3. Selecteer de NSG voor wie u logboekregistratie wilt inschakelen.
4. Selecteer **onder BEWAKING** de optie **Diagnostische logboeken** en selecteer **vervolgens Diagnostische** gegevens in- en weer te geven, zoals wordt weergegeven in de volgende afbeelding:

   ![Diagnostische gegevens inschakelen](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Voer **onder Diagnostische instellingen** de volgende gegevens in of selecteer deze. Selecteer vervolgens **Opslaan:**

    | Instelling                                                                                     | Waarde                                                          |
    | ---------                                                                                   |---------                                                       |
    | Naam                                                                                        | Een naam naar keuze.  Bijvoorbeeld: *myNsgDiagnostics*      |
    | **Archiveren naar een opslagaccount,** **streamen naar een Event Hub** en Verzenden naar Log **Analytics** | U kunt zoveel bestemmingen selecteren als u wilt. Zie Logboekbestemmingen voor meer informatie [over deze .](#log-destinations)                                                                                                                                           |
    | LOG                                                                                         | Selecteer een of beide logboekcategorieën. Zie Logboekcategorieën voor meer informatie over de gegevens die voor elke categorie [zijn geregistreerd.](#log-categories)                                                                                                                                             |
6. Logboeken weergeven en analyseren. Zie Logboeken weergeven [en analyseren voor meer informatie.](#view-and-analyze-logs)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell op uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell op uw computer hebt uitgevoerd, hebt u de Azure PowerShell module versie 1.0.0 of hoger nodig. Voer `Get-Module -ListAvailable Az` uit op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal gebruikt, moet u ook uitvoeren om u aan te melden bij Azure met een account met `Connect-AzAccount` [de benodigde machtigingen.](virtual-network-network-interface.md#permissions)

Als u logboekregistratie van resources wilt inschakelen, hebt u de id van een bestaande NSG nodig. Als u geen bestaande NSG hebt, kunt u er een maken met [New-AzNetworkSecurityGroup.](/powershell/module/az.network/new-aznetworksecuritygroup)

Haal de netwerkbeveiligingsgroep op voor wie u resourcelogregistratie wilt inschakelen met [Get-AzNetworkSecurityGroup.](/powershell/module/az.network/get-aznetworksecuritygroup) Als u bijvoorbeeld een NSG met de naam *myNsg* wilt ophalen die bestaat in een resourcegroep met de naam *myResourceGroup,* voert u de volgende opdracht in:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

U kunt resourcelogboeken schrijven naar drie doeltypen. Zie Logboekbestemmingen voor [meer informatie.](#log-destinations) In dit artikel worden logboeken als voorbeeld verzonden naar de *Log Analytics-bestemming.* Haal een bestaande Log Analytics-werkruimte op [met Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Als u bijvoorbeeld een bestaande werkruimte met de *naam myWorkspace* wilt ophalen in een resourcegroep met de *naam myWorkspaces,* voert u de volgende opdracht in:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Als u geen bestaande werkruimte hebt, kunt u er een maken met [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Er zijn twee soorten logboekregistratie waar u logboeken voor kunt inschakelen. Zie Logboekcategorieën [voor meer informatie.](#log-categories) Schakel resourcelogregistratie in voor de NSG [met Set-AzDiagnosticSetting.](/powershell/module/az.monitor/set-azdiagnosticsetting) In het volgende voorbeeld worden zowel gebeurtenis- als tellercategoriegegevens in de werkruimte voor een NSG gelogd, met behulp van de -ID's voor de NSG en de werkruimte die u eerder hebt opgehaald:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Als u alleen gegevens voor de ene categorie of de andere wilt logboeken, in plaats van beide, voegt u de optie toe aan de vorige opdracht, gevolgd door `-Categories` *NetworkSecurityGroupEvent* of *NetworkSecurityGroupRuleCounter.* Als u zich wilt aanmelden bij een [andere](#log-destinations) bestemming dan een Log Analytics-werkruimte, gebruikt u de juiste parameters voor een Azure [Storage-account](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) of [Event Hub.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)

Logboeken weergeven en analyseren. Zie Logboeken weergeven [en analyseren voor meer informatie.](#view-and-analyze-logs)

### <a name="azure-cli"></a>Azure CLI

U kunt de volgende opdrachten uitvoeren in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de Azure CLI uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u de CLI vanaf uw computer hebt uitgevoerd, hebt u versie 2.0.38 of hoger nodig. Voer `az --version` uit op uw computer om de geïnstalleerde versie te vinden. Zie Azure CLI installeren als u een upgrade [wilt uitvoeren.](/cli/azure/install-azure-cli) Als u de CLI lokaal gebruikt, moet u ook uitvoeren om u aan te melden bij Azure met een account met `az login` [de benodigde machtigingen.](virtual-network-network-interface.md#permissions)

Als u logboekregistratie van resources wilt inschakelen, hebt u de id van een bestaande NSG nodig. Als u geen bestaande NSG hebt, kunt u er een maken [met az network nsg create.](/cli/azure/network/nsg#az_network_nsg_create)

Haal de netwerkbeveiligingsgroep op voor wie u resourcelogregistratie wilt inschakelen [met az network nsg show](/cli/azure/network/nsg#az_network_nsg_show). Als u bijvoorbeeld een NSG met de naam *myNsg* wilt ophalen die bestaat in een resourcegroep met de naam *myResourceGroup,* voert u de volgende opdracht in:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

U kunt resourcelogboeken schrijven naar drie doeltypen. Zie Logboekbestemmingen voor [meer informatie.](#log-destinations) In dit artikel worden logboeken als voorbeeld verzonden naar de *Log Analytics-bestemming.* Zie Logboekcategorieën [voor meer informatie.](#log-categories)

Schakel resourcelogboekregistratie in voor de NSG [met az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) In het volgende voorbeeld worden zowel gebeurtenis- als tellercategoriegegevens in een bestaande werkruimte met de naam *myWorkspace* gelogd, die bestaat in een resourcegroep met de naam *myWorkspaces* en de id van de NSG die u eerder hebt opgehaald:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Als u geen bestaande werkruimte hebt, kunt u er een maken met de [Azure Portal](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [PowerShell.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) Er zijn twee soorten logboekregistratie waar u logboeken voor kunt inschakelen.

Als u alleen gegevens voor de ene of de andere categorie wilt opslaan, verwijdert u de categorie waar u in de vorige opdracht geen gegevens voor wilt opslaan. Als u zich wilt aanmelden bij een [andere](#log-destinations) bestemming dan een Log Analytics-werkruimte, gebruikt u de juiste parameters voor een Azure [Storage-account](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) [of Event Hub.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)

Logboeken weergeven en analyseren. Zie Logboeken weergeven [en analyseren voor meer informatie.](#view-and-analyze-logs)

## <a name="log-destinations"></a>Logboekbestemmingen

Diagnostische gegevens kunnen zijn:
- [Geschreven naar een Azure Storage account](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage), voor controle of handmatige inspectie. U kunt de bewaartijd (in dagen) opgeven met diagnostische instellingen voor resources.
- [Gestreamd naar een Event Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) voor opname door een service van derden of een aangepaste analyseoplossing, zoals PowerBI.
- [Geschreven naar Azure Monitor logboeken.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)

## <a name="log-categories"></a>Logboekcategorieën

Gegevens in JSON-indeling worden geschreven voor de volgende logboekcategorieën:

### <a name="event"></a>Gebeurtenis

Het gebeurtenislogboek bevat informatie over welke NSG-regels worden toegepast op VM's, op basis van het MAC-adres. De volgende gegevens worden geregistreerd voor elke gebeurtenis. In het volgende voorbeeld worden de gegevens vastgelegd voor een virtuele machine met het IP-adres 192.168.1.4 en een MAC-adres van 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Regelteller

Het logboek met regeltellers bevat informatie over elke regel die wordt toegepast op resources. De volgende voorbeeldgegevens worden geregistreerd telkens wanneer een regel wordt toegepast. In het volgende voorbeeld worden de gegevens vastgelegd voor een virtuele machine met het IP-adres 192.168.1.4 en een MAC-adres van 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Het bron-IP-adres voor de communicatie wordt niet geregistreerd. U kunt [echter NSG-stroomlogboeken](../network-watcher/network-watcher-nsg-flow-logging-portal.md) inschakelen voor een NSG, die alle informatie over de regelteller registreert, evenals het bron-IP-adres dat de communicatie heeft gestart. NSG-stroomlogboekgegevens worden naar een Azure Storage-account geschreven. U kunt de gegevens analyseren met de [verkeersanalysemogelijkheden](../network-watcher/traffic-analytics.md) van Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Logboeken weergeven en analyseren

Zie Overzicht van Azure-platformlogboeken voor meer informatie over het weergeven van [resourcelogboekgegevens.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Als u diagnostische gegevens verzendt naar:
- **Azure Monitor:** u kunt de analyseoplossing voor [netwerkbeveiligingsgroep](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) gebruiken voor verbeterde inzichten. De oplossing biedt visualisaties voor NSG-regels die verkeer per MAC-adres van de netwerkinterface in een virtuele machine toestaan of weigeren.
- **Azure Storage account:** gegevens worden naar een PT1H.jsin het bestand geschreven. U vindt het volgende:
  - Gebeurtenislogboek in het volgende pad: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Logboek voor regeltellers in het volgende pad: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboekregistratie van activiteiten.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Logboekregistratie van activiteiten is standaard ingeschakeld voor NSG's die zijn gemaakt via een van beide Azure-implementatiemodellen. Als u wilt bepalen welke bewerkingen zijn uitgevoerd op NSG's in het activiteitenlogboek, moet u zoeken naar vermeldingen die de volgende resourcetypen bevatten:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Zie NSG-stroomlogboekregistratie voor meer informatie over het vastleggen van diagnostische gegevens als u het bron-IP-adres voor elke stroom [wilt opnemen.](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
