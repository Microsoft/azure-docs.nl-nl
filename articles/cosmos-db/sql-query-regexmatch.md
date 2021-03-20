---
title: RegexMatch in Azure Cosmos DB-query taal
description: Meer informatie over de functie RegexMatch SQL System in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341634"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Biedt mogelijkheden voor reguliere expressies. Reguliere expressies zijn een beknopte en flexibele notatie voor het zoeken van tekst patronen. Azure Cosmos DB maakt gebruik van [perl-compatibele reguliere expressies (pcre)](http://www.pcre.org/). 

## <a name="syntax"></a>Syntaxis
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is de teken reeks expressie die moet worden doorzocht.  
  
*str_expr2*  
   Is de reguliere expressie.

*str_expr3*  
   Is de teken reeks van geselecteerde para meters die moeten worden gebruikt met de reguliere expressie. Deze teken reeks waarde is optioneel. Als u RegexMatch zonder wijzigings functies wilt uitvoeren, kunt u een lege teken reeks toevoegen of helemaal weglaten. 

Meer informatie over de [syntaxis voor het maken van reguliere expressies vindt u in perl](https://perldoc.perl.org/perlre). 

Azure Cosmos DB ondersteunt de volgende vier para meters:

| Modifier | Beschrijving |
| ------ | ----------- |
| `m` | Behandel de teken reeks expressie die moet worden doorzocht als meerdere regels. Zonder deze optie wordt "^" en "$" overeenkomen aan het begin of einde van de teken reeks en niet aan elke afzonderlijke regel. |
| `s` | '. ' Toestaan om een wille keurig teken, inclusief een nieuwe regel teken, te koppelen. | 
| `i` | Case bij het vergelijken van patronen negeren. |
| `x` | Alle spatie tekens negeren. |

## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie. Retourneert ongedefinieerd als de teken reeks expressie die moet worden doorzocht, de reguliere expressie of de geselecteerde wijzigings functies ongeldig zijn.
  
## <a name="examples"></a>Voorbeelden
  
In het volgende eenvoudige RegexMatch-voor beeld wordt de teken reeks "ABCD" voor de reguliere expressie met een aantal verschillende wijzigingen gecontroleerd.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Dit is de resultatenset.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

Met RegexMatch kunt u meta tekens gebruiken om complexere Zoek opdrachten uit te voeren die anders niet mogelijk zijn met de systeem functies StartsWith, EndsWith, contains of StringEquals. Hier volgen enkele aanvullende voor beelden, die u kunt uitvoeren met behulp van de voedings gegevensset die beschikbaar is via de [Azure Cosmos DB query Playground](https://www.documentdb.com/sql/demo). 

> [!NOTE] 
> Als u een meta teken in een reguliere expressie moet gebruiken en niet wilt dat het een speciale betekenis heeft, moet u het meta teken met gebruiken `\` .

**Controleer de items die een beschrijving hebben die het woord ' Salt ' exact eenmaal bevat:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**Controleer de items die een beschrijving hebben die een getal tussen 0 en 99 bevat:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Controleer de items die een beschrijving hebben die vier letter woorden bevat die beginnen met "S" of "s":**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy) als de reguliere expressie kan worden onderverdeeld in StartsWith-, EndsWith-, contain-of StringEquals-systeem functies.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
