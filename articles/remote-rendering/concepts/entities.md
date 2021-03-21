---
title: Entiteiten
description: Definitie van entiteiten in het bereik van de Azure remote rendering API
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 29952353b8c3452d95bcced163fafa81fe158f64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593398"
---
# <a name="entities"></a>Entiteiten

Een *entiteit* vertegenwoordigt een beweegbaar object in ruimte en is de fundamentele bouw steen van extern gerenderde inhoud.

## <a name="entity-properties"></a>Entiteits eigenschappen

Entiteiten hebben een trans formatie die is gedefinieerd door een positie, rotatie en schaal. Op zichzelf hebben entiteiten geen waarneem bare functionaliteit. In plaats daarvan wordt gedrag toegevoegd via onderdelen, die zijn gekoppeld aan entiteiten. Als u bijvoorbeeld een [CutPlaneComponent](../overview/features/cut-planes.md)  koppelt, wordt er een knip vlak gemaakt op de positie van de entiteit.

Het belangrijkste aspect van de entiteit zelf is de hiërarchie en de resulterende hiërarchische trans formatie. Als er bijvoorbeeld meerdere entiteiten als onderliggende items zijn gekoppeld aan een gedeelde bovenliggende entiteit, kunnen al deze entiteiten worden verplaatst, geroteerd en geschaald in gelijktijdig door de trans formatie van de bovenliggende entiteit te wijzigen. De status van de entiteit `enabled` kan ook worden gebruikt om de zicht baarheid en reacties op Ray-casts voor een volledige subgrafiek in de hiërarchie uit te scha kelen.

Een entiteit is een unieke eigenaar van het bovenliggende item, wat inhoudt dat wanneer het bovenliggende item wordt vernietigd met `Entity.Destroy()` , de onderliggende en alle verbonden [onderdelen](components.md)hiervan zijn. Het verwijderen van een model uit de scène wordt dus gerealiseerd door te bellen `Destroy` op het hoofd knooppunt van een model, dat wordt geretourneerd door `RenderingSession.Connection.LoadModelAsync()` of door de SAS-variant `RenderingSession.Connection.LoadModelFromSasAsync()` .

Entiteiten worden gemaakt wanneer de server inhoud laadt of wanneer de gebruiker een object wil toevoegen aan de scène. Als een gebruiker bijvoorbeeld een knip vlak wil toevoegen om de binnenkant van een net te visualiseren, kan de gebruiker een entiteit maken waarbij het vlak moet bestaan en vervolgens de component knip vlak toevoegen.

## <a name="create-an-entity"></a>Een entiteit maken

Gebruik de volgende code om een nieuwe entiteit toe te voegen aan de scène, bijvoorbeeld om deze aan te geven als een hoofd object voor het laden van modellen of het koppelen van onderdelen eraan:

```cs
Entity CreateNewEntity(RenderingSession session)
{
    Entity entity = session.Connection.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<RenderingSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Connection()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Query functies

Er zijn twee soorten query functies voor entiteiten: synchrone en asynchrone aanroepen. Synchrone query's kunnen alleen worden gebruikt voor gegevens die aanwezig zijn op de client en heeft geen veel reken kracht. Voor beelden zijn query's voor onderdelen, relatieve object transformaties of bovenliggende/onderliggende relaties. Asynchrone query's worden gebruikt voor gegevens die zich alleen op de server bevinden of een extra berekening vergt die te duur is om te worden uitgevoerd op de client. Voor beelden zijn ruimtelijke grenzen query's of query's voor meta gegevens.

### <a name="querying-components"></a>Query's uitvoeren op onderdelen

Als u een onderdeel van een specifiek type wilt zoeken, gebruikt u `FindComponentOfType` :

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Query's uitvoeren op trans formaties

Transformatie query's zijn synchrone aanroepen voor het object. Het is belang rijk te weten dat trans formaties die via de API worden opgevraagd, lokale ruimte transformaties zijn ten opzichte van het bovenliggende object. Uitzonde ringen zijn hoofd objecten waarvoor lokale ruimte en wereld wijde ruimte identiek zijn.

> [!NOTE]
> Er is geen speciale API voor het opvragen van de wereld wijde trans formatie van wille keurige objecten.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>Ruimtelijke grenzen bevragen

Grenzen van query's zijn asynchrone aanroepen die op een volledige object hiërarchie worden uitgevoerd, met behulp van één entiteit als basis. Zie het speciale hoofd stuk over [object grenzen](object-bounds.md).

### <a name="querying-metadata"></a>Meta gegevens opvragen

Meta gegevens zijn extra gegevens opgeslagen in objecten die door de server worden genegeerd. Meta gegevens van objecten zijn in feite een set (naam, waarde) paren, waarbij de _waarde_ van het type Numeriek, Booleaans of teken reeks kan zijn. Meta gegevens kunnen met het model worden geëxporteerd.

Meta gegevens query's zijn asynchrone aanroepen voor een specifieke entiteit. De query retourneert alleen de meta gegevens van één entiteit, niet de samengevoegde gegevens van een subgraph.

```cs
Task<ObjectMetadata> metaDataQuery = entity.QueryMetadataAsync();
ObjectMetadata metaData = await metaDataQuery;
ObjectMetadataEntry entry = metaData.GetMetadataByName("MyInt64Value");
System.Int64 intValue = entry.AsInt64;
// ...
```

```cpp
entity->QueryMetadataAsync([](Status status, ApiHandle<ObjectMetadata> metaData) 
{
    if (status == Status::OK)
    {
        ApiHandle<ObjectMetadataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
        int64_t intValue = *entry->GetAsInt64();

        // ...
    }
});
```

De query wordt uitgevoerd, zelfs als het object geen meta gegevens bevat.

## <a name="api-documentation"></a>API-documentatie

* [Klasse van C#-entiteit](/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RenderingConnection. CreateEntity ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createentity)
* [Klasse C++-entiteit](/cpp/api/remote-rendering/entity)
* [C++ RenderingConnection:: CreateEntity ()](/cpp/api/remote-rendering/renderingconnection#createentity)

## <a name="next-steps"></a>Volgende stappen

* [Onderdelen](components.md)
* [Objectgrenzen](object-bounds.md)