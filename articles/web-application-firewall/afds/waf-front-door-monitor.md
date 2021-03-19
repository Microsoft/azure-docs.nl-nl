---
title: Bewaking en logboek registratie van de firewall van Azure Web Application
description: WAF (Web Application firewall) leren met-ingang controle en logboek registratie
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84808954"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Bewaking en logboek registratie van de firewall van Azure Web Application

Azure Web Application firewall (WAF) bewaking en logboek registratie worden verschaft via logboek registratie en integratie met Azure Monitor-en Azure Monitor-Logboeken.

## <a name="azure-monitor"></a>Azure Monitor

WAF met-ingang-logboek is geïntegreerd met [Azure monitor](../../azure-monitor/overview.md). Met Azure Monitor kunt u Diagnostische gegevens bijhouden, waaronder WAF-waarschuwingen en-Logboeken. U kunt WAF-bewaking configureren binnen de voor deur van de portal op het tabblad **Diagnostische gegevens** of via de Azure Monitor-service rechtstreeks.

Ga vanuit Azure Portal naar het resource type voor deur. Op het tabblad **bewakings** / **gegevens** aan de linkerkant kunt u **WebApplicationFirewallRequestCount** toevoegen om het aantal aanvragen bij te houden dat overeenkomt met WAF-regels. Aangepaste filters kunnen worden gemaakt op basis van actie typen en regel namen.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>Logboeken en diagnostische gegevens

WAF met de voor deur biedt gedetailleerde rapportage over elke bedreiging die wordt gedetecteerd. Logboekregistratie is geïntegreerd met de logboeken van Azure Diagnostics en waarschuwingen worden vastgelegd in de JSON-indeling. Deze logboeken kunnen worden geïntegreerd met [Azure Monitor-logboeken](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) registreert alle aanvragen. FrontdoorWebApplicationFirewallLog registreert elke aanvraag die overeenkomt met een WAF-regel die het onderstaande schema heeft:

| Eigenschap  | Beschrijving |
| ------------- | ------------- |
|Actie|Actie uitgevoerd voor de aanvraag|
| ClientIp | Het IP-adres van de client die de aanvraag heeft ingediend. Als er een X-doorgestuurd is voor de header in de aanvraag, wordt het client-IP-adres uit het veld header opgehaald. |
| ClientPort | De IP-poort van de client die de aanvraag heeft ingediend. |
| Details|Aanvullende details over de overeenkomende aanvraag |
|| matchVariableName: de http-parameter naam van de aanvraag komt bijvoorbeeld overeen met header namen|
|| matchVariableValue: waarden die de overeenkomst hebben geactiveerd|
| Host | De host-header van de overeenkomende aanvraag |
| Beleid | De naam van het WAF-beleid waaraan de aanvraag is gekoppeld. |
| PolicyMode | De bewerkings modus van het WAF-beleid. Mogelijke waarden zijn "preventie" en "detectie" |
| RequestUri | Volledige URI van de overeenkomende aanvraag. |
| RuleName | De naam van de WAF-regel waarmee de aanvraag overeenkomt. |
| SocketIp | Het bron-IP-adres dat wordt weer gegeven door WAF. Dit IP-adres is gebaseerd op de TCP-sessie, onafhankelijk van eventuele aanvraag headers.|
| TrackingReference | De unieke verwijzings reeks die een aanvraag identificeert die wordt geleverd door de voor deur, ook verzonden als X-Azure-ref-header naar de client. Vereist voor het zoeken naar details in de logboeken van de toegang voor een specifieke aanvraag. |

Het volgende query voorbeeld retourneert WAF-Logboeken in geblokkeerde aanvragen:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Hier volgt een voor beeld van een geregistreerde aanvraag in WAF-logboek:

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

De volgende voorbeeld query retourneert AccessLogs-vermeldingen:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Hier volgt een voor beeld van een vastgelegde aanvraag in het Access-logboek:

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [voor deur](../../frontdoor/front-door-overview.md).
