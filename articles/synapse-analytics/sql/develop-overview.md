---
title: Resources voor het ontwikkelen Synapse SQL functies
description: Ontwikkelconcepten, ontwerpbeslissingen, aanbevelingen en coderingstechnieken voor Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4d842414d3046692c982ca3203957a96f8a01b37
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377327"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Ontwerpbeslissingen en coderingstechnieken voor Synapse SQL functies in Azure Synapse Analytics
In dit artikel vindt u een lijst met resources voor toegewezen SQL-pool en serverloze SQL-poolfuncties van Synapse SQL. De aanbevolen artikelen zijn opgesplitst in twee secties: Belangrijke ontwerpbeslissingen en ontwikkelings- en coderingstechnieken.

Het doel van deze artikelen is u te helpen bij het ontwikkelen van de optimale technische benadering voor de Synapse SQL onderdelen binnen Azure Synapse Analytics.

## <a name="key-design-decisions"></a>Belangrijke ontwerpbeslissingen
In de onderstaande artikelen worden concepten en ontwerpbeslissingen voor Synapse SQL beschreven:

| Artikel | toegewezen SQL-pool | serverloze SQL-pool |
| ------- | -------- | ------------- |
| [Verbindingen](connect-overview.md)                    | Ja | Ja |
| [Resource-klassen en gelijktijdigheid](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Ja    | Nee |
| [Transacties](develop-transactions.md)              | Ja | Nee |
| [Door de gebruiker gedefinieerde schema's](develop-user-defined-schemas.md) | Ja | Ja |
| [Tabeldistributie](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Ja | Nee |
| [Tabelindexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Ja | Nee |
| [Tabelpartities](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Ja | Nee |
| [Statistieken](develop-tables-statistics.md)            | Ja | Ja |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Ja | Nee |
| [Externe tabellen](develop-tables-external-tables.md) | Ja | Ja |
| [CETAS](develop-tables-cetas.md)                     | Ja | Ja |


## <a name="recommendations"></a>Aanbevelingen

Hieronder vindt u essentiële artikelen waarin specifieke coderingstechnieken, tips en aanbevelingen voor ontwikkeling worden benadrukt:

| Artikel | toegewezen SQL-pool | serverloze SQL-pool |
| ------- | -------- | ------------- |
| [Opgeslagen procedures](develop-stored-procedures.md)  | Ja                | Ja                      |
| [Labels](develop-label.md)                           | Ja                | Nee                      |
| [Weergaven](develop-views.md)                             | Ja                | Ja                     |
| [Tijdelijke tabellen](develop-tables-temporary.md)       | Ja                | Ja                     |
| [Dynamic SQL](develop-dynamic-sql.md)                 | Ja                | Ja                     |
| [Lussen](develop-loops.md)                         | Ja                | Ja                     |
| [Groeperen op opties](develop-group-by-options.md)       | Ja                | Nee                      |
| [Variabele toewijzing](develop-variable-assignment.md) | Ja                | Ja                     |

## <a name="next-steps"></a>Volgende stappen
Zie [T-SQL-instructies voor SQL-pool voor meer naslaginformatie.](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

