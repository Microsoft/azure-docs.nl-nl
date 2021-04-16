---
title: Best practices voor laden van gegevens
description: Aanbevelingen en prestatieoptimalisaties voor het laden van gegevens in een toegewezen SQL-pool Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9fe7ef8e6ccbadd5e78de5bfd5f137132dbe6319
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567941"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-azure-synapse-analytics"></a>Best practices voor het laden van gegevens in een toegewezen SQL-pool Azure Synapse Analytics

In dit artikel vindt u aanbevelingen en prestatieoptimalisaties voor het laden van gegevens.

## <a name="prepare-data-in-azure-storage"></a>Gegevens voorbereiden in Azure Storage

Als u de latentie wilt minimaliseren, moet u de opslaglaag en uw toegewezen SQL-pool op een andere locatie opslaan.

Bij het exporteren van gegevens in een ORC-bestandsindeling kunnen er Java-geheugenfouten optreden wanneer er grote tekstkolommen zijn. U kunt deze beperking omzeilen door slechts een subset van de kolommen te exporteren.

PolyBase kan rijen met meer dan 1.000.000 bytes aan gegevens niet laden. Wanneer u gegevens in de tekstbestanden in Azure-blob-opslag of Azure Data Lake Store zet, moeten deze minder dan 1.000.000 bytes aan gegevens bevatten. Deze bytebeperking geldt ongeacht het tabelschema.

Alle bestandsindelingen hebben verschillende prestatiekenmerken. Gebruik voor het snelste laadproces gecomprimeerde tekstbestanden. Het verschil tussen UTF-8- en UTF-16-prestaties is minimaal.

Splits grote gecomprimeerde bestanden in kleinere gecomprimeerde bestanden.

## <a name="run-loads-with-enough-compute"></a>Laadbelastingen uitvoeren met voldoende rekenkracht

Voer voor de hoogste laadsnelheid slechts één taak tegelijk uit. Voer een zo klein mogelijk aantal laadtaken tegelijk uit als dit niet haalbaar is. Als u een grote laadklus verwacht, kunt u overwegen om uw toegewezen SQL-pool op te schalen vóór de belasting.

Als u loads wilt uitvoeren met geschikte rekenresources, maakt u gebruikers voor het laadproces die zijn aangewezen voor het uitvoeren van loads. Wijs elke gebruiker voor het laden toe aan een specifieke resourceklasse of workloadgroep. Als u een load wilt uitvoeren, moet u zich aanmelden als een van de gebruikers voor het laden en vervolgens de belasting uitvoeren. De load wordt uitgevoerd met de resourceklasse van de gebruiker.  Deze methode is eenvoudiger dan de resourceklasse van een gebruiker aanpassen om te voldoen aan de huidige benodigde resourceklasse.

### <a name="create-a-loading-user"></a>Een gebruiker voor laden maken

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

Als u een belasting wilt uitvoeren met resources voor de staticRC20-resourceklassen, moet u zich aanmelden als LoaderRC20 en de belasting uitvoeren.

Voer loads bij voorkeur uit onder statische en niet onder dynamische resourceklassen. Het gebruik van de statische resourceklassen garandeert dezelfde resources, ongeacht uw [datawarehouse-eenheden.](resource-consumption-models.md) Als u een dynamische resourceklasse gebruikt, variëren de resources afhankelijk van uw serviceniveau. Voor dynamische klassen betekent een lager serviceniveau dat u waarschijnlijk een grotere resourceklasse moet gebruiken voor uw gebruiker van het laadproces.

## <a name="allow-multiple-users-to-load"></a>Meerdere gebruikers toestaan te laden

Vaak is het nodig dat meerdere gebruikers gegevens kunnen laden in een datawarehouse. Voor het laden [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) zijn CONTROL-machtigingen van de database vereist.  De CONTROL-machtiging biedt beheertoegang tot alle schema's. Mogelijk wilt u niet alle gebruikers die laadtaken uitvoeren, beheertoegang tot alle schema's verlenen. Als u machtigingen wilt beperken, kunt u de instructie DENY CONTROL gebruiken.

Denk bijvoorbeeld aan databaseschema's, schema_A voor afdeling A, en schema_B voor afdeling B. Laat databasegebruikers gebruiker_A en gebruiker_B gebruikers zijn voor PolyBase die respectievelijk laden in afdeling A en B. Beide zijn voorzien van databasemachtigingen voor CONTROL. De makers van schema A en B vergrendelen nu hun schema's met DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A en user_B zijn nu vergrendeld voor het schema van de andere afdeling.

## <a name="load-to-a-staging-table"></a>Laden naar een faseringstabel

De hoogste laadsnelheid voor het verplaatsen van gegevens naar een datawarehousetabel kunt u verkrijgen door gegevens te laden naar een tijdelijke tabel.  Definieer de faseringstabel als een heap en gebruik round-robin voor de distributieoptie.

Bedenk dat laden meestal een proces met twee stappen is waarin u eerst naar een tijdelijke tabel laadt en de gegevens vervolgens in een productiedatawarehousetabel invoegt. Als de productietabel een hash-distributiepunt gebruikt, is de totale tijd voor het laden en invoegen mogelijk sneller als u de faseringstabel met de hash-distributie definieert. Het laden naar de faseringstabel duurt langer, maar de tweede stap van het invoegen van de rijen in de productietabel leidt niet tot de verplaatsing van gegevens in de distributies.

## <a name="load-to-a-columnstore-index"></a>Laden naar een columnstore-index

Columnstore-indexen vereisen grote hoeveelheden geheugen voor het comprimeren van gegevens tot hoogwaardige rijgroepen. Voor de beste compressie en efficiëntie van de index moet de columnstore-index maximaal 1.048.576 rijen in elke rijgroep comprimeren. Bij geheugenbelasting kan het zijn dat de columnstore-index de maximale compressiesnelheden niet kan halen. Dit heeft gevolgen voor de queryprestaties. Zie voor gedetailleerde informatie [Columnstore geheugenoptimalisaties](data-load-columnstore-compression.md).

- Zorg dat de gebruiker van het laadproces voldoende geheugen heeft om maximale compressiesnelheden te bereiken. Gebruik hiervoor gebruikers voor het laadproces die lid zijn van een middelgrote of grote resourceklasse.
- Laad genoeg rijen om nieuwe rijgroepen volledig te vullen. Tijdens het bulksgewijs laden worden elke 1.048.576 rijen als een volledige rijgroep rechtstreeks in de columnstore gecomprimeerd. Laadtaken met minder dan 102.400 rijen verzenden de rijen naar de deltastore waarin rijen zijn ondergebracht in een b-tree-index. Als u te weinig rijen laadt, gaan deze mogelijk allemaal naar de deltastore en worden ze niet direct naar columnstore-indeling gecomprimeerd.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Batchgrootte vergroten bij gebruik van SQLBulkCopy-API of BCP

Zoals eerder vermeld, biedt het laden met PolyBase de hoogste doorvoer met Synapse SQL-pool. Als u PolyBase niet kunt gebruiken om te laden en de SQLBulkCopy-API (of BCP) moet gebruiken, kunt u overwegen om de batchgrootte te vergroten voor een betere doorvoer. Een goede vuistregel is een batchgrootte tussen 100.000 en 1 miljoen rijen.

## <a name="manage-loading-failures"></a>Laadfouten beheren

Een load met behulp van een externe tabel kan mislukken met de fout *Query afgebroken--de maximale weigeringsdrempelwaarde is bereikt tijdens het lezen vanuit een externe bron*. Dit bericht geeft aan dat uw externe gegevens vervuilde records bevatten. Een gegevensrecord wordt als 'vervuild' beschouwd als de gegevenstypen en het aantal kolommen niet overeenkomen met de kolomdefinities van de externe tabel of als de gegevens niet overeenkomen met de externe bestandsindeling.

U kunt vervuilde records voorkomen door ervoor te zorgen dat uw externe tabel- en bestandindelingsdefinities correct zijn en uw externe gegevens overeenstemmen met deze definities. Als een subset van externe gegevensrecords ongeldig is, kunt u ervoor kiezen deze records voor uw query's te weigeren door gebruik te maken van de weigeringsopties in CREATE EXTERNAL TABLE.

## <a name="insert-data-into-a-production-table"></a>Gegevens invoegen in een productietabel

Een eenmalige laadtaak naar een kleine tabel met een [INSERT-instructie](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true) of zelfs een periodieke herlaadtaak kan een acceptabel resultaat geven met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Singleton-invoegingen zijn echter niet zo efficiënt als het uitvoeren van bulksgewijs laden.

Als u de hele dag door duizenden of meerdere enkele gegevens wilt invoeren, voeg de gegevens dan samen tot een batch zodat deze bulksgewijs kunt laden.  Ontwikkel uw processen om de afzonderlijke gegevens aan een bestand toe te voegen en maak vervolgens een ander proces dat het bestand periodiek laadt.

## <a name="create-statistics-after-the-load"></a>Statistieken maken na het laden

Om de queryprestaties te verbeteren, is het belangrijk om statistieken te maken voor alle kolommen van alle tabellen na de eerste keer laden, of grote wijzigingen in de gegevens. Statistieken maken kan handmatig worden uitgevoerd of u kunt statistieken [voor automatisch maken inschakelen.](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Zie [statistieken](develop-tables-statistics.md) voor gedetailleerde uitleg van statistieken. In het volgende voorbeeld ziet u hoe u handmatig statistieken maakt voor vijf kolommen van de Customer_Speed tabel.

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

Voor elk opslagaccount waarvan de sleutel is gewijzigd, moet u [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) uitvoeren.

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

- Zie ELT ontwerpen voor meer informatie over PolyBase en het ontwerpen van een ELT-proces (Extract, Load, and Transform) [Azure Synapse Analytics.](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Voor een zelfstudie over het [laden gebruikt u PolyBase om gegevens uit Azure Blob Storage te laden naar Azure Synapse Analytics.](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
- Zie [Uw workload controleren met DMV's](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor het controleren van het laden van gegevens.