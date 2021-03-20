---
title: Eenheids tests ontwikkelen voor stateful Services
description: Meer informatie over het testen van de eenheid in azure Service Fabric voor stateful Services en speciale overwegingen om tijdens de ontwikkeling rekening mee te houden.
ms.topic: conceptual
ms.date: 09/04/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 287c0544daa3c44d91fd336b502c496b9b4bb266
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89011392"
---
# <a name="create-unit-tests-for-stateful-services"></a>Eenheids tests maken voor stateful Services
Door de eenheid te testen Service Fabric stateful-Services, worden veelvoorkomende fouten gedetecteerd die niet noodzakelijkerwijs zouden worden onderschept door conventionele toepassingen of specifieke eenheids tests op basis van een domein. Tijdens het ontwikkelen van eenheids tests voor stateful Services, zijn er enkele speciale aandachtspunten die u in acht moet nemen.

1. Elke replica voert toepassings code uit, maar onder een andere context. Als de service gebruikmaakt van drie replica's, wordt de service code uitgevoerd op drie knoop punten parallel onder verschillende context/rol.
2. De status opgeslagen in de stateful service moet consistent zijn voor alle replica's. De status Manager en de betrouw bare verzamelingen geven deze consistentie out-of-the-box. De status in het geheugen moet echter worden beheerd door de toepassings code.
3. Elke replica wijzigt rollen op een bepaald moment tijdens het uitvoeren van het cluster. Een secundaire replica wordt een primair, in het geval dat het knoop punt dat als host fungeert voor de primaire, niet beschikbaar of overbelast is. Dit is natuurlijk gedrag voor Service Fabric daarom moeten services moeten plannen die uiteindelijk onder een andere rol moeten worden uitgevoerd.

In dit artikel wordt ervan uitgegaan dat de [Services voor het testen van de eenheid zijn gelezen in service Fabric](service-fabric-concepts-unit-testing.md) .

## <a name="the-servicefabricmocks-library"></a>De bibliotheek ServiceFabric. modelers
Vanaf versie 3.3.0 biedt [ServiceFabric.-modellen](https://www.nuget.org/packages/ServiceFabric.Mocks/) een API voor het aftrekken van de indeling van de replica's en het status beheer. Dit wordt gebruikt in de voor beelden.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/) 
 [Github](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.-modellen zijn geen eigendom van of worden onderhouden door micro soft. Dit is echter de aanbevolen bibliotheek van micro soft voor de stateful Services voor het testen van eenheden.*

## <a name="set-up-the-mock-orchestration-and-state"></a>De indeling en status van het model instellen
Als onderdeel van de rang schikking van een test, worden er een model replicaset en status Manager gemaakt. De replicaset maakt vervolgens een eigen exemplaar van de geteste service voor elke replica. Er wordt ook eigenaar van levenscyclus gebeurtenissen, zoals `OnChangeRole` en `RunAsync` . De status Manager van de Modeler zorgt ervoor dat alle bewerkingen die worden uitgevoerd op de status Manager worden uitgevoerd en bewaard als de daad werkelijke status Manager.

1. Maak een service Factory-gemachtigde waarmee wordt geïnstantieerd dat de service wordt getest. Dit moet gelijk zijn aan of hetzelfde zijn als de call back service Factory die meestal wordt gevonden in `Program.cs` voor een service Fabric service of actor. Dit moet de volgende hand tekening volgen:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Een instantie van `MockReliableStateManager` klasse maken. Hiermee worden alle interacties met de status Manager gesimuleerd.
3. Maak een instantie van `MockStatefulServiceReplicaSet<TStatefulService>` waar `TStatefulService` wordt het type van de service die wordt getest. Hiervoor moet de gemachtigde die is gemaakt in stap #1 en de status beheerder in #2
4. Replica's toevoegen aan de replicaset. Geef de rol op (zoals primair, ActiveSecondary, IdleSecondary) en de ID van de replica
   > Wacht op de replica-Id's. Deze worden waarschijnlijk gebruikt tijdens de Act-en bevestigings delen van een eenheids test.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Service aanvragen uitvoeren
Service aanvragen kunnen worden uitgevoerd op een specifieke replica met behulp van de gebruiks gemak eigenschappen en lookups.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Een service verplaatsing uitvoeren
De replicaset van de Modeler stelt verschillende manieren voor het activeren van verschillende typen service verplaatsingen.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Alles samenvoegen
De volgende test laat zien hoe u een replicaset met drie knoop punten instelt en controleert of de gegevens beschikbaar zijn vanaf een secundaire nadat een rol is gewijzigd. Een veelvoorkomend probleem is dat dit kan worden veroorzaakt doordat de gegevens `InsertAsync` die zijn toegevoegd, zijn opgeslagen in een geheugen of een betrouw bare verzameling zonder dat ze worden uitgevoerd `CommitAsync` . In beide gevallen is het secundaire is niet gesynchroniseerd met de primaire. Dit zou leiden tot inconsistente reacties na het verplaatsen van de service.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Leer hoe u [service-naar-service-communicatie](service-fabric-testability-scenarios-service-communication.md) kunt testen en [fouten kunt simuleren met behulp van beheerde chaos](service-fabric-controlled-chaos.md).
