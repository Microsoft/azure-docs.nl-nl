---
title: Lijst met uitsluitingen voor Web Application firewall in azure front deur-Azure Portal
description: Dit artikel bevat informatie over de configuratie van een uitsluitings lijst in azure front met de Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a92679bb3114c4a60870424f3ec68a8de7b303da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499914"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application firewall (WAF) met uitsluitings lijsten voor de front-deur service 

Soms kan Web Application firewall (WAF) een aanvraag blok keren die u wilt toestaan voor uw toepassing. Active Directory voegt bijvoorbeeld tokens in die worden gebruikt voor verificatie. Deze tokens kunnen speciale tekens bevatten die een onjuiste positieve waarde van de WAF-regels kunnen activeren. Met WAF-uitsluitings lijsten kunt u bepaalde kenmerken van aanvragen van een WAF-evaluatie weglaten.  Een uitsluitings lijst kan worden geconfigureerd met behulp van  [Power shell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [Azure cli](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)of de Azure Portal. In het volgende voor beeld ziet u de Azure Portal configuratie. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Uitsluitings lijsten configureren met behulp van de Azure Portal
**Uitsluitingen beheren** is toegankelijk vanuit de WAF-portal onder **beheerde regels**

![Uitsluitings ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ exclusion_add beheren](../media/waf-front-door-exclusion/exclusion2.png)

 Een voor beeld van een uitsluitings lijst: ![ exclusion_define beheren](../media/waf-front-door-exclusion/exclusion3.png)

In dit voor beeld wordt de waarde in het veld voor de *gebruikers* header uitgesloten. Een geldige aanvraag kan het *gebruikers* veld bevatten dat een teken reeks bevat die een SQL-injectie regel activeert. U kunt de para meter *gebruiker* in dit geval uitsluiten, zodat de regel WAF niets in het veld evalueert.

De volgende kenmerken kunnen worden toegevoegd aan de uitsluitings lijsten op naam. De waarden van de velden die u gebruikt, worden niet geëvalueerd op basis van WAF-regels, maar hun namen worden geëvalueerd. De uitsluitings lijsten verwijderen de inspectie van de veld waarde.

* Naam van aanvraag header
* Cookie naam van aanvraag
* Naam van de query teken reeks argumenten
* Bericht argument naam van aanvraag Body

U kunt een exacte overeenkomst voor de aanvraag header, hoofd tekst, cookie of query teken reeks kenmerken opgeven.  Of u kunt eventueel gedeeltelijke overeenkomsten opgeven. De volgende Opera tors worden ondersteund:

- **Is gelijk aan**: deze operator wordt gebruikt voor een exacte overeenkomst. Als u bijvoorbeeld een header met de naam **bearerToken** wilt selecteren, gebruikt u de operator equals met de selector ingesteld als **bearerToken**.
- **Begint met**: deze operator komt overeen met alle velden die beginnen met de opgegeven selector-waarde.
- **Eindigt op**: deze operator komt overeen met alle aanvraag velden die eindigen op de opgegeven selector-waarde.
- **Contains**: deze operator komt overeen met alle aanvraag velden die de opgegeven selector-waarde bevatten.
- **Is gelijk aan**: deze operator komt overeen met alle aanvraag velden. * is de waarde selector.

Namen van kopteksten en cookies zijn niet hoofdletter gevoelig.

Als de waarde van een header, cookie waarde, post argument of query argument een fout-positieve waarde voor sommige regels retourneert, kunt u dat gedeelte van de aanvraag uitsluiten van de regel:


|matchVariableName uit WAF-logboeken  |Regel uitsluiting in Portal  |
|---------|---------|
|CookieValue: SOME_NAME        |De naam van de aanvraag cookie is gelijk aan SOME_NAME|
|HeaderValue: SOME_NAME        |De naam van de aanvraag header is gelijk aan SOME_NAME|
|PostParamValue: SOME_NAME     |De naam van de post-argument tekst van de aanvraag is gelijk aan SOME_NAME|
|QueryParamValue: SOME_NAME    |De naam van de argumenten van de query reeks is gelijk aan SOME_NAME|


Momenteel worden alleen regel uitsluitingen voor de bovenstaande matchVariableNames in de WAF-logboeken ondersteund. Voor elk ander matchVariableNames moet u regels uitschakelen die een onjuiste positieve waarde geven, of een aangepaste regel maken waarmee deze aanvragen expliciet worden toegestaan. Met name als de matchVariableName is ingesteld op cookie naam, header naam, PostParamName of QueryParamName, betekent dit dat de naam zelf de regel wordt geactiveerd. Regel uitsluiting biedt op dit moment geen ondersteuning voor deze matchVariableNames.


Als u een post-argument van een aanvraag hoofdtekst met de naam *Foo* uitsluit, moet er geen regel POSTPARAMVALUE: Foo weer geven als matchVariableName in uw WAF-Logboeken. Het is echter mogelijk dat er nog steeds een regel met matchVariableName InitialBodyContents wordt weer geven die overeenkomt met de waarde van de post param FOO, aangezien de para meters van de post een onderdeel zijn van de InitialBodyContents.

U kunt uitsluitings lijsten Toep assen op alle regels in de set met beheerde regels, op regels voor een specifieke regel groep of op een enkele regel zoals weer gegeven in het vorige voor beeld.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Uitsluiting definiëren op basis van Firewall logboeken van Web Application
 De [firewall bewaking en logboek registratie van Azure Web Application](waf-front-door-monitor.md) toont overeenkomende details van een geblokkeerde aanvraag. Als de waarde van een header, cookie waarde, post argument of query argument een fout-positieve waarde voor sommige regels retourneert, kunt u dat deel van de aanvraag uitsluiten van de regel. In de volgende tabel ziet u voor beelden van waarden uit WAF-logboeken en de bijbehorende uitsluitings voorwaarden.

|matchVariableName uit WAF-logboeken    |Regel uitsluiting in Portal|
|--------|------|
|CookieValue: SOME_NAME  |De naam van de aanvraag cookie is gelijk aan SOME_NAME|
|HeaderValue: SOME_NAME  |De naam van de aanvraag header is gelijk aan SOME_NAME|
|PostParamValue: SOME_NAME|  De naam van de post-argument tekst van de aanvraag is gelijk aan SOME_NAME|
|QueryParamValue: SOME_NAME| De naam van de argumenten van de query reeks is gelijk aan SOME_NAME|


## <a name="next-steps"></a>Volgende stappen

Nadat u uw WAF-instellingen hebt geconfigureerd, leert u hoe u uw WAF-Logboeken kunt weer geven. Zie voor meer informatie [Diagnostische gegevens over de voor deur](../afds/waf-front-door-monitor.md).