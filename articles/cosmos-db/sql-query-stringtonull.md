---
title: StringToNull in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie StringToNull in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ee91ca3a32bacbf6590877f49bf74499fc00a8f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339577"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een expressie die is vertaald naar null. Als expressie niet kan worden vertaald, retourneert ongedefinieerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die als een null-expressie moet worden geparseerd.
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een null-expressie of een niet-gedefinieerde waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe de `StringToNull` verschillende typen zich gedraagt. 

Hier volgen enkele voor beelden met geldige invoer.

 Witruimte is alleen toegestaan vóór of na null.

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Dit is de resultatenset.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Hier volgen enkele voor beelden met ongeldige invoer.

Null is hoofdletter gevoelig en moet worden geschreven met alleen kleine letters, dat wil zeggen ' null '.

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Dit is de resultatenset.  
  
```json
[{}]
```  

De door gegeven expressie wordt geparseerd als een null-expressie. deze invoer kan niet worden geëvalueerd om null te typen en daarom ongedefinieerd te retour neren.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
