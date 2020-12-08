---
title: Gedeelde database
description: Azure Synapse Analytics biedt een gedeeld metagegevensmodel. Wanneer u een database in een serverloze Apache Spark-pool maakt, is deze toegankelijk vanuit de serverloze SQL-pool en SQL-poolengines.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 14ae8b8670db53761d085bc019711e829633601b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451819"
---
# <a name="azure-synapse-analytics-shared-database"></a>Gedeelde Azure Synapse Analytics-databases

Met Azure Synapse Analytics kunnen de verschillende rekenengines voor de werkruimte databases en tabellen delen tussen de serverloze Apache Spark-pools en serverloze SQL-poolengine.

Een database die is gemaakt met een Spark-taak wordt weergegeven met dezelfde naam in alle huidige en toekomstige Spark-pools in de werkruimte, met inbegrip van de serverloze SQL-poolengine.

De standaard Spark-database met de naam `default` wordt ook weergegeven in de context van de serverloze SQL-pool met de naam `default`.

Omdat de databases asynchroon worden gesynchroniseerd naar de serverloze SQL-pool, is er een kleine vertraging voordat ze worden weergegeven.

## <a name="manage-a-spark-created-database"></a>Een met Spark gemaakte database beheren

Gebruik Spark voor het beheren van met Spark gemaakte databases. U kunt een database bijvoorbeeld verwijderen via een Spark-pooltaak en er tabellen in maken vanuit Spark.

Als u objecten in een met Spark gemaakte database maakt met een serverloze SQL-pool, of als u de database probeert te verwijderen, wordt de bewerking uitgevoerd. De oorspronkelijke Spark-database wordt echter niet gewijzigd.

## <a name="how-name-conflicts-are-handled"></a>Hoe naamconflicten worden verwerkt

Als de naam van een Spark-database een conflict veroorzaakt met de naam van een bestaande serverloze SQL-pooldatabase, wordt een achtervoegsel toegevoegd aan serverloze SQL-pool in de Spark-database. Het achtervoegsel in de serverloze SQL-pool is `_<workspace name>-ondemand-DefaultSparkConnector`.

Er wordt bijvoorbeeld een Spark-database met de naam `mydb` gemaakt in de Azure Synapse-werkruimte `myws` terwijl er al een serverloze SQL-pooldatabase met die naam bestaat. Dan moet er in de serverloze SQL-pool naar de Spark-database worden verwezen met de naam `mydb_myws-ondemand-DefaultSparkConnector`.

> [!CAUTION]
> Let op: u mag geen afhankelijkheid opnemen van dit gedrag.

## <a name="security-model"></a>Beveiligingsmodel

De Spark-databases en -tabellen, gecombineerd met hun gesynchroniseerde weergaven in de SQL-engine, worden beveiligd op het onderliggende opslagniveau.

De beveiligingsprincipal die een database maakt, wordt als de eigenaar van die database beschouwd en beschikt over alle rechten voor de database en de bijbehorende objecten.

Als u een beveiligingsprincipal, zoals een gebruiker of een beveiligingsgroep, toegang tot een database wilt geven, geeft u de juiste POSIX-map en bestandsmachtigingen op voor de onderliggende mappen en bestanden in de map `warehouse`. 

Als u bijvoorbeeld wilt dat een beveiligingsprincipal een tabel in een database leest, moeten alle mappen die beginnen bij de databasemap in de map `warehouse` de machtigingen `X` en `R` hebben die aan die beveiligingsprincipal zijn toegewezen. Daarnaast zijn voor bestanden (zoals de onderliggende gegevensbestanden van de tabel) `R` machtigingen vereist. 

Als voor een beveiligingsprincipal is vereist dat objecten moeten kunnen worden gemaakt of verwijderd in een database, zijn aanvullende `W`-machtigingen vereist voor de mappen en bestanden in de map `warehouse`.

## <a name="examples"></a>Voorbeelden

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Een Spark-database maken en verbinden met een serverloze SQL-pool

Maak eerst een nieuwe Spark-database met de naam `mytestdb` met behulp van een Spark-cluster dat u al hebt gemaakt in uw werkruimte. U kunt bijvoorbeeld een Spark C#-notebook gebruiken met de volgende .NET for Spark-instructie:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Na een korte vertraging wordt de database in de serverloze SQL-pool weergegeven. Voer bijvoorbeeld de volgende instructie uit vanuit de serverloze SQL-pool.

```sql
SELECT * FROM sys.databases;
```

Controleer of `mytestdb` is opgenomen in de resultaten.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over gedeelde Azure Synapse Analytics-metagegevens](overview.md)
- [Meer informatie over gedeelde Azure Synapse Analytics-metagegevenstabellen](table.md)
