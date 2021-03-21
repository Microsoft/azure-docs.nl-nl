---
title: Gebeurtenissen in op actors gebaseerde Azure Service Fabric Actors
description: Meer informatie over gebeurtenissen voor Service Fabric Reliable Actors, een efficiënte manier om te communiceren tussen actor en client.
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 591e3539715ba0873e44f56a93d05df6552bb1ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571365"
---
# <a name="actor-events"></a>Actor gebeurtenissen
Actor gebeurtenissen bieden een manier om meldingen over de beste inspanningen van de actor naar de clients te verzenden. Actor gebeurtenissen zijn ontworpen voor communicatie tussen clients en mogen niet worden gebruikt voor communicatie tussen actors.

De volgende code fragmenten laten zien hoe actor-gebeurtenissen in uw toepassing kunnen worden gebruikt.

Definieer een interface die de gebeurtenissen beschrijft die worden gepubliceerd door de actor. Deze interface moet zijn afgeleid van de `IActorEvents` interface. De argumenten van de methoden moeten van het [gegevens contract serialiseerbaar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)zijn. De methoden moeten void retour neren, omdat gebeurtenis meldingen op één manier en best effort worden uitgevoerd.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Declareer de gebeurtenissen die zijn gepubliceerd door de actor in de actor-interface.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Implementeer aan de client zijde de gebeurtenis-handler.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Maak op de client een proxy voor de actor die de gebeurtenis publiceert en Abonneer u op de gebeurtenissen.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

In het geval van failovers kan de actor een failover uitvoeren naar een ander proces of knoop punt. De actor-proxy beheert de actieve abonnementen en abonneert deze automatisch opnieuw. U kunt het interval voor opnieuw abonnementen beheren via de `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Als u zich wilt afmelden, gebruikt u de `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Op de actor publiceert u de gebeurtenissen wanneer ze plaatsvinden. Als zich abonnees op de gebeurtenis bevinden, verzendt de actors-runtime de melding.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Volgende stappen
* [Actor herbetreedbaarheid](service-fabric-reliable-actors-reentrancy.md)
* [De functie voor het controleren van actor en prestaties](service-fabric-reliable-actors-diagnostics.md)
* [Referentie documentatie voor actor-API](/previous-versions/azure/dn971626(v=azure.100))
* [C#-voorbeeld code](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET core-voorbeeld code](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-voorbeeld code](https://github.com/Azure-Samples/service-fabric-java-getting-started)
