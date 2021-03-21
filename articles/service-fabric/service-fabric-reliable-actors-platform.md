---
title: Reliable Actors op Service Fabric
description: Hierin wordt beschreven hoe Reliable Actors op Reliable Services worden gelaagd en de functies van het Service Fabric platform gebruikt.
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e204b47b7fa9f528341507c315c926159d524e13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789579"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hoe Reliable Actors het Service Fabric platform gebruiken?
In dit artikel wordt uitgelegd hoe Reliable Actors werkt op het Azure Service Fabric-platform. Reliable Actors uitgevoerd in een framework dat wordt gehost in een implementatie van een stateful reliable-service met de naam *actor service*. De actor-service bevat alle onderdelen die nodig zijn voor het beheren van de levens cyclus en het verzenden van berichten voor uw actors:

* De actor-runtime beheert de levens cyclus, garbage collection en dwingt toegang met één thread af.
* Een externe listener voor actor service accepteert RAS-aanroepen naar actors en verzendt deze naar een dispatcher om naar het juiste actor-exemplaar te sturen.
* De actor State-provider verloopt status providers (zoals de provider van de betrouw bare verzamelingen) en biedt een adapter voor actor status beheer.

Deze onderdelen vormen samen het reliable actor-Framework.

## <a name="service-layering"></a>Service lagen
Omdat de actor service zelf een betrouw bare service is, zijn alle [toepassings modellen](service-fabric-application-model.md), levenscyclus, [verpakking](service-fabric-package-apps.md), [implementatie](service-fabric-deploy-remove-applications.md), upgrade en schaal concepten van reliable Services op dezelfde manier op actor Services toegepast.

![Actor service layering][1]

In het voor gaande diagram ziet u de relatie tussen de Service Fabric toepassings raamwerken en gebruikers code. Blauwe elementen vertegenwoordigen het Reliable Services-toepassings raamwerk, het betrouw bare actor-Framework vertegenwoordigt en groen staat voor gebruikers code.

In Reliable Services neemt uw service de klasse over `StatefulService` . Deze klasse is afgeleid van `StatefulServiceBase` (of `StatelessService` voor stateless Services). In Reliable Actors gebruikt u de actor-service. De actor-service is een andere implementatie van de `StatefulServiceBase` klasse die het actor patroon implementeert waar uw actoren worden uitgevoerd. Omdat de actor service zelf slechts een implementatie van is `StatefulServiceBase` , kunt u uw eigen service schrijven die is afgeleid van `ActorService` en serviceniveau functies op dezelfde manier als bij overname zou implementeren `StatefulService` , zoals:

* Back-up en herstel van de service.
* Gedeelde functionaliteit voor alle actors, bijvoorbeeld een circuit onderbreker.
* Externe procedure aanroepen in de actor-service zelf en op elke afzonderlijke actor.

Zie [implementatie van functies op service niveau in uw actor service](service-fabric-reliable-actors-using.md)voor meer informatie.

## <a name="application-model"></a>Toepassingsmodel
Actor Services zijn Reliable Services, waardoor het toepassings model hetzelfde is. De hulpprogram ma's voor het bouwen van actor Framework genereren echter enkele van de toepassings model bestanden voor u.

### <a name="service-manifest"></a>Service manifest
De hulpprogram ma's voor het bouwen van actor Framework genereren automatisch de inhoud van het ServiceManifest.xml bestand van uw actor-service. Dit bestand bevat:

* Type actor service. De type naam wordt gegenereerd op basis van de project naam van uw actor. Op basis van het kenmerk persistentie op uw actor wordt de HasPersistedState-vlag ook dienovereenkomstig ingesteld.
* Code pakket.
* Configuratie pakket.
* Resources en eind punten.

### <a name="application-manifest"></a>Manifest van de toepassing
De hulpprogram ma's voor het bouwen van actor Framework maken automatisch een standaard service definitie voor uw actor service. De build-hulpprogram ma's vullen de standaard service-eigenschappen in:

* Aantal replica sets wordt bepaald door het kenmerk persistentie van uw actor. Telkens wanneer het kenmerk persistentie op uw actor wordt gewijzigd, wordt het aantal replica sets in de standaard service definitie dienovereenkomstig opnieuw ingesteld.
* Het partitie schema en het bereik zijn ingesteld op uniforme Int64 met het volledige Int64-sleutel bereik.

## <a name="service-fabric-partition-concepts-for-actors"></a>Service Fabric partitie concepten voor Actors
Actor Services zijn gepartitioneerde stateful Services. Elke partitie van een actor service bevat een set actors. Service partities worden automatisch gedistribueerd over meerdere knoop punten in Service Fabric. Actor instanties worden als resultaat gedistribueerd.

![Actor partitioneren en distribueren][5]

Reliable Services kunnen worden gemaakt met verschillende partitie schema's en partitie sleutel bereik. De actor-service maakt gebruik van het schema Int64 partitioneren met het volledige Int64-sleutel bereik om actors toe te wijzen aan partities.

### <a name="actor-id"></a>Actor-ID
Aan elke actor die in de service is gemaakt, is een unieke ID gekoppeld die wordt vertegenwoordigd door de `ActorId` klasse. `ActorId` is een ondoorzichtige ID-waarde die kan worden gebruikt voor een uniforme distributie van actors in de service partities door wille keurige Id's te genereren:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Elke `ActorId` wordt naar een Int64 gehasht. Daarom moet de actor-service een schema voor gegevens type Int64 gebruiken met het volledige Int64-sleutel bereik. Aangepaste ID-waarden kunnen echter worden gebruikt voor een `ActorID` , inclusief guid's/uuid, teken reeksen en Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Wanneer u GUID'S/UUID en teken reeksen gebruikt, worden de waarden gehasht naar een Int64. Wanneer u echter expliciet een Int64 levert aan een `ActorId` , wordt het gegevens type int64 rechtstreeks toegewezen aan een partitie zonder verdere hashing. U kunt deze methode gebruiken om te bepalen met welke partitie de actors worden geplaatst.


## <a name="next-steps"></a>Volgende stappen
* [Beheer van actor status](service-fabric-reliable-actors-state-management.md)
* [Actor-levens cyclus en garbagecollection](service-fabric-reliable-actors-lifecycle.md)
* [Naslag documentatie voor actors-API](/dotnet/api/microsoft.servicefabric.actors)
* [.NET-voorbeeld code](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-voorbeeld code](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
