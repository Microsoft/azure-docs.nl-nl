---
title: Wiskundige functies in Azure Cosmos DB query taal
description: Meer informatie over de wiskundige functies in Azure Cosmos DB voor het uitvoeren van een berekening, op basis van invoer waarden die zijn opgegeven als argumenten en het retour neren van een numerieke waarde.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 63d349c8cfff52932d51ce7143aba33521c43890
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549183"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Wiskundige functies (Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met elke wiskundige functie wordt een berekening op basis van de opgegeven invoerwaarden uitgevoerd en een numerieke waarde geretourneerd.

U kunt query's uitvoeren zoals in het volgende voor beeld:

```sql
    SELECT VALUE ABS(-4)
```

Het resultaat is:

```json
    [4]
```

## <a name="functions"></a>Functions

De volgende ingebouwde wiskundige functies voeren een berekening uit, meestal op basis van invoer argumenten, en retour neren een numerieke expressie:
 
* [ABS](sql-query-abs.md)
* [ACOS](sql-query-acos.md)
* [ASIN](sql-query-asin.md)
* [ATAN](sql-query-atan.md)
* [ATN2](sql-query-atn2.md)
* [CEILING](sql-query-ceiling.md)
* [COS](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [DEGREES](sql-query-degrees.md)
* [EXP](sql-query-exp.md)
* [FLOOR](sql-query-floor.md)
* [LOG](sql-query-log.md)
* [LOG10](sql-query-log10.md)
* [PI](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [RADIANS](sql-query-radians.md)
* [RAND](sql-query-rand.md)
* [ROUND](sql-query-round.md)
* [SIGN](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [SQRT](sql-query-sqrt.md)
* [SQUARE](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
Alle wiskundige functies, met uitzonde ring van ASELECT, zijn deterministische functies. Dit betekent dat ze dezelfde resultaten retour neren telkens wanneer ze worden aangeroepen met een specifieke set invoer waarden.

## <a name="next-steps"></a>Volgende stappen

- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregaties](sql-query-aggregate-functions.md)
