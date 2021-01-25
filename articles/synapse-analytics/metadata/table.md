---
title: Gedeelde metagegevenstabellen
description: Azure Synapse Analytics biedt een gedeeld metagegevensmodel. Wanneer u een tabel in een serverloze Apache Spark-pool maakt, is deze toegankelijk vanuit de serverloze SQL-pool en de toegewezen SQL-pool zonder dat de gegevens worden gedupliceerd.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: b93addfe659847187dffe61f12f5a2bfac9dca21
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209624"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Gedeelde Azure Synapse Analytics-metagegevenstabellen


Met Azure Synapse Analytics kunnen de verschillende rekenengines voor de werkruimte databases en door Parquet ondersteunde tabellen delen tussen de Apache Spark-pools en de serverloze SQL-pool.

Wanneer een database is gemaakt met een Spark-taak, kunt u er tabellen in maken met Spark waarin Parquet als de opslagindeling wordt gebruikt. Deze tabellen zijn direct beschikbaar voor het uitvoeren van query's door een Spark-pool van de Azure Synapse-werkruimte. Ze kunnen ook worden gebruikt vanuit een van de Spark-taken waarvoor machtigingen gelden.

De door Spark gemaakte, beheerde en externe tabellen worden ook beschikbaar gemaakt als externe tabellen met dezelfde naam in de bijbehorende gesynchroniseerde database in de serverloze SQL-pool. In [Exposing a Spark table in SQL](#expose-a-spark-table-in-sql) (Een Spark-tabel in SQL weergeven) vindt u meer informatie over tabelsynchronisatie.

Omdat de tabellen asynchroon worden gesynchroniseerd naar de serverloze SQL-pool, is er een kleine vertraging voordat ze worden weergegeven.

## <a name="manage-a-spark-created-table"></a>Een met Spark gemaakte tabel beheren

Gebruik Spark voor het beheren van met Spark gemaakte databases. U kunt een database bijvoorbeeld verwijderen via een serverloze Apache Spark-pooltaak en er tabellen in maken vanuit Spark.

Als u objecten in een dergelijke database maakt met een serverloze SQL-pool, of als u de database probeert te verwijderen, mislukt de bewerking. De oorspronkelijke Spark-database kan niet worden gewijzigd via een serverloze SQL-pool.

## <a name="expose-a-spark-table-in-sql"></a>Een Spark-tabel beschikbaar maken in SQL

### <a name="shared-spark-tables"></a>Gedeelde Spark-tabellen

Spark biedt twee typen tabellen die Azure Synapse automatisch beschikbaar maakt in SQL:

- Beheerde tabellen

  Spark biedt veel opties voor het opslaan van gegevens in beheerde tabellen, zoals TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA en LIBSVM. Deze bestanden worden normaal gesproken opgeslagen in de map `warehouse` waarin de gegevens van de beheerde tabel worden opgeslagen.

- Externe tabellen

  Spark biedt ook manieren om externe tabellen te maken op basis van bestaande gegevens, door de optie `LOCATION` op te geven of de Hive-indeling te gebruiken. Dergelijke externe tabellen kunnen verschillende gegevensindelingen hebben, waaronder Parquet.

Azure Synapse deelt momenteel alleen beheerde en externe Spark-tabellen waarvan de gegevens worden opgeslagen in de Parquet-indeling met de SQL-engines. Tabellen die worden ondersteund door andere indelingen, worden niet automatisch gesynchroniseerd. U kunt dergelijke tabellen mogelijk expliciet als een externe tabel in uw eigen SQL-database synchroniseren als de SQL-engine de onderliggende indeling van de tabel ondersteunt.

### <a name="share-spark-tables"></a>Spark-tabellen delen

De deelbaar beheerde en externe Spark-tabellen die worden weergegeven in de SQL-engine als externe tabellen met de volgende eigenschappen:

- De gegevensbron van de externe SQL-tabel is de gegevensbron die de locatiemap van de Spark-tabel voorstelt.
- De bestandsindeling van de externe SQL-tabel is Parquet.
- De referenties voor toegang van de externe SQL-tabel zijn passthrough.

Aangezien alle Spark-tabelnamen geldige SQL-tabelnamen zijn en alle Spark-kolomnamen geldige SQL-kolomnamen zijn, worden de Spark-tabelnamen en -kolomnamen gebruikt voor de externe SQL-tabel.

Spark-tabellen bieden andere gegevenstypen dan de Synapse SQL-engines. In de volgende tabel worden de gegevenstypen van de Spark-tabel toegewezen aan de SQL-typen:

| Spark-gegevenstype | SQL-gegevenstype | Opmerkingen |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Met de sortering `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Wordt geserialiseerd in JSON met de sortering `Latin1_General_100_BIN2_UTF8` |
| `map`       |    `varchar(max)`   | Wordt geserialiseerd in JSON met de sortering `Latin1_General_100_BIN2_UTF8` |
| `struct`    |    `varchar(max)`   | Wordt geserialiseerd in JSON met de sortering `Latin1_General_100_BIN2_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Beveiligingsmodel

De Spark-databases en -tabellen en hun gesynchroniseerde weergaven in de SQL-engine worden beveiligd op het onderliggende opslagniveau. Omdat deze momenteel geen machtigingen hebben voor de objecten zelf, kunnen de objecten worden weergegeven in de objectverkenner.

De beveiligingsprincipal die een beheerde tabel maakt, wordt beschouwd als de eigenaar van die tabel en beschikt over alle rechten voor de tabel en de onderliggende mappen en bestanden. Daarnaast wordt de eigenaar van de database automatisch mede-eigenaar van de tabel.

Als u een externe Spark- of SQL-tabel met verificatiedoorvoer maakt, worden de gegevens alleen beveiligd op map- en bestandsniveau. Als iemand een query uitvoert op dit type externe tabel, wordt de inzender van de beveiligingsidentiteit van de query doorgegeven aan het bestandssysteem, waarna de toegangsrechten van die inzender worden gecontroleerd.

Zie [Gedeelde database van Azure Synapse Analytics](database.md)voor meer informatie over het instellen van machtigingen voor de mappen en bestanden.

## <a name="examples"></a>Voorbeelden

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Een beheerde tabel maken die wordt ondersteund door Parquet in Spark en een query uitvoeren vanuit een serverloze SQL-pool

In dit scenario hebt u een Spark-database met de naam `mytestdb`. Zie [Een Spark-database maken en verbinden met een serverloze SQL-pool](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Maak een beheerde Spark-tabel met SparkSQL door de volgende opdracht uit te voeren:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Met deze opdracht maakt u de tabel `myParquetTable` in de database `mytestdb`. Na een korte vertraging wordt de tabel in uw serverloze SQL-pool weergegeven. Voer bijvoorbeeld de volgende instructie uit vanuit uw serverloze SQL-pool.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Controleer of `myParquetTable` is opgenomen in de resultaten.

>[!NOTE]
>Een tabel die Parquet niet gebruikt als de opslagindeling, wordt niet gesynchroniseerd.

Voeg vervolgens enkele waarden in de tabel in vanuit Spark, bijvoorbeeld met de volgende C# Spark-instructies in een C#-notebook:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

Nu kunt u de gegevens van uw serverloze SQL-pool als volgt lezen:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Als het goed is, wordt de volgende rij als resultaat weergegeven:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Een externe tabel maken die wordt ondersteund door Parquet in Spark en een query uitvoeren vanuit een serverloze SQL-pool

In dit voorbeeld maakt u een externe Spark-tabel via de Parquet-gegevensbestanden die zijn gemaakt in het vorige voorbeeld voor de beheerde tabel.

Bijvoorbeeld met SparkSQL-uitvoering:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Vervang de tijdelijke aanduiding `<fs>` door de naam van het bestandssysteem dat het standaardbestandssysteem is voor de werkruimte en de tijdelijke aanduiding `<synapse_ws>` met de naam van de Synapse-werkruimte die u gebruikt om dit voorbeeld uit te voeren.

Met het vorige voorbeeld maakt u de tabel `myExtneralParquetTable` in de database`mytestdb`. Na een korte vertraging wordt de tabel in uw serverloze SQL-pool weergegeven. Voer bijvoorbeeld de volgende instructie uit vanuit uw serverloze SQL-pool.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Controleer of `myExternalParquetTable` is opgenomen in de resultaten.

Nu kunt u de gegevens van uw serverloze SQL-pool als volgt lezen:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Als het goed is, wordt de volgende rij als resultaat weergegeven:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over gedeelde Azure Synapse Analytics-metagegevens](overview.md)
- [Meer informatie over gedeelde Azure Synapse Analytics-metagegevensdatabase](database.md)


