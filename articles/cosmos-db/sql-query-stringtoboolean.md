---
title: StringToBoolean in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie StringToBoolean in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2ad7ca9e2e50395effcc50e776eee3f1740fbb7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337911"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een expressie die is vertaald naar een Booleaanse waarde. Als expressie niet kan worden vertaald, retourneert ongedefinieerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die moet worden geparseerd als een Boole-expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een booleaanse expressie of een niet-gedefinieerde waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe de `StringToBoolean` verschillende typen zich gedraagt. 
 
 Hier volgen enkele voor beelden met geldige invoer.

Spatie is alleen toegestaan vóór of na "True"/"false".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Dit is de resultatenset.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Hier volgen enkele voor beelden met ongeldige invoer.

 Boole-waarden zijn hoofdletter gevoelig en moeten met alle kleine letters worden geschreven, dat wil zeggen ' True ' en ' false '.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Dit is de resultatenset.  
  
```json
[{}]
``` 

De door gegeven expressie wordt geparseerd als een Boole-expressie. deze invoer kan niet worden geëvalueerd om Booleaanse waarden te typen en retour neren dus niet-gedefinieerd.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
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
