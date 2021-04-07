---
title: Wat is Azure front deur Standard/Premium Route?
description: Dit artikel helpt u te begrijpen hoe de standaard/Premium van Azure overeenkomt met de routerings regel die moet worden gebruikt voor een binnenkomende aanvraag.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099198"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Wat is de route van Azure front deur Standard/Premium (preview)?

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

De standaard/Premium route van Azure front deur definieert hoe het verkeer wordt afgehandeld wanneer de inkomende aanvraag arriveert op de Azure front-deur omgeving. Via de route-instellingen wordt een koppeling gedefinieerd tussen een domein en een back-end-groep. Door de geavanceerde functies, zoals patroon, in te scha kelen, kunt u de controle over het verkeer gedetailleerder instellen.

Een standaard-en Premium-routerings configuratie voor de voor deur bestaat uit twee belang rijke onderdelen: ' links en rechts '. We komen overeen met de inkomende aanvraag aan de linkerkant van de route en aan de rechter kant wordt gedefinieerd hoe de aanvraag wordt verwerkt.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

### <a name="incoming-match-left-hand-side"></a>Inkomende overeenkomst (links)

De volgende eigenschappen bepalen of de binnenkomende aanvraag overeenkomt met de routerings regel (of aan de linkerkant):

* **HTTP-protocollen** (http/https)
* **Hosts** (bijvoorbeeld www- \. foo.com, \* . bar.com)
* **Paden** (bijvoorbeeld/ \* ,/gebruikers/ \* ,/file.gif)

Deze eigenschappen worden intern uitgevouwen zodat elke combi natie van protocol/host/pad een mogelijke overeenkomende set is.

### <a name="route-data-right-hand-side"></a>Gegevens routeren (aan de rechter kant)

De beslissing over het verwerken van de aanvraag is afhankelijk van het feit of caching is ingeschakeld of niet voor de route. Als een antwoord in de cache niet beschikbaar is, wordt de aanvraag doorgestuurd naar de juiste back-end.

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

Als standaard/Premium van Azure front deur de specifieke frontend-host en het filteren van mogelijke routerings regels naar alleen de routes met die frontend-host wordt bepaald. De voor deur filtert vervolgens de routerings regels op basis van het aanvraag pad. We gebruiken een soort gelijke logica als frontend-hosts:

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

Zodra de standaard/Premium van Azure voor de voor deur overeenkomt met één routerings regel, dan moet worden gekozen hoe de aanvraag moet worden verwerkt. Als voor de Azure front-deur Standard/Premium een antwoord in de cache beschikbaar is voor de overeenkomende routerings regel, wordt de aanvraag teruggestuurd naar de client. In het volgende voor beeld van Azure front deur Standard/Premium wordt geëvalueerd of u een regel hebt ingesteld voor de overeenkomende routerings regel. Als er geen regelset is gedefinieerd, wordt de aanvraag doorgestuurd naar de back-end-groep. Anders wordt de regelset uitgevoerd in de volg orde waarin ze zijn geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md).
