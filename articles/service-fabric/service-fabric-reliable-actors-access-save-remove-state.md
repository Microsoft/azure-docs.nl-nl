---
title: Azure Service Fabric-status beheren
description: Meer informatie over het openen, opslaan en verwijderen van de status van een Azure Service Fabric reliable actor en overwegingen bij het ontwerpen van een toepassing.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1e796232719342883efc2aa5dd377d586f3039ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571314"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Reliable Actors status openen, opslaan en verwijderen
[Reliable actors](service-fabric-reliable-actors-introduction.md) zijn objecten met één thread die zowel de logica als de status kunnen inkapselen, en de status betrouwbaar kan onderhouden. Elk actor-exemplaar heeft een eigen [status Manager](service-fabric-reliable-actors-state-management.md): een gegevens structuur op basis van een woorden lijst die op betrouw bare wijze sleutel/waarde-paren opslaat. De status Manager is een wrapper rond een State-provider. U kunt deze gebruiken om gegevens op te slaan, ongeacht welke [persistentie-instelling](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) wordt gebruikt.

State Manager-sleutels moeten teken reeksen zijn. Waarden zijn Gene riek en kunnen elk type zijn, inclusief aangepaste typen. Waarden die zijn opgeslagen in de status Manager moeten gegevens contract serialiseerbaar zijn omdat ze via het netwerk kunnen worden verzonden naar andere knoop punten tijdens de replicatie en kunnen worden geschreven naar de schijf, afhankelijk van de instelling van de status persistentie van een actor.

De status Manager stelt algemene woordenboek methoden beschikbaar voor het beheren van de status, vergelijkbaar met die in de betrouw bare woorden lijst.

Zie [Aanbevolen procedures voor het beheren van de actor status](service-fabric-reliable-actors-state-management.md#best-practices)voor meer informatie.

## <a name="access-state"></a>Toegangs status
De status is toegankelijk via de status Manager per sleutel. Status Manager-methoden zijn allemaal asynchroon, omdat deze mogelijk schijf-I/O vereisen wanneer actors de status persistent hebben. Bij eerste toegang worden status objecten in het geheugen opgeslagen in de cache. Herhaal Access-toegangs objecten rechtstreeks vanuit het geheugen en retour neer synchroon zonder schijf-I/O of asynchrone overhead voor context schakeling. In de volgende gevallen wordt een status object uit de cache verwijderd:

* Een actor-methode genereert een onverwerkte uitzonde ring nadat een object is opgehaald uit de status Manager.
* Een actor wordt opnieuw geactiveerd, hetzij na het deactiveren of na de fout.
* Status van de status provider pagina's op schijf. Dit gedrag is afhankelijk van de implementatie van de State-provider. De standaard status provider voor de `Persisted` instelling heeft dit gedrag.

U kunt de status ophalen met behulp van een standaard *Get* -bewerking die `KeyNotFoundException` wordt gegenereerd (C#) of `NoSuchElementException` (Java) als er geen vermelding voor de sleutel bestaat:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

U kunt de status ook ophalen met behulp van een *TryGet* -methode die niet genereert als er geen vermelding voor een sleutel bestaat:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Status opslaan
De methode voor het ophalen van status Manager retourneert een verwijzing naar een object in het lokale geheugen. Als u dit object in alleen lokaal geheugen wijzigt, wordt het niet blijvend opgeslagen. Wanneer een object wordt opgehaald uit de status Manager en gewijzigd, moet het worden geplaatst in de status Manager om blijvend te kunnen opslaan.

U kunt de status invoegen met behulp van een onvoorwaardelijke *set*, die overeenkomt met de `dictionary["key"] = value` syntaxis:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

U kunt een status toevoegen met behulp van een *add* -methode. Deze methode genereert `InvalidOperationException` (C#) of `IllegalStateException` (Java) wanneer wordt geprobeerd een sleutel toe te voegen die al bestaat.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

U kunt ook een status toevoegen met behulp van een *TryAdd* -methode. Deze methode wordt niet gegenereerd wanneer er wordt geprobeerd een sleutel toe te voegen die al bestaat.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Aan het einde van een actor-methode slaat de status Manager automatisch waarden op die zijn toegevoegd of gewijzigd door een INSERT-of update-bewerking. Een ' Save ' kan bestaan uit persistentie op schijf en replicatie, afhankelijk van de gebruikte instellingen. Waarden die niet zijn gewijzigd, blijven niet behouden of worden gerepliceerd. Als er geen waarden zijn gewijzigd, gebeurt er niets. Als het opslaan mislukt, wordt de gewijzigde status verwijderd en wordt de oorspronkelijke status opnieuw geladen.

U kunt de status ook hand matig opslaan door de `SaveStateAsync` methode op de actor-basis aan te roepen:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Status verwijderen
U kunt de status permanent verwijderen uit de status Manager van een actor door de methode *Remove* aan te roepen. Deze methode genereert `KeyNotFoundException` (C#) of `NoSuchElementException` (Java) wanneer wordt geprobeerd een sleutel te verwijderen die niet bestaat.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

U kunt de status ook permanent verwijderen met de methode *TryRemove* . Deze methode wordt niet gegenereerd wanneer wordt geprobeerd een sleutel te verwijderen die niet bestaat.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Volgende stappen

De status die is opgeslagen in Reliable Actors moet worden geserialiseerd voordat deze naar de schijf wordt geschreven en kan worden gerepliceerd voor hoge Beschik baarheid. Meer informatie over het [serialiseren van het type actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Klik vervolgens op meer informatie over [actor Diagnostics en prestatie bewaking](service-fabric-reliable-actors-diagnostics.md).
