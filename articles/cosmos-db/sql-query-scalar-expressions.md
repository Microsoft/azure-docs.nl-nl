---
title: Scalaire expressies in Azure Cosmos DB SQL-query's
description: Meer informatie over de SQL-syntaxis van scalaire expressies voor Azure Cosmos DB. In dit artikel wordt ook beschreven hoe u scalaire expressies kunt combi neren in complexe expressies met behulp van Opera tors.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 34df2d2f4a2209cca93c3f7ac12dcd203bf4a089
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339628"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Scalaire expressies in Azure Cosmos DB SQL-query's
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De [component SELECT](sql-query-select.md) ondersteunt scalaire expressies. Een scalaire expressie is een combi natie van symbolen en Opera tors die kunnen worden geëvalueerd om één waarde te verkrijgen. Voor beelden van scalaire expressies zijn: constanten, eigenschaps verwijzingen, matrix element verwijzingen, alias verwijzingen of functie aanroepen. Scalaire expressies kunnen worden gecombineerd tot complexe expressies met behulp van Opera tors.

## <a name="syntax"></a>Syntaxis
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumenten
  
- `<constant>`  
  
   Vertegenwoordigt een constante waarde. Zie de sectie [constanten](sql-query-constants.md) voor meer informatie.  
  
- `input_alias`  
  
   Vertegenwoordigt een waarde die is gedefinieerd door de component die is `input_alias` geïntroduceerd in `FROM` .  
  Deze waarde is gegarandeerd niet- **gedefinieerd** .**ongedefinieerde** waarden in de invoer worden overgeslagen.  
  
- `<scalar_expression>.property_name`  
  
   Vertegenwoordigt een waarde van de eigenschap van een object. Als de eigenschap niet bestaat of als er wordt verwezen naar de eigenschap in een waarde die geen object is, wordt de expressie geëvalueerd als niet- **gedefinieerde** waarde.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Vertegenwoordigt een waarde van de eigenschap met de naam `property_name` of het matrix element met `array_index` de index van een matrix. Als de eigenschap/matrix index niet bestaat of als er wordt verwezen naar de eigenschap/matrix index op een waarde die geen object/matrix is, wordt de expressie geëvalueerd als niet-gedefinieerde waarde.  
  
- `unary_operator <scalar_expression>`  
  
   Vertegenwoordigt een operator die wordt toegepast op een enkele waarde. Zie de sectie [Opera tors](sql-query-operators.md) voor meer informatie.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Vertegenwoordigt een operator die wordt toegepast op twee waarden. Zie de sectie [Opera tors](sql-query-operators.md) voor meer informatie.  
  
- `<scalar_function_expression>`  
  
   Vertegenwoordigt een waarde die is gedefinieerd door een resultaat van een functie aanroep.  
  
- `udf_scalar_function`  
  
   De naam van de door de gebruiker gedefinieerde scalaire functie.  
  
- `builtin_scalar_function`  
  
   De naam van de ingebouwde scalaire functie.  
  
- `<create_object_expression>`  
  
   Vertegenwoordigt een waarde die wordt opgehaald door het maken van een nieuw object met opgegeven eigenschappen en hun waarden.  
  
- `<create_array_expression>`  
  
   Vertegenwoordigt een waarde die wordt opgehaald door een nieuwe matrix te maken met opgegeven waarden als elementen  
  
- `parameter_name`  
  
   Vertegenwoordigt een waarde van de opgegeven parameter naam. Parameter namen moeten een enkel \@ als het eerste teken hebben.  
  
## <a name="remarks"></a>Opmerkingen
  
  Wanneer u een ingebouwde of door de gebruiker gedefinieerde scalaire functie aanroept, moeten alle argumenten worden gedefinieerd. Als een van de argumenten niet is gedefinieerd, wordt de functie niet aangeroepen en wordt het resultaat niet gedefinieerd.  
  
  Bij het maken van een object wordt elke eigenschap die niet-gedefinieerde waarde is toegewezen, overgeslagen en niet opgenomen in het gemaakte object.  
  
  Wanneer u een matrix maakt, wordt een element waarde die niet is **gedefinieerd** , overgeslagen en niet in het gemaakte object opgenomen. Dit zorgt ervoor dat het volgende gedefinieerde element zodanig wordt geplaatst dat de gemaakte matrix geen indexen overgeslagen.  

## <a name="examples"></a>Voorbeelden

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

U ziet deze uitvoer:

```json
    [{
      "$1": 1.33333
    }]
```

In de volgende query is het resultaat van de scalaire expressie een Booleaanse waarde:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

U ziet deze uitvoer:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Subquery's](sql-query-subquery.md)