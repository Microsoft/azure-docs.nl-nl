---
title: Aangepaste regel voor Web Application Firewall voor Azure front-deur
description: Meer informatie over het gebruik van aangepaste regels voor Web Application firewall (WAF) om uw webtoepassingen te beschermen tegen kwaad aardige aanvallen.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "79475821"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Aangepaste regels voor Web Application Firewall met Azure front-deur

Met de Web Application firewall (WAF) van Azure kunt u de toegang tot uw webtoepassingen beheren op basis van de voor waarden die u definieert. Een aangepaste WAF-regel bestaat uit een prioriteits nummer, regel type, overeenkomst voorwaarden en een actie. Er zijn twee typen aangepaste regels: overeenkomst regels en frequentie limiet regels. Een regel voor overeenkomsten bepaalt de toegang op basis van een set overeenkomende voor waarden, terwijl een frequentie limiet regel de toegang bepaalt op basis van de overeenkomende voor waarden en de tarieven van binnenkomende aanvragen. U kunt een aangepaste regel uitschakelen om te voor komen dat deze wordt geëvalueerd, maar blijft de configuratie behouden. 

## <a name="priority-match-conditions-and-action-types"></a>Prioriteit, overeenkomst voorwaarden en actie typen

U kunt de toegang beheren met een aangepaste WAf-regel die een prioriteits nummer, een regel type, een matrix met matching voorwaarden en een actie definieert. 

- **Prioriteit:** is een uniek geheel getal dat de volg orde van de evaluatie van WAF-regels beschrijft. Regels met een lagere prioriteit worden geëvalueerd voor regels met hogere waarden. Prioriteits nummers moeten uniek zijn onder alle aangepaste regels.

- **Actie:** definieert hoe een aanvraag moet worden gerouteerd als er een WAF-regel wordt gevonden. U kunt een van de onderstaande acties kiezen die moeten worden toegepast wanneer een aanvraag overeenkomt met een aangepaste regel.

    - Met *Allow* -WAF wordt de zoek naar de back-end doorgestuurd, wordt een vermelding geregistreerd in WAF-logboeken en wordt het programma afgesloten.
    - *Block* -request is geblokkeerd, WAF stuurt een reactie naar de client zonder de aanvraag door te sturen naar de back-end. WAF registreert een vermelding in WAF-Logboeken.
    - *Log* -WAF registreert een vermelding in WAF-logboeken en gaat door met het evalueren van de volgende regel.
    - *Redirect-WAF* stuurt een aanvraag om naar een opgegeven URI, registreert een vermelding in WAF-logboeken en wordt afgesloten.

- **Match-voor waarde:** definieert een matching variabele, een operator en de waarde match. Elke regel kan meerdere match voorwaarden bevatten. Een match-voor waarde kan zijn gebaseerd op de geografische locatie, client-IP-adressen (CIDR), grootte of teken reeks overeenkomst. Overeenkomende teken reeksen kunnen worden vergeleken met een lijst met overeenkomende variabelen.
  - **Overeenkomende variabele:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - Cookies
  - **And**
    - Any: wordt vaak gebruikt voor het definiëren van de standaard actie als er geen regels overeenkomen. Any is een overeenkomende operator voor alle.
    - Is gelijk aan
    - Contains
    - LessThan: beperking van grootte
    - GreaterThan: beperking van grootte
    - LessThanOrEqual: beperking van grootte
    - GreaterThanOrEqual: beperking van grootte
    - BeginsWith
    - EndsWith
    - Reguliere
  
  - **Regex** biedt geen ondersteuning voor de volgende bewerkingen: 
    - Backreferences en vastleggen van subexpressies
    - Wille keurige verklaringen met een breedte nul
    - Verwijzingen naar subroutines en recursieve patronen
    - Voorwaardelijke patronen
    - Backtracking
    - De \c single-byte-instructie
    - De instructie \R nieuwe-regel overeenkomst
    - De richt lijn \K begin van overeenkomst reset
    - Bijschriften en Inge sloten code
    - Atomische groepering en behorende Kwant oren

  - **Negatie [Optioneel]:** U kunt de voor waarde ' *negatie* ' instellen op ' True ' als het resultaat van een voor waarde moet worden genegeerd.
      
  - **Transformeren [Optioneel]:** Een lijst met teken reeksen met namen van trans formaties voordat de overeenkomst wordt geprobeerd. Dit kunnen de volgende trans formaties zijn:
     - Converteren 
     - Kleine letters
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **Overeenkomende waarde:** Ondersteunde waarden voor HTTP-aanvraag methoden zijn:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - VERGREN delen
     - Sluit
     - PROFIEL
     - Opties
     - PROPFIND
     - PROP patch
     - MKCOL
     - COPY
     - Ga

## <a name="examples"></a>Voorbeelden

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Voor beeld van aangepaste regels voor WAF op basis van http-para meters

Hier volgt een voor beeld waarin de configuratie van een aangepaste regel met twee match voorwaarden wordt weer gegeven. Aanvragen zijn afkomstig van een opgegeven site, zoals gedefinieerd door de verwijzings punt, en de query teken reeks bevat geen ' wacht woord '.

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Hieronder ziet u een voor beeld van een configuratie voor het blok keren van de methode ' PUT ':

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Grootte beperking

U kunt een aangepaste regel maken die de beperking van de grootte voor een deel van een binnenkomende aanvraag aangeeft. De onderstaande regel blokkeert bijvoorbeeld een URL die langer is dan 100 tekens.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Volgende stappen
- [Een firewall beleid voor webtoepassingen configureren met behulp van Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Meer informatie over [Web Application Firewall met de voor deur](afds-overview.md)
- Lees hoe u [een Front Door maakt](../../frontdoor/quickstart-create-front-door.md).

