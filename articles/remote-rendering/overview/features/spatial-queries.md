---
title: Ruimtelijke query's
description: Ruimtelijke query's uitvoeren in een scène
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c664df586c260b3e16f64c071190055dbaeccd24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594041"
---
# <a name="spatial-queries"></a>Ruimtelijke query's

Ruimtelijke query's zijn bewerkingen waarmee u de externe rendering-service kunt vragen welke objecten zich in een gebied bevinden. Ruimtelijke query's worden vaak gebruikt voor het implementeren van interacties, zoals het bepalen van het object waarnaar een gebruiker verwijst.

Alle ruimtelijke query's worden geëvalueerd op de server. Daarom zijn deze asynchrone bewerkingen en worden de resultaten bereikt met een vertraging die afhankelijk is van de netwerk latentie. Omdat bij elke ruimtelijke query netwerk verkeer wordt gegenereerd, moet u er daarom voor zorgen dat er niet te veel in één keer een actie wordt uitgevoerd.

## <a name="collision-meshes"></a>Botsingen netten

Ruimtelijke query's worden aangedreven door de [Havok-fysieke](https://www.havok.com/products/havok-physics) engine en vereisen dat er een speciaal botsend net aanwezig is. [Model conversie](../../how-tos/conversion/model-conversion.md) genereert standaard botsingen. Als u geen ruimtelijke query's nodig hebt voor een complex model, kunt u de functie voor het genereren van botsingen uitschakelen in de [conversie opties](../../how-tos/conversion/configure-model-conversion.md), omdat deze op verschillende manieren invloed heeft:

* De [model conversie](../../how-tos/conversion/model-conversion.md) duurt aanzienlijk langer.
* De geconverteerde model bestands grootten zijn merkbaar groter, wat invloed heeft op de download snelheid.
* Het laden van runtime-tijden is langer.
* CPU-geheugen gebruik van runtime is hoger.
* Er is een lichte runtime-prestatie overhead voor elk model exemplaar.

## <a name="ray-casts"></a>Ray-casts

Een *Ray-cast* is een ruimtelijke query waarbij de runtime controleert welke objecten door een Ray worden doorzocht, vanaf een bepaalde positie en naar een bepaalde richting wijzen. Als Optima Lise ring wordt er ook een maximum Ray-afstand gegeven om niet te zoeken naar objecten die te ver weg staan.

```cs
async void CastRay(RenderingSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastQueryResult result = await session.Connection.RayCastQueryAsync(rayCast);
    RayCastHit[] hits = result.Hits;
    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<RenderingSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = {0, 0, 0};
    rayCast.EndPos = {0, 0, 1};
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    session->Connection()->RayCastQueryAsync(rayCast, [](Status status, ApiHandle<RayCastQueryResult> result)
    {
        if (status == Status::OK)
        {
            std::vector<RayCastHit> hits;
            result->GetHits(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        }
    });
}
```


Er zijn drie methoden voor het verzamelen van treffers:

* **`Closest` :** In deze modus wordt alleen de meest overeenkomende treffer gerapporteerd.
* **`Any` :** De voor keur geven aan deze modus wanneer u wilt weten *of* een Ray iets zou kunnen raken, maar niet zeker weet wat er precies is bereikt. Deze query kan aanzienlijk goed koper zijn om te evalueren, maar heeft ook maar weinig toepassingen.
* **`All` :** In deze modus worden alle treffers langs de Ray gerapporteerd, gesorteerd op afstand. Gebruik deze modus alleen als u meer nodig hebt dan de eerste treffer. Beperk het aantal gerapporteerde treffers met de `MaxHits` optie.

Om te voor komen dat objecten selectief worden uitgesloten van Ray-casts, kan het onderdeel [HierarchicalStateOverrideComponent](override-hierarchical-state.md) worden gebruikt.

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Resultaat van treffer

Het resultaat van een Ray cast-query is een matrix met treffers. De matrix is leeg als er geen object is geraakt.

Een treffer heeft de volgende eigenschappen:

* **`HitEntity` :** De [entiteit](../../concepts/entities.md) is geraakt.
* **`SubPartId` :** Welk *subnet* is bereikt in een [MeshComponent](../../concepts/meshes.md). Kan worden gebruikt voor het indexeren `MeshComponent.UsedMaterials` en opzoeken van het [materiaal](../../concepts/materials.md) op dat moment.
* **`HitPosition` :** De positie waar de Ray het object kruist.
* **`HitNormal` :** De wereld wijde ruimte van het net op de positie van het snij punt.
* **`DistanceToHit` :** De afstand tussen de Ray-begin positie en de treffer.

## <a name="api-documentation"></a>API-documentatie

* [C# RenderingConnection. RayCastQueryAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.raycastqueryasync)
* [C++ RenderingConnection:: RayCastQueryAsync ()](/cpp/api/remote-rendering/renderingconnection#raycastqueryasync)

## <a name="next-steps"></a>Volgende stappen

* [Objectgrenzen](../../concepts/object-bounds.md)
* [Hiërarchische provincies negeren](override-hierarchical-state.md)