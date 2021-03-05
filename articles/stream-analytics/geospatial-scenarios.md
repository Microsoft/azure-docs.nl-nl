---
title: Geoomheining en georuimtelijke aggregatie met Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics gebruikt voor geoomheining en georuimtelijke aggregatie.
ms.service: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: fc9442b01685deb710c49adf6be84af740a11805
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174985"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Geoomheining en georuimtelijke aggregatie scenario's met Azure Stream Analytics

Met ingebouwde georuimtelijke functies kunt u Azure Stream Analytics gebruiken om toepassingen te ontwikkelen voor scenario's zoals vloot beheer, het delen van wijzigingen, verbonden auto's en het bijhouden van activa.

## <a name="geofencing"></a>Geofencing

Azure Stream Analytics ondersteunt real-time latentie berekeningen in realtime in de Cloud en op de IoT Edge runtime.

### <a name="geofencing-scenario"></a>Scenario met geoomheining

Een productie bedrijf moet activa op hun gebouwen volgen. Ze hebben elk apparaat voorzien van een GPS en willen meldingen ontvangen als een apparaat een bepaald gebied verlaat.

Referentie gegevens die in dit voor beeld worden gebruikt, hebben de geofence-informatie voor de gebouwen en de apparaten die in elk van de gebouwen zijn toegestaan. Houd er rekening mee dat referentie gegevens statisch of langzaam kunnen worden gewijzigd. Statische referentie gegevens worden gebruikt voor dit scenario. Een gegevens stroom verzendt voortdurend de apparaat-ID en de huidige positie.

### <a name="define-geofences-in-reference-data"></a>Geofences definiëren in referentie gegevens

Een geofence kan worden gedefinieerd met behulp van een geojson-object. Voor taken met compatibiliteits versie 1,2 en hoger kunnen geofences ook worden gedefinieerd met behulp van bekende tekst (WKT) als `NVARCHAR(MAX)` . WKT is een Open Geospatial Consortium-standaard (OGC) die wordt gebruikt om ruimtelijke gegevens in een tekst indeling weer te geven.

De ingebouwde georuimtelijke functies kunnen gedefinieerde geofences gebruiken om erachter te komen of een element zich in of uit een specifieke geofence-veelhoek bevindt.

De volgende tabel is een voor beeld van geofence-referentie gegevens die kunnen worden opgeslagen in Azure Blob-opslag of een Azure SQL-tabel. Elke site wordt vertegenwoordigd door een georuimtelijke veelhoek en elk apparaat is gekoppeld aan een toegestane site-ID.

|SiteID|Sitenaam|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond gebouw 41"|"VEELHOEK ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|B|
|2|"Redmond gebouw 40"|"VEELHOEK ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|Één|
|3|"Redmond built 22"|"VEELHOEK ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|G|

### <a name="generate-alerts-with-geofence"></a>Waarschuwingen genereren met geofence

Apparaten kunnen hun ID en locatie elke minuut verzenden via een stroom met de naam `DeviceStreamInput` . De volgende tabel is een stroom van invoer.

|DeviceID|Geopositie|
|--------|-----------|
|Één|"PUNT (-122.13292341559497 47.636318374032726)"|
|B|"PUNT (-122.13338475554553 47.63743531308874)"|
|G|"PUNT (-122.13354001095752 47.63627622505007)"|

U kunt een query schrijven die de stream van het apparaat koppelt aan de geofence-referentie gegevens en een waarschuwing genereert telkens wanneer een apparaat buiten het gebouw is dat is toegestaan.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

De volgende afbeelding geeft de geofences. U kunt zien waar de apparaten zich bevinden in overeenstemming met de stroom gegevens invoer.

![Geofences bouwen](./media/geospatial-scenarios/building-geofences.png)

Het apparaat ' C ' bevindt zich in de gebouw-ID 2, maar dit is niet toegestaan volgens de referentie gegevens. Dit apparaat moet zich bevinden in gebouw-ID 3. Als u deze taak uitvoert, wordt er een waarschuwing gegenereerd voor deze specifieke schending.

### <a name="site-with-multiple-allowed-devices"></a>Site met meerdere toegestane apparaten

Als een site meerdere apparaten toestaat, kan een matrix met apparaat-Id's worden gedefinieerd in `AllowedDeviceID` en een User-Defined-functie kan worden gebruikt in de `WHERE` component om te controleren of de apparaat-id van de stream overeenkomt met een apparaat-id in die lijst. Raadpleeg voor meer informatie de [Java script UDF](stream-analytics-javascript-user-defined-functions.md) -zelf studie voor Cloud taken en de C#-zelf studie over de [UDF](stream-analytics-edge-csharp-udf.md) voor Edge-taken.

## <a name="geospatial-aggregation"></a>Georuimtelijke aggregatie

Azure Stream Analytics ondersteunt in realtime georuimtelijke aggregatie in de Cloud en op de IoT Edge runtime.

### <a name="geospatial-aggregation-scenario"></a>Scenario voor georuimtelijke aggregatie

Een cab-bedrijf wil een realtime toepassing bouwen om hun cab-Stuur Programma's te begeleiden die op zoek zijn naar de gebieden van de steden die een hogere vraag ondervinden.

Het bedrijf slaat logische regio's van de stad op als referentie gegevens. Elke regio wordt gedefinieerd door een RegionID, regionaam en geofence.

### <a name="define-the-geofences"></a>De geofences definiëren

De volgende tabel is een voor beeld van geofence-referentie gegevens die kunnen worden opgeslagen in Azure Blob-opslag of een Azure SQL-tabel. Elke regio wordt vertegenwoordigd door een georuimtelijke veelhoek, die wordt gebruikt om te correleren met de aanvragen die afkomstig zijn van streaming-gegevens.

Deze veelhoeken zijn alleen ter referentie en vertegenwoordigen geen werkelijke plaats logische of fysieke schei dingen.

|RegionID|RegionName|Geofence|
|--------|----------|--------|
|1|Onmogelijk|"VEELHOEK ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264))"|
|2|"Chinatown"|"VEELHOEK ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"VEELHOEK ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Verzamel gegevens over een tijd venster

De volgende tabel bevat streaming-gegevens van ' onderdrukkingen '.

|UserID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|Één|"PUNT (-74.00726861389182 40.71610611981975)"|"PUNT (-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|B|"PUNT (-74.00249841021645 40.723827238895666)"|"PUNT (-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|G|"PUNT (-73.99680120565864 40.716439898624024)"|"PUNT (-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|!|"PUNT (-74.00741090068288 40.71615626755086)"|"PUNT (-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Met de volgende query wordt de stroom van het apparaat toegevoegd aan de geofence-referentie gegevens en wordt het aantal aanvragen per regio in een tijd venster van 15 minuten elke minuut berekend.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Met deze query wordt een aantal aanvragen per minuut voor de laatste 15 minuten uitgevoerd op elke regio binnen de stad. Deze informatie kan eenvoudig worden weer gegeven door Power BI dash board of kunnen worden verzonden naar alle Stuur Programma's als SMS-tekst berichten via integratie met services zoals Azure functions.

In de onderstaande afbeelding ziet u de uitvoer van de query naar Power BI dash board. 

![Resultaat uitvoer op Power BI dash board](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Stream Analytics georuimtelijke functies](stream-analytics-geospatial-functions.md)
* [Georuimtelijke functies (Azure Stream Analytics)](/stream-analytics-query/geospatial-functions)