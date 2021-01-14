---
title: Best practices voor serverloze SQL-pools
description: Aanbevelingen en aanbevolen procedures voor het werken met serverloze SQL-groep.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: c752bc6ae49f009056067545fde292dc29027d5d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208128"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Aanbevolen procedures voor serverloze SQL-groepen in azure Synapse Analytics

In dit artikel vindt u een verzameling aanbevolen procedures voor het gebruik van serverloze SQL-groepen. Een serverloze SQL-groep is een resource in azure Synapse Analytics.

## <a name="general-considerations"></a>Algemene overwegingen

Met serverloze SQL-pool kunt u een query uitvoeren op bestanden in uw Azure-opslag accounts. Het bevat geen lokale opslag-of opname mogelijkheden. Alle bestanden waarvan de query doelen zijn, zijn dus extern naar een serverloze SQL-groep. Alles wat verband houdt met het lezen van bestanden uit de opslag kan gevolgen hebben voor de prestaties van query's.

## <a name="colocate-your-storage-and-serverless-sql-pool"></a>Uw opslag-en serverloze SQL-groep opzoeken

Als u de latentie wilt minimaliseren, gaat u naar uw Azure Storage-account of CosmosDB analytische opslag en uw serverloze SQL-groeps eindpunt. Opslag accounts en eind punten die zijn ingericht tijdens het maken van de werk ruimte bevinden zich in dezelfde regio.

Voor optimale prestaties kunt u, als u toegang krijgt tot andere opslag accounts met serverloze SQL-groep, ervoor zorgen dat ze zich in dezelfde regio bevinden. Als ze zich niet in dezelfde regio bevinden, wordt de latentie verhoogd voor de netwerk overdracht van de gegevens tussen de externe regio en de regio van het eind punt.

## <a name="azure-storage-throttling"></a>Azure Storage beperking

Meerdere toepassingen en services kunnen toegang krijgen tot uw opslag account. Opslag beperking treedt op wanneer de gecombineerde IOPS of door Voer die worden gegenereerd door toepassingen, services en de werk belasting van de serverloze SQL-groep de limieten van het opslag account overschrijden. Als gevolg hiervan krijgt u een aanzienlijk negatief effect op de query prestaties.

Wanneer beperking wordt gedetecteerd, heeft de serverloze SQL-pool een ingebouwde verwerking om het probleem op te lossen. Serverloze SQL-pool maakt aanvragen voor opslag in een langzamer tempo tot de beperking is opgelost.

> [!TIP]
> Voor een optimale uitvoering van query's kunt u het opslag account niet onder andere workloads belasten tijdens het uitvoeren van query's.

## <a name="prepare-files-for-querying"></a>Bestanden voorbereiden voor het uitvoeren van query's

Als dat mogelijk is, kunt u bestanden voorbereiden voor betere prestaties:

- Converteer grote CSV-en JSON-naar-Parquet. Parquet is een kolom indeling. Omdat het gecomprimeerd is, zijn de bestands grootten kleiner dan CSV-of JSON-bestanden die dezelfde gegevens bevatten. Serverloze SQL-groep kan de kolommen en rijen die niet nodig zijn in de query overs Laan als u Parquet-bestanden leest. Een serverloze SQL-pool heeft minder tijd en minder opslag aanvragen nodig om deze te lezen.
- Als een query is gericht op één groot bestand, kunt u deze in meerdere kleinere bestanden splitsen.
- Probeer de grootte van het CSV-bestand te hand haven tussen 100 MB en 10 GB.
- Het is beter om even grote bestanden te hebben voor één OPENROWSET-pad of een externe tabel locatie.
- Partitioneer uw gegevens door partities op te slaan in verschillende mappen of bestands namen. Zie [functies filename en filepath gebruiken om specifieke partities te bereiken](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Joker tekens pushen naar lagere niveaus in het pad

U kunt joker tekens in het pad gebruiken om [meerdere bestanden en mappen](query-data-storage.md#query-multiple-files-or-folders)op te vragen. Serverloze SQL-pool bevat bestanden in uw opslag account, te beginnen met de eerste * opslag-API. Hiermee worden bestanden geëlimineerd die niet overeenkomen met het opgegeven pad. Het verminderen van de eerste lijst met bestanden kan de prestaties verbeteren als er veel bestanden zijn die overeenkomen met het opgegeven pad tot het eerste Joker teken.

## <a name="use-appropriate-data-types"></a>De juiste gegevens typen gebruiken

De gegevens typen die u in uw query gebruikt, zijn van invloed op de prestaties. U kunt betere prestaties krijgen als u deze richt lijnen volgt: 

- Gebruik de kleinste gegevens grootte die geschikt is voor de grootste mogelijke waarde.
  - Als de Maxi maal toegestane teken waarde 30 tekens lang is, gebruikt u een teken gegevens type van lengte 30.
  - Gebruik **char** of **NCHAR** als alle waarden van een teken kolom een vaste grootte hebben. Gebruik anders **varchar** of **nvarchar**.
  - Als de maximum waarde voor de kolom integer 500 is, gebruikt u **smallint** omdat het het kleinste gegevens type is dat deze waarde kan bevatten. In [dit artikel](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)vindt u bereiken met een geheel getal.
- Gebruik, indien mogelijk, **varchar** en **char** in plaats van **nvarchar** en **NCHAR**.
- Gebruik, indien mogelijk, gegevens typen op basis van een geheel getal. SORTEREN, samen voegen en groeperen op bewerkingen zijn sneller voltooid op gehele getallen dan bij teken gegevens.
- Als u schema-deferrometalie gebruikt, [controleert u de instelde gegevens typen](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Instelde gegevens typen controleren

Met [schema-deinterferentie](query-parquet-files.md#automatic-schema-inference) kunt u snel query's schrijven en gegevens verkennen zonder dat u de bestands schema's kent. De kosten voor dit gemak zijn dat uitgestelde gegevens typen groter kunnen zijn dan de werkelijke gegevens typen. Dit gebeurt wanneer er onvoldoende gegevens aanwezig zijn in de bron bestanden om ervoor te zorgen dat het juiste gegevens type wordt gebruikt. Parquet-bestanden bevatten bijvoorbeeld geen meta gegevens over de maximale teken kolom lengte. Een serverloze SQL-groep leidt deze als varchar (8000).

U kunt [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) gebruiken om de resulterende gegevens typen van uw query te controleren.

In het volgende voor beeld ziet u hoe u de verstelde gegevens typen kunt optimaliseren. Deze procedure wordt gebruikt om de uitgestelde gegevens typen weer te geven: 
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
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|DATETIME2 (7)|8|
|0|3|passenger_count|int|4|

Nadat u de voorziene gegevens typen voor de query weet, kunt u de juiste gegevens typen opgeven:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>De functies filename en filepath gebruiken om specifieke partities te bereiken

Gegevens zijn vaak ingedeeld in partities. U kunt een serverloze SQL-groep instrueren voor het opvragen van specifieke mappen en bestanden. Als u dit doet, vermindert het aantal bestanden en de hoeveelheid gegevens die de query moet lezen en verwerken. Een extra bonus is dat u betere prestaties krijgt.

Meer informatie over de functies [filename](query-data-storage.md#filename-function) en [filepath](query-data-storage.md#filepath-function) vindt u in de voor beelden voor het [uitvoeren van query's op specifieke bestanden](query-specific-files.md).

> [!TIP]
> Converteer de resultaten van het bestandspad en de bestands namen altijd naar de juiste gegevens typen. Als u teken gegevens typen gebruikt, moet u ervoor zorgen dat u de juiste lengte gebruikt.

> [!NOTE]
> Functies die worden gebruikt voor de partitie-eliminatie, het bestandspad en de bestands naam, worden momenteel niet ondersteund voor externe tabellen, behalve die die automatisch worden gemaakt voor elke tabel die is gemaakt in Apache Spark voor Azure Synapse Analytics.

Als uw opgeslagen gegevens niet zijn gepartitioneerd, kunt u het partitioneren. Op die manier kunt u deze functies gebruiken om query's te optimaliseren die op deze bestanden zijn gericht. Wanneer u een [query uitvoert op gepartitioneerde Apache Spark voor Azure Synapse-tabellen](develop-storage-files-spark-tables.md) vanuit een serverloze SQL-pool, wordt de query automatisch alleen gericht op de benodigde bestanden.

## <a name="use-parser_version-20-to-query-csv-files"></a>PARSER_VERSION 2,0 gebruiken voor het opvragen van CSV-bestanden

U kunt een met prestaties geoptimaliseerde parser gebruiken wanneer u CSV-bestanden opvraagt. Zie [PARSER_VERSION](develop-openrowset.md)voor meer informatie.

## <a name="manually-create-statistics-for-csv-files"></a>Hand matig statistieken maken voor CSV-bestanden

Een serverloze SQL-groep is afhankelijk van de statistieken voor het genereren van optimale query-uitvoerings plannen. Statistieken worden automatisch gemaakt voor kolommen in Parquet-bestanden wanneer dat nodig is. Op dit moment worden er geen statistieken automatisch gemaakt voor kolommen in CSV-bestanden. u moet statistieken hand matig maken voor kolommen die u in query's gebruikt, met name die in DISTINCT, samen voegen, waar, sorteren op en groeperen op. Controleer de [statistieken in de serverloze SQL-groep](develop-tables-statistics.md#statistics-in-serverless-sql-pool) voor meer informatie.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS gebruiken om de query prestaties te verbeteren en samen te voegen

[CETAS](develop-tables-cetas.md) is een van de belangrijkste functies die beschikbaar zijn in een SERVERloze SQL-groep. CETAS is een parallelle bewerking die meta gegevens van externe tabellen maakt en de selectie query resultaten exporteert naar een set bestanden in uw opslag account.

U kunt CETAS gebruiken voor het opslaan van veelgebruikte delen van query's, zoals gekoppelde referentie tabellen, naar een nieuwe set bestanden. U kunt vervolgens deel nemen aan deze afzonderlijke externe tabel in plaats van het herhalen van algemene samen voegingen in meerdere query's.

Omdat CETAS Parquet-bestanden genereert, worden er automatisch statistieken gemaakt wanneer de eerste query deze externe tabel bedoelt, wat resulteert in betere prestaties voor volgende query's die zijn gegenereerd met CETAS.

## <a name="azure-ad-pass-through-performance"></a>Pass-Through Azure AD-prestaties

Met serverloze SQL-groep hebt u toegang tot bestanden in de opslag met behulp van de Pass-Through-of SAS-referenties van Azure Active Directory (Azure AD). U kunt tragere prestaties bieden met Azure AD Pass-Through dan met SAS.

Als u betere prestaties nodig hebt, kunt u het beste SAS-referenties gebruiken om toegang te krijgen tot opslag totdat Azure AD Pass-Through-prestaties zijn verbeterd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel [over probleem oplossing](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor oplossingen voor veelvoorkomende problemen. Zie [Aanbevolen procedures voor exclusieve SQL-groepen](best-practices-sql-pool.md) voor specifieke instructies als u werkt met een exclusieve SQL-groep in plaats van een serverloze SQL-groep.
