---
title: Anomalie detectie in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics en Azure Machine Learning samen kunt gebruiken om afwijkingen te detecteren.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: c57a3920dac3e18e248109fafdf61fdfa871c54d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023394"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Anomalie detectie in Azure Stream Analytics

Azure Stream Analytics biedt in zowel de Cloud als de Azure IoT Edge een machine learning ingebouwde oplossing voor afwijkings detectie die kan worden gebruikt voor het bewaken van de twee meest voorkomende afwijkingen: tijdelijk en permanent. Met de functies **AnomalyDetection_SpikeAndDip** en **AnomalyDetection_ChangePoint** kunt u anomalie detectie rechtstreeks in uw stream Analytics-taak uitvoeren.

De machine learning modellen nemen een uniforme steek proef van de tijd reeks uit. Als de tijd reeks niet uniform is, kunt u een aggregatie stap invoegen met een tumblingvenstertriggers-venster voordat u anomalie detectie aanroept.

De machine learning-bewerkingen bieden op dit moment geen ondersteuning voor seizoensgebonden trends of multi-variate-correlaties.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Anomalie detectie met behulp van machine learning in Azure Stream Analytics

In de volgende video ziet u hoe u in realtime een anomalie kunt detecteren met behulp van machine learning functies in Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Model gedrag

Over het algemeen verbetert de nauw keurigheid van het model met meer gegevens in de sliding window. De gegevens in de opgegeven sliding window worden beschouwd als onderdeel van het normale waarden bereik voor dat tijds bestek. Het model beschouwt alleen gebeurtenis geschiedenis over de sliding window om te controleren of de huidige gebeurtenis afwijkt. Naarmate de sliding window verplaatst, worden oude waarden uit de training van het model verwijderd.

De functies werken met een bepaald normaal, afhankelijk van wat ze tot nu toe hebben gezien. Uitbijters worden geïdentificeerd door te vergelijken met het tot stand gebrachte normaal, binnen het betrouwbaarheids niveau. De grootte van het venster moet worden gebaseerd op de minimale gebeurtenissen die nodig zijn om het model te trainen voor normaal gedrag, zodat het kan worden herkend wanneer een afwijkend probleem optreedt.

De reactie tijd van het model neemt toe met de grootte van de geschiedenis omdat deze moet worden vergeleken met een hoger aantal gebeurtenissen in het verleden. Het is raadzaam om alleen het vereiste aantal gebeurtenissen voor betere prestaties op te nemen.

Hiaten in de tijd reeks kunnen een resultaat zijn van het model dat geen gebeurtenissen op bepaalde tijdstippen ontvangt. Deze situatie wordt afgehandeld door Stream Analytics de logica van de toerekening te gebruiken. De geschiedenis grootte, evenals een tijds duur, voor dezelfde sliding window wordt gebruikt om de gemiddelde snelheid te berekenen waarmee gebeurtenissen naar verwachting arriveren.

Een afwijkings generator die [hier](https://aka.ms/asaanomalygenerator) beschikbaar is, kan worden gebruikt voor het invoeren van een IOT-hub met gegevens met verschillende afwijkende patronen. Een ASA-taak kan worden ingesteld met behulp van deze anomalie detectie functies om te lezen van deze IOT hub en afwijkingen te detecteren.

## <a name="spike-and-dip"></a>Piek en DIP

Tijdelijke afwijkingen in een time series-gebeurtenis stroom worden aangeduid als pieken en spannings dips. Pieken en spannings dips kunnen worden bewaakt met behulp van de operator Machine Learning op basis [AnomalyDetection_SpikeAndDip](/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Voor beeld van Prikker en DIP-afwijkingen](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Als in hetzelfde sliding window een tweede Prikker kleiner is dan de eerste, is de berekende score voor de kleinere Prikker waarschijnlijk niet significant genoeg ten opzichte van de score voor de eerste Prikker binnen het opgegeven betrouwbaarheids niveau. U kunt proberen het betrouwbaarheids niveau van het model te verlagen om dergelijke afwijkingen te detecteren. Als u echter te veel waarschuwingen ontvangt, kunt u een hoger betrouwbaarheids interval gebruiken.

In de volgende voorbeeld query wordt uitgegaan van een uniforme invoer snelheid van één gebeurtenis per seconde in een sliding window van twee minuten met een geschiedenis van 120 gebeurtenissen. De Final SELECT-instructie haalt de score en de afwijkings status op en voert deze uit met een betrouwbaarheids niveau van 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Punt wijzigen

Permanente afwijkingen in een time series-gebeurtenis stroom zijn wijzigingen in de distributie van waarden in de gebeurtenis stroom, zoals niveau wijzigingen en trends. In Stream Analytics worden dergelijke afwijkingen gedetecteerd met behulp van de operator Machine Learning op basis van [AnomalyDetection_ChangePoint](/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) .

Permanente wijzigingen die langer duren dan pieken en spannings dips en kunnen duiden op een onherstelbare gebeurtenis (sen). Permanente wijzigingen zijn doorgaans niet zichtbaar voor het blote oog, maar kunnen wel worden gedetecteerd met de operator **AnomalyDetection_ChangePoint** .

De volgende afbeelding is een voor beeld van een niveau wijziging:

![Voor beeld van anomalie wijziging van niveau](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

De volgende afbeelding is een voor beeld van een trend wijziging:

![Voor beeld van anomalie wijziging van trend](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

In de volgende voorbeeld query wordt uitgegaan van een uniforme invoer snelheid van één gebeurtenis per seconde in een sliding window van 20 minuten met een geschiedenis grootte van 1200 gebeurtenissen. De Final SELECT-instructie haalt de score en de afwijkings status op en voert deze uit met een betrouwbaarheids niveau van 80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Prestatie kenmerken

De prestaties van deze modellen zijn afhankelijk van de geschiedenis grootte, de duur van het venster, het laden van gebeurtenissen en of het gebruik van partitionering op functie niveau wordt gebruikt. In deze sectie worden deze configuraties beschreven en vindt u voor beelden voor het opvangen van de opname snelheid van 1K, 5K en 10K-gebeurtenissen per seconde.

* **Geschiedenis formaat** : deze modellen voeren lineair uit met de **geschiedenis grootte**. Hoe langer het geschiedenis formaat, hoe langer het model is om een nieuwe gebeurtenis te beoordelen. Dit komt doordat de modellen de nieuwe gebeurtenis vergelijken met elk van de gebeurtenissen in de geschiedenis buffer.
* **Duur** van het venster: de duur van het **venster** moet weer geven hoe lang het duurt om zoveel gebeurtenissen te ontvangen als aangegeven door de geschiedenis grootte. Zonder dat er veel gebeurtenissen in het venster zijn, worden in Azure Stream Analytics ontbrekende waarden toegerekend. Daarom is het CPU-verbruik een functie van de geschiedenis grootte.
* **Gebeurtenis belasting** : hoe groter de **gebeurtenis belasting**, hoe meer werk wordt uitgevoerd door de modellen, waardoor het CPU-verbruik wordt beïnvloed. De taak kan worden uitgeschaald door deze ongeëvenaard parallel te maken, ervan uitgaande dat het zinvol is voor bedrijfs logica om meer invoer partities te gebruiken.
* Partitioneren op functie **niveau**  -  **Partitioneren op functie niveau** wordt uitgevoerd met behulp van ```PARTITION BY``` de functie aanroep van de afwijkings detectie. Dit type partitionering voegt een overhead toe, aangezien de status moet worden bijgehouden voor meerdere modellen tegelijk. Partitioneren op functie niveau wordt gebruikt in scenario's zoals het partitioneren op apparaatniveau.

### <a name="relationship"></a>Relatie
De geschiedenis grootte, de duur van het venster en de totale gebeurtenis belasting zijn op de volgende manier gerelateerd:

windowDuration (in MS) = 1000 * historySize/(totale invoer gebeurtenissen per seconde/aantal invoer partities)

Wanneer u de functie partitioneert door deviceId, voegt u ' PARTITION BY deviceId ' toe aan de functie voor afwijkings detectie.

### <a name="observations"></a>Opmerkingen over
De volgende tabel bevat de door Voer waarnemingen voor één knoop punt (6 SU) voor de niet-gepartitioneerde Case:

| Geschiedenis grootte (gebeurtenissen) | Duur van het venster (MS) | Totaal aantal invoer gebeurtenissen per seconde |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2200 |
| 600 | 728 | 1.650 |
| 6.000 | 10.910 | 1100 |

De volgende tabel bevat de door Voer waarnemingen voor één knoop punt (6 SU) voor de gepartitioneerde Case:

| Geschiedenis grootte (gebeurtenissen) | Duur van het venster (MS) | Totaal aantal invoer gebeurtenissen per seconde | Aantal apparaten |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1.091 | 1100 | 10 |
| 600 | 10.910 | 1100 | 10 |
| 6.000 | 218.182 | <550 | 10 |
| 60 | 21.819 | 550 | 100 |
| 600 | 218.182 | 550 | 100 |
| 6.000 | 2.181.819 | <550 | 100 |

Voorbeeld code voor het uitvoeren van de niet-gepartitioneerde configuraties hierboven bevindt zich in de [streaming op schaal opslag plaats](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) van Azure-voor beelden. De code maakt een stream Analytics-taak zonder partitioneren op functie niveau, waarbij Event hub wordt gebruikt als invoer en uitvoer. De invoer belasting wordt gegenereerd met test-clients. Elke invoer gebeurtenis is een 1 KB JSON-document. Gebeurtenissen simuleren een IoT-apparaat dat JSON-gegevens verzendt (voor Maxi maal 1K apparaten). De geschiedenis grootte, de duur van het venster en de totale gebeurtenis belasting zijn variërend op twee invoer partities.

> [!Note]
> Voor een nauw keuriger schatting past u de voor beelden aan uw scenario aan.

### <a name="identifying-bottlenecks"></a>Knel punten identificeren
Gebruik het deel venster metrieken in uw Azure Stream Analytics-taak om knel punten in uw pijp lijn te identificeren. Bekijk **invoer-en uitvoer gebeurtenissen** voor door Voer en [' watermerk vertraging '](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) of achterstands **gebeurtenissen** om te zien of de taak de invoer snelheid bijhoudt. Voor metrische gegevens van Event hub zoekt u naar **vertraagde aanvragen** en past u de drempel waarden dienovereenkomstig aan. Bekijk voor Cosmos DB metrische gegevens het **maximum aantal gebruikte ru/s per partitie sleutel bereik** onder door Voer om ervoor te zorgen dat de partitie sleutel bereik op gelijke wijze worden verbruikt. Bewaak **logboek-io** en **CPU** voor Azure SQL DB.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)