---
title: Functies in azure Service Fabric actors implementeren
description: Hierin wordt beschreven hoe u uw eigen actor-service schrijft waarmee functies op service niveau worden geïmplementeerd op dezelfde manier als wanneer u StatefulService overneemt.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: d39ec93e0ad03d6c860bae9d0790e860c95457a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575564"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Functies op service niveau in uw actor service implementeren

Zoals beschreven in [service lagen](service-fabric-reliable-actors-platform.md#service-layering), is de actor service zelf een betrouw bare service. U kunt uw eigen service schrijven die is afgeleid van `ActorService` . U kunt functies op service niveau ook implementeren op dezelfde manier als wanneer u een stateful service overneemt, zoals:

- Back-up en herstel van de service.
- Gedeelde functionaliteit voor alle actors, bijvoorbeeld een circuit onderbreker.
- Externe procedure aanroepen in de actor-service zelf en op elke afzonderlijke actor.

## <a name="use-the-actor-service"></a>De actor-service gebruiken

Actor-exemplaren hebben toegang tot de actor-service waarin ze worden uitgevoerd. Via de actor-service kunnen actor-exemplaren de service context programmatisch ophalen. De service context heeft de partitie-ID, service naam, toepassings naam en andere informatie over het Azure Service Fabric-platform.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Net als alle Reliable Services moet de actor-service zijn geregistreerd bij een service type in de Service Fabric runtime. Voor de actor-service om uw actor-exemplaren uit te voeren, moet uw actor-type ook zijn geregistreerd bij de actor-service. De `ActorRuntime`-registratiemethode doet dit voor actors. In het eenvoudigste geval kunt u het actor type registreren en de actor-service gebruikt vervolgens de standaard instellingen.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

U kunt ook een lambda gebruiken die wordt verschaft door de registratie methode om de actor service zelf te maken. U kunt vervolgens de actor-service configureren en de actor-exemplaren expliciet bouwen. U kunt afhankelijkheden voor uw actor injecteren via de bijbehorende constructor.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Actor service-methoden

De actor-service implementeert `IActorService` (c#) of `ActorService` (Java), die op zijn beurt implementeert `IService` (c#) of `Service` (Java). Deze interface wordt gebruikt door Reliable Services externe toegang, waarmee externe procedure aanroepen op service methoden mogelijk wordt. Het bevat serviceniveau methoden die extern kunnen worden aangeroepen via service Remoting. U kunt deze gebruiken om actors op te [sommen](service-fabric-reliable-actors-enumerate.md) en te [verwijderen](service-fabric-reliable-actors-delete-actors.md) .


## <a name="custom-actor-service"></a>Aangepaste actor service

Met behulp van de actor-registratie-Lambda kunt u uw eigen aangepaste actor service registreren die is afgeleid van `ActorService` (C#) en `FabricActorService` (Java). U kunt vervolgens uw eigen functionaliteit op service niveau implementeren door een service klasse te schrijven die overneemt `ActorService` (C#) of `FabricActorService` (Java). Een aangepaste actor-service neemt alle actor runtime-functies van `ActorService` (C#) of `FabricActorService` (Java) over. Het kan worden gebruikt voor het implementeren van uw eigen service methoden.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>Actor back-up en herstel implementeren

Een aangepaste actor service kan een methode beschikbaar stellen voor het maken van back-ups van actor gegevens door gebruik te maken van de externe listener die al aanwezig is in `ActorService` . Zie voor een voor beeld [Backup en Restore actors](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Actor die gebruikmaakt van een externe v2-stack (interface compatibel)

De externe v2 (interface compatibel, bekend als V2_1) stack beschikt over alle functies van de v2-externe stack. De interface is compatibel met de externe v1-stack, maar is niet achterwaarts compatibel met v2 en v1. Als u een upgrade wilt uitvoeren van v1 naar V2_1 zonder gevolgen voor de beschik baarheid van de service, volgt u de stappen in de volgende sectie.

De volgende wijzigingen zijn vereist voor het gebruik van de externe V2_1 stack:

1. Voeg het volgende assembly-kenmerk toe aan actor-interfaces.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Bouw en upgrade actor service-en actor-client projecten om aan de slag te gaan met het gebruik van de v2-stack.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Upgrade van actor service naar externe v2-stack (interface compatibel) zonder dat dit van invloed is op de beschik baarheid van de service

Deze wijziging is een upgrade in twee stappen. Volg de stappen in deze reeks.

1. Voeg het volgende assembly-kenmerk toe aan actor-interfaces. Met dit kenmerk worden twee listeners gestart voor de actor service, V1 (bestaande) en de V2_1-listener. Werk de actor service bij met deze wijziging.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Upgrade de actor-clients nadat u de vorige upgrade hebt voltooid.
   Met deze stap zorgt u ervoor dat de actor-proxy gebruikmaakt van de externe V2_1 stack.

3. Deze stap is optioneel. Wijzig het vorige kenmerk om de V1-listener te verwijderen.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Actor die gebruikmaakt van de externe v2-stack

Met het versie 2,8 NuGet-pakket kunnen gebruikers nu gebruikmaken van de externe v2-stack, waarmee betere functies, zoals aangepaste serialisatie, worden uitgevoerd. Remoting v2 is niet achterwaarts compatibel met de bestaande externe stack (nu de V1-communicatie stack genoemd).

De volgende wijzigingen zijn vereist voor het gebruik van de externe v2-stack.

1. Voeg het volgende assembly-kenmerk toe aan actor-interfaces.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Bouw en voer een upgrade uit voor de actor service-en actor-client projecten om te beginnen met het gebruik van de v2-stack.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>De actor-service upgraden naar de externe v2-stack zonder de beschik baarheid van de service te beïnvloeden

Deze wijziging is een upgrade in twee stappen. Volg de stappen in deze reeks.

1. Voeg het volgende assembly-kenmerk toe aan actor-interfaces. Met dit kenmerk worden twee listeners gestart voor de actor service, V1 (bestaande) en de v2-listener. Werk de actor service bij met deze wijziging.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Upgrade de actor-clients nadat u de vorige upgrade hebt voltooid.
   Met deze stap zorgt u ervoor dat de actor-proxy gebruikmaakt van de externe v2-stack.

3. Deze stap is optioneel. Wijzig het vorige kenmerk om de V1-listener te verwijderen.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Volgende stappen

* [Beheer van actor status](service-fabric-reliable-actors-state-management.md)
* [Actor-levens cyclus en garbagecollection](service-fabric-reliable-actors-lifecycle.md)
* [Naslag documentatie voor actors-API](/previous-versions/azure/dn971626(v=azure.100))
* [.NET-voorbeeld code](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-voorbeeld code](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
