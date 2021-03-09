---
title: Taak bewaking in Azure Stream Analytics begrijpen
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics taken bewaken in de Azure Portal.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: seodec18
ms.openlocfilehash: 6d3558511721a91c3a195cb510a1a00d5d8a9a51
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487875"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Meer informatie over Stream Analytics taak bewaking en het bewaken van query's

## <a name="introduction-the-monitor-page"></a>Inleiding: de pagina controleren
De belangrijkste prestatie gegevens voor de Azure Portal-Opper vlakken die kunnen worden gebruikt voor het bewaken en oplossen van de prestaties van query's en taken. Als u deze metrische gegevens wilt zien, bladert u naar de Stream Analytics-taak waarvoor u de metrische gegevens wilt bekijken en bekijkt u de sectie **bewaking** op de pagina overzicht.  

![Koppeling voor taak bewaking Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Het venster wordt weer gegeven zoals hieronder weer gegeven:

![Dash board Stream Analytics taak bewaking](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Beschik bare metrische gegevens voor Stream Analytics
| Gegevens                 | Definitie                               |
| ---------------------- | ---------------------------------------- |
| Inachterstand, invoer gebeurtenissen       | Aantal invoer gebeurtenissen dat achterstand is. Een waarde die niet gelijk is aan nul voor deze metriek houdt in dat uw taak het aantal binnenkomende gebeurtenissen niet kan blijven gebruiken. Als deze waarde langzaam toeneemt of constant niet-nul is, moet u uw taak uitschalen. U kunt meer informatie vinden door te kijken naar [streaming-eenheden begrijpen en aanpassen](stream-analytics-streaming-unit-consumption.md). |
| Gegevens conversie fouten | Aantal uitvoer gebeurtenissen dat niet kan worden geconverteerd naar het verwachte uitvoer schema. Fout beleid kan worden gewijzigd in drop om gebeurtenissen te verwijderen die in dit scenario optreden. |
| Percentage CPU-gebruik (preview-versie)       | Het percentage CPU-gebruik dat door uw taak wordt gebruikt. Zelfs als deze waarde zeer hoog is (90% of hoger), mag u het aantal SUs alleen op basis van deze metrische gegevens verhogen. Als het aantal ingrijpende invoer gebeurtenissen of watermerk vertraging toeneemt, kunt u deze CPU% gebruikwaarde gebruiken om te bepalen of CPU het knel punt is. Het is mogelijk dat deze metriek af en toe pieken heeft. U wordt aangeraden schaal tests uit te voeren om de bovengrens van uw taak te bepalen, waarna de invoer van de achterstand of het watermerk vertraging wordt verhoogd vanwege een CPU-knel punt. |
| Vroege invoer gebeurtenissen       | Gebeurtenissen waarvan het tijds tempel van de toepassing voor meer dan vijf minuten eerder is dan de aankomst tijd. |
| Mislukte functie aanvragen | Aantal mislukte Azure Machine Learning functie aanroepen (indien aanwezig). |
| Functie gebeurtenissen        | Het aantal gebeurtenissen dat is verzonden naar de Azure Machine Learning functie (indien aanwezig). |
| Functie aanvragen      | Het aantal aanroepen naar de Azure Machine Learning functie (indien aanwezig). |
| Fouten bij het deserialiseren van de invoer       | Aantal invoer gebeurtenissen dat niet kan worden gedeserialiseerd.  |
| Invoer gebeurtenis bytes      | De hoeveelheid gegevens die wordt ontvangen door de Stream Analytics taak, in bytes. Dit kan worden gebruikt om te valideren dat gebeurtenissen worden verzonden naar de invoer bron. |
| Invoer gebeurtenissen           | Het aantal records dat uit de invoer gebeurtenissen is gedeserialiseerd. Dit aantal bevat geen binnenkomende gebeurtenissen die leiden tot fouten bij deserialisatie. Dezelfde gebeurtenissen kunnen door Stream Analytics meerdere keren worden opgenomen in scenario's zoals interne herstel bewerkingen en Self-join's. Daarom wordt aanbevolen geen invoer gebeurtenissen en metrische gegevens over uitvoer gebeurtenissen te verwachten als uw taak een eenvoudige ' Pass Through-query heeft. |
| Invoer bronnen ontvangen       | Het aantal berichten dat is ontvangen door de taak. Voor Event hub is een bericht één Event Data. Voor blob is een bericht één blob. Houd er rekening mee dat invoer bronnen worden geteld vóór deserialisatie. Als er fouten zijn opgetreden bij de serialisatie, kunnen invoer bronnen groter zijn dan invoer gebeurtenissen. Anders kan het kleiner zijn dan of gelijk zijn aan invoer gebeurtenissen, omdat elk bericht meerdere gebeurtenissen kan bevatten. |
| Late invoer gebeurtenissen      | Gebeurtenissen die later zijn aangekomen dan het geconfigureerde tolerantie venster voor late aankomst. Meer informatie over het [Azure stream Analytics van overwegingen voor gebeurtenis orders](./stream-analytics-time-handling.md) . |
| Out-of-order gebeurtenissen    | Het aantal gebeurtenissen dat in de juiste volg orde is ontvangen en dat is verwijderd of een aangepast tijds tempel heeft gegeven, op basis van het beleid voor het best Ellen van gebeurtenissen. Dit kan worden beïnvloed door de configuratie van de instelling voor het verouderde tolerantie venster. |
| Uitvoer gebeurtenissen          | De hoeveelheid gegevens die door de Stream Analytics taak wordt verzonden naar het uitvoer doel, in aantal gebeurtenissen. |
| Runtime-fouten         | Totaal aantal fouten met betrekking tot de verwerking van query's (zonder fouten gevonden tijdens het opnemen van gebeurtenissen of het uitvoeren van resultaten) |
| % Gebruik       | Het percentage geheugen dat wordt gebruikt door uw taak. Als SU% gebruik consistent is boven 80%, wordt de vertraging van het water merk oplopend en wordt het aantal achterstands gebeurtenissen verhoogd, overweegt u de streaming-eenheden te verhogen. Hoog gebruik geeft aan dat de taak de Maxi maal toegewezen bronnen gebruikt. |
| Watermerk vertraging       | De maximale vertraging voor het water merk over alle partities van alle uitvoer in de taak. |

U kunt deze metrische gegevens gebruiken om [de prestaties van uw stream Analytics-taak te bewaken](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Bewaking aanpassen in de Azure Portal
U kunt het type grafiek, de weer gegeven metrische gegevens en het tijds bereik in de instellingen van de grafiek wijzigen. Zie [How to Customize monitoring (bewaking aanpassen](../azure-monitor/data-platform.md)) voor meer informatie.

  ![Tijd diagram van de query controle Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Nieuwste uitvoer
Een ander interessant gegevens punt voor het bewaken van uw taak is de tijd van de laatste uitvoer, zoals weer gegeven op de pagina overzicht.
Dit is de tijd van de toepassing (dus de tijd die de tijds tempel van de gebeurtenis gegevens gebruikt) van de laatste uitvoer van uw taak.

## <a name="get-help"></a>Hulp vragen
Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)
