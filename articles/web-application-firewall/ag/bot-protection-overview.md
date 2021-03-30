---
title: Overzicht van WAF op Azure-toepassing gateway bot
titleSuffix: Azure Web Application Firewall
description: Dit artikel bevat een overzicht van Web Application Firewall (WAF) op Application Gateway bot-beveiliging
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83714896"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Overzicht van Azure Web Application firewall op Azure-toepassing gateway-bot

Ongeveer 20% van het Internet verkeer is van een beschadigd bots. Dit zijn dingen zoals uitval, scannen en zoeken naar beveiligings problemen in uw webtoepassing. Wanneer deze botsingen worden gestopt bij de Web Application firewall (WAF), kunnen ze niet worden aangevallen. Ze kunnen ook geen resources en services gebruiken, zoals uw back-end en andere onderliggende infra structuur.

U kunt een beheerde regelset voor bescherming tegen bots inschakelen voor uw WAF, om aanvragen van bekende schadelijke IP-adressen te blokkeren of te registreren. De IP-adressen zijn afkomstig uit de feed Bedreigingsinformatie van Microsoft. Intelligent Security Graph voorziet de feed van gegevens en wordt gebruikt door verschillende services, waaronder Azure Security Center.

> [!IMPORTANT]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)-   voor beelden voor meer informatie.

## <a name="use-with-owasp-rulesets"></a>Gebruiken met OWASP-rules

U kunt de regelset voor bescherming tegen bots gebruiken, naast een van de OWASP-regelsets (2.2.9, 3.0 en 3.1). Op elk gewenst moment kan slechts één OWASP-regelset worden gebruikt. De regelset voor bescherming tegen bots bevat een extra regel die wordt weergegeven in een aparte regelset. Het object heeft de titel **Microsoft_BotManagerRuleSet_0** 1 en u kunt het in-of uitschakelen zoals de andere OWASP-regels.

![Bot-ruleSet](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>RuleSet-update

De lijst van de regelset voor de beperking van bots met bekende schadelijke IP-adressen wordt meerdere keren per dag bijgewerkt vanuit de Microsoft Bedreigingsinformatie-feed, zodat deze gesynchroniseerd blijft met de bots. Uw webtoepassingen worden voortdurend beschermd, zelfs als de aanvalsvectoren van bots worden gewijzigd.

## <a name="log-example"></a>Logboek voorbeeld

Hier volgt een voor beeld van een logboek vermelding voor bot-beveiliging:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Volgende stappen

- [Bescherming tegen bots configureren voor Web Application Firewall in Azure Application Gateway (preview)](bot-protection.md)
