---
title: Bevat in Azure Cosmos DB query taal
description: Meer informatie over hoe de CONTAINs-functie voor SQL-systeem in Azure Cosmos DB een Booleaanse waarde retourneert die aangeeft of de eerste teken reeks expressie de tweede bevat
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4b6835b22e5cfa4ca703b95d70e20112b8723def
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339169"
---
# <a name="contains-azure-cosmos-db"></a>BEVAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retourneert een Booleaanse waarde die aangeeft of de eerste tekenreeksexpressie de tweede bevat.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is de teken reeks expressie die moet worden doorzocht.  
  
*str_expr2*  
   Is de teken reeks expressie die u wilt zoeken.  

*bool_expr* Optionele waarde voor het negeren van case. Als deze waarde is ingesteld op True, wordt een niet-hoofdletter gevoelige zoek actie uitgevoerd. Indien niet opgegeven, is deze waarde false.
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt gecontroleerd of "ABC" bevat "AB" en "ABC" bevat "A".  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 Dit is de resultatenset.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

Het RU-verbruik van bevat wordt verhoogd naarmate de kardinaliteit van de eigenschap in de systeem functie toeneemt. Met andere woorden, als u controleert of een eigenschaps waarde een bepaalde teken reeks bevat, zijn de kosten voor de query-RU afhankelijk van het aantal mogelijke waarden voor die eigenschap.

Denk bijvoorbeeld aan twee eigenschappen: stad en land. De kardinaliteit van de stad is 5.000 en de kardinaliteit van het land is 200. Hier volgen twee voor beelden van query's:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

De eerste query gebruikt waarschijnlijk meer RUs dan de tweede query, omdat de kardinaliteit van de stad hoger is dan het land.

Als de grootte van de eigenschap in contains groter is dan 1 KB voor sommige documenten, moet deze documenten worden geladen met de query-engine. In dit geval kan de query-engine de contains-functie niet volledig evalueren met een index. De RU-kosten voor contains zijn hoog als u een groot aantal documenten hebt met een eigenschaps grootte van meer dan 1 KB.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
