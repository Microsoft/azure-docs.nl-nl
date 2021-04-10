---
title: Bedrijfs IoT-gegevens van Azure verzamelen in ADX
description: In deze zelf studie leert u hoe u IIoT-gegevens ophaalt in ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787698"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Zelf studie: Azure Industrial IoT-gegevens verzamelen in ADX

Het Azure Industrial IoT-platform (IIoT) combineert Edge-modules en Cloud micro Services met een aantal Azure PaaS-Services om mogelijkheden te bieden voor de detectie van industriële activa en om gegevens van deze activa te verzamelen met OPC UA. [Azure Data Explorer (ADX)](https://docs.microsoft.com/azure/data-explorer) is een natuurlijke bestemming voor IIoT-gegevens met functies voor gegevens analyse waarmee u flexibele query's kunt uitvoeren op de opgenomen gegevens van de OPC UA-servers die zijn verbonden met de IOT hub via de OPC-Uitgever. Hoewel een ADX-cluster gegevens rechtstreeks uit het IoT Hub kan opnemen, verwerkt het IIoT-platform verdere verwerking van de gegevens, zodat deze beter is te gebruiken voordat u deze op de Event hub plaatst, wanneer een volledige implementatie van de micro Services wordt gebruikt (Zie de architectuur van het IIoT-platform).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een tabel maken in ADX
> * De Event hub verbinden met het ADX-cluster
> * De gegevens in ADX analyseren

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>De gegevens beschikbaar maken in het ADX-cluster om effectief een query uit te voeren 

Als we de bericht indeling van de Event hub bekijken (zoals gedefinieerd door de klasse micro soft. Azure. IIoT. OpcUa. Subscriber. Models. MonitoredItemMessageModel), kunnen we een hint zien voor de structuur die we nodig hebben voor het ADX-tabel schema.

![Structuur](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Hieronder vindt u de stappen die we nodig hebben om de gegevens beschikbaar te maken in het ADX-cluster en om de gegevens effectief op te vragen.  
1. Maak een ADX-cluster. Als u nog geen ADX-cluster met het IIoT-platform hebt ingericht of als u een ander cluster wilt gebruiken, volgt u de stappen in [dit onderwerp](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster). 
2. Schakel streaming-opname in het ADX-cluster in, zoals [hier](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster)wordt uitgelegd. 
3. Maak een ADX-data base door de [volgende stappen te volgen.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database)

Voor de volgende stap gebruiken we de ADX- [web-interface](https://docs.microsoft.com/azure/data-explorer/web-query-data) om de benodigde query's uit te voeren. Zorg ervoor dat u uw cluster toevoegt aan de web-interface, zoals wordt uitgelegd in de koppeling.  
 
4. Maak een tabel in ADX om de opgenomen gegevens in op te slaan.  Hoewel de MonitoredItemMessageModel-klasse kan worden gebruikt om het schema van de ADX-tabel te definiëren, is het raadzaam om de gegevens eerst op te nemen in een faserings tabel met één kolom van het type [dynamisch](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic). Dit biedt ons meer flexibiliteit bij het verwerken van de gegevens en verwerking in andere tabellen (mogelijk gecombineerd met andere gegevens bronnen) die de behoeften voor meerdere use cases verzorgen. Met de volgende ADX-query wordt de faserings tabel ' iiot_stage ' gemaakt met één kolom ' payload ',

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Daarnaast moet er een toewijzing van een JSON-opname worden toegevoegd om het cluster het volledige JSON-bericht van de Event hub in de faserings tabel te laten plaatsen

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. Onze tabel is nu klaar om gegevens van de Event hub te ontvangen. 
6. Gebruik de instructies [hier](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) om de Event hub te koppelen aan het ADX-cluster en de gegevens op te nemen in de faserings tabel. We hoeven alleen de verbinding te maken omdat er al een event hub is ingericht door het IIoT-platform.  
7. Zodra de verbinding is geverifieerd, gaan de gegevens naar onze tabel en na een korte vertraging in de weg om de gegevens in onze tabel te controleren. Gebruik de volgende query in de ADX-webinterface om een voor beeld van een gegevens van 10 rijen te bekijken. Hier kunt u zien hoe de gegevens in de payload eruitzien als de MonitoredItemMessageModel-klasse die u eerder hebt beschreven.

![Query](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. We gaan nu een analyse uitvoeren op deze gegevens door de dynamische gegevens in de kolom Payload rechtstreeks te parseren. In dit voor beeld berekenen we het gemiddelde van de telemetrie die wordt geïdentificeerd door ' DisplayName ': ' PositiveTrendData ', in een periode van tien minuten, op alle records die zijn opgenomen sinds een specifiek tijds punt (gedefinieerd door de variabele min_t) min_t = datetime (2020-10-23); iiot_stage | waarbij todatetime (payload. Tijds tempel) > min_t | waarbij toString (payload. DisplayName) = = ' PositiveTrendData ' | samenvat event_avg = AVG (ToDouble (payload. Waarde)) op bak (todatetime (payload. Tijds tempel), 10 m)
 
Omdat de kolom Payload een dynamisch gegevens type bevat, moeten we gegevens conversie uitvoeren op het moment van de query, zodat de berekeningen worden uitgevoerd op de juiste gegevens typen.

![Tijds tempel van nettolading](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Zoals eerder vermeld, is het opnemen van de OPC UA-gegevens in een faserings tabel met één ' dynamische ' kolom, waardoor we flexibiliteit bieden. Het uitvoeren van gegevens type conversies op het moment van de query kan echter leiden tot vertragingen bij het uitvoeren van de query's, met name als het gegevens volume groot is en er veel gelijktijdige query's zijn. In deze fase kunnen we een andere tabel maken met de gegevens typen die al zijn vastgesteld, zodat we de gegevens type conversies van het query-tijd voor komen.
 
9. Maak een nieuwe tabel voor de geparseerde gegevens die uit een beperkte selectie bestaan uit de inhoud van de dynamische ' payload ' in de faserings tabel. Houd er rekening mee dat we een kolom waarde hebben gemaakt voor elk van de verwachte gegevens typen die in onze telemetrie worden verwacht.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Maak een functie (op database niveau) om de vereiste gegevens van de faserings tabel te projecteren. Hier selecteren we de ' time stamp ', ' PublisherId ', ' DisplayName ', ' data type ' en ' NodeId ' items uit de kolom Payload en project deze als ' Tag_Timestamp ', ' Tag_PublisherId ', ' tag ', ' Tag_Datatype ', Tag_NodeId '. Het item ' value ' wordt geprojecteerd als drie verschillende onderdelen op basis van het gegevens type.

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Meer informatie over het toewijzen van gegevens typen in ADX vindt u [hier](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)en voor functies in ADX kunt u [hier](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions)beginnen.
 
11. De functie van de vorige stap Toep assen op de geparseerde tabel met behulp van een update beleid. Met het update [beleid](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) krijgt ADX automatisch gegevens aan een doel tabel toe wanneer er nieuwe gegevens in de bron tabel worden ingevoegd, op basis van een transformatie query die wordt uitgevoerd op de gegevens die in de bron tabel worden ingevoegd. We kunnen de volgende query gebruiken om de geparseerde tabel als doel en de tabel fase toe te wijzen als de bron voor het update beleid dat is gedefinieerd door de functie die we in de vorige stap hebben gemaakt.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Zodra de bovenstaande query wordt uitgevoerd, gaan gegevens in de stroom en vullen we de doel tabel iiot_parsed in. We kunnen de gegevens in iiot_parsed als volgt bekijken.

![Geparseerde tabel](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Laten we nu kijken hoe we de analyses in een vorige stap kunnen herhalen. Bereken het gemiddelde van de telemetrie die is geïdentificeerd door ' DisplayName ': ' PositiveTrendData ', in een periode van tien minuten, op alle records die zijn opgenomen sinds een specifiek tijds punt (gedefinieerd door de variabele min_t). Omdat we nu de waarden van de PositveTrendData-tag hebben opgeslagen in een kolom van het gegevens type Double, verwachten we een verbetering van de query prestaties.

![Analyses herhalen](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Laat de prestaties van de query ten slotte in beide gevallen vergelijken. We kunnen de tijd vinden die nodig is om de query uit te voeren met de ' stats ' in de ADX-gebruikers interface (die zich boven de query resultaten kan bevinden).  

![Query prestaties 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Query prestaties 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

We kunnen zien dat de query die gebruikmaakt van de geparseerde tabel ongeveer twee keer zo snel is als de faserings tabel. In dit voor beeld hebben we een kleine gegevensset en er zijn geen gelijktijdige query's actief, waardoor het effect op de uitvoerings tijd van de query niet geweldig is, maar een realistische werk belasting een grote invloed zou kunnen hebben op de prestaties. Daarom is het belang rijk om de verschillende gegevens typen te scheiden in verschillende kolommen.

> [!NOTE] 
> Het update beleid werkt alleen voor de gegevens die worden opgenomen in de faserings tabel nadat het beleid is ingesteld en niet van toepassing is op bestaande gegevens. Hierbij moet rekening worden gehouden wanneer het update beleid bijvoorbeeld moet worden gewijzigd. Volledige details vindt u in de ADX-documentatie.

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u de standaard waarden van de configuratie wijzigt, kunt u 

> [!div class="nextstepaction"]
> [Industriële IoT-onderdelen configureren](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Visualiseren en analyseren van de gegevens met behulp van Time Series Insights](tutorial-visualize-data-time-series-insights.md)