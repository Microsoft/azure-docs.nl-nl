---
title: Real-time IoT-gegevens stromen verwerken met Azure Stream Analytics
description: IoT-sensortags en -gegevensstromen met Stream Analytics en realtime-gegevensverwerking
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: a438401ff93c20d8759e6128936c3626bd3de484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012679"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Real-time IoT-gegevens stromen verwerken met Azure Stream Analytics

In dit artikel leert u hoe u gegevensstroom verwerkings logica kunt maken voor het verzamelen van gegevens van Internet of Things IoT-apparaten. U gebruikt een Real-World Internet of Things (IoT) use-case om te laten zien hoe u snel en economisch uw oplossing kunt bouwen.

## <a name="prerequisites"></a>Vereisten

* Maak een gratis [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/).
* Down load voorbeeld query en gegevens bestanden van [github](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scenario

Contoso, een bedrijf op het gebied van industriële automatisering, heeft hun fabricageproces volledig geautomatiseerd. De machines in dit bedrijf hebben sensoren die in realtime gegevensstromen kunnen genereren. In dit scenario wil een werkvloerbeheerder realtime-inzichten op basis van de sensorgegevens om naar patronen te zoeken en hierop te reageren. U kunt Stream Analytics query taal (SAQL) gebruiken voor de sensor gegevens om interessante patronen te vinden in de binnenkomende gegevens stroom.

In dit voor beeld worden de gegevens gegenereerd op basis van een Texas Instruments sensor tag-apparaat. De nettolading van de gegevens heeft de JSON-indeling en ziet er ongeveer als volgt uit:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

In een werkelijk scenario hebt u honderden van dit soort sensoren die gebeurtenissen als een stroom kunnen genereren. In het ideale geval is er een gateway-apparaat waarop bepaalde code wordt uitgevoerd die deze gebeurtenissen pusht naar [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) of [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Uw Stream Analytics-taak zou deze gebeurtenissen opnemen van Event Hubs en realtime analysequery’s uitvoeren tegen de stromen. Dan kunt u de resultaten verzenden naar één van de [ondersteunde outputs](stream-analytics-define-outputs.md).

Voor het gebruiksgemak biedt deze introductiehandleiding een bestand met voorbeeldgegevens dat is verkregen uit echte Sensor Tag-apparaten. U kunt query's uitvoeren op de voorbeeldgegevens en resultaten weergeven. In volgende zelfstudies leert u hoe u een taak verbindt met in- en uitvoer, en deze implementeert in de Azure-service.

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Selecteer in de [Azure Portal](https://portal.azure.com) **+ een resource maken** in het navigatie menu aan de linkerkant. Selecteer vervolgens **Stream Analytics taak** uit **Analytics**.
   
    ![Een nieuwe Stream Analytics-taak maken](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Voer een unieke taaknaam in en controleer of het abonnement het juiste abonnement is voor de taak. Maak een nieuwe resource groep of selecteer een bestaande in uw abonnement.

1. Selecteer een locatie voor uw taak. Gebruik dezelfde locatie voor de resource groep en alle resources om de verwerkings snelheid te verhogen en de kosten te verlagen. Nadat u de configuraties hebt gemaakt, selecteert u **maken**.
   
    ![Details van het maken van een nieuwe Stream Analytics-taak](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Een Azure Stream Analytics-query maken
De volgende stap na het maken van de taak is het schrijven van een query. U kunt query's testen op voorbeeld gegevens zonder een invoer-of uitvoer verbinding te maken met uw taak.

Down load de [HelloWorldASA-InputStream.jsop](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) van github. Ga vervolgens naar uw Azure Stream Analytics-taak in de Azure Portal.

Selecteer **query** onder **taak topologie** in het menu links. Selecteer vervolgens **voorbeeld invoer uploaden**. Upload het `HelloWorldASA-InputStream.json` bestand en selecteer **OK**.

![Tegel Stream Analytics dashboard query](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

U ziet dat een voor beeld van de gegevens automatisch wordt ingevuld in de **voorbeeld tabel voor invoer** .

![Preview-Voorbeeld invoer gegevens](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Query: de onbewerkte gegevens archiveren

De meest eenvoudige vorm van een query is het doorgeven van gegevens waarmee alle invoergegevens worden gearchiveerd op de aangewezen uitvoer. Deze query is de standaard query die wordt ingevuld in een nieuwe Azure Stream Analytics taak.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Selecteer **test query** en Bekijk de resultaten in de tabel **test resultaten** .

![Test resultaten voor Stream Analytics query](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Query: gegevens filteren op basis van een voorwaarde

We proberen de resultaten te filteren op basis van een voor waarde. We willen alleen resultaten weer geven voor de gebeurtenissen die afkomstig zijn van sensora.

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Plak de query in de editor en selecteer **test query** om de resultaten te bekijken.

![Een gegevensstroom filteren](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Query: waarschuwingen om zakelijke werkstromen te activeren

We gaan onze query gedetailleerder maken. Voor elk type sensor willen we de gemiddelde temperatuur per tijdvenster van 30 seconden controleren en alleen resultaten weergeven als de gemiddelde temperatuur hoger is dan 100 graden.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30-secondenfilterquery](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

U ziet resultaten die slechts 245 rijen en namen van Sens oren bevatten waarbij de gemiddelde Tempe ratuur aangeven groter is dan 100. In deze query wordt de stroom gebeurtenissen gegroepeerd op **dspl**, de sensornaam, en voor een **tumblingvenster** van 30 seconden. Tijdelijke query's moeten aangeven hoe de tijd moet worden uitgevoerd. Als u de **time stamp by** -component gebruikt, hebt u de kolom **OUTPUTTIME** opgegeven om tijden te koppelen aan alle tijdelijke berekeningen. Meer informatie over [time management](/stream-analytics-query/time-management-azure-stream-analytics) en [windowing functions](/stream-analytics-query/windowing-azure-stream-analytics)vindt u in.

### <a name="query-detect-absence-of-events"></a>Query: detecteren van afwezigheid van gebeurtenissen

Hoe kunnen we een query schrijven om een gebrek aan invoergebeurtenissen te vinden? U vindt de laatste keer dat een sensor gegevens heeft verzonden en vervolgens de volgende vijf seconden geen gebeurtenissen heeft verzonden.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Detecteren van afwezigheid van gebeurtenissen](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

We gebruiken hier een **LEFT OUTER**-join voor dezelfde gegevensstroom (self-join). Voor een **INNER**-join wordt alleen een resultaat geretourneerd wanneer een overeenkomst is gevonden.  Als in geval van een **LEFT OUTER**-join geen overeenkomst wordt gevonden met een gebeurtenis vanaf de linkerkant van de join, wordt een rij met NULL voor alle kolommen in de rechterkant geretourneerd. Deze techniek is heel nuttig om de afwezigheid van gebeurtenissen op te sporen. Zie voor meer informatie [samen voegen](/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Conclusie

Het doel van dit artikel is te demonstreren hoe u verschillende Stream Analytics query taal query's schrijft en resultaten in de browser ziet. Dit is echter alleen om aan de slag te gaan. Stream Analytics ondersteunt tal van in- en uitvoer en kan zelfs gebruikmaken van functies in Azure Machine Learning, waardoor het een krachtig hulpprogramma wordt om gegevensstromen te analyseren. Lees voor meer informatie over het schrijven van query's het artikel over [algemene querypatronen](stream-analytics-stream-analytics-query-patterns.md).