---
title: Objectgrenzen
description: Legt uit hoe ruimtelijke object grenzen kunnen worden opgevraagd
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594481"
---
# <a name="object-bounds"></a>Objectgrenzen

Object grenzen vertegenwoordigen het volume dat een [entiteit](entities.md) en de onderliggende items innemen. Bij de externe rendering van Azure worden object grenzen altijd gegeven als aan elkaar *uitgelijnde selectie vakjes* (AABB). Object grenzen kunnen zich in *lokale ruimte* of in de *wereld ruimte* bevinden. In beide gevallen zijn ze altijd gecentreerd, wat betekent dat de gebieden en het volume kunnen verschillen tussen de lokale en wereld wijde ruimte weergave.

## <a name="querying-object-bounds"></a>Object grenzen opvragen

Het afsluitende begrenzingsvak van de lokale as van een [net](meshes.md) kan rechtstreeks vanuit de netresource worden opgevraagd. Deze grenzen kunnen worden omgezet in de lokale ruimte of wereld wijde ruimte van een entiteit met behulp van de trans formatie van de entiteit.

Het is mogelijk om de grenzen van een volledige object hiërarchie op deze manier te berekenen, maar dat moet de hiërarchie door lopen, query's uitvoeren op de grenzen voor elk net en ze hand matig combi neren. Deze bewerking is zowel omslachtig als inefficiënt.

Een betere manier is om een entiteit te bellen of aan te roepen `QueryLocalBoundsAsync` `QueryWorldBoundsAsync` . De berekening wordt vervolgens geoffload naar de server en geretourneerd met minimale vertraging.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>API-documentatie

* [C#-entiteit. QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C++-entiteit:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Volgende stappen

* [Ruimtelijke query's](../overview/features/spatial-queries.md)