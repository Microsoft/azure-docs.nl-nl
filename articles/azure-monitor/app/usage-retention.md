---
title: Bewaren van webapp-gebruikers met Azure-toepassing Insights analyseren
description: Hoeveel gebruikers retour neren uw app?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 37de27e8dc707133595b71b39e45bdec554289e5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583386"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analyse van gebruikers retentie voor webtoepassingen met Application Insights

De functie voor het bewaren van [Azure-toepassing Insights](./app-insights-overview.md) helpt u bij het analyseren van het aantal gebruikers dat naar uw app terugkeert, en hoe vaak ze bepaalde taken uitvoeren of doel stellingen bereiken. Als u bijvoorbeeld een spel site uitvoert, kunt u het aantal gebruikers dat terugkeert naar de site vergelijken nadat een spel is verloren met het nummer dat als resultaat heeft gegeven na de hoogste bieder. Deze kennis kan u helpen om zowel uw gebruikers ervaring als uw bedrijfs strategie te verbeteren.

## <a name="get-started"></a>Aan de slag

Als u de gegevens in het hulp programma voor retentie nog niet ziet in de Application Insights Portal, [leert u hoe u aan de slag kunt gaan met de gebruiks hulpprogramma's](usage-overview.md).

## <a name="the-retention-tool"></a>Het hulp programma voor retentie

![Retentie-informatie](./media/usage-retention/retention.png)

1. Met de werk balk kunnen gebruikers nieuwe Bewaar rapporten maken, bestaande Bewaar rapporten openen, het huidige retentie Rapport opslaan of opslaan als, wijzigingen aanbrengen in opgeslagen rapporten, gegevens in het rapport vernieuwen, het rapport delen via e-mail of directe koppeling en toegang krijgen tot de documentatie pagina. 
2. Standaard toont de Bewaar periode alle gebruikers die er iets voor hebben gedaan en iets anders heeft geduurd. U kunt een andere combi natie van gebeurtenissen selecteren om de focus op specifieke gebruikers activiteiten te verfijnen.
3. Voeg een of meer filters op eigenschappen toe. U kunt zich bijvoorbeeld richten op gebruikers in een bepaald land of een bepaalde regio. Klik op **bijwerken** nadat u de filters hebt ingesteld. 
4. In het algemene Bewaar diagram ziet u een overzicht van de gebruikers retentie over de geselecteerde tijds periode. 
5. Het raster toont het aantal gebruikers dat is bewaard volgens de opbouw functie voor query's in #2. Elke rij vertegenwoordigt een cohort van gebruikers die een gebeurtenis in de weer gegeven tijds periode hebben uitgevoerd. Elke cel in de rij laat zien hoeveel van de cohort ten minste één keer in een latere periode is geretourneerd. Sommige gebruikers kunnen in meer dan één periode terugkeren. 
6. De inzichten kaarten geven vijf begin gebeurtenissen en vijf geretourneerde gebeurtenissen weer om gebruikers een beter inzicht te geven in het retentie Rapport. 

![Muis aanwijzer vasthouden](./media/usage-retention/hover.png)

Gebruikers kunnen met de muis aanwijzer over cellen op het retentie hulpprogramma klikken om toegang te krijgen tot de analyse knop en tool tips waarin wordt uitgelegd wat de cel betekent. De analyse knop neemt gebruikers in het analyse hulpprogramma met een vooraf gevulde query om gebruikers uit de cel te genereren. 

## <a name="use-business-events-to-track-retention"></a>Zakelijke gebeurtenissen gebruiken om Bewaar perioden bij te houden

Meet gebeurtenissen die belang rijke bedrijfs activiteiten vertegenwoordigen om de meest nuttige analyse van de Bewaar periode te verkrijgen. 

Veel gebruikers kunnen bijvoorbeeld een pagina in uw app openen zonder het spel te spelen dat wordt weer gegeven. Het bijhouden van alleen de pagina weergaven levert daarom een onnauwkeurige schatting op van het aantal mensen dat het spel heeft geretourneerd nadat het eerder is geweest. Om een duidelijke afbeelding van het retour neren van spelers te krijgen, moet uw app een aangepaste gebeurtenis verzenden wanneer een gebruiker daad werkelijk speelt.  

Het is een goed idee om aangepaste gebeurtenissen te coderen die belang rijke zakelijke acties vertegenwoordigen en deze te gebruiken voor de retentie analyse. Als u het resultaat van het spel wilt vastleggen, moet u een regel code schrijven om een aangepaste gebeurtenis naar Application Insights te verzenden. Als u het bestand in de webpagina code of in Node.JS schrijft, ziet het er als volgt uit:

```JavaScript
    appinsights.trackEvent("won game");
```

Of in ASP.NET-Server code:

```csharp
   telemetry.TrackEvent("won game");
```

Meer [informatie over het schrijven van aangepaste gebeurtenissen](./api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Volgende stappen
- Begin met het verzenden van [aangepaste gebeurtenissen](./api-custom-events-metrics.md#trackevent) of [pagina weergaven](./api-custom-events-metrics.md#page-views)om gebruiks ervaringen in te scha kelen.
- Als u al aangepaste gebeurtenissen of pagina weergaven verzendt, kunt u de gebruiks hulpprogramma's verkennen om te leren hoe gebruikers uw service gebruiken.
    - [Gebruikers, sessies, gebeurtenissen](usage-segmentation.md)
    - [Trechters](usage-funnels.md)
    - [Gebruikersstromen](usage-flows.md)
    - [Werkmappen](../visualize/workbooks-overview.md)
    - [Gebruikers context toevoegen](usage-send-user-context.md)

