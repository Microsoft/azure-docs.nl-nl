---
title: Uitsluitingslijsten voor Web Application Firewall in Azure Front Door - Azure Portal
description: Dit artikel bevat informatie over de configuratie van uitsluitingslijsten in Azure Front met de Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 83baf03c414d9b0f7acb6a93db03794a539a3c58
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860829"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application Firewall (WAF) met uitsluitingslijsten Front Door Service 

Soms Web Application Firewall (WAF) een aanvraag blokkeren die u wilt toestaan voor uw toepassing. Active Directory voegt bijvoorbeeld tokens toe die worden gebruikt voor verificatie. Deze tokens kunnen speciale tekens bevatten die een fout-positief uit de WAF-regels kunnen activeren. Met WAF-uitsluitingslijsten kunt u bepaalde aanvraagkenmerken weglaten uit een WAF-evaluatie.  Een uitsluitingslijst kan worden geconfigureerd met  [behulp van PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject) [Azure CLI,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add) [REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)of de Azure Portal. In het volgende voorbeeld ziet u de Azure Portal configuratie. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Uitsluitingslijsten configureren met behulp van Azure Portal
**Uitsluitingen beheren** is toegankelijk vanuit de WAF-portal onder **Beheerde regels**

![Uitsluiting beheren ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ Exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Een voorbeeld van een uitsluitingslijst: ![ Exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

In dit voorbeeld wordt de waarde in het veld *gebruikersheader* uitgesloten. Een geldige aanvraag kan het *gebruikersveld bevatten* dat een tekenreeks bevat die een SQL-injectieregel activeert. U kunt de *gebruikersparameter* in dit geval uitsluiten, zodat de WAF-regel niets evalueert in het veld.

De volgende kenmerken kunnen op naam worden toegevoegd aan uitsluitingslijsten. De waarden van de velden die u gebruikt, worden niet geëvalueerd op basis van WAF-regels, maar de namen ervan worden geëvalueerd. De uitsluitingslijsten verwijderen de inspectie van de waarde van het veld.

* Naam van aanvraagheader
* Cookienaam aanvragen
* Naam van queryreeks args
* Aanvraag body post args name

U kunt een exacte overeenkomende aanvraagheader, hoofdtekst, cookie of queryreekskenmerk opgeven.  U kunt desgewenst ook gedeeltelijke overeenkomsten opgeven. De volgende operators zijn de ondersteunde overeenkomstcriteria:

- **Is gelijk** aan: deze operator wordt gebruikt voor een exacte overeenkomst. Als u bijvoorbeeld een koptekst met de **naam bearerToken** wilt selecteren, gebruikt u de operator is gelijk aan met de selector ingesteld als **bearerToken**.
- **Begint met**: deze operator komt overeen met alle velden die beginnen met de opgegeven selectorwaarde.
- **Eindigt op**: deze operator komt overeen met alle aanvraagvelden die eindigen op de opgegeven selectorwaarde.
- **Contains:** deze operator komt overeen met alle aanvraagvelden die de opgegeven selectorwaarde bevatten.
- **Is gelijk aan elke**: deze operator komt overeen met alle aanvraagvelden. * is de selectorwaarde.

Header- en cookienamen zijn niet hoofd- en hoofd- en hoofdteksten.

Als een headerwaarde, cookiewaarde, post-argumentwaarde of queryargumentwaarde voor sommige regels fout-positieven produceert, kunt u dat deel van de aanvraag uitsluiten van overweging door de regel:


|matchVariableName uit WAF-logboeken  |Regeluitsluiting in portal  |
|---------|---------|
|CookieValue:SOME_NAME        |Cookienaam aanvragen is gelijk aan SOME_NAME|
|HeaderValue:SOME_NAME        |Naam van aanvraagheader is gelijk aan SOME_NAME|
|PostParamValue:SOME_NAME     |Aanvraag body post args name Equals SOME_NAME|
|QueryParamValue:SOME_NAME    |Naam van queryreeks args is gelijk aan SOME_NAME|


We ondersteunen momenteel alleen regeluitsluitingen voor de bovenstaande matchVariableNames in hun WAF-logboeken. Voor andere matchVariableNames moet u regels uitschakelen die fout-positieven geven, of een aangepaste regel maken die deze aanvragen expliciet toestaat. Met name wanneer de matchVariableName CookieName, HeaderName, PostParamName of QueryParamName is, betekent dit dat de naam zelf de regel activeert. Regeluitsluiting biedt op dit moment geen ondersteuning voor deze matchVariableNames.


Als u een aanvraag body post args met de naam *FOO* uitsluit, mag geen regel PostParamValue:FOO als de matchVariableName in uw WAF-logboeken. Mogelijk ziet u echter nog steeds een regel met matchVariableName InitialBodyContents die overeenkomt met de waarde van de post-param FOO, omdat postparam-waarden deel uitmaken van initialBodyContents.

U kunt uitsluitingslijsten toepassen op alle regels in de beheerde regelset, op regels voor een specifieke regelgroep of op één regel, zoals weergegeven in het vorige voorbeeld.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Uitsluiting definiëren op basis van Web Application Firewall logboeken
 [Azure Web Application Firewall controle en logboekregistratie toont](waf-front-door-monitor.md) overeenkomende details van een geblokkeerde aanvraag. Als een headerwaarde, cookiewaarde, postargumentwaarde of queryargumentwaarde voor sommige regels fout-positieven produceert, kunt u dat deel van de aanvraag uitsluiten van de regel. De volgende tabel bevat voorbeeldwaarden uit WAF-logboeken en de bijbehorende uitsluitingsvoorwaarden.

|matchVariableName uit WAF-logboeken    |Regeluitsluiting in portal|
|--------|------|
|CookieValue:SOME_NAME  |Cookienaam aanvragen is gelijk aan SOME_NAME|
|HeaderValue:SOME_NAME  |Naam van aanvraagheader is gelijk aan SOME_NAME|
|PostParamValue:SOME_NAME|  Aanvraag body post args name Equals SOME_NAME|
|QueryParamValue:SOME_NAME| Naam van queryreeks is gelijk aan SOME_NAME|


## <a name="next-steps"></a>Volgende stappen

Nadat u uw WAF-instellingen hebt geconfigureerd, leert u hoe u uw WAF-logboeken kunt weergeven. Zie diagnostische Front Door [voor meer informatie.](../afds/waf-front-door-monitor.md)