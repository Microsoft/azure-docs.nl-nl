---
title: Azure Monitor logboeken voor Azure Firewall
description: Azure Monitor-logboeken kunnen worden gebruikt voor het analyseren van uw Azure Firewall. Een voorbeeld bestand is ingebouwd in de ontwerp functie voor weer gaven in Azure Monitor.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/11/2020
ms.author: victorh
ms.openlocfilehash: 86538f6d0467eb15e549179166ca957902a2d0c3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659553"
---
# <a name="azure-monitor-logs-for-azure-firewall"></a>Azure Monitor logboeken voor Azure Firewall

De volgende Azure Monitor logboeken kunnen worden gebruikt om uw Azure Firewall-logboeken te analyseren. Het voorbeeld bestand is gebouwd in de weer gave designer in Azure Monitor, de [weer gave designer in azure monitor](../azure-monitor/platform/view-designer.md) artikel bevat meer informatie over het ontwerp van de weer gave.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Azure Monitor logboeken weer geven

Hier kunt u een voor beeld van een visualisatie Azure Monitor logboeken configureren. U kunt de voorbeeld visualisatie downloaden uit de opslag plaats [Azure-docs-JSON-samples](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview) . De eenvoudigste manier is om met de rechter muisknop op de Hyper link op deze pagina te klikken en *Opslaan als* en een naam op te geven, zoals **AzureFirewall. omsview**. 

Voer de volgende stappen uit om de weer gave toe te voegen aan uw Log Analytics-werk ruimte:

1. Open de werk ruimte Log Analytics in het Azure Portal.
2. Open de **weer gave Designer** onder **Algemeen**.
3. Klik op **Import**.
4. Blader en selecteer het **AzureFirewall. omsview** -bestand dat u eerder hebt gedownload.
5. Klik op **Opslaan**.

Hier ziet u hoe de weer gave zoekt naar de logboek gegevens van de toepassings regel:

![Logboek gegevens toepassings regel](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

En voor de logboek gegevens van de netwerk regel:

![Logboek gegevens van netwerk regel]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Azure Firewall registreert de gegevens onder AzureDiagnostics met categorie als **AzureFirewallApplicationRule** of **AzureFirewallNetworkRule**. De gegevens met de details worden opgeslagen in het veld msg_s. Met de operator [parseren](/azure/kusto/query/parseoperator) kunnen we de verschillende interessante eigenschappen uit het msg_s veld extra heren. De onderstaande query's extra heren de informatie voor beide categorieën.

## <a name="application-rules-log-data-query"></a>Query voor logboek gegevens van toepassings regels

Met de volgende query worden de logboek gegevens van de toepassings regel geparseerd. In de verschillende opmerkingen regels vindt u enkele richt lijnen voor het bouwen van de query:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

Dezelfde query in een meer versmalde indeling:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>Query voor logboek gegevens van netwerk regels

Met de volgende query worden de logboek gegevens van de netwerk regel geparseerd. In de verschillende opmerkingen regels vindt u enkele richt lijnen voor het bouwen van de query:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

Dezelfde query in een meer versmalde indeling:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>Query voor logboek gegevens van bedreigings informatie

Met de volgende query worden de logboek gegevens van de bedreigings informatie regel geparseerd:

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="sample-logs"></a>Voobeeldlogboeken

In de volgende logboek voorbeelden worden de gegevens weer gegeven die zijn opgenomen in een logboek vermelding.

:::image type="content" source="media/log-analytics-samples/log1.png" alt-text="Scherm opname van een logboek vermelding. Meerdere waarden zijn zichtbaar, zoals een tijds tempel, een protocol, een poort nummer, een actie, een regel verzameling en een regel." border="false":::

:::image type="content" source="media/log-analytics-samples/log2.png" alt-text="Scherm opname van een logboek vermelding. Meerdere waarden zijn zichtbaar, zoals een tijds tempel, een protocol, bron-en doel-I P-adressen en een actie." border="false":::

:::image type="content" source="media/log-analytics-samples/log3.png" alt-text="Scherm opname van een logboek vermelding. Meerdere waarden zijn zichtbaar, zoals een tijds tempel, een protocol, bron-en doel-I P-adressen en-poorten en een bericht." border="false":::
## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: Azure firewall logboeken en metrische gegevens controleren](./firewall-diagnostics.md)voor meer informatie over Azure firewall bewaking en diagnostische gegevens.