---
title: Component OFFSET LIMIT in Azure Cosmos DB
description: Meer informatie over het gebruik van de component OFFSET LIMIT voor het overs Laan en het uitvoeren van bepaalde waarden bij het uitvoeren van query's in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338288"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Component OFFSET LIMIT in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De component OFFSET LIMIT is een optionele component waarmee u een aantal waarden uit de query kunt overs Laan. Het aantal OFFSET en het aantal LIMIETen zijn vereist in de component OFFSET LIMIT.

Wanneer de OFFSET limiet wordt gebruikt in combi natie met een ORDER BY-component, wordt de resultatenset gemaakt door overs Laan en de geordende waarden uit te voeren. Als er geen ORDER BY-component wordt gebruikt, resulteert dit in een deterministische volg orde van waarden.

## <a name="syntax"></a>Syntaxis
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenten

- `<offset_amount>`

   Hiermee geeft u het aantal items op dat door de query resultaten moet worden overgeslagen.

- `<limit_amount>`
  
   Hiermee geeft u het gehele getal op van items die de query resultaten moeten bevatten

## <a name="remarks"></a>Opmerkingen
  
  Zowel het `OFFSET` aantal als het `LIMIT` aantal is vereist in de `OFFSET LIMIT` component. Als er een optionele `ORDER BY` component wordt gebruikt, wordt de resultatenset gemaakt door de overs laan van de geordende waarden over te slaan. Anders retourneert de query een vaste volg orde van waarden.

  De RU-kosten van een query met `OFFSET LIMIT` worden verhoogd naarmate er meer termen worden gecompenseerd. Voor query's met [meerdere pagina's met resultaten](sql-query-pagination.md)raden we u aan om [vervolg tokens](sql-query-pagination.md#continuation-tokens)te gebruiken. Vervolg tokens zijn een ' blad wijzer ' voor de locatie waar de query later kan worden hervat. Als u gebruikt `OFFSET LIMIT` , is er geen blad wijzer. Als u de volgende pagina van de query wilt retour neren, moet u beginnen met het begin.
  
  Gebruik `OFFSET LIMIT` voor gevallen waarin u items volledig wilt overs Laan en client bronnen wilt opslaan. U moet bijvoorbeeld gebruiken `OFFSET LIMIT` Als u wilt door gaan naar het 1000th-query resultaat en geen resultaten meer wilt weer geven van 1 tot en met 999. Op de back-end wordt `OFFSET LIMIT` nog steeds elk item geladen, met inbegrip van de items die worden overgeslagen. Het prestatie voordeel is een besparing van client bronnen door verwerkings items te voor komen die niet nodig zijn.

## <a name="examples"></a>Voorbeelden

Hier volgt een query waarmee de eerste waarde wordt overgeslagen en de tweede waarde wordt geretourneerd (op volg orde van de naam van de residente plaats):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

U ziet deze uitvoer:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Hier volgt een query waarmee de eerste waarde wordt overgeslagen en de tweede waarde wordt geretourneerd (zonder volg orde):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

U ziet deze uitvoer:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [ORDER BY-component](sql-query-order-by.md)
