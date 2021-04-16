---
title: Best practices voor het laden van gegevens voor toegewezen SQL-pools
description: Aanbevelingen en prestatieoptimalisaties voor het laden van gegevens met behulp van toegewezen SQL-pools in Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 294b2a5188f0dfd8cb29f21bdbb29236b1740231
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565862"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Best practices voor het laden van gegevens met behulp van toegewezen SQL-pools in Azure Synapse Analytics

In dit artikel vindt u aanbevelingen en prestatieoptimalisaties voor het laden van gegevens met behulp van een toegewezen SQL-pool.

## <a name="preparing-data-in-azure-storage"></a>Gegevens voorbereiden in Azure Storage

Als u de latentie wilt minimaliseren, moet u de opslaglaag en uw toegewezen SQL-pool colocatie.

Bij het exporteren van gegevens in een ORC-bestandsindeling kunnen er Java-geheugenfouten optreden wanneer er grote tekstkolommen zijn. U kunt deze beperking omzeilen door slechts een subset van de kolommen te exporteren.

Alle bestandsindelingen hebben verschillende prestatiekenmerken. Gebruik voor het snelste laadproces gecomprimeerde tekstbestanden. Het verschil tussen UTF-8- en UTF-16-prestaties is minimaal.

Splits grote gecomprimeerde bestanden in kleinere gecomprimeerde bestanden.

## <a name="running-loads-with-enough-compute"></a>Laadtaken uitvoeren met voldoende rekenkracht

Voer voor de hoogste laadsnelheid slechts één taak tegelijk uit. Als dat niet haalbaar is, moet u een minimaal aantal belastingen tegelijk uitvoeren. Als u een grote laadklus verwacht, kunt u overwegen om uw toegewezen SQL-pool op te schalen vóór de belasting.

Als u loads wilt uitvoeren met geschikte rekenresources, maakt u gebruikers voor het laadproces die zijn aangewezen voor het uitvoeren van loads. Classificeer elke gebruiker voor het laden van een specifieke werkbelastinggroep. Als u een belasting wilt uitvoeren, moet u zich aanmelden als een van de gebruikers die het laden heeft uitgevoerd en vervolgens de belasting uitvoeren. De belasting wordt uitgevoerd met de werkbelastinggroep van de gebruiker.  

### <a name="example-of-creating-a-loading-user"></a>Voorbeeld van het maken van een gebruiker voor het laadproces

In dit voorbeeld wordt een gebruiker voor laden gemaakt die is geclassificeerd voor een specifieke werkbelastinggroep. De eerste stap is **verbinding maken met de master** en een aanmelding maken.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Maak verbinding met de toegewezen SQL-pool en maak een gebruiker. In de volgende code wordt ervan uitgenomen dat u bent verbonden met de database mySampleDataWarehouse. U ziet hoe u een gebruiker met de naam loader maakt en de gebruiker machtigingen geeft om tabellen te maken en te laden met behulp van de [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Vervolgens wordt de gebruiker in de workloadgroep DataLoads met maximale resources classificeert. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>Dit is een uiterst voorbeeld van het toewijzen van 100% resources van de SQL-pool aan één belasting. Hiermee krijgt u een maximale gelijktijdigheid van 1. Let erop dat dit alleen moet worden gebruikt voor de initiële belasting, waarbij u extra workloadgroepen met hun eigen configuraties moet maken om resources in uw workloads te beheren. 

Als u een belasting wilt uitvoeren met resources voor de werkbelastinggroep voor het laden, meld u zich aan als loader en voer de belasting uit.

## <a name="allowing-multiple-users-to-load-polybase"></a>Meerdere gebruikers toestaan te laden (PolyBase)

Vaak is het nodig dat meerdere gebruikers gegevens laden in een toegewezen SQL-pool. Voor het laden [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (PolyBase) zijn CONTROL-machtigingen van de database vereist.  De CONTROL-machtiging biedt beheertoegang tot alle schema's.

Mogelijk wilt u niet alle gebruikers die laadtaken uitvoeren, beheertoegang tot alle schema's verlenen. Als u machtigingen wilt beperken, kunt u de instructie DENY CONTROL gebruiken.

Denk bijvoorbeeld aan databaseschema's, schema_A voor afdeling A, en schema_B voor afdeling B. Laat databasegebruikers gebruiker_A en gebruiker_B gebruikers zijn voor PolyBase die respectievelijk laden in afdeling A en B. Beide zijn voorzien van databasemachtigingen voor CONTROL. De makers van schema A en B vergrendelen nu hun schema's met DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A en user_B zijn nu vergrendeld voor het schema van de andere afdeling.

## <a name="loading-to-a-staging-table"></a>Laden naar een faseringstabel

Laad gegevens in een faseringstabel om de snelste laadsnelheid te bereiken voor het verplaatsen van gegevens naar een toegewezen SQL-pooltabel.  Definieer de faseringstabel als een heap en gebruik round-robin voor de distributieoptie.

Houd er rekening mee dat laden meestal een proces in twee stappen is waarin u eerst naar een faseringstabel laadt en vervolgens de gegevens invoegt in een toegewezen SQL-pooltabel voor productie. Als de productietabel een hash-distributiepunt gebruikt, is de totale tijd voor het laden en invoegen mogelijk sneller als u de faseringstabel met de hash-distributie definieert.

Het laden naar de faseringstabel duurt langer, maar de tweede stap van het invoegen van de rijen in de productietabel leidt niet tot de verplaatsing van gegevens in de distributies.

## <a name="loading-to-a-columnstore-index"></a>Laden naar een columnstore-index

Columnstore-indexen vereisen grote hoeveelheden geheugen voor het comprimeren van gegevens tot hoogwaardige rijgroepen. Voor de beste compressie en efficiëntie van de index moet de columnstore-index maximaal 1.048.576 rijen in elke rijgroep comprimeren.

Bij geheugenbelasting kan het zijn dat de columnstore-index de maximale compressiesnelheden niet kan halen. Dit scenario heeft weer invloed op de queryprestaties. Zie voor gedetailleerde informatie [Columnstore geheugenoptimalisaties](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Zorg dat de gebruiker van het laadproces voldoende geheugen heeft om maximale compressiesnelheden te bereiken. Gebruik hiervoor gebruikers voor het laadproces die lid zijn van een middelgrote of grote resourceklasse.
- Laad genoeg rijen om nieuwe rijgroepen volledig te vullen. Tijdens een bulksgewijze laadtaak worden elke 1.048.576 rijen rechtstreeks in de columnstore gecomprimeerd als een volledige rijgroep. Laadtaken met minder dan 102.400 rijen verzenden de rijen naar de deltastore waarin rijen zijn ondergebracht in een b-tree-index.

> [!NOTE]
> Als u te weinig rijen laadt, worden ze mogelijk allemaal doorgeleid naar de deltastore en niet direct gecomprimeerd in columnstore-indeling.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Batchgrootte vergroten bij gebruik van SqLBulkCopy API of bcp

Laden met de COPY-instructie biedt de hoogste doorvoer met toegewezen SQL-pools. Als u de COPY niet kunt gebruiken om te laden en de [SqLBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)moet gebruiken, kunt u overwegen om de batchgrootte te vergroten voor een betere doorvoer.

> [!TIP]
> Een batchgrootte tussen 100.000.000 en 1 miljoen rijen is de aanbevolen basislijn voor het bepalen van de optimale batchcapaciteit.

## <a name="handling-loading-failures"></a>Afhandeling van fouten bij het laden

Een load met behulp van een externe tabel kan mislukken met de fout *Query afgebroken--de maximale weigeringsdrempelwaarde is bereikt tijdens het lezen vanuit een externe bron*. Dit bericht geeft aan dat uw externe gegevens vervuilde records bevatten.

Een gegevensrecord wordt als vervuild beschouwd als deze voldoet aan een van de volgende voorwaarden:

- De gegevenstypen en het aantal kolommen komen niet overeen met de kolomdefinities van de externe tabel.
- De gegevens voldoen niet aan de opgegeven externe bestandsindeling.

U kunt vervuilde records voorkomen door ervoor te zorgen dat uw externe tabel- en bestandindelingsdefinities correct zijn en uw externe gegevens overeenstemmen met deze definities.

Als een subset van externe gegevensrecords vervuild is, kunt u ervoor kiezen om deze records voor uw query's af te wijzen met behulp van de weigeringsopties in [CREATE EXTERNAL TABLE (Transact-SQL).](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="inserting-data-into-a-production-table"></a>Gegevens in een productietabel invoegen

Een eenmalige laadtaak naar een kleine tabel met een [INSERT-instructie](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) of zelfs een periodieke herlaadtaak kan een acceptabel resultaat geven met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Het invoegen van singletons is echter niet zo efficiënt als bulksgewijs laden.

Als u de hele dag door duizenden of meerdere enkele gegevens wilt invoeren, voeg de gegevens dan samen tot een batch zodat deze bulksgewijs kunt laden.  Ontwikkel uw processen om de afzonderlijke gegevens aan een bestand toe te voegen en maak vervolgens een ander proces dat het bestand periodiek laadt.

## <a name="creating-statistics-after-the-load"></a>Statistieken maken na het laden

Voor optimale resultaten van uw query's is het belangrijk dat u statistieken maakt voor alle kolommen van alle tabellen nadat de gegevens voor het eerst zijn geladen of wanneer de gegevens substantieel zijn gewijzigd. Het maken van statistieken kan handmatig worden uitgevoerd of u kunt [AUTO_CREATE_STATISTICS.](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)

Zie [statistieken](sql-data-warehouse-tables-statistics.md) voor gedetailleerde uitleg van statistieken. In het volgende voorbeeld ziet u hoe u handmatig statistieken maakt voor vijf kolommen van de Customer_Speed tabel.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Opslagsleutels roteren (PolyBase)

Het is verstandig uit veiligheidsoverwegingen de toegangssleutel in de blob-opslag regelmatig te wijzigen. Er zijn twee opslagsleutels voor uw blob-opslagaccount, waarmee u de sleutels kunt wijzigen.

Sleutels van het Microsoft Azure Storage-account draaien:

Voor elk opslagaccount waarvan de sleutel is gewijzigd, moet u [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) uitvoeren.

Voorbeeld:

De oorspronkelijke sleutel wordt gemaakt

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Rotate key from key 1 to key 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Er hoeven geen andere wijzigingen te worden aangebracht aan onderliggende externe gegevensbronnen.

## <a name="next-steps"></a>Volgende stappen

- Zie ELT ontwerpen voor meer informatie over de COPY-instructie of PolyBase bij het ontwerpen van een ELT-proces (Extract, Load, and Transform) [voor meer Azure Synapse Analytics.](design-elt-data-loading.md)
- Gebruik voor een zelfstudie over het laden de instructie COPY om gegevens uit Azure Blob Storage te [laden naar Synapse SQL.](./load-data-from-azure-blob-storage-using-copy.md)
- Zie [Uw workload controleren met DMV's](sql-data-warehouse-manage-monitor.md) voor het controleren van het laden van gegevens.