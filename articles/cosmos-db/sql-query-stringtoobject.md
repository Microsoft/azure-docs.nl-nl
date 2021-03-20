---
title: StringToObject in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie StringToObject in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 369c86c8fc2ef4d125c3f13db4eb3af88b7e726e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337843"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een expressie die is vertaald naar een object. Als expressie niet kan worden vertaald, retourneert ongedefinieerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die moet worden geparseerd als een JSON-object expressie. Houd er rekening mee dat geneste teken reeks waarden moeten worden geschreven met dubbele aanhalings tekens die geldig zijn. Zie [JSON.org](https://json.org/) voor meer informatie over de JSON-indeling.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een object expressie of een niet-gedefinieerde waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe de `StringToObject` verschillende typen zich gedraagt. 
  
 Hier volgen enkele voor beelden met geldige invoer.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Dit is de resultatenset.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Hier volgen enkele voor beelden met ongeldige invoer.
Hoewel ze geldig zijn binnen een query, worden ze niet geparseerd naar geldige objecten. Teken reeksen in de teken reeks van een object moeten worden voorafgegaan door ' { \\ ' a \\ ': \\ ' Str \\ '} ' of de omliggende aanhaling moet één ' {' a ': ' str '} ' zijn.

Enkele aanhalings tekens rond eigenschapnamen zijn geen geldige JSON-namen.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Dit is de resultatenset.

```json
[{}]
```  

Eigenschaps namen zonder omringende aanhalings tekens zijn geen geldige JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Dit is de resultatenset.

```json
[{}]
``` 

Hier volgen enkele voor beelden met ongeldige invoer.

 De door gegeven expressie wordt geparseerd als een JSON-object. deze invoer levert geen object op en retourneert dus niet-gedefinieerd.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Dit is de resultatenset.

```json
[{}]
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
