---
title: Problemen met Azure Stream Analytics query's oplossen
description: In dit artikel worden technieken beschreven voor het oplossen van problemen met query's in Azure Stream Analytics-taken.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0dc36b817d5b5cdf731edecd64e1879c153d866a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015127"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Problemen met Azure Stream Analytics query's oplossen

In dit artikel worden veelvoorkomende problemen beschreven met het ontwikkelen van Stream Analytics query's en het oplossen van problemen.

In dit artikel worden veelvoorkomende problemen met het ontwikkelen van Azure Stream Analytics query's beschreven, het oplossen van problemen met query's en het oplossen van de problemen. Voor een groot aantal probleemoplossings stappen moeten resource logboeken zijn ingeschakeld voor uw Stream Analytics-taak. Als u geen resource Logboeken hebt ingeschakeld, raadpleegt u [problemen oplossen Azure stream Analytics met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>De verwachte uitvoer wordt niet geproduceerd met de query

1.  Controleer fouten door lokaal te testen:

    - Selecteer op Azure Portal op het tabblad **query** de optie **testen**. Gebruik de gedownloade voorbeeld gegevens om [de query te testen](stream-analytics-test-query.md). Controleer eventuele fouten en probeer deze te corrigeren.   
    - U kunt [uw query ook lokaal testen](stream-analytics-live-data-local-testing.md) met Azure stream Analytics-hulpprogram Ma's voor Visual Studio of [Visual Studio code](visual-studio-code-local-run-live-input.md). 

2.  [Debug-query's stap voor stap lokaal met behulp van taak diagram](debug-locally-using-job-diagram-vs-code.md) in azure stream Analytics-hulpprogram Ma's voor Visual Studio code. In het taak diagram wordt weer gegeven hoe gegevens stromen van invoer bronnen (Event hub, IoT Hub, enzovoort) via meerdere query stappen worden uitgevoerd en tot slot naar uitvoer-Sinks. Elke query stap wordt toegewezen aan een tijdelijke resultatenset die in het script is gedefinieerd met behulp van de instructie WITH. U kunt de gegevens in elke tussenliggende resultatenset weer geven, evenals de metrieken, om de bron van het probleem te vinden.

    ![Resultaat van voor beeld van taak diagram](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  Als u [**time stamp by**](/stream-analytics-query/timestamp-by-azure-stream-analytics)gebruikt, controleert u of de gebeurtenissen tijds tempels hebben die groter zijn dan de [begin tijd](./stream-analytics-time-handling.md)van de taak.

4.  Vermijd veelvoorkomende Valk uilen, zoals:
    - Een [**where**](/stream-analytics-query/where-azure-stream-analytics) -component in de query heeft alle gebeurtenissen gefilterd, waardoor er geen uitvoer kan worden gegenereerd.
    - Een [**cast**](/stream-analytics-query/cast-azure-stream-analytics) -functie mislukt, waardoor de taak mislukt. Als u geen cast-fouten wilt voor komen, gebruikt u [**TRY_CAST**](/stream-analytics-query/try-cast-azure-stream-analytics) in plaats daarvan.
    - Wanneer u venster functies gebruikt, wacht u tot de volledige venster duur een uitvoer van de query ziet.
    - De tijds tempel voor gebeurtenissen gaat vooraf aan de begin tijd van de taak en gebeurtenissen worden verwijderd.
    - De voor waarden voor [**samen voegen**](/stream-analytics-query/join-azure-stream-analytics) komen niet overeen. Als er geen overeenkomsten zijn, is er geen uitvoer.

5.  Zorg ervoor dat het beleid voor het best Ellen van gebeurtenissen is geconfigureerd zoals verwacht. Ga naar **instellingen** en selecteer [**evenementen ordenen**](./stream-analytics-time-handling.md). Het beleid wordt *niet* toegepast wanneer u de knop **testen** gebruikt om de query te testen. Dit resultaat is een verschil tussen het testen van in-browser en het uitvoeren van de taak in de productie omgeving. 

6. Fouten opsporen met behulp van activiteit en resource logboeken:
    - Gebruik [activiteiten logboeken](../azure-resource-manager/management/view-activity-logs.md)en filter om fouten op te sporen en op te sporen.
    - Gebruik [taak bron logboeken](stream-analytics-job-diagnostic-logs.md) om fouten te identificeren en op te sporen.

## <a name="resource-utilization-is-high"></a>Het resource gebruik is hoog

Zorg ervoor dat u in Azure Stream Analytics kunt profiteren van parallel Lise ring. U kunt de [schaal aanpassen met query parallel Lise ring](stream-analytics-parallelization.md) van stream Analytics-taken door het configureren van invoer partities en het afstemmen van de definitie van de analyse query.

Als het resource gebruik consistent is dan 80%, wordt de vertraging van het water merk verhoogd en wordt het aantal achterstands gebeurtenissen Oplopend. Overweeg streaming-eenheden te verhogen. Hoog gebruik geeft aan dat de taak de Maxi maal toegewezen bronnen gebruikt.

## <a name="debug-queries-progressively"></a>Fouten progressief opsporen in query's

In realtime gegevens verwerking kan het handig zijn om te weten hoe de gegevens in het midden van de query eruitzien. U kunt dit zien met behulp van het taak diagram in Visual Studio. Als u Visual Studio niet hebt, kunt u extra stappen uitvoeren om tussenliggende gegevens uit te voeren.

Omdat invoer of stappen van een Azure Stream Analytics taak meerdere keren kunnen worden gelezen, kunt u extra SELECT INTO-instructies schrijven. Hierbij voert u tussenliggende gegevens uit naar opslag en kunt u de juistheid van de gegevens controleren, *net als bij* het opsporen van fouten in een programma.

De volgende voorbeeld query in een Azure Stream Analytics-taak heeft één stroom invoer, twee referentie gegevens invoer en een uitvoer naar Azure Table Storage. De query voegt gegevens van de Event Hub en twee referentie-blobs toe om de naam en categorie gegevens op te halen:

![Voor beeld Stream Analytics selectie in query](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Houd er rekening mee dat de taak wordt uitgevoerd, maar er worden geen gebeurtenissen geproduceerd in de uitvoer. Op de **bewakings** tegel, die hier wordt weer gegeven, kunt u zien dat de invoer gegevens produceert, maar u weet niet welke stap van de **samen voeging** heeft geleid tot alle gebeurtenissen die moeten worden verwijderd.

![De tegel Stream Analytics bewaking](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

In dit geval kunt u een aantal extra instructies voor SELECT INTO toevoegen aan de tussenliggende samenvoegings resultaten en de gegevens die van de invoer worden gelezen.

In dit voor beeld hebben we twee nieuwe tijdelijke uitvoer bewerkingen toegevoegd. Ze kunnen een wille keurige Sink zijn. Hier gebruiken we Azure Storage als voor beeld:

![Toevoegen van extra SELECT INTO-instructies aan Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

U kunt de query dan als volgt herschrijven:

![SELECTIE in Stream Analytics query opnieuw schrijven](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Start de taak nu opnieuw en laat deze enkele minuten uitvoeren. Zoek vervolgens temp1 en temp2 met Visual Studio Cloud Explorer om de volgende tabellen te maken:

**temp1-tabel** 
 ![ SELECTEREN in temp1-tabel Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2-tabel** 
 ![ SELECTEREN in temp2-tabel Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Zoals u ziet, zijn temp1 en temp2 beide gegevens en de kolom naam correct ingevuld in temp2. Maar omdat er nog steeds geen gegevens in de uitvoer zijn, is er een fout opgetreden:

![SELECTEREN in output1-tabel zonder gegevens Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Door de gegevens te bemonsteren, kunt u bijna zeker zijn dat het probleem met de tweede samen voeging is. U kunt de referentie gegevens van de BLOB downloaden en een kijkje nemen:

![IN ref-tabel selecteren Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Zoals u kunt zien, is de indeling van de GUID in deze referentie gegevens afwijkend van de indeling van de kolom [van] in temp2. Daarom zijn de gegevens niet zoals verwacht in output1.

U kunt de gegevens indeling herstellen, uploaden naar referentie-Blob en het opnieuw proberen:

![SELECTEREN in tijdelijke tabel Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Deze keer worden de gegevens in de uitvoer opgemaakt en ingevuld zoals verwacht.

![SELECTEREN in uiteindelijke tabel Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Help opvragen

Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)
