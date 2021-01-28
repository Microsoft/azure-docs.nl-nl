---
title: Diagnostische bron logboek registratie voor een netwerk beveiligings groep
titlesuffix: Azure Virtual Network
description: Meer informatie over het inschakelen van de diagnostische resource logboeken voor gebeurtenis-en regel items voor een Azure-netwerk beveiligings groep.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 412556f3bd517539fc8ccad94c4de52226f16597
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946223"
---
# <a name="resource-logging-for-a-network-security-group"></a>Bron logboek registratie voor een netwerk beveiligings groep

Een netwerk beveiligings groep (NSG) bevat regels voor het toestaan of weigeren van verkeer naar een subnet van een virtueel netwerk, een netwerk interface of beide. 

Wanneer u logboek registratie voor een NSG inschakelt, kunt u de volgende typen bron logboek gegevens verzamelen:

* **Gebeurtenis:** Vermeldingen worden vastgelegd waarvoor NSG-regels worden toegepast op Vm's, op basis van een MAC-adres.
* **Regel teller:** Bevat vermeldingen voor het aantal keren dat elke NSG regel wordt toegepast om verkeer te weigeren of toe te staan. De status voor deze regels wordt elke 300 seconden verzameld.

Resource logboeken zijn alleen beschikbaar voor Nsg's die zijn geïmplementeerd via het Azure Resource Manager-implementatie model. Het is niet mogelijk om bron logboek registratie in te scha kelen voor Nsg's die zijn geïmplementeerd via het klassieke implementatie model. Zie [Wat is Azure-implementatie modellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)? voor een beter inzicht in de twee modellen.

Bron logboek registratie wordt afzonderlijk ingeschakeld voor *elk* NSG waarvoor u Diagnostische gegevens wilt verzamelen. Zie Azure [activity logging](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)(Engelstalig) als u in plaats daarvan wilt werken met activiteiten Logboeken.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

U kunt de [Azure-Portal](#azure-portal), [Power shell](#powershell)of de [Azure cli](#azure-cli) gebruiken om bron logboek registratie in te scha kelen.

### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [portal](https://portal.azure.com).
2. Selecteer **alle services** en typ vervolgens *netwerk beveiligings groepen*. Wanneer **netwerk beveiligings groepen** worden weer gegeven in de zoek resultaten, selecteert u deze.
3. Selecteer de NSG waarvoor u logboek registratie wilt inschakelen.
4. Selecteer onder **bewaking** **Diagnostische logboeken** en selecteer vervolgens **Diagnostische gegevens inschakelen**, zoals wordt weer gegeven in de volgende afbeelding:

   ![Diagnostische gegevens inschakelen](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Voer onder **Diagnostische instellingen** de volgende informatie in of Selecteer deze, en selecteer vervolgens **Opslaan**:

    | Instelling                                                                                     | Waarde                                                          |
    | ---------                                                                                   |---------                                                       |
    | Naam                                                                                        | Een naam van uw keuze.  Bijvoorbeeld: *myNsgDiagnostics*      |
    | **Archiveren naar een opslag account**, **streamen naar een event hub** en **verzenden naar log Analytics** | U kunt zoveel bestemmingen selecteren als u kiest. Zie [logboek doelen](#log-destinations)voor meer informatie over elk van deze.                                                                                                                                           |
    | LOG                                                                                         | Selecteer een van beide of beide logboek categorieën. Zie [logboek categorieën](#log-categories)voor meer informatie over de gegevens die voor elke categorie worden vastgelegd.                                                                                                                                             |
6. Logboeken weer geven en analyseren. Zie [Logboeken weer geven en analyseren](#view-and-analyze-logs)voor meer informatie.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud shell](https://shell.azure.com/powershell), of door Power shell uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u Power shell vanaf uw computer uitvoert, hebt u de Azure PowerShell module versie 1.0.0 of hoger nodig. Voer uit `Get-Module -ListAvailable Az` op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u Power shell lokaal uitvoert, moet u ook uitvoeren `Connect-AzAccount` om u aan te melden bij Azure met een account dat over de [benodigde machtigingen](virtual-network-network-interface.md#permissions)beschikt.

Als u de bron logboek registratie wilt inschakelen, hebt u de id van een bestaande NSG nodig. Als u geen bestaande NSG hebt, kunt u er een maken met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Haal de netwerk beveiligings groep op waarvoor u de bron logboek registratie wilt inschakelen met [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Als u bijvoorbeeld een NSG met de naam *mijnnbg* wilt ophalen die bestaat in een resource groep met de naam *myResourceGroup*, voert u de volgende opdracht in:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

U kunt resource logboeken naar drie doel typen schrijven. Zie [logboek doelen](#log-destinations)voor meer informatie. In dit artikel worden logboeken als voor beeld naar de *log Analytics* bestemming verzonden. Haal een bestaande Log Analytics-werk ruimte op met [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Als u bijvoorbeeld een bestaande werk ruimte met de naam *myWorkspace* in een resource groep met de naam *myWorkspaces* wilt ophalen, voert u de volgende opdracht in:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Als u geen bestaande werk ruimte hebt, kunt u er een maken met [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Er zijn twee soorten logboek registratie waarvoor u Logboeken kunt inschakelen. Zie [logboek categorieën](#log-categories)voor meer informatie. Schakel bron logboek registratie in voor de NSG met [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). In het volgende voor beeld worden zowel gebeurtenis-als item categorie gegevens in de werk ruimte voor een NSG vastgelegd, met behulp van de Id's voor de NSG en de werk ruimte die u eerder hebt opgehaald:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Als u alleen gegevens wilt vastleggen voor de ene categorie of de andere, in plaats van beide, voegt u de `-Categories` optie toe aan de vorige opdracht, gevolgd door *NetworkSecurityGroupEvent* of *NetworkSecurityGroupRuleCounter*. Als u zich wilt aanmelden op een andere [bestemming](#log-destinations) dan een log Analytics-werk ruimte, gebruikt u de juiste para meters voor een Azure- [opslag account](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) of [Event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Logboeken weer geven en analyseren. Zie [Logboeken weer geven en analyseren](#view-and-analyze-logs)voor meer informatie.

### <a name="azure-cli"></a>Azure CLI

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud shell](https://shell.azure.com/bash), of door de Azure cli vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u de CLI vanaf uw computer uitvoert, hebt u versie 2.0.38 of hoger nodig. Voer uit `az --version` op uw computer om de geïnstalleerde versie te vinden. Als u een upgrade wilt uitvoeren, raadpleegt u [Azure cli installeren](/cli/azure/install-azure-cli). Als u de CLI lokaal uitvoert, moet u ook uitvoeren om u `az login` aan te melden bij Azure met een account dat over de [benodigde machtigingen](virtual-network-network-interface.md#permissions)beschikt.

Als u de bron logboek registratie wilt inschakelen, hebt u de id van een bestaande NSG nodig. Als u geen bestaande NSG hebt, kunt u er een maken met [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

Haal de netwerk beveiligings groep op waarvoor u bron logboek registratie wilt inschakelen met [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show). Als u bijvoorbeeld een NSG met de naam *mijnnbg* wilt ophalen die bestaat in een resource groep met de naam *myResourceGroup*, voert u de volgende opdracht in:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

U kunt resource logboeken naar drie doel typen schrijven. Zie [logboek doelen](#log-destinations)voor meer informatie. In dit artikel worden logboeken als voor beeld naar de *log Analytics* bestemming verzonden. Zie [logboek categorieën](#log-categories)voor meer informatie.

Schakel bron logboek registratie in voor de NSG met [AZ monitor Diagnostic-settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). In het volgende voor beeld worden zowel gebeurtenis-als item categorie gegevens vastgelegd in een bestaande werk ruimte met de naam *myWorkspace*, die voor komt in een resource groep met de naam *MYWORKSPACES* en de id van de NSG die u eerder hebt opgehaald:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Als u geen bestaande werk ruimte hebt, kunt u er een maken met behulp van de [Azure Portal](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Power shell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Er zijn twee soorten logboek registratie waarvoor u Logboeken kunt inschakelen.

Als u alleen gegevens wilt vastleggen voor de ene categorie of de andere, verwijdert u de categorie waarvoor u geen gegevens wilt registreren in de vorige opdracht. Als u zich wilt aanmelden op een andere [bestemming](#log-destinations) dan een log Analytics-werk ruimte, gebruikt u de juiste para meters voor een Azure- [opslag account](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) of [Event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Logboeken weer geven en analyseren. Zie [Logboeken weer geven en analyseren](#view-and-analyze-logs)voor meer informatie.

## <a name="log-destinations"></a>Logboek bestemmingen

Diagnostische gegevens kunnen zijn:
- [Naar een Azure Storage-account geschreven](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)voor controle of hand matige inspectie. U kunt de Bewaar tijd (in dagen) opgeven met behulp van de diagnostische instellingen van de resource.
- [Gestreamd naar een event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) voor opname door een service van derden of een aangepaste analyse oplossing, zoals PowerBI.
- [Naar Azure monitor-logboeken geschreven](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).

## <a name="log-categories"></a>Logboek Categorieën

Gegevens in JSON-indeling worden geschreven voor de volgende logboek Categorieën:

### <a name="event"></a>Gebeurtenis

Het gebeurtenis logboek bevat informatie over welke NSG-regels worden toegepast op Vm's, op basis van een MAC-adres. De volgende gegevens worden geregistreerd voor elke gebeurtenis. In het volgende voor beeld worden de gegevens geregistreerd voor een virtuele machine met het IP-adres 192.168.1.4 en een MAC-adres van 00-0D-3A-92-6A-7C:

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

### <a name="rule-counter"></a>Regel teller

Het logboek regel item bevat informatie over elke regel die wordt toegepast op resources. De volgende voorbeeld gegevens worden geregistreerd telkens wanneer een regel wordt toegepast. In het volgende voor beeld worden de gegevens geregistreerd voor een virtuele machine met het IP-adres 192.168.1.4 en een MAC-adres van 00-0D-3A-92-6A-7C:

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
> Het bron-IP-adres voor de communicatie is niet geregistreerd. U kunt [logboek registratie](../network-watcher/network-watcher-nsg-flow-logging-portal.md) van de NSG-stroom inschakelen voor een NSG, waarbij alle gegevens van de regel teller worden vastgelegd, evenals het bron-IP-adres dat de communicatie heeft gestart. NSG-stroomlogboekgegevens worden naar een Azure Storage-account geschreven. U kunt de gegevens analyseren met de functie [Traffic Analytics](../network-watcher/traffic-analytics.md) van Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Logboeken weer geven en analyseren

Zie [overzicht van Azure-platform logboeken](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het weer geven van resource logboek gegevens. Als u Diagnostische gegevens verzendt naar:
- **Azure monitor logboeken**: u kunt de [analyse oplossing voor netwerk beveiligings groepen](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) gebruiken voor uitgebreid inzicht. De oplossing biedt visualisaties voor NSG-regels waarmee verkeer, per MAC-adres, van de netwerk interface in een virtuele machine wordt toegestaan of geweigerd.
- **Azure Storage account**: gegevens worden geschreven naar een PT1H.jsbestand. U kunt het volgende vinden:
  - Gebeurtenis logboek in het volgende pad: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Logboek regel items in het volgende pad: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [activiteiten registreren](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Activiteiten logboek registratie is standaard ingeschakeld voor Nsg's die zijn gemaakt via een Azure-implementatie model. Als u wilt bepalen welke bewerkingen zijn voltooid op Nsg's in het activiteiten logboek, zoekt u naar vermeldingen die de volgende resource typen bevatten:
  - Micro soft. ClassicNetwork/networkSecurityGroups
  - Micro soft. ClassicNetwork/networkSecurityGroups/securityRules
  - Micro soft. Network/networkSecurityGroups
  - Micro soft. Network/networkSecurityGroups/securityRules
- Zie [NSG flow logging](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)(Engelstalig) voor meer informatie over het vastleggen van diagnostische gegevens, zodat u het bron-IP-adres voor elke stroom kunt gebruiken.
