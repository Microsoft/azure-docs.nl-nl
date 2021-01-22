---
title: Wat is een toegewezen SQL-pool (voorheen SQL DW)?
description: Een toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics is de functionaliteit voor datawarehousing voor ondernemingen in Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9077ce35065b1bf45646496cc4c43d6def82d958
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685222"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Wat is een toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics?

Azure Synapse Analytics is een analyseservice die datawarehousing voor ondernemingen en big data-analyses combineert. Toegewezen SQL-pool (voorheen SQL DW) verwijst naar de functies voor datawarehousing voor ondernemingen, die beschikbaar zijn in Azure Synapse Analytics.



![Toegewezen SQL-pool (voorheen SQL DW) in relatie tot Azure Synapse](./media/sql-data-warehouse-overview-what-is/dedicated-sql-pool.png)



Toegewezen SQL-pool (voorheen SQL DW) vertegenwoordigt een verzameling analytische resources die worden ingericht tijdens het gebruik van Synapse SQL. De grootte van een toegewezen SQL-pool (voorheen SQL DW) wordt bepaald door DWU’s (Data Warehousing Unit).

Zodra uw toegewezen SQL-pool is gemaakt, kunt u big data met eenvoudige [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL-query's importeren en vervolgens de kracht van de gedistribueerde-queryengine gebruiken om krachtige analyses uit te voeren. Terwijl u de gegevens integreert en analyseert, wordt de toegewezen SQL-pool (voorheen SQL DW) de centrale bron waarop uw bedrijf kan rekenen voor het verkrijgen van snellere en meer robuuste inzichten.

> [!NOTE]
>Verken de [Documentatie voor Azure Synapse Analytics](../overview-what-is.md).
> 

## <a name="key-component-of-a-big-data-solution"></a>Belangrijk onderdeel van een big data-oplossing

Datawarehousing is een belangrijk onderdeel van een end-to-end big data-oplossing in de cloud.

![Data Warehouse-oplossing](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

In een cloudgegevensoplossing zijn gegevens opgenomen in big data-archieven uit verschillende bronnen. Nadat de gegevens zijn opgenomen in een big data-archief, worden ze met behulp van Hadoop-, Spark- en machine learning-algoritmen voorbereid en getraind. Wanneer de gegevens klaar zijn voor complexe analyse, maakt de toegewezen SQL-pool gebruik van PolyBase om query's uit te voeren op de big data-archieven. PolyBase gebruikt standaard T-SQL-query's om de gegevens op te slaan in de tabellen van de toegewezen SQL-pool (voorheen SQL DW).

In een toegewezen SQL-pool (voorheen SQL DW) worden gegevens opgeslagen in relationele tabellen met opslag in kolommen. Deze indeling beperkt de kosten voor gegevensopslag aanzienlijk en verbetert de prestaties van query's. Wanneer de gegevens zijn opgeslagen, kunt u op grote schaal analyses uitvoeren. Vergeleken met traditionele databasesystemen duurt het analyseren van query's seconden in plaats van minuten, of uren in plaats van dagen.

De analyseresultaten kunnen worden verzonden naar wereldwijde rapportagedatabases of toepassingen. Bedrijfsanalisten kunnen vervolgens inzichten verkrijgen om goed gefundeerde zakelijke beslissingen te maken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse-architectuur](massively-parallel-processing-mpp-architecture.md) verkennen
- Snel [een toegewezen SQL-pool maken](create-data-warehouse-portal.md)
- [Voorbeeldgegevens laden](./load-data-from-azure-blob-storage-using-copy.md).
- [Video's verkennen](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

Of bekijk enkele andere Azure Synapse-resources.

- Zoeken in [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Functieaanvragen](https://feedback.azure.com/forums/307516-sql-data-warehouse) indienen
- [Een ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
- Zoeken op de [Microsoft Q&A-vragenpagina](/answers/topics/azure-synapse-analytics.html)
- Zoeken in het [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw)