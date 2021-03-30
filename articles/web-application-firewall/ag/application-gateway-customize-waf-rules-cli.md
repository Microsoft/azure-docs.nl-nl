---
title: Regels aanpassen met CLI-Azure Web Application firewall
description: Dit artikel bevat informatie over het aanpassen van de firewall regels voor webtoepassingen in Application Gateway met de Azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048532"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Firewall regels voor web-apps aanpassen met behulp van Azure CLI

De Azure-toepassing gateway Web Application firewall (WAF) biedt beveiliging voor webtoepassingen. Deze beveiligingen worden verzorgd door de OWASP (open Web Application Security project) kern regelset (CRS). Sommige regels kunnen valse positieven veroorzaken en werkelijk verkeer blok keren. Daarom biedt Application Gateway de mogelijkheid om regel groepen en regels aan te passen. Zie voor meer informatie over de specifieke regel groepen en regels de [lijst met CRS-regel groepen en-regels voor Web Application firewall](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Regel groepen en-regels weer geven

De volgende code voorbeelden laten zien hoe u regels en regel groepen kunt weer geven die kunnen worden geconfigureerd.

### <a name="view-rule-groups"></a>Regel groepen weer geven

In het volgende voor beeld ziet u hoe de regel groepen worden weer gegeven:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

De volgende uitvoer is een afgekapte reactie van het vorige voor beeld:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Regels in een regel groep weer geven

In het volgende voor beeld ziet u hoe u regels kunt weer geven in een opgegeven regel groep:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

De volgende uitvoer is een afgekapte reactie van het vorige voor beeld:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Regels uitschakelen

In het volgende voor beeld worden regels `910018` en `910017` een toepassings gateway uitgeschakeld:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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
