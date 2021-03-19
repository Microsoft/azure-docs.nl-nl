---
title: Regels aanpassen met Power shell
titleSuffix: Azure Web Application Firewall
description: Dit artikel bevat informatie over het aanpassen van de firewall regels voor webtoepassingen in Application Gateway met Power shell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74048510"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Firewall regels voor web-apps aanpassen met behulp van Power shell

De Azure-toepassing gateway Web Application firewall (WAF) biedt beveiliging voor webtoepassingen. Deze beveiligingen worden verzorgd door de OWASP (open Web Application Security project) kern regelset (CRS). Sommige regels kunnen valse positieven veroorzaken en werkelijk verkeer blok keren. Daarom biedt Application Gateway de mogelijkheid om regel groepen en regels aan te passen. Zie voor meer informatie over de specifieke regel groepen en regels de [lijst met CRS-regel groepen en-regels voor Web Application firewall](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Regel groepen en-regels weer geven

De volgende code voorbeelden laten zien hoe u regels en regel groepen kunt weer geven die kunnen worden geconfigureerd op een WAF-toepassings gateway.

### <a name="view-rule-groups"></a>Regel groepen weer geven

In het volgende voor beeld ziet u hoe regel groepen worden weer gegeven:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

De volgende uitvoer is een afgekapte reactie van het vorige voor beeld:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Regels uitschakelen

In het volgende voor beeld worden regels `911011` en `911012` een toepassings gateway uitgeschakeld:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Regels verplicht

De volgende lijst bevat voor waarden die ervoor zorgen dat de WAF de aanvraag blokkeert in de preventie modus (in de detectie modus worden ze als uitzonde ringen geregistreerd). Deze kunnen niet worden geconfigureerd of uitgeschakeld:

* Fout bij het parseren van de aanvraag tekst leidt ertoe dat de aanvraag wordt geblokkeerd, tenzij de hoofd inspectie is uitgeschakeld (XML-, JSON-, formulier gegevens)
* De gegevens lengte van de aanvraag tekst (zonder bestanden) is groter dan de geconfigureerde limiet
* De aanvraag tekst (inclusief bestanden) is groter dan de limiet
* Er is een interne fout opgetreden in de WAF-engine

CRS 3. x specifiek:

* Drempel waarde voor binnenkomende afwijkings Score overschreden

## <a name="next-steps"></a>Volgende stappen

Nadat u de uitgeschakelde regels hebt geconfigureerd, kunt u meer informatie over het weer geven van uw WAF-logboeken bekijken. Zie [Application Gateway Diagnostics (diagnostische](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)gegevens) voor meer informatie.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
