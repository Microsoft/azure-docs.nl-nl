---
title: Best practices voor laden van gegevens
description: Aanbevelingen en prestatie optimalisaties voor het laden van gegevens in een exclusieve SQL-groep Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 31014d336b5122251cf8be4a166520064776fce3
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118163"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-azure-synapse-analytics"></a>Aanbevolen procedures voor het laden van gegevens in een toegewezen SQL-groep Azure Synapse Analytics

In dit artikel vindt u aanbevelingen en prestatie optimalisaties voor het laden van gegevens.

## <a name="prepare-data-in-azure-storage"></a>Gegevens voorbereiden in Azure Storage

Als u de latentie wilt minimaliseren, gaat u naar uw opslaglaag en uw toegewezen SQL-groep.

Bij het exporteren van gegevens in een ORC-bestandsindeling kunnen er Java-geheugenfouten optreden wanneer er grote tekstkolommen zijn. U kunt deze beperking omzeilen door slechts een subset van de kolommen te exporteren.

Poly Base kan rijen met meer dan 1.000.000 bytes aan gegevens niet laden. Wanneer u gegevens in de tekstbestanden in Azure-blob-opslag of Azure Data Lake Store zet, moeten deze minder dan 1.000.000 bytes aan gegevens bevatten. Deze bytebeperking geldt ongeacht het tabelschema.

Alle bestandsindelingen hebben verschillende prestatiekenmerken. Gebruik voor het snelste laadproces gecomprimeerde tekstbestanden. Het verschil tussen UTF-8- en UTF-16-prestaties is minimaal.

Splits grote gecomprimeerde bestanden in kleinere gecomprimeerde bestanden.

## <a name="run-loads-with-enough-compute"></a>Laden uitvoeren met voldoende reken kracht

Voer voor de hoogste laadsnelheid slechts één taak tegelijk uit. Voer een zo klein mogelijk aantal laadtaken tegelijk uit als dit niet haalbaar is. Als u een grote laad taak verwacht, kunt u overwegen om uw toegewezen SQL-groep vóór de belasting te schalen.

Als u loads wilt uitvoeren met geschikte rekenresources, maakt u gebruikers voor het laadproces die zijn aangewezen voor het uitvoeren van loads. Wijs elke laad gebruiker toe aan een specifieke resource klasse of werkbelasting groep. Als u een belasting wilt uitvoeren, meldt u zich aan als een van de laad gebruikers en voert u de belasting uit. De load wordt uitgevoerd met de resourceklasse van de gebruiker.  Deze methode is eenvoudiger dan de resourceklasse van een gebruiker aanpassen om te voldoen aan de huidige benodigde resourceklasse.

### <a name="create-a-loading-user"></a>Een laad gebruiker maken

In dit voorbeeld wordt een gebruiker voor het laadproces gemaakt voor de resourceklasse staticrc20. De eerste stap is **verbinding maken met de master** en een aanmelding maken.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Maak verbinding met het datawarehouse en maak een gebruiker. In de volgende code wordt ervan uitgegaan dat u verbonden bent met de database mySampleDataWarehouse. U ziet hoe u een gebruiker maakt met de naam LoaderRC20 en hoe u die gebruiker machtiging voor het beheer van een database geeft. Vervolgens wordt de gebruiker toegevoegd als lid van de databaserol staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Als u een belasting wilt uitvoeren met resources voor de resource klassen staticRC20, meldt u zich aan als LoaderRC20 en voert u de belasting uit.

Voer loads bij voorkeur uit onder statische en niet onder dynamische resourceklassen. Het gebruik van de statische resource klassen garandeert dezelfde bronnen, ongeacht uw [Data Warehouse-eenheden](resource-consumption-models.md). Als u een dynamische resourceklasse gebruikt, variëren de resources afhankelijk van uw serviceniveau. Voor dynamische klassen betekent een lager serviceniveau dat u waarschijnlijk een grotere resourceklasse moet gebruiken voor uw gebruiker van het laadproces.

## <a name="allow-multiple-users-to-load"></a>Meerdere gebruikers toestaan om te laden

Vaak is het nodig dat meerdere gebruikers gegevens kunnen laden in een datawarehouse. Bij het laden met de [Create Table als Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) zijn machtigingen vereist van de data base.  De CONTROL-machtiging biedt beheertoegang tot alle schema's. Mogelijk wilt u niet alle gebruikers die laadtaken uitvoeren, beheertoegang tot alle schema's verlenen. Als u machtigingen wilt beperken, kunt u de instructie DENY CONTROL gebruiken.

Denk bijvoorbeeld aan databaseschema's, schema_A voor afdeling A, en schema_B voor afdeling B. Laat databasegebruikers gebruiker_A en gebruiker_B gebruikers zijn voor PolyBase die respectievelijk laden in afdeling A en B. Beide zijn voorzien van databasemachtigingen voor CONTROL. De makers van schema A en B vergrendelen nu hun schema's met DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A en user_B zijn nu uitgesloten van het schema van de andere afdeling.

## <a name="load-to-a-staging-table"></a>Laden naar een faseringstabel

De hoogste laadsnelheid voor het verplaatsen van gegevens naar een datawarehousetabel kunt u verkrijgen door gegevens te laden naar een tijdelijke tabel.  Definieer de faseringstabel als een heap en gebruik round-robin voor de distributieoptie.

Bedenk dat laden meestal een proces met twee stappen is waarin u eerst naar een tijdelijke tabel laadt en de gegevens vervolgens in een productiedatawarehousetabel invoegt. Als de productietabel een hash-distributiepunt gebruikt, is de totale tijd voor het laden en invoegen mogelijk sneller als u de faseringstabel met de hash-distributie definieert. Het laden naar de faseringstabel duurt langer, maar de tweede stap van het invoegen van de rijen in de productietabel leidt niet tot de verplaatsing van gegevens in de distributies.

## <a name="load-to-a-columnstore-index"></a>Laden naar een column store-index

Columnstore-indexen vereisen grote hoeveelheden geheugen voor het comprimeren van gegevens tot hoogwaardige rijgroepen. Voor de beste compressie en efficiëntie van de index moet de columnstore-index maximaal 1.048.576 rijen in elke rijgroep comprimeren. Bij geheugenbelasting kan het zijn dat de columnstore-index de maximale compressiesnelheden niet kan halen. Dit heeft gevolgen voor de query prestaties. Zie voor gedetailleerde informatie [Columnstore geheugenoptimalisaties](data-load-columnstore-compression.md).

- Zorg dat de gebruiker van het laadproces voldoende geheugen heeft om maximale compressiesnelheden te bereiken. Gebruik hiervoor gebruikers voor het laadproces die lid zijn van een middelgrote of grote resourceklasse.
- Laad genoeg rijen om nieuwe rijgroepen volledig te vullen. Tijdens een bulksgewijs laden worden elke 1.048.576-rijen direct in de column Store gecomprimeerd als een volledig Rijg roep. Laadtaken met minder dan 102.400 rijen verzenden de rijen naar de deltastore waarin rijen zijn ondergebracht in een b-tree-index. Als u te weinig rijen laadt, gaan deze mogelijk allemaal naar de deltastore en worden ze niet direct naar columnstore-indeling gecomprimeerd.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Batch grootte verg Roten bij gebruik van SQLBulkCopy-API of BCP

Zoals eerder is vermeld, biedt het laden met poly base de hoogste door Voer met Synapse SQL-pool. Als u geen poly Base kunt gebruiken om te laden en u de SQLBulkCopy-API (of BCP) moet gebruiken, kunt u overwegen om de Batch grootte te verg Roten voor een betere door voer. Dit is een goede regel van een miniatuur is een batch grootte tussen 100.000 en 1M rijen.

## <a name="manage-loading-failures"></a>Laad fouten beheren

Een load met behulp van een externe tabel kan mislukken met de fout *Query afgebroken--de maximale weigeringsdrempelwaarde is bereikt tijdens het lezen vanuit een externe bron*. Dit bericht geeft aan dat uw externe gegevens vervuilde records bevatten. Een gegevensrecord wordt als 'vervuild' beschouwd als de gegevenstypen en het aantal kolommen niet overeenkomen met de kolomdefinities van de externe tabel of als de gegevens niet overeenkomen met de externe bestandsindeling.

U kunt vervuilde records voorkomen door ervoor te zorgen dat uw externe tabel- en bestandindelingsdefinities correct zijn en uw externe gegevens overeenstemmen met deze definities. Als een subset van externe gegevensrecords ongeldig is, kunt u ervoor kiezen deze records voor uw query's te weigeren door gebruik te maken van de weigeringsopties in CREATE EXTERNAL TABLE.

## <a name="insert-data-into-a-production-table"></a>Gegevens in een productie tabel invoegen

Een eenmalige laadtaak naar een kleine tabel met een [INSERT-instructie](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) of zelfs een periodieke herlaadtaak kan een acceptabel resultaat geven met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Singleton-invoeg bladen zijn echter niet zo efficiënt als het uitvoeren van een bulksgewijs laden.

Als u de hele dag door duizenden of meerdere enkele gegevens wilt invoeren, voeg de gegevens dan samen tot een batch zodat deze bulksgewijs kunt laden.  Ontwikkel uw processen om de afzonderlijke gegevens aan een bestand toe te voegen en maak vervolgens een ander proces dat het bestand periodiek laadt.

## <a name="create-statistics-after-the-load"></a>Statistieken maken na het laden

Om de query prestaties te verbeteren, is het belang rijk dat u statistieken maakt voor alle kolommen van alle tabellen na de eerste belasting, of dat er grote wijzigingen in de gegevens optreden. Het maken van statistieken kan hand matig worden uitgevoerd of u kunt [automatisch gemaakte statistieken](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)inschakelen.

Zie [statistieken](develop-tables-statistics.md) voor gedetailleerde uitleg van statistieken. In het volgende voor beeld ziet u hoe u hand matig statistieken maakt voor vijf kolommen van de tabel Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Opslagsleutels draaien

Het is verstandig uit veiligheidsoverwegingen de toegangssleutel in de blob-opslag regelmatig te wijzigen. Er zijn twee opslagsleutels voor uw blob-opslagaccount, waarmee u de sleutels kunt wijzigen.

Sleutels van het Microsoft Azure Storage-account draaien:

Voor elk opslagaccount waarvan de sleutel is gewijzigd, moet u [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) uitvoeren.

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

- Zie [ontwerp ELT voor Azure Synapse Analytics voor](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)meer informatie over poly base en het ontwerpen van een uitpak-, laad-en transformatie proces.
- Gebruik poly Base voor het laden van [gegevens uit Azure Blob-opslag naar Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)voor een zelf studie die u kunt laden.
- Zie [Uw workload controleren met DMV's](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor het controleren van het laden van gegevens.