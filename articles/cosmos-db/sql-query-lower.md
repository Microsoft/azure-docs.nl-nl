---
title: LAGER in Azure Cosmos DB query taal
description: Meer informatie over de lagere SQL-systeem functie in Azure Cosmos DB om een teken reeks expressie te retour neren na het omzetten van een hoofd letter-teken in kleine letters
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dd1d9ddefd67cadb92632fd6db7a1fbd5a34f35a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338426"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een tekenreeksexpressie na het converteren van tekens in hoofdletters naar kleine letters.  

De lagere systeem functie maakt geen gebruik van de index. Als u van plan bent om niet-hoofdletter gevoelige vergelijkingen te maken, kan de functie lagere systeem een aanzienlijke hoeveelheid van RU verbruiken. Als dit het geval is, kunt u in plaats van het gebruik van de functie lagere systeem gegevens elke keer voor vergelijkingen te normaliseren, de behuizing tijdens het invoegen normaliseren. Vervolgens wordt een query zoals SELECT * FROM c waarbij LOWER (c. name) = ' Bob ' gewoon geselecteerd * van c waarbij c.name = ' Bob '.

## <a name="syntax"></a>Syntaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u kunt gebruiken `LOWER` in een query.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Dit is de resultatenset.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
