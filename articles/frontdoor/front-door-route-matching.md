---
title: Azure front-deur-routerings regel die overeenkomt met bewaking | Microsoft Docs
description: Dit artikel helpt u te begrijpen hoe de voor deur van Azure overeenkomt met de routerings regel die moet worden gebruikt voor een inkomende aanvraag
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 67940db973f494cd4a12c2f16db528e0b113d656
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91449202"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>Hoe aanvragen worden vergeleken met een routerings regel

Na het tot stand brengen van een verbinding en het volt ooien van een TLS-Handshake, kunt u, wanneer een aanvraag binnenkomt op een van de eerste punten van de voor deur, bepalen aan welke specifieke routerings regel de aanvraag moet voldoen en vervolgens de gedefinieerde actie in de configuratie uitvoeren. In het volgende document wordt uitgelegd hoe de voor deur bepaalt welke route configuratie moet worden gebruikt bij het verwerken van een HTTP-aanvraag.

## <a name="structure-of-a-front-door-route-configuration"></a>Structuur van een configuratie voor een front-deur route
De configuratie van een voor deur routerings regel bestaat uit twee belang rijke onderdelen: een ' aan de linkerkant ' en een ' rechter kant '. We komen overeen met de inkomende aanvraag aan de linkerkant van de route, terwijl aan de rechter kant wordt gedefinieerd hoe de aanvraag wordt verwerkt.

### <a name="incoming-match-left-hand-side"></a>Inkomende overeenkomst (links)
De volgende eigenschappen bepalen of de binnenkomende aanvraag overeenkomt met de routerings regel (of aan de linkerkant):

* **HTTP-protocollen** (http/https)
* **Hosts** (bijvoorbeeld www- \. foo.com, \* . bar.com)
* **Paden** (bijvoorbeeld/ \* ,/gebruikers/ \* ,/file.gif)

Deze eigenschappen worden intern uitgevouwen zodat elke combi natie van protocol/host/pad een mogelijke overeenkomende set is.

### <a name="route-data-right-hand-side"></a>Gegevens routeren (aan de rechter kant)
De beslissing over het verwerken van de aanvraag is afhankelijk van het feit of caching is ingeschakeld of niet voor de specifieke route. Als er geen antwoord in de cache voor de aanvraag is, wordt de aanvraag doorgestuurd naar de juiste back-end in de geconfigureerde back-end-groep.

## <a name="route-matching"></a>Route vergelijking
In deze sectie wordt de nadruk gelegd op de manier waarop we overeenkomen met een bepaalde voor deur routerings regel. Het basis concept is dat we altijd overeenkomen met de **meest specifieke overeenkomst eerst** kijken alleen aan de linkerkant.  We passen eerst op basis van het HTTP-protocol, vervolgens de frontend-host en vervolgens het pad.

### <a name="frontend-host-matching"></a>Overeenkomende host voor frontend
Bij het vergelijken van frontend-hosts gebruiken we de hieronder beschreven logica:

1. Zoek naar een route ring met een exacte overeenkomst op de host.
2. Als er geen exacte frontend-hosts overeenkomen, moet u de aanvraag afwijzen en een 400-fout met een onjuiste aanvraag verzenden.

Om dit proces verder uit te leggen, bekijken we een voor beeld van de configuratie van front-deur routes (alleen aan de linkerkant):

| Routeringsregel | Frontend-hosts | Pad |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /gebruikers/\* |
| C | www \. -fabrikam.com, Foo.Adventure-Works.com  | /\*, /images/\* |

Als de volgende binnenkomende aanvragen zijn verzonden naar de voor deur, zouden ze overeenkomen met de volgende routerings regels van boven:

| Inkomende frontend-host | Overeenkomende regel (s) voor route ring |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www- \. fabrikam.com | C |
| images.fabrikam.com | Fout 400: ongeldige aanvraag |
| foo.adventure-works.com | C |
| contoso.com | Fout 400: ongeldige aanvraag |
| www- \. Adventure-Works.com | Fout 400: ongeldige aanvraag |
| www- \. northwindtraders.com | Fout 400: ongeldige aanvraag |

### <a name="path-matching"></a>Pad zoeken
Nadat u de specifieke frontend-host hebt bepaald en mogelijke routerings regels hebt gefilterd op alleen de routes met die frontend-host, worden de routerings regels vervolgens gefilterd op basis van het aanvraag pad. We gebruiken een soort gelijke logica als frontend-hosts:

1. Zoek naar een routerings regel met een exacte overeenkomst voor het pad
2. Als er geen exacte overeenkomst paden zijn, zoekt u naar routerings regels met een pad naar een Joker teken dat overeenkomt met
3. Als er geen routerings regels met een overeenkomend pad worden gevonden, kunt u de aanvraag afwijzen en een 400: fout HTTP-antwoord voor een ongeldige aanvraag retour neren.

>[!NOTE]
> Alle paden zonder joker teken worden als exacte paden beschouwd. Zelfs als het pad in een slash eindigt, wordt het toch als exacte overeenkomst beschouwd.

Bekijk voor meer uitleg een andere set voor beelden:

| Routeringsregel | Frontend-host    | Pad     |
|-------|---------|----------|
| A     | www- \. contoso.com | /        |
| B     | www- \. contoso.com | /\*      |
| C     | www- \. contoso.com | /ab      |
| D     | www- \. contoso.com | /abc     |
| E     | www- \. contoso.com | ABC    |
| F     | www- \. contoso.com | ABC\*  |
| G     | www- \. contoso.com | /abc/def |
| H     | www- \. contoso.com | /Path   |

Gezien de configuratie is het volgende voor beeld dat overeenkomt met de tabel:

| Binnenkomende aanvraag    | Overeenkomende route |
|---------------------|---------------|
| www- \. contoso.com/            | A             |
| www- \. contoso.com/a           | B             |
| www- \. contoso.com/AB          | C             |
| www- \. contoso.com/ABC         | D             |
| www- \. contoso.com/abzzz       | B             |
| www- \. contoso.com/ABC/        | E             |
| www- \. contoso.com/abc/d       | F             |
| www- \. contoso.com/abc/def     | G             |
| www- \. contoso.com/ABC/defzzz  | F             |
| www- \. contoso.com/abc/def/ghi | F             |
| www- \. contoso.com/Path        | B             |
| www- \. contoso.com/Path/       | H             |
| www- \. contoso.com/Path/zzz    | B             |

>[!WARNING]
> </br> Als er geen routerings regels zijn voor een exacte front-end-frontend-host met een ' catch-all route Path ( `/*` ), is er geen overeenkomst met een routerings regel.
>
> Voorbeeld configuratie:
>
> | Route | Host             | Pad    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /API\* |
>
> Overeenkomende tabel:
>
> | Binnenkomende aanvraag       | Overeenkomende route |
> |------------------------|---------------|
> | profile.domain.com/other | Geen. Fout 400: ongeldige aanvraag |

### <a name="routing-decision"></a>Routerings beslissing
Zodra we overeenkomen met één regel voor de voor deur, moeten we kiezen hoe de aanvraag moet worden verwerkt. Als voor de overeenkomende routerings regel een antwoord in de cache beschikbaar is, wordt hetzelfde door gegeven aan de client. Anders is het volgende dat wordt geëvalueerd, of u nu het [herschrijven van url's (aangepast doorstuur traject)](front-door-url-rewrite.md) hebt geconfigureerd voor de regel voor de overeenkomende route ring of niet. Als er geen aangepast forwarding Path is gedefinieerd, wordt de aanvraag doorgestuurd naar de juiste back-end in de geconfigureerde back-end-groep. Anders wordt het pad naar de aanvraag bijgewerkt volgens het [aangepaste forwarding-pad](front-door-url-rewrite.md) dat is gedefinieerd en wordt vervolgens doorgestuurd naar de back-end.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
