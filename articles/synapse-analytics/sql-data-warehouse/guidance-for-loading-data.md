---
title: Aanbevolen procedures voor het laden van gegevens voor toegewezen SQL-groepen
description: Aanbevelingen en prestatie optimalisaties voor het laden van gegevens met behulp van exclusieve SQL-groepen in azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 39625914f179dfc8d5511b9a3d386cc8332b7efa
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456298"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Aanbevolen procedures voor het laden van gegevens met behulp van exclusieve SQL-groepen in azure Synapse Analytics

In dit artikel leert u aanbevelingen en prestatie optimalisaties voor het laden van gegevens met een exclusieve SQL-groep.

## <a name="preparing-data-in-azure-storage"></a>Gegevens voorbereiden in Azure Storage

Als u de latentie wilt minimaliseren, gaat u naar uw opslaglaag en uw toegewezen SQL-groep.

Bij het exporteren van gegevens in een ORC-bestandsindeling kunnen er Java-geheugenfouten optreden wanneer er grote tekstkolommen zijn. U kunt deze beperking omzeilen door slechts een subset van de kolommen te exporteren.

Alle bestandsindelingen hebben verschillende prestatiekenmerken. Gebruik voor het snelste laadproces gecomprimeerde tekstbestanden. Het verschil tussen UTF-8- en UTF-16-prestaties is minimaal.

Splits grote gecomprimeerde bestanden in kleinere gecomprimeerde bestanden.

## <a name="running-loads-with-enough-compute"></a>Laadtaken uitvoeren met voldoende rekenkracht

Voer voor de hoogste laadsnelheid slechts één taak tegelijk uit. Als dat niet het geval is, voert u tegelijkertijd een mini maal aantal loads uit. Als u een grote laad taak verwacht, kunt u overwegen om uw toegewezen SQL-groep vóór de belasting te schalen.

Als u loads wilt uitvoeren met geschikte rekenresources, maakt u gebruikers voor het laadproces die zijn aangewezen voor het uitvoeren van loads. Classificeer elke laad gebruiker naar een specifieke werkbelasting groep. Als u een belasting wilt uitvoeren, meldt u zich aan als een van de laad gebruikers en voert u de belasting uit. De belasting wordt uitgevoerd met de werkbelasting groep van de gebruiker.  

### <a name="example-of-creating-a-loading-user"></a>Voorbeeld van het maken van een gebruiker voor het laadproces

In dit voor beeld wordt een door een gebruiker geclassificeerde belasting gemaakt aan een specifieke werkbelasting groep. De eerste stap is **verbinding maken met de master** en een aanmelding maken.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Verbinding maken met de toegewezen SQL-groep en een gebruiker aanmaken. In de volgende code wordt ervan uitgegaan dat u bent verbonden met de data base met de naam mySampleDataWarehouse. Hier ziet u hoe u een gebruiker met de naam loader maakt en de gebruiker machtigingen geeft om tabellen te maken en te laden met behulp van de [instructie Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Vervolgens wordt de gebruiker geclassificeerd met de werkbelasting groep DataLoads met het maximum aantal resources. 

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

Als u een belasting wilt uitvoeren met resources voor de werkbelasting groep laden, meldt u zich aan als Loader en voert u de belasting uit.

## <a name="allowing-multiple-users-to-load-polybase"></a>Meerdere gebruikers toestaan om te laden (poly base)

Het is vaak nood zakelijk dat meerdere gebruikers gegevens laden naar een toegewezen SQL-groep. Bij het laden met de [Create Table als Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (poly base) zijn machtigingen vereist van de-data base.  De CONTROL-machtiging biedt beheertoegang tot alle schema's.

Mogelijk wilt u niet alle gebruikers die laadtaken uitvoeren, beheertoegang tot alle schema's verlenen. Als u machtigingen wilt beperken, kunt u de instructie DENY CONTROL gebruiken.

Denk bijvoorbeeld aan databaseschema's, schema_A voor afdeling A, en schema_B voor afdeling B. Laat databasegebruikers gebruiker_A en gebruiker_B gebruikers zijn voor PolyBase die respectievelijk laden in afdeling A en B. Beide zijn voorzien van databasemachtigingen voor CONTROL. De makers van schema A en B vergrendelen nu hun schema's met DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A en user_B zijn nu uitgesloten van het schema van de andere afdeling.

## <a name="loading-to-a-staging-table"></a>Laden naar een faseringstabel

Als u de snelste laad snelheid voor het verplaatsen van gegevens naar een exclusieve SQL-groeps tabel wilt verzorgen, laadt u gegevens in een faserings tabel.  Definieer de faseringstabel als een heap en gebruik round-robin voor de distributieoptie.

Houd er rekening mee dat laden meestal een proces in twee stappen is waarin u eerst naar een faserings tabel laadt en vervolgens de gegevens invoegt in een tabel met een toegewezen SQL-groep voor productie. Als de productietabel een hash-distributiepunt gebruikt, is de totale tijd voor het laden en invoegen mogelijk sneller als u de faseringstabel met de hash-distributie definieert.

Het laden naar de faseringstabel duurt langer, maar de tweede stap van het invoegen van de rijen in de productietabel leidt niet tot de verplaatsing van gegevens in de distributies.

## <a name="loading-to-a-columnstore-index"></a>Laden naar een columnstore-index

Columnstore-indexen vereisen grote hoeveelheden geheugen voor het comprimeren van gegevens tot hoogwaardige rijgroepen. Voor de beste compressie en efficiëntie van de index moet de columnstore-index maximaal 1.048.576 rijen in elke rijgroep comprimeren.

Bij geheugenbelasting kan het zijn dat de columnstore-index de maximale compressiesnelheden niet kan halen. Dit scenario heeft invloed op de prestaties van query's. Zie voor gedetailleerde informatie [Columnstore geheugenoptimalisaties](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Zorg dat de gebruiker van het laadproces voldoende geheugen heeft om maximale compressiesnelheden te bereiken. Gebruik hiervoor gebruikers voor het laadproces die lid zijn van een middelgrote of grote resourceklasse.
- Laad genoeg rijen om nieuwe rijgroepen volledig te vullen. Tijdens een bulksgewijze laadtaak worden elke 1.048.576 rijen rechtstreeks in de columnstore gecomprimeerd als een volledige rijgroep. Laadtaken met minder dan 102.400 rijen verzenden de rijen naar de deltastore waarin rijen zijn ondergebracht in een b-tree-index.

> [!NOTE]
> Als u te weinig rijen laadt, kunnen ze alle routes naar de Delta Store en worden ze niet onmiddellijk gecomprimeerd naar de column Store-indeling.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Batch grootte verg Roten bij gebruik van SqLBulkCopy-API of BCP

Het laden met de instructie COPY biedt de hoogste door Voer met toegewezen SQL-groepen. Als u de te laden kopie niet kunt gebruiken en u de [SqLBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)moet gebruiken, kunt u overwegen om de Batch grootte te verg Roten voor een betere door voer.

> [!TIP]
> Een batch grootte tussen 100 K en 1M rijen is de aanbevolen basis lijn voor het bepalen van de optimale grootte van de batch capaciteit.

## <a name="handling-loading-failures"></a>Afhandeling van fouten bij het laden

Een load met behulp van een externe tabel kan mislukken met de fout *Query afgebroken--de maximale weigeringsdrempelwaarde is bereikt tijdens het lezen vanuit een externe bron*. Dit bericht geeft aan dat uw externe gegevens vervuilde records bevatten.

Een gegevens record wordt als vuil beschouwd als deze voldoet aan een van de volgende voor waarden:

- De gegevens typen en het aantal kolommen komen niet overeen met de kolom definities van de externe tabel.
- De gegevens voldoen niet aan de opgegeven externe bestands indeling.

U kunt vervuilde records voorkomen door ervoor te zorgen dat uw externe tabel- en bestandindelingsdefinities correct zijn en uw externe gegevens overeenstemmen met deze definities.

Als een subset van externe gegevens records ongeldig is, kunt u ervoor kiezen deze records voor uw query's af te wijzen met behulp van de afwijzings opties in [Create External Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="inserting-data-into-a-production-table"></a>Gegevens in een productietabel invoegen

Een eenmalige laadtaak naar een kleine tabel met een [INSERT-instructie](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) of zelfs een periodieke herlaadtaak kan een acceptabel resultaat geven met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Het invoegen van singletons is echter niet zo efficiënt als bulksgewijs laden.

Als u de hele dag door duizenden of meerdere enkele gegevens wilt invoeren, voeg de gegevens dan samen tot een batch zodat deze bulksgewijs kunt laden.  Ontwikkel uw processen om de afzonderlijke gegevens aan een bestand toe te voegen en maak vervolgens een ander proces dat het bestand periodiek laadt.

## <a name="creating-statistics-after-the-load"></a>Statistieken maken na het laden

Voor optimale resultaten van uw query's is het belangrijk dat u statistieken maakt voor alle kolommen van alle tabellen nadat de gegevens voor het eerst zijn geladen of wanneer de gegevens substantieel zijn gewijzigd. Het maken van statistieken kan hand matig worden uitgevoerd of u kunt [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)inschakelen.

Zie [statistieken](sql-data-warehouse-tables-statistics.md) voor gedetailleerde uitleg van statistieken. In het volgende voor beeld ziet u hoe u hand matig statistieken maakt voor vijf kolommen van de tabel Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Opslag sleutels (poly base) draaien

Het is verstandig uit veiligheidsoverwegingen de toegangssleutel in de blob-opslag regelmatig te wijzigen. Er zijn twee opslagsleutels voor uw blob-opslagaccount, waarmee u de sleutels kunt wijzigen.

Sleutels van het Microsoft Azure Storage-account draaien:

Voor elk opslagaccount waarvan de sleutel is gewijzigd, moet u [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uitvoeren.

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

- Zie [ontwerp ELT voor Azure Synapse Analytics voor](design-elt-data-loading.md)meer informatie over de instructie copy of poly Base bij het ontwerpen van een proces voor extract, Load en Transform (ELT).
- Voor het laden van een zelf studie [gebruikt u de instructie Copy om gegevens van Azure Blob-opslag te laden naar Synapse SQL](load-data-from-azure-blob-storage-using-polybase.md).
- Zie [Uw workload controleren met DMV's](sql-data-warehouse-manage-monitor.md) voor het controleren van het laden van gegevens.
