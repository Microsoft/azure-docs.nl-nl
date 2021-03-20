---
title: StringToArray in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie StringToArray in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7ae1f69e92e890daae528eb1f4dfb95f76560043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337979"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een expressie die is vertaald naar een matrix. Als expressie niet kan worden vertaald, retourneert ongedefinieerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die moet worden geparseerd als een JSON-matrix expressie. 
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een matrix expressie of is niet gedefinieerd. 
  
## <a name="remarks"></a>Opmerkingen
  Geneste teken reeks waarden moeten worden geschreven met dubbele aanhalings tekens die een geldige JSON moeten hebben. Zie [JSON.org](https://json.org/)voor meer informatie over de JSON-indeling. Deze systeem functie maakt geen gebruik van de index.
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe de `StringToArray` verschillende typen zich gedraagt. 
  
 Hier volgen enkele voor beelden met geldige invoer.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Dit is de resultatenset.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Hier volgt een voor beeld van ongeldige invoer. 
   
 Enkele aanhalings tekens in de matrix zijn geen geldige JSON.
Hoewel ze wel geldig zijn binnen een query, worden ze niet geparseerd naar geldige matrices. Teken reeksen binnen de matrix teken reeks moeten worden voorafgegaan door ' [ \\ ' \\ ] ' of de omliggende aanhaling moet één ' ['] ' bevatten.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Dit is de resultatenset.

```json
[{}]
```

Hier volgen enkele voor beelden van ongeldige invoer.
   
 De door gegeven expressie wordt geparseerd als een JSON-matrix. de volgende evaluaties zijn niet van het type matrix en daarom wordt ongedefinieerd geretourneerd.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Dit is de resultatenset.

```json
[{}]
```

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
