---
title: Best practices voor serverloze SQL-pools
description: Aanbevelingen en aanbevolen procedures voor het werken met serverloze SQL-pool.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a2656d5c23a465856eee1e84d2c4f6900b21ec41
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477465"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Best practices voor serverloze SQL-pool in Azure Synapse Analytics

In dit artikel vindt u een aantal best practices voor het gebruik van serverloze SQL-pool. Serverloze SQL-pool is een resource in Azure Synapse Analytics.

Met serverloze SQL-pool kunt u query's uitvoeren op bestanden in uw Azure-opslagaccounts. Het heeft geen lokale opslag- of opnamemogelijkheden. Alle bestanden die de querydoelen hebben, zijn dus extern voor serverloze SQL-pool. Alles met betrekking tot het lezen van bestanden uit de opslag kan van invloed zijn op de queryprestaties.

Enkele algemene richtlijnen zijn:
- Zorg ervoor dat uw clienttoepassingen colocated zijn met de serverloze SQL-pool.
  - Als u clienttoepassingen buiten Azure gebruikt (bijvoorbeeld Power BI Desktop, SSMS, ADS), moet u ervoor zorgen dat u de serverloze pool gebruikt in een regio dicht bij uw clientcomputer.
- Zorg ervoor dat de opslag (Azure Data Lake, Cosmos DB) en serverloze SQL-pool zich in dezelfde regio hebben.
- Probeer de [opslagindeling te optimaliseren](#prepare-files-for-querying) met behulp van partitionering en houd uw bestanden binnen het bereik tussen 100 MB en 10 GB.
- Als u een groot aantal resultaten retourneert, moet u ervoor zorgen dat u SSMS of ADS gebruikt en niet Synapse Studio. Synapse Studio is een webhulpprogramma dat niet is ontworpen voor grote resultatensets. 
- Als u resultaten filtert op tekenreekskolom, kunt u proberen enige `BIN2_UTF8` collatie te gebruiken.
- Probeer de resultaten aan de clientzijde in de cache op te Power BI de importmodus of Azure Analysis Services en vernieuw ze periodiek. De serverloze SQL-pools kunnen geen interactieve ervaring bieden in Power BI Direct Query-modus als u complexe query's gebruikt of een grote hoeveelheid gegevens verwerkt.

## <a name="client-applications-and-network-connections"></a>Clienttoepassingen en netwerkverbindingen

Zorg ervoor dat uw clienttoepassing is verbonden met de dichtstbijzijnde Synapse-werkruimte met de optimale verbinding.
- Een clienttoepassing co-locatie met de Synapse-werkruimte. Als u toepassingen zoals Power BI of Azure Analysis Service gebruikt, moet u ervoor zorgen dat deze zich in dezelfde regio als waarin u uw Synapse-werkruimte hebt geplaatst. Maak indien nodig de afzonderlijke werkruimten die zijn gekoppeld aan uw clienttoepassingen. Het plaatsen van een clienttoepassing en de Synapse-werkruimte in een andere regio kan leiden tot een grotere latentie en tragere streaming van resultaten.
- Als u gegevens leest uit uw on-premises toepassing, moet u ervoor zorgen dat de Synapse-werkruimte zich in de regio bevindt die zich dicht bij uw locatie bevindt.
- Zorg ervoor dat er tijdens het lezen van een grote hoeveelheid gegevens geen problemen zijn met de netwerkbandbreedte.
- Gebruik Synapse Studio niet om een grote hoeveelheid gegevens te retourneren. Synapse Studio is een webhulpprogramma dat gebruikmaakt van het HTTPS-protocol om gegevens over te dragen. Gebruik Azure Data Studio of SQL Server Management Studio om een grote hoeveelheid gegevens te lezen.

## <a name="storage-and-content-layout"></a>Indeling van opslag en inhoud

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Uw opslag en serverloze SQL-pool co-locatie

Als u de latentie wilt minimaliseren, moet u uw Azure-opslagaccount of CosmosDB-analyseopslag en het eindpunt van uw serverloze SQL-pool op een andere locatie zetten. Opslagaccounts en eindpunten die zijn ingericht tijdens het maken van de werkruimte bevinden zich in dezelfde regio.

Als u toegang hebt tot andere opslagaccounts met een serverloze SQL-pool, moet u ervoor zorgen dat ze zich in dezelfde regio hebben voor optimale prestaties. Als ze zich niet in dezelfde regio, is er een verhoogde latentie voor de netwerkoverdracht van de gegevens tussen de externe regio en de regio van het eindpunt.

### <a name="azure-storage-throttling"></a>Azure Storage beperken

Meerdere toepassingen en services hebben mogelijk toegang tot uw opslagaccount. Opslagbeperking treedt op wanneer de gecombineerde IOPS of doorvoer die wordt gegenereerd door toepassingen, services en serverloze SQL-poolworkload de limieten van het opslagaccount overschrijdt. Als gevolg hiervan ervaart u een aanzienlijk negatief effect op de queryprestaties.

Wanneer beperking wordt gedetecteerd, heeft de serverloze SQL-pool ingebouwde verwerking om deze op te lossen. Serverloze SQL-pool maakt in een trager tempo aanvragen naar de opslag totdat de beperking is opgelost.

> [!TIP]
> Voor een optimale queryuitvoering moet u het opslagaccount tijdens het uitvoeren van de query niet belasten met andere workloads.

### <a name="azure-ad-pass-through-performance"></a>Pass-through-prestaties van Azure AD

Met een serverloze SQL-pool kunt u toegang krijgen tot bestanden in de opslag met behulp Azure Active Directory pass-through- of SAS-referenties (Azure AD). Mogelijk ervaart u tragere prestaties met Azure AD Pass-through dan met SAS.

Als u betere prestaties nodig hebt, gebruikt u SAS-referenties om toegang te krijgen tot de opslag.

### <a name="prepare-files-for-querying"></a>Bestanden voorbereiden voor het uitvoeren van query's

Indien mogelijk kunt u bestanden voorbereiden voor betere prestaties:

- Grote CSV en JSON converteren naar Parquet. Parquet is een kolomindeling. Omdat het gecomprimeerd is, zijn de bestandsgrootten kleiner dan CSV- of JSON-bestanden die dezelfde gegevens bevatten. Serverloze SQL-pool kan de kolommen en rijen die niet nodig zijn in de query overslaan als u Parquet-bestanden leest. Serverloze SQL-pool heeft minder tijd en minder opslagaanvragen nodig om deze te lezen.
- Als een query is gericht op één groot bestand, kunt u deze opsplitsen in meerdere kleinere bestanden.
- Probeer de CSV-bestandsgrootte tussen 100 MB en 10 GB te houden.
- Het is beter om bestanden van gelijke grootte te hebben voor één OPENROWSET-pad of een externe tabelLOCATIE.
- Partitioneren van uw gegevens door partities op te slaan in verschillende mappen of bestandsnamen. Zie [Use filename and filepath functions to target specific partitions (Bestandsnaam- en bestandspadfuncties gebruiken om te richten op specifieke partities).](#use-filename-and-filepath-functions-to-target-specific-partitions)

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>Colocatie van uw analytische opslag van CosmosDB en serverloze SQL-pool

Zorg ervoor dat de analytische opslag van CosmosDB in dezelfde regio wordt geplaatst als de Synapse-werkruimte. Query's in meerdere regio's kunnen grote latentie veroorzaken. Gebruik de eigenschap region in connection string om expliciet de regio op te geven waarin de analytische opslag wordt geplaatst (zie Query's uitvoeren op [CosmosDb met serverloze SQL-pool):](query-cosmos-db-analytical-store.md#overview)

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>CSV-optimalisaties

### <a name="use-parser_version-20-to-query-csv-files"></a>Gebruik PARSER_VERSION 2.0 om een query uit te voeren op CSV-bestanden

U kunt een voor prestaties geoptimaliseerde parser gebruiken wanneer u een query uitvoert op CSV-bestanden. Zie voor meer [informatie PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Handmatig statistieken maken voor CSV-bestanden

Serverloze SQL-pool is afhankelijk van statistieken voor het genereren van optimale uitvoeringsplannen voor query's. Statistieken worden automatisch gemaakt voor kolommen in Parquet-bestanden wanneer dat nodig is. Op dit moment worden statistieken niet automatisch gemaakt voor kolommen in CSV-bestanden en moet u handmatig statistieken maken voor kolommen die u gebruikt in query's, met name de kolommen die worden gebruikt in DISTINCT, JOIN, WHERE, ORDER BY en GROUP BY. Controleer [de statistieken in een serverloze SQL-pool](develop-tables-statistics.md#statistics-in-serverless-sql-pool) voor meer informatie.


## <a name="data-types"></a>Gegevenstypen

### <a name="use-appropriate-data-types"></a>De juiste gegevenstypen gebruiken

De gegevenstypen die u in uw query gebruikt, zijn van invloed op de prestaties. U kunt betere prestaties krijgen als u deze richtlijnen volgt: 

- Gebruik de kleinste gegevensgrootte die geschikt is voor de grootst mogelijke waarde.
  - Als de maximale tekenwaardelengte 30 tekens is, gebruikt u een tekengegevenstype van 30 tekens.
  - Als alle tekenkolomwaarden een vaste grootte hebben, gebruikt u **teken** of **nchar.** Gebruik anders **varchar** of **nvarchar**.
  - Als de maximale kolomwaarde voor een geheel getal 500 is, gebruikt u **smallint** omdat dit het kleinste gegevenstype is dat geschikt is voor deze waarde. In dit artikel vindt u de gegevenstypebereiken [voor gehele getallen.](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- Gebruik zo mogelijk **varchar** en **char** in plaats van **nvarchar** en **nchar**.
- Gebruik indien mogelijk gegevenstypen op basis van gehele getallen. De bewerkingen SORT, JOIN en GROUP BY worden sneller uitgevoerd op gehele getallen dan op tekengegevens.
- Als u schemadeferentie gebruikt, controleert [u de uitgestelde gegevenstypen](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Uitgestelde gegevenstypen controleren

[Met schemadeferentie](query-parquet-files.md#automatic-schema-inference) kunt u snel query's schrijven en gegevens verkennen zonder dat u bekend bent met bestandsschema's. De kosten van dit gemak zijn dat afgeleide gegevenstypen mogelijk groter zijn dan de werkelijke gegevenstypen. Dit gebeurt wanneer er onvoldoende informatie in de bronbestanden staat om ervoor te zorgen dat het juiste gegevenstype wordt gebruikt. Parquet-bestanden bevatten bijvoorbeeld geen metagegevens over de maximale tekenkolomlengte. Serverloze SQL-pool heeft dus de instructie varchar(8000).

U kunt de [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) om de resulterende gegevenstypen van uw query te controleren.

In het volgende voorbeeld ziet u hoe u afgeleide gegevenstypen kunt optimaliseren. Deze procedure wordt gebruikt om de afgeleide gegevenstypen weer te geven: 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Dit is de resultatenset:

|is_hidden|column_ordinal|naam|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

Nadat u de afgeleide gegevenstypen voor de query weet, kunt u de juiste gegevenstypen opgeven:

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Filteroptimalisatie

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Jokertekens pushen naar lagere niveaus in het pad

U kunt jokertekens in uw pad gebruiken om query's uit te voeren [op meerdere bestanden en mappen.](query-data-storage.md#query-multiple-files-or-folders) Serverloze SQL-pool bevat bestanden in uw opslagaccount, beginnend bij de eerste * met behulp van opslag-API. Hiermee worden bestanden verwijderd die niet overeenkomen met het opgegeven pad. Het verminderen van de eerste lijst met bestanden kan de prestaties verbeteren als er veel bestanden zijn die overeenkomen met het opgegeven pad tot het eerste jokerteken.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Bestandsnaam- en bestandspadfuncties gebruiken om te richten op specifieke partities

Gegevens worden vaak ingedeeld in partities. U kunt serverloze SQL-pool instrueren om query's uit te voeren op bepaalde mappen en bestanden. Dit vermindert het aantal bestanden en de hoeveelheid gegevens die de query moet lezen en verwerken. Een extra bonus is dat u betere prestaties bereikt.

Lees voor meer informatie over de functies [bestandsnaam](query-data-storage.md#filename-function) [en bestandspad](query-data-storage.md#filepath-function) en zie de voorbeelden voor [het uitvoeren van query's op specifieke bestanden.](query-specific-files.md)

> [!TIP]
> Cast altijd de resultaten van de functies filepath en filename naar de juiste gegevenstypen. Als u tekengegevenstypen gebruikt, moet u de juiste lengte gebruiken.

> [!NOTE]
> Functies die worden gebruikt voor het verwijderen van partities, bestandspad en bestandsnaam, worden momenteel niet ondersteund voor externe tabellen, met andere dan functies die automatisch worden gemaakt voor elke tabel die is gemaakt in Apache Spark voor Azure Synapse Analytics.

Als uw opgeslagen gegevens niet zijn gepart partitioneerd, kunt u overwegen deze te partitioneren. Op die manier kunt u deze functies gebruiken om query's te optimaliseren die zijn gericht op deze bestanden. Wanneer u [gepart partitioneerde Apache Spark](develop-storage-files-spark-tables.md) voor Azure Synapse tabellen uit een serverloze SQL-pool, wordt de query automatisch alleen gericht op de benodigde bestanden.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>De juiste collatie gebruiken om predicaat-pushdown te gebruiken voor tekenkolommen

Gegevens in het Parquet-bestand zijn ingedeeld in rijgroepen. Serverloze SQL-pool slaat rijgroepen over op basis van het opgegeven predicaat in de WHERE-component en vermindert dus de IO, wat resulteert in betere queryprestaties. 

Houd er rekening mee dat predicaat-pushdown voor tekenkolommen in Parquet-bestanden alleen wordt ondersteund Latin1_General_100_BIN2_UTF8 voor het sorteren van tekens. U kunt de collatie voor een bepaalde kolom opgeven met behulp van de WITH-component. Als u deze collatie niet opgeeft met behulp van de WITH-component, wordt databaseseratie gebruikt.

## <a name="optimize-repeating-queries"></a>Herhalende query's optimaliseren

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS gebruiken om queryprestaties en joins te verbeteren

[CETAS](develop-tables-cetas.md) is een van de belangrijkste functies die beschikbaar zijn in een serverloze SQL-pool. CETAS is een parallelle bewerking die externe tabelmetagegevens maakt en de SELECT-queryresultaten exporteert naar een set bestanden in uw opslagaccount.

U kunt CETAS gebruiken om veelgebruikte onderdelen van query's, zoals samengevoegde referentietabellen, te materialiseren in een nieuwe set bestanden. U kunt vervolgens deelnemen aan deze enkele externe tabel in plaats van algemene joins in meerdere query's te herhalen.

Wanneer CETAS Parquet-bestanden genereert, worden er automatisch statistieken gemaakt wanneer de eerste query is gericht op deze externe tabel, wat resulteert in verbeterde prestaties voor volgende query's die zijn gericht op de tabel die is gegenereerd met CETAS.

## <a name="next-steps"></a>Volgende stappen

Lees het [artikel over probleemoplossing](resources-self-help-sql-on-demand.md) voor oplossingen voor veelvoorkomende problemen. Zie Best practices for dedicated SQL pools (Best practices voor toegewezen SQL-pools) voor specifieke richtlijnen als u werkt met een toegewezen SQL-pool in plaats van een serverloze [SQL-pool.](best-practices-dedicated-sql-pool.md)
