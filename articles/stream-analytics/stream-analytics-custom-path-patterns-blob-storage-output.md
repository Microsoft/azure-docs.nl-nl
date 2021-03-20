---
title: Aangepaste BLOB-uitvoer partitioneren Azure Stream Analytics
description: In dit artikel worden de aangepaste paden voor het pad naar de tijd en de aangepaste velden of kenmerken van de Blob Storage-uitvoer van Azure Stream Analytics-taken beschreven.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: cb9d8edd24dcc8809f2b207a4db80653b0e140e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98014033"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Aangepaste BLOB-uitvoer partitioneren Azure Stream Analytics

Azure Stream Analytics ondersteunt aangepaste BLOB-uitvoer partities met aangepaste velden of kenmerken en aangepaste DateTime-paden. 

## <a name="custom-field-or-attributes"></a>Aangepaste velden of kenmerken

Aangepaste veld-of invoer kenmerken verbeteren stroomafwaartse werk stromen voor gegevens verwerking en rapporten door meer controle over de uitvoer toe te staan.

### <a name="partition-key-options"></a>Opties voor partitie sleutels

De partitie sleutel of kolom naam die wordt gebruikt voor het partitioneren van invoer gegevens kan elk teken bevatten dat wordt geaccepteerd voor [BLOB-namen](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata). Het is niet mogelijk om geneste velden als partitie sleutel te gebruiken, tenzij u deze gebruikt in combi natie met aliassen, maar u kunt bepaalde tekens gebruiken om een hiërarchie van bestanden te maken. U kunt bijvoorbeeld de volgende query gebruiken om een kolom te maken die gegevens uit twee andere kolommen combineert om een unieke partitie sleutel te maken.

```sql
SELECT name, id, CONCAT(name, "/", id) AS nameid
```

De partitie sleutel moet NVARCHAR (MAX), BIGINT, FLOAT of BIT (1,2 compatibiliteits niveau of hoger) zijn. DateTime-, matrix-en record typen worden niet ondersteund, maar kunnen worden gebruikt als partitie sleutels als ze worden geconverteerd naar teken reeksen. Zie [Azure stream Analytics gegevens typen](/stream-analytics-query/data-types-azure-stream-analytics)voor meer informatie.

### <a name="example"></a>Voorbeeld

Stel dat een taak invoer gegevens ontvangt van Live gebruikers sessies die zijn verbonden met een externe video game service, waarbij opgenomen gegevens een kolom bevatten **client_id** om de sessies te identificeren. Als u de gegevens wilt partitioneren op **client_id**, stelt u het veld patroon van BLOB-pad in om een partitie token **{client_id}** op te nemen in BLOB-uitvoer eigenschappen bij het maken van een taak. Als gegevens met verschillende **client_id** waarden door de stream Analytics taak worden uitgevoerd, worden de uitvoer gegevens opgeslagen in afzonderlijke mappen op basis van één **client_id** waarde per map.

![Pad patroon met client-id](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

En als de taak invoer sensor gegevens van miljoenen Sens oren bevat, waarbij elke sensor een **sensor_id** heeft, zou het patroon van het pad **{sensor_id}** zijn voor het partitioneren van elke sensor gegevens naar andere mappen.  


Met behulp van de REST API kan het gedeelte uitvoer van een JSON-bestand dat voor de aanvraag wordt gebruikt, er als volgt uitzien:  

![REST API uitvoer](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Zodra de taak wordt uitgevoerd, kan de container *clients* er als volgt uitzien:  

![Container clients](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Elke map kan meerdere blobs bevatten waarbij elke Blob een of meer records bevat. In het bovenstaande voor beeld bevindt zich één Blob in een 06000000 map met de volgende inhoud:

![Inhoud van BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

U ziet dat elke record in de BLOB een **client_id** kolom heeft die overeenkomt met de mapnaam, aangezien de kolom die wordt gebruikt voor het partitioneren van de uitvoer in het uitvoerpad, is **client_id**.

### <a name="limitations"></a>Beperkingen

1. Er is slechts één aangepaste partitie sleutel toegestaan in de BLOB-uitvoer eigenschap van het pad patroon. Alle volgende paden zijn geldig:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Partitie sleutels zijn niet hoofdletter gevoelig, waardoor partitie sleutels als ' John ' en ' John ' gelijkwaardig zijn. Expressies kunnen ook niet worden gebruikt als partitie sleutels. Bijvoorbeeld: **{Columna + columnB}** werkt niet.  

3. Wanneer een invoer stroom bestaat uit records met een kardinaliteit van de partitie sleutel onder 8000, worden de records toegevoegd aan bestaande blobs en worden alleen nieuwe blobs gemaakt wanneer dat nodig is. Als de kardinaliteit meer dan 8000 is, is er geen garantie dat er bestaande blobs worden geschreven naar en er geen nieuwe blobs worden gemaakt voor een wille keurig aantal records met dezelfde partitie sleutel.

4. Als de BLOB-uitvoer is [geconfigureerd als onveranderbaar](../storage/blobs/storage-blob-immutable-storage.md), maakt stream Analytics elke keer dat er gegevens worden verzonden een nieuwe blob.

## <a name="custom-datetime-path-patterns"></a>Patronen voor aangepaste DateTime-paden

Met de aangepaste DateTime-paden kunt u een uitvoer indeling opgeven die wordt afgestemd op de conventies van Hive-streaming, waardoor Azure Stream Analytics de mogelijkheid om gegevens te verzenden naar Azure HDInsight en Azure Databricks voor downstream-verwerking. Aangepaste DateTime-paden kunnen eenvoudig worden geïmplementeerd met behulp `datetime` van het sleutel woord in het veld voor voegsel voor het pad van de BLOB-uitvoer, samen met de indelings aanduiding. Bijvoorbeeld `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Ondersteunde tokens

De volgende indelings specificatie tokens kunnen alleen worden gebruikt of in combi natie met aangepaste DateTime-indelingen:

|Indelings aanduiding   |Beschrijving   |Resultaten van de voorbeeld tijd 2018-01-02T10:06:08|
|----------|-----------|------------|
|{DateTime: jjjj}|Het jaar als een getal van vier cijfers|2018|
|{DateTime: MM}|Maand van 01 tot en met 12|01|
|{DateTime: M}|Maand van 1 tot en met 12|1|
|{DateTime: dd}|Dag van 01 tot 31|02|
|{DateTime: d}|Dag van 1 tot 31|2|
|{DateTime: HH}|Uur met een 24-uurs notatie van 00 tot 23|10|
|{DateTime: mm}|Minuten van 00 tot 60|06|
|{DateTime: m}|Minuten van 0 tot 60|6|
|{DateTime: SS}|Seconden van 00 tot 60|08|

Als u geen aangepaste DateTime-patronen wilt gebruiken, kunt u het token {date} en/of {time} toevoegen aan het pad voor voegsel om een vervolg keuzelijst met ingebouwde datum notaties te genereren.

![Stream Analytics oude datum notaties](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Uitbreid baarheid en beperkingen

U kunt zoveel tokens gebruiken `{datetime:<specifier>}` Als u wilt in het pad patroon totdat u het pad voor het voor voegsel van de spatie bereikt. Indelings aanduidingen kunnen niet worden gecombineerd in één token naast de combi naties die al door de vervolg keuzelijst voor datum en tijd worden weer gegeven. 

Voor een pad-partitie van `logs/MM/dd` :

|Geldige expressie   |Ongeldige expressie   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

U kunt dezelfde indelings specificatie meerdere keren gebruiken in het voor voegsel van het pad. Het token moet elke keer worden herhaald.

### <a name="hive-streaming-conventions"></a>Conventies voor Hive-streaming

Aangepaste paden voor Blob Storage kunnen worden gebruikt in combi natie met de component streaming-Conventie, waarbij wordt verwacht dat mappen worden aangeduid met `column=` de naam in de mapnaam.

Bijvoorbeeld `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Aangepaste uitvoer elimineert de moeite van het wijzigen van tabellen en het hand matig toevoegen van partities aan poort gegevens tussen Azure Stream Analytics en Hive. In plaats daarvan kunnen veel mappen automatisch worden toegevoegd met behulp van:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Voorbeeld

Maak een opslag account, een resource groep, een Stream Analytics taak en een invoer bron op basis van de [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) Quick Start Guide (Engelstalig). Gebruik dezelfde voorbeeld gegevens die worden gebruikt in de Quick start-gids, ook beschikbaar op [github](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Maak een BLOB-uitvoer Sink met de volgende configuratie:

![BLOB-uitvoer-Sink Stream Analytics maken](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Het volledige pad patroon is als volgt:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Wanneer u de taak start, wordt er in de BLOB-container een mapstructuur gemaakt op basis van het patroon van het pad. U kunt inzoomen op het niveau van de dag.

![BLOB-uitvoer Stream Analytics met aangepast pad-patroon](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de uitvoer van Azure Stream Analytics](stream-analytics-define-outputs.md)
