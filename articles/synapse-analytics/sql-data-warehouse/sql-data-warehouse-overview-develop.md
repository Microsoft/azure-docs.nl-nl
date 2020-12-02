---
title: Resources voor het ontwikkelen van een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics
description: Ontwikkel concepten, ontwerp beslissingen, aanbevelingen en coderings technieken voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6b34c70b453c26fe27a51e1aa802564864640cb9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453677"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Ontwerp beslissingen en coderings technieken voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics 

 In dit artikel vindt u aanvullende bronnen om u te helpen beter inzicht te krijgen in belang rijke beslissingen, aanbevelingen en coderings technieken voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse.

## <a name="key-design-decisions"></a>Voornaamste ontwerp beslissingen

De volgende artikelen markeren concepten en ontwerp beslissingen voor het ontwikkelen van een gedistribueerd data warehouse met behulp van de toegewezen SQL-groep (voorheen SQL DW) in azure Synapse:

* [inbel](sql-data-warehouse-connect-overview.md)
* [gelijktijdigheid](resource-classes-for-workload-management.md)
* [transacties](sql-data-warehouse-develop-transactions.md)
* [door de gebruiker gedefinieerde schema's](sql-data-warehouse-develop-user-defined-schemas.md)
* [tabel distributie](sql-data-warehouse-tables-distribute.md)
* [tabel indexen](sql-data-warehouse-tables-index.md)
* [tabel partities](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [autoriteiten](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Aanbevelingen voor ontwikkeling en code ring

De volgende artikelen bevatten specifieke coderings technieken, tips en aanbevelingen voor het ontwikkelen van een toegewezen SQL-groep (voorheen SQL DW):

* [opgeslagen procedures](sql-data-warehouse-develop-stored-procedures.md)
* [Labels](sql-data-warehouse-develop-label.md)
* [Weergaven](performance-tuning-materialized-views.md)
* [tijdelijke tabellen](sql-data-warehouse-tables-temporary.md)
* [dynamische SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [lussen](sql-data-warehouse-develop-loops.md)
* [groeperen op Opties](sql-data-warehouse-develop-group-by-options.md)
* [variabele toewijzing](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Volgende stappen

Zie [T-SQL-instructies](sql-data-warehouse-reference-tsql-statements.md)voor meer informatie.
