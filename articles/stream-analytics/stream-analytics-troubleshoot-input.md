---
title: Probleemoplossings invoer voor Azure Stream Analytics
description: In dit artikel worden technieken beschreven voor het oplossen van problemen met de invoer verbindingen in Azure Stream Analytics taken.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 2d7171c9ec1e60447fb3342caa72098fb2eb9337
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019904"
---
# <a name="troubleshoot-input-connections"></a>Problemen met invoerverbindingen oplossen

In dit artikel worden veelvoorkomende problemen beschreven met Azure Stream Analytics invoer verbindingen, het oplossen van invoer problemen en het oplossen van de problemen. Voor een groot aantal probleemoplossings stappen moeten resource logboeken zijn ingeschakeld voor uw Stream Analytics-taak. Als u geen resource Logboeken hebt ingeschakeld, raadpleegt u [problemen oplossen Azure stream Analytics met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Invoer gebeurtenissen niet ontvangen door taak 

1.  De connectiviteit voor invoer en uitvoer testen. Controleer de verbinding met de invoer en uitvoer met behulp van de knop **verbinding testen** voor elke invoer en uitvoer.

2.  Controleer de ingevoerde gegevens.

    1. Gebruik de knop [**voorbeeld gegevens**](./stream-analytics-test-query.md) voor elke invoer. Down load de voorbeeld gegevens van de invoer.
        
    1. Inspecteer de voorbeeld gegevens om inzicht te krijgen in het schema en de [gegevens typen](/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Controleer de [metrische gegevens van de Event hub](../event-hubs/event-hubs-metrics-azure-monitor.md) om er zeker van te zijn dat er gebeurtenissen worden verzonden. Metrische bericht waarden moeten groter zijn dan nul als Event Hubs berichten ontvangt.

3.  Zorg ervoor dat u een tijds bereik hebt geselecteerd in de invoer voorbeeld. Kies **tijds bereik selecteren** en voer een voorbeeld duur in voordat u uw query test.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Ongeldige invoergebeurtenissen veroorzaken deserialisatiefouten 

Problemen met deserialisatie worden veroorzaakt wanneer de invoer stroom van uw Stream Analytics-taak ongeldige berichten bevat. Een verkeerd ingedeeld bericht kan bijvoorbeeld worden veroorzaakt door een ontbrekend haakje of een accolade in een JSON-object of een onjuiste time stamp notatie in het veld tijd. 
 
Wanneer een Stream Analytics taak een onjuist gevormd bericht van een invoer ontvangt, wordt het bericht niet meer verzonden en ontvangt u een waarschuwing. Er wordt een waarschuwings symbool weer gegeven op de tegel **invoer** van uw stream Analytics taak. Het volgende waarschuwings symbool bestaat al zolang de taak de status wordt uitgevoerd heeft:

![Tegel Azure Stream Analytics ingangen](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Resource logboeken inschakelen om de details van de fout en het bericht (Payload) te bekijken die de fout hebben veroorzaakt. Er zijn verschillende redenen waarom het deserialiseren van fouten kan optreden. Zie [invoer gegevens fouten](data-errors.md#input-data-errors)voor meer informatie over specifieke fouten in de deserialisatie. Als resource logboeken niet zijn ingeschakeld, is er een korte melding beschikbaar in de Azure Portal.

![Waarschuwings melding over invoer gegevens](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Als de nettolading van het bericht groter is dan 32 KB of een binaire indeling heeft, voert u de CheckMalformedEvents.cs-code uit die beschikbaar is in de GitHub-voor [beelden-opslag plaats](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Deze code leest de partitie-ID, offset en drukt de gegevens af die zich in die offset bevinden. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>De taak overschrijdt de maximum aantal ontvangers van Event hub

Een best practice voor het gebruik van Event Hubs is het gebruik van meerdere consumenten groepen voor de schaal baarheid van taken. Het aantal lezers in de Stream Analytics-taak voor een specifieke invoer is van invloed op het aantal lezers in één consumer groep. Het exacte aantal ontvangers is gebaseerd op interne implementatie Details voor de scale-out-topologie logica en wordt niet extern weer gegeven. Het aantal lezers dat kan veranderen wanneer een taak wordt gestart of tijdens taak upgrades.

De volgende fout berichten worden weer gegeven wanneer het aantal ontvangers het maximum overschrijdt. Het fout bericht bevat een lijst met bestaande verbindingen die zijn gemaakt met Event hub onder een Consumer groep. De code `AzureStreamAnalytics` geeft aan dat de verbindingen van de Azure streaming-service zijn.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> Wanneer het aantal lezers wijzigt tijdens een taak upgrade, worden tijdelijke waarschuwingen naar audit logboeken geschreven. Stream Analytics taken worden automatisch hersteld van deze tijdelijke problemen.

### <a name="add-a-consumer-group-in-event-hubs"></a>Een Consumer groep toevoegen in Event Hubs

Voer de volgende stappen uit om een nieuwe consumenten groep toe te voegen in uw Event Hubs-exemplaar:

1. Meld u aan bij Azure Portal.

2. Zoek uw event hub.

3. Selecteer **Event hubs** onder de kop **entities** .

4. Selecteer de Event hub op naam.

5. Selecteer op de pagina **Event hubs-exemplaar** , onder de kop **entiteiten** , **consumenten groepen**. Er wordt een gebruikers groep met de naam **$default** weer gegeven.

6. Selecteer **+ consumenten groep** om een nieuwe consumenten groep toe te voegen. 

   ![Een Consumer groep toevoegen in Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Wanneer u de invoer in de Stream Analytics taak hebt gemaakt om te verwijzen naar de Event hub, hebt u de Consumer groep opgegeven. **$Default** wordt gebruikt wanneer er geen is opgegeven. Nadat u een nieuwe consumenten groep hebt gemaakt, bewerkt u de Event hub-invoer in de Stream Analytics taak en geeft u de naam van de nieuwe consumenten groep op.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>De lezers per partitie overschrijden de Event Hubs limiet

Als uw streaming-query syntaxis meerdere keren verwijst naar dezelfde invoer Event hub-bron, kan de taak engine meerdere lezers per query van diezelfde consumenten groep gebruiken. Wanneer er te veel verwijzingen naar dezelfde consumenten groep zijn, kan de taak de limiet van vijf overschrijden en een fout gegenereerd. In deze omstandigheden kunt u verder delen door meerdere invoer in meerdere consumenten groepen te gebruiken met behulp van de oplossing die in de volgende sectie wordt beschreven. 

Scenario's waarin het aantal lezers per partitie de Event Hubs limiet van vijf tekens overschrijden, zijn onder andere:

* Meerdere SELECT-instructies: als u meerdere SELECT-instructies gebruikt die naar **dezelfde** Event hub invoer verwijzen, zorgt elke SELECT-instructie ervoor dat een nieuwe ontvanger wordt gemaakt.

* UNION: wanneer u een UNION gebruikt, is het mogelijk dat er meerdere invoer waarden zijn die verwijzen naar **dezelfde** Event hub en consumenten groep.

* SELF-lid: wanneer u een SELF-KOPPELINGs bewerking gebruikt, is het mogelijk om **hetzelfde** Event hub meerdere keren te gebruiken.

De volgende aanbevolen procedures kunnen helpen bij het beperken van scenario's waarin het aantal lezers per partitie de Event Hubs limiet van vijf overschrijdt.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Uw query splitsen in meerdere stappen met behulp van een WITH-component

De WITH-component geeft een tijdelijke benoemde resultatenset waarnaar kan worden verwezen door een FROM-component in de query. U definieert de WITH-component in het uitvoerings bereik van één SELECT-instructie.

In plaats van deze query bijvoorbeeld:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Gebruikt u deze query:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Zorg ervoor dat invoer gegevens aan verschillende consumenten groepen zijn gebonden

Voor query's waarin drie of meer invoer waarden zijn gekoppeld aan dezelfde Event Hubs consumenten groep, maakt u afzonderlijke consumenten groepen. Hiervoor moet u aanvullende Stream Analytics invoer maken.

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Afzonderlijke invoer maken met verschillende consumenten groepen

U kunt afzonderlijke invoer maken met verschillende consumenten groepen voor dezelfde event hub. De volgende SAMENVOEG query is een voor beeld waarbij *InputOne* en *InputTwo* naar dezelfde event hub-bron verwijzen. Elke query kan afzonderlijke invoer hebben met verschillende consumenten groepen. De SAMENVOEG query is maar een voor beeld.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>De lezers per partitie overschrijden de IoT Hub limiet

Stream Analytics-taken gebruiken het ingebouwde [Event hub-compatibele eind punt](../iot-hub/iot-hub-devguide-messages-read-builtin.md) van IOT hub om verbinding te maken en gebeurtenissen van IOT hub te lezen. Als uw Lees bewerking per partitie de limieten van IoT Hub overschrijdt, kunt u de [oplossingen voor Event hub](#readers-per-partition-exceeds-event-hubs-limit) gebruiken om deze op te lossen. U kunt een Consumer groep maken voor het ingebouwde eind punt via IoT Hub Portal-eindpunt sessie of via de [IOT hub SDK](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup).

## <a name="get-help"></a>Help opvragen

Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)