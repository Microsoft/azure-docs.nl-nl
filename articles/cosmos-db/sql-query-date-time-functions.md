---
title: Datum-en tijd functies in Azure Cosmos DB query taal
description: Meer informatie over de functies datum en tijd van SQL-systeem in Azure Cosmos DB voor het uitvoeren van DateTime-en time stamp-bewerkingen.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cfa81b6ec5f10218a70de6b9b55e502d87898194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549171"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Datum-en tijd functies (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met de functies datum en tijd kunt u datum-en tijds tempel bewerkingen uitvoeren in Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Functies voor het verkrijgen van de datum en tijd

Met de volgende scalaire functies kunt u de huidige UTC-datum en-tijd in drie formulieren ophalen: een teken reeks die voldoet aan de ISO 8601-indeling, een numerieke tijds tempel waarvan de waarde het aantal milliseconden is verstreken sinds de UNIX-epoche, of numerieke maat streepjes waarvan de waarde het aantal 100 nano seconden Ticks is dat is verstreken sinds de UNIX-epoche :

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Functies voor het werken met datum/tijd-waarden

Met de volgende functies kunt u eenvoudig waarden voor DateTime, Time Stamp en Ticks bewerken:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>Volgende stappen

- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregaties](sql-query-aggregate-functions.md)
