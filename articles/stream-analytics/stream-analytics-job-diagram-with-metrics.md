---
title: Fout opsporing op basis van gegevens in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u de Azure Stream Analytics-taak kunt oplossen met behulp van het taak diagram en de metrische gegevens in de Azure Portal.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: 6d20454515088ccca87665d9b3b27c0d82c3cdf9
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020397"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Gegevensgestuurde foutopsporing met behulp van het taakdiagram

Het taak diagram op de Blade **bewaking** in het Azure Portal kan u helpen uw taak pijplijn te visualiseren. Hierin worden invoer, uitvoer en querystappen weergegeven. U kunt het taakdiagram gebruiken om de metrische gegevens voor elke stap te onderzoeken, om de bron van het probleem sneller te isoleren bij het oplossen van problemen.

## <a name="using-the-job-diagram"></a>Het taak diagram gebruiken

Selecteer in de Azure Portal, terwijl u in een Stream Analytics-taak, onder **ondersteuning en probleem oplossing**, **taak diagram**:

![Taak diagram met metrische gegevens-locatie](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selecteer elke query stap om de bijbehorende sectie in een deel venster voor het bewerken van query's weer te geven. Een grafiek met metrische gegevens voor de stap wordt weer gegeven in een onderste deel venster op de pagina.

![Taak diagram met metrische gegevens-basis taak](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Als u de partities van de Azure Event Hubs invoer wilt zien, selecteert u **...** Er wordt een context menu weer gegeven. U kunt ook de invoer fusie bekijken.

![Taak diagram met metrische gegevens-partitie uitbreiden](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Als u de metrische grafiek slechts voor één partitie wilt weer geven, selecteert u het partitie knooppunt. De metrische gegevens worden aan de onderkant van de pagina weer gegeven.

![Taak diagram met metrische gegevens-meer metrische gegevens](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Als u het grafiek met metrische gegevens voor een fusie wilt weer geven, selecteert u het knoop punt fusie. In het volgende diagram ziet u dat er geen gebeurtenissen zijn verwijderd of aangepast.

![Taak diagram met metrische gegevens-raster](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Als u de details van de metrische waarde en tijd wilt weer geven, wijst u de grafiek aan.

![Taak diagram met metrische gegevens-aanwijzen](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Problemen oplossen met metrische gegevens

De metrische waarde **QueryLastProcessedTime** geeft aan wanneer een specifieke stap gegevens heeft ontvangen. Door de topologie te bekijken, kunt u naar achteren werken vanuit de uitvoer processor om te zien welke stap geen gegevens ontvangt. Als er geen gegevens worden opgehaald uit een stap, gaat u naar de query stap alleen voor deze. Controleer of de voor gaande query stap een tijd venster heeft en of er voldoende tijd is verstreken voor het uitvoeren van gegevens. (Houd er rekening mee dat Windows op het hele uur is uitgelijnd.)
 
Als de voor gaande query stap een invoer processor is, gebruikt u de metrische gegevens invoer om de volgende gerichte vragen te beantwoorden. Ze kunnen u helpen bepalen of er gegevens worden opgehaald uit de invoer bronnen van een taak. Controleer elke partitie als de query gepartitioneerd is.
 
### <a name="how-much-data-is-being-read"></a>Hoeveel gegevens worden er gelezen?

*   **InputEventsSourcesTotal** is het aantal gelezen gegevens eenheden. Bijvoorbeeld het aantal blobs.
*   **InputEventsTotal** is het aantal gelezen gebeurtenissen. Deze metrische waarde is beschikbaar per partitie.
*   **InputEventsInBytesTotal** is het aantal gelezen bytes.
*   **InputEventsLastArrivalTime** wordt bijgewerkt met elke ontvangen gebeurtenis in wachtrij.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Wordt de tijd vooruit verplaatst? Als er feitelijke gebeurtenissen worden gelezen, wordt er mogelijk geen interpunctie gegenereerd.

*   **InputEventsLastPunctuationTime** geeft aan wanneer er interpunctie is gegenereerd om ervoor te zorgen dat de tijd vooruit blijft lopen. Als er geen interpunctie wordt gegeven, kan de gegevens stroom worden geblokkeerd.
 
### <a name="are-there-any-errors-in-the-input"></a>Zijn er fouten in de invoer?

*   **Inputeventseventdatanulltotal telt** is een telling van gebeurtenissen die null-gegevens bevatten.
*   **Inputeventsserializererrorstotal telt alle** is een telling van gebeurtenissen die niet correct kunnen worden gedeserialiseerd.
*   **Inputeventsdegradedtotal telt alle** is een telling van gebeurtenissen waarvan het probleem zich voordoet, anders dan bij deserialisatie.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Worden gebeurtenissen verwijderd of aangepast?

*   **InputEventsEarlyTotal** is het aantal gebeurtenissen met een tijds tempel van de toepassing voor de bovengrens.
*   **Inputeventsearlytotal** is het aantal gebeurtenissen met een tijds tempel van de toepassing na de bovengrens.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** is het aantal gebeurtenissen dat voor de begin tijd van de taak is verwijderd.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Vallen we achter in het lezen van gegevens?

*   In de achterstand van de **invoer (totaal)** wordt uitgelegd hoe veel meer berichten moeten worden gelezen voor Event hubs en Azure IOT hub-invoer. Wanneer dit aantal groter is dan 0, betekent dit dat uw taak de gegevens niet kan verwerken wanneer deze zich in bevindt. In dat geval moet u mogelijk het aantal streaming-eenheden verhogen en/of ervoor zorgen dat uw taak kan worden geparallelleerd. Op de  [pagina query parallel Lise ring](./stream-analytics-parallelization.md)vindt u meer informatie. 


## <a name="get-help"></a>Help opvragen
Voor aanvullende hulp gaat u [naar onze micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslag informatie voor de query taal Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Naslag informatie over Stream Analytics beheer REST API](/rest/api/streamanalytics/)