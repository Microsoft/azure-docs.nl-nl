---
title: Zelfondersteuning voor serverloze SQL-pools
description: Deze sectie bevat informatie die u kan helpen bij het oplossen van problemen met een serverloze SQL-pool.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f94134b2a06155b1b1f390175578e501a840038b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669420"
---
# <a name="self-help-for-serverless-sql-pool"></a>Zelfondersteuning voor serverloze SQL-pools

Dit artikel bevat informatie over het oplossen van de meest voorkomende problemen met een serverloze SQL-pool in Azure Synapse Analytics.

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Serverloze SQL-pool is niet beschikbaar in Synapse Studio

Als Synapse Studio geen verbinding kan maken met een serverloze SQL-pool, ziet u dat de serverloze SQL-pool niet beschikbaar is of dat de status 'Offline' wordt weergegeven. Dit probleem treedt meestal op in een van de volgende gevallen:

1) Uw netwerk verhindert communicatie met de back-end van Azure Synapse. Het meest voorkomende probleem is dat poort 1443 wordt geblokkeerd. Als u de serverloze SQL-pool wilt laten werken, heft u de blokkering van deze poort op. Er kunnen ook andere problemen zijn waardoor de serverloze SQL-pool niet werkt, [raadpleeg de volledige gids voor probleemoplossing voor meer informatie](../troubleshoot/troubleshoot-synapse-studio.md).
2) U bent niet gemachtigd om u aan te melden bij de serverloze SQL-pool. Als u toegang wilt krijgen, moet een van de beheerders van de Azure Synapse-werkruimte u toevoegen aan de rol Werkruimtebeheerder of SQL-beheerder. [Bekijk de volledige handleiding over toegangsbeheer voor meer informatie](../security/synapse-workspace-access-control-overview.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>De query is mislukt omdat het bestand niet kan worden geopend

Als uw query mislukt met het foutbericht 'Bestand kan niet worden geopend omdat het niet bestaat of wordt gebruikt door een ander proces' en u zeker weet dat het bestand bestaat en dat het niet wordt gebruikt door een ander proces, betekent dit dat de serverloze SQL-pool geen toegang kan krijgen tot het bestand. Dit probleem treedt meestal op omdat uw Azure Active Directory-identiteit geen rechten heeft om het bestand te openen. Een serverloze SQL-pool probeert standaard toegang tot het bestand met uw Azure Active Directory-identiteit te krijgen. Om dit probleem op te lossen, moet u de juiste rechten hebben voor toegang tot het bestand. De eenvoudigste manier is om uzelf de rol 'Bijdrager voor opslagblobgegevens' toe te kennen voor het opslagaccount waarnaar u een query wilt uitvoeren. 
- [Ga naar de volledige handleiding over toegangsbeheer voor opslag via Azure Active Directory voor meer informatie](../../storage/common/storage-auth-aad-rbac-portal.md). 
- [Ga naar beheer toegang tot opslag accounts voor serverloze SQL-groepen in azure Synapse Analytics](develop-storage-files-storage-access-control.md)

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>De query is mislukt omdat deze niet kan worden uitgevoerd vanwege huidige resourcebeperkingen 

Als uw query mislukt met het foutbericht 'Deze query kan niet worden uitgevoerd vanwege huidige resourcebeperkingen', betekent dit dat de serverloze SQL-pool deze niet kan uitvoeren vanwege resourcebeperkingen: 

- Zorg dat u gegevenstypen van redelijke grootte gebruikt. Geef ook een schema op voor Parquet-bestanden voor kolommen met tekenreeksen, omdat deze standaard VARCHAR(8000) zijn. 

- Als uw query CSV-bestanden aanroept, kunt u overwegen om [statistieken te maken](develop-tables-statistics.md#statistics-in-serverless-sql-pool). 

- Raadpleeg de pagina over [best practices voor prestaties voor uw serverloze SQL-pool](best-practices-sql-on-demand.md) om uw query te optimaliseren.  

## <a name="create-statement-is-not-supported-in-master-database"></a>De instructie CREATE wordt niet ondersteund in de hoofddatabase

Uw query mislukt met het foutbericht:

> Kan de query niet uitvoeren. Fout: CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT wordt niet ondersteund in de hoofddatabase. 

Dit betekent dat de hoofddatabase in de serverloze SQL-pool geen ondersteuning biedt voor het maken van:
  - Externe tabellen
  - Externe gegevensbronnen
  - Referenties van databasebereik
  - Externe bestandsindelingen

Oplossing:

  1. Een gebruikersdatabase maken:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Voer de instructie create uit in de context van <DATABASE_NAME> die eerder voor de hoofddatabase is mislukt. 
  
  Voorbeeld voor het maken van een externe bestandsindeling:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van een serverloze SQL-pool:

- [Query's uitvoeren op één CSV-bestand](query-single-csv-file.md)

- [Query's uitvoeren op mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)

- [Query's uitvoeren op specifieke bestanden](query-specific-files.md)

- [Query's uitvoeren op Parquet-bestanden](query-parquet-files.md)

- [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md)

- [Query's uitvoeren op JSON-bestanden](query-json-files.md)

- [Weergaven maken en gebruiken](create-use-views.md)

- [Externe tabellen maken en gebruiken](create-use-external-tables.md)

- [Queryresultaten opslaan in opslag](create-external-table-as-select.md)
