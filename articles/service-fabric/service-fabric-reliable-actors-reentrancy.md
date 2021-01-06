---
title: Herbetreedbaarheid in azure Service Fabric Actors
description: Inleiding tot herbetreedbaarheid voor Service Fabric Reliable Actors, een manier om blok kering op basis van de aanroep context te voor komen.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 9578b676e46d21fe5d30de92ad59c852a8b05f70
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915875"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors herbetreedbaarheid
Met de Reliable Actors runtime kunt u standaard herbetreedbaarheid op basis van logische oproepen. Hiermee kunnen actors worden ingetrokken als ze zich in dezelfde aanroep context keten bevinden. Actor A verzendt bijvoorbeeld een bericht naar actor B, die een bericht naar actor C verzendt. Als onderdeel van de bericht verwerking, als actor C actor A aanroept, wordt het bericht weer gegeven, zodat het is toegestaan. Andere berichten die deel uitmaken van een andere aanroep context, worden geblokkeerd op actor A tot de verwerking is voltooid.

Er zijn twee opties beschikbaar voor actor herbetreedbaarheid gedefinieerd in de `ActorReentrancyMode` Enum:

* `LogicalCallContext` (standaard gedrag)
* `Disallowed` -schakelt herbetreedbaarheid uit

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Herbetreedbaarheid kunnen `ActorService` tijdens de registratie worden geconfigureerd in de instellingen van een. De instelling is van toepassing op alle actor-exemplaren die zijn gemaakt in de actor-service.

In het volgende voor beeld ziet u een actor service waarmee de herbetreedbaarheid-modus wordt ingesteld op `ActorReentrancyMode.Disallowed` . Als een actor een bericht naar een andere actor verzendt, wordt er in dit geval een uitzonde ring van het type `FabricException` gegenereerd.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over herbetreedbaarheid in de [ACTOR API-referentie documentatie](/dotnet/api/microsoft.servicefabric.actors?view=azure-dotnet))
