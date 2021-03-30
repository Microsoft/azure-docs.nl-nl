---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80272608"
---
| Gegevenstype | Functies die zijn toegestaan in lambda-expressies met `any` | Functies die zijn toegestaan in lambda-expressies met `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Alles behalve `search.ismatch` en `search.ismatchscoring` | Hetzelfde |
| `Collection(Edm.String)` | Vergelijkingen met `eq` of `search.in` <br/><br/> Combi neren van subexpressies met `or` | Vergelijkingen met `ne` of `not search.in()` <br/><br/> Combi neren van subexpressies met `and` |
| `Collection(Edm.Boolean)` | Vergelijkingen met `eq` of `ne` | Hetzelfde |
| `Collection(Edm.GeographyPoint)` | Gebruiken `geo.distance` met `lt` of `le` <br/><br/> `geo.intersects` <br/><br/> Combi neren van subexpressies met `or` | Gebruiken `geo.distance` met `gt` of `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combi neren van subexpressies met `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Vergelijkingen met `eq` ,,,, `ne` `lt` `gt` `le` of `ge` <br/><br/> Combi neren van vergelijkingen met andere subexpressies met `or` <br/><br/> Vergelijkingen combi neren, behalve `ne` met andere subexpressies met `and` <br/><br/> Expressies die gebruikmaken van combi Naties van `and` en `or` in [disjunctive normaal formulier (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Vergelijkingen met `eq` ,,,, `ne` `lt` `gt` `le` of `ge` <br/><br/> Combi neren van vergelijkingen met andere subexpressies met `and` <br/><br/> Vergelijkingen combi neren, behalve `eq` met andere subexpressies met `or` <br/><br/> Expressies die gebruikmaken van combi Naties van `and` en `or` in [Conjunctive normaal formulier (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
