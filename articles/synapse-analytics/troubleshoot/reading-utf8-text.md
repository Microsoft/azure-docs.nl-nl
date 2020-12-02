---
title: 'Problemen oplossen: UTF-8-tekst van CSV-of PARQUET-bestanden lezen met serverloze SQL-groep'
description: UTF-8-tekst lezen vanuit CSV-of PARQUET-bestanden met serverloze SQL-groep in azure Synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 11/24/2020
ms.openlocfilehash: 238880cb3f3628df7591e8d08e3057ebfd885900
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466423"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Problemen met het lezen van UTF-8-tekst van CSV-of Parquet-bestanden oplossen met serverloze SQL-groep in azure Synapse Analytics

Dit artikel bevat stappen voor het oplossen van problemen met het lezen van UTF-8-tekst vanuit CSV-of Parquet-bestanden met serverloze SQL-groep in azure Synapse Analytics.

Als UTF-8-tekst uit een CSV-of PARQUET-bestand wordt gelezen met serverloze SQL-pool, worden sommige speciale tekens zoals ü en ö onjuist geconverteerd als de query VARCHAR kolommen met niet-UTF8-sorteringen retourneert. Dit is een bekend probleem in SQL Server en Azure SQL. Niet-UTF8-sortering is de standaard waarde in Synapse SQL, zodat klant query's worden beïnvloed. Klanten die gebruikmaken van standaard-Engelse tekens en een subset van uitgebreide Latijnse tekens, merken mogelijk niet de conversie fouten. De onjuiste conversie wordt gedetailleerd uitgelegd in [altijd UTF-8-sorteringen gebruiken om UTF-8-tekst te lezen in serverloze SQL-groep](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Tijdelijke oplossing

De tijdelijke oplossing voor dit probleem is om altijd UTF-8-sortering te gebruiken bij het lezen van UTF-8-tekst van CSV-of PARQUET-bestanden.

-   In veel gevallen hoeft u alleen UTF8-sortering in te stellen voor de data base (bewerking van meta gegevens).
-   Als u geen UTF8-sortering hebt opgegeven op externe tabellen die UTF8-gegevens lezen, moet u de beïnvloede externe tabellen opnieuw maken en de UTF8-sortering instellen op VARCHAR kolommen (meta gegevens bewerking).


## <a name="next-steps"></a>Volgende stappen

* [CETAS met Synapse SQL](../sql/develop-tables-cetas.md)
* [Quickstart: Serverloze SQL-pools gebruiken](../quickstart-sql-on-demand.md)
