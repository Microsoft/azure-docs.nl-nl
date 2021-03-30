---
title: StringToNumber in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie StringToNumber in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f2622958a2315458ccd01da4aea7c6b75941e755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339560"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een expressie die is vertaald naar een getal. Als expressie niet kan worden vertaald, retourneert ongedefinieerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die moet worden geparseerd als een JSON-nummer expressie. Getallen in JSON moeten een geheel getal of een drijvende komma zijn. Zie [JSON.org](https://json.org/) voor meer informatie over de JSON-indeling.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie of een niet-gedefinieerde waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe de `StringToNumber` verschillende typen zich gedraagt. 

Witruimte is alleen toegestaan vóór of na het getal.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Dit is de resultatenset.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

In JSON moet een geldig getal ofwel een geheel getal zijn of een getal met een drijvende komma.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Dit is de resultatenset.  
  
```json
{{}}
```  

De door gegeven expressie wordt geparseerd als een numerieke expressie. deze invoer kan niet worden geëvalueerd om een getal te typen en daarom ongedefinieerd te retour neren. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Dit is de resultatenset.  
  
```json
{{}}
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
