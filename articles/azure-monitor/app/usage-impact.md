---
title: Gebruiks impact van Azure-toepassing Insights | Micro soft docs
description: Analyseer hoe verschillende eigenschappen mogelijke conversie tarieven voor delen van uw apps hebben.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8ce49488124f07f05b8df2d9f4eae41e041aa0aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026186"
---
# <a name="impact-analysis-with-application-insights"></a>Impact analyse met Application Insights

Impact analyseert hoe laad tijden en andere eigenschappen invloed hebben op de conversie snelheid voor verschillende onderdelen van uw app. Om het nauw keuriger te plaatsen, detecteert het **hoe elke dimensie** van **een pagina weergave**, **aangepaste gebeurtenis** of **aanvraag** van invloed is op het gebruik van een andere **pagina weergave** of een **aangepaste gebeurtenis**. 

![Hulp programma voor impact](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Weet u nog steeds niet wat invloed heeft?

Een manier om de impact te zien is als het ultieme hulp programma voor het afzetten van argumenten met iemand in uw team over hoe traag in sommige aspecten van uw site invloed heeft op de werking van de gebruiker. Hoewel gebruikers een zekere mate van tragheid kunnen verdragen, geeft de impact u inzicht in de manier waarop het beste de optimalisatie en de prestaties van de gebruikers conversie kan worden gebalanceerd.

Maar het analyseren van prestaties is slechts een subset van de mogelijkheden van impact. Omdat de impact aangepaste gebeurtenissen en dimensies ondersteunt, is het beantwoorden van vragen zoals de keuze van de gebruikers browser die overeenkomt met de verschillende snelheid van de conversie, maar een paar muis klikken.

![Scherm afbeeldings conversie per browser](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Uw Application Insights-resource moet pagina weergaven of aangepaste gebeurtenissen bevatten voor het gebruik van het hulp programma impact. [Meer informatie over het instellen van uw app voor het automatisch verzamelen van pagina weergaven met de Application Insights java script SDK](./javascript.md). Denk er ook aan dat u bij het analyseren van de correlatie, voor beelden van de grootte.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Is de laad tijd van pagina's van invloed op het aantal mensen dat op mijn pagina wordt geconverteerd?

Kies voor het beantwoorden van vragen met het effect hulp programma een eerste pagina weergave, aangepaste gebeurtenis of aanvraag.

![Scherm afbeelding die laat zien waar een eerste pagina weergave, aangepaste gebeurtenis of aanvraag moet worden gekozen.](./media/usage-impact/0002-dropdown.png)

1. Selecteer een pagina weergave in de vervolg keuzelijst **voor de pagina weergave** .
2. Zorg **ervoor dat de** vervolg keuzelijst **analyseren hoe** de standaard waarde is geselecteerd (in deze context **duur** is een alias voor de **laad tijd** van de pagina.)
3. Selecteer een aangepaste gebeurtenis voor de **gevolgen van het gebruik van de** vervolg keuzelijst. Deze gebeurtenis moet overeenkomen met een UI-element in de pagina weergave die u hebt geselecteerd in stap 1.

![Scherm opname van resultaten](./media/usage-impact/0003-results.png)

In dit exemplaar is de laad tijd van de **product pagina** de conversie factor voor het kopen van het **product** hoger. Op basis van de bovenstaande distributie kan een optimale verhouding van 3,5 seconden voor de pagina belasting worden aangehouden om een mogelijke 55% conversie te krijgen. Meer verbeteringen in prestaties om de laad tijd onder 3,5 seconden te verminderen, zijn momenteel niet gecorreleerd met aanvullende conversie voordelen.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Wat gebeurt er als u pagina weergaven of laad tijden op aangepaste manieren bijhoudt?

Impact ondersteunt zowel standaard als aangepaste eigenschappen en metingen. Gebruik wat u wilt. Gebruik in plaats van duur filters voor de primaire en secundaire gebeurtenissen om meer specifieke te krijgen.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Worden gebruikers uit verschillende landen of regio's geconverteerd met verschillende tarieven?

1. Selecteer een pagina weergave in de vervolg keuzelijst **voor de pagina weergave** .
2. Kies land of regio in **analyseren hoe de** vervolg keuzelijst
3. Selecteer voor de **effecten van de vervolg keuzelijst het gebruik van** een aangepaste gebeurtenis die overeenkomt met een UI-element in de pagina weergave die u in stap 1 hebt gekozen.

In dit geval passen de resultaten niet meer in een doorlopend x-as-model, zoals in het eerste voor beeld. In plaats daarvan wordt een visualisatie weer gegeven die vergelijkbaar is met een gesegmenteerde trechter. Sorteer op **gebruik** om de variatie van de conversie naar uw aangepaste gebeurtenis op basis van het land of de regio weer te geven.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hoe berekent het onderdeel impact deze conversie tarieven?

Onder de motorkap is de impact van het hulp programma afhankelijk van de [correlatie coëfficiënt van Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Resultaten worden berekend tussen-1 en 1 met-1 die een negatieve lineaire correlatie vertegenwoordigen en 1 die een positieve lineaire correlatie vertegenwoordigt.

De algemene uitsplitsing van de werking van impact analyse is als volgt:

Laat _een_ = de hoofd pagina weer geven/aangepaste gebeurtenis/aanvraag die u in de eerste vervolg keuzelijst selecteert. (**Voor de pagina weergave**).

Laat _B_ = de secundaire pagina weergave/aangepaste gebeurtenis die u selecteert (heeft **invloed op het gebruik van**).

Impact kijkt naar een voor beeld van alle sessies van gebruikers in het geselecteerde tijds bereik. Voor elke sessie zoekt het naar elk exemplaar van _A_.

Sessies worden vervolgens onderverdeeld in twee verschillende soorten _subsessies_ op basis van een van de volgende twee omstandigheden:

- Een geconverteerde subsessie bestaat uit een sessie die eindigt op een _B_ -gebeurtenis en _omvat alle gebeurtenissen_ die vóór _B_ worden uitgevoerd.
- Een niet-geconverteerde subsessie treedt op wanneer alle _a_ plaatsvindt zonder een Terminal _B_.

Hoe invloed wordt uiteindelijk berekend, is afhankelijk van het feit of er wordt geanalyseerd op basis van metrische gegevens of dimensies. Voor metrische gegevens van alle _a_ in een subsessie wordt het gemiddelde berekend. Overwegende dat voor dimensies de waarde van elk _A een_ bijdrage levert aan _1/N_ aan de waarde die is toegewezen aan _B_ , waarbij _N_ het aantal in de subsessie _is._

## <a name="next-steps"></a>Volgende stappen

- Begin met het verzenden van [aangepaste gebeurtenissen](./api-custom-events-metrics.md#trackevent) of [pagina weergaven](./api-custom-events-metrics.md#page-views)om gebruiks ervaringen in te scha kelen.
- Als u al aangepaste gebeurtenissen of pagina weergaven verzendt, kunt u de gebruiks hulpprogramma's verkennen om te leren hoe gebruikers uw service gebruiken.
    - [Trechters](usage-funnels.md)
    - [Bewaar](usage-retention.md)
    - [Gebruikersstromen](usage-flows.md)
    - [Werkmappen](../visualize/workbooks-overview.md)
    - [Gebruikers context toevoegen](./usage-overview.md)