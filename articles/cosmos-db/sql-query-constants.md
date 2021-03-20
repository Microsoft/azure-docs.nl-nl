---
title: SQL-constanten in Azure Cosmos DB
description: Meer informatie over hoe de SQL-query constanten in Azure Cosmos DB worden gebruikt om een specifieke gegevens waarde weer te geven
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: b0973dca980b65b85ac66cc1b3c9ed45f0f9c779
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339196"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL-query constanten  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Een constante, ook wel een letterlijke of scalaire waarde genoemd, is een symbool dat een specifieke gegevens waarde vertegenwoordigt. De notatie van een constante is afhankelijk van het gegevens type van de waarde die het vertegenwoordigt.  
  
 **Ondersteunde scalaire gegevens typen:**  
  
|**Type**|**Volg orde van waarden**|  
|-|-|  
|**Undefined**|Enkele waarde: niet **gedefinieerd**|  
|**Null**|Enkele waarde: **Null**|  
|**Booleaans**|Waarden: **False**, **True**.|  
|**Number**|Een drijvende-komma getal met dubbele precisie, IEEE 754-standaard.|  
|**Tekenreeks**|Een reeks van nul of meer Unicode-tekens. Teken reeksen moeten tussen enkele of dubbele aanhalings tekens worden geplaatst.|  
|**Array**|Een reeks van nul of meer elementen. Elk element kan een waarde van elk scalair gegevens type zijn, behalve niet **gedefinieerd**.|  
|**Object**|Een niet-geordende set met nul of meer naam/waarde-paren. Naam is een Unicode-teken reeks, waarde kan van elk scalair gegevens type zijn, behalve niet **gedefinieerd**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntax
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a> Opmerkingen
  
* `<undefined_constant>; Undefined`  
  
  Vertegenwoordigt een niet-gedefinieerde waarde van het type niet gedefinieerd.  
  
* `<null_constant>; null`  
  
  Vertegenwoordigt een **Null** -waarde van het type **Null**.  
  
* `<boolean_constant>`  
  
  Vertegenwoordigt een constante van het type Boolean.  
  
* `false`  
  
  Vertegenwoordigt een **onwaare** waarde van het type Boolean.  
  
* `true`  
  
  Vertegenwoordigt een **True** -waarde van het type Boolean.  
  
* `<number_constant>`  
  
  Vertegenwoordigt een constante.  
  
* `decimal_literal`  
  
  Decimale letterlijke waarden zijn getallen die worden weer gegeven met de decimale notatie of met een weten schappelijke notatie.  
  
* `hexadecimal_literal`  
  
  Hexadecimale letterlijke waarden zijn getallen die worden weer gegeven met het voor voegsel ' 0x ' gevolgd door een of meer hexadecimale cijfers.  
  
* `<string_constant>`  
  
  Vertegenwoordigt een constante van het type teken reeks.  
  
* `string _literal`  
  
  Literals voor teken reeksen zijn Unicode-teken reeksen die worden vertegenwoordigd door een reeks van nul of meer Unicode-tekens of escape-reeksen. Letterlijke teken reeksen worden tussen enkele aanhalings tekens (apostrof: ') of dubbele aanhalings tekens (aanhalings teken: ') geplaatst.  
  
  Volgende escape-reeksen zijn toegestaan:  
  
|**Escape reeks**|**Beschrijving**|**Unicode-teken**|  
|-|-|-|  
|\\'|apostrof (')|U + 0027|  
|\\"|aanhalings teken (")|U + 0022|  
|\\\ |omgekeerde schuine streep ( \\ )|U + 005C|  
|\\/|schuine streep (/)|U + 002F|  
|\b|Backspace|U + 0008|  
|\f|formulier feed|U + 000C|  
|\n|regel invoer|U + 000A|  
|\r| regelterugloop|U + 000D|  
|\|tabbesturingselement|U + 0009|  
|\uXXXX|Een Unicode-teken dat is gedefinieerd door 4 hexadecimale cijfers.|U + XXXX|  

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Documentgegevens modelleren](modeling-data.md)
