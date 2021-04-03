---
title: Overzicht van het Service Fabric-programmeermodel
description: 'Service Fabric biedt twee frameworks voor het maken van services: het actor-Framework en het Services-Framework. Ze bieden afzonderlijke verhandelingen in eenvoud en controle.'
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 523c9e0064d8b78698f1a0dd3544bda58436800c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575717"
---
# <a name="service-fabric-programming-model-overview"></a>Overzicht van het Service Fabric-programmeermodel

Service Fabric biedt meerdere manieren om uw services te schrijven en te beheren. Services kunnen ervoor kiezen om de Service Fabric-Api's te gebruiken om optimaal te profiteren van de functies en toepassings raamwerken van het platform. Services kunnen ook alle gecompileerde uitvoer bare Program ma's zijn die zijn geschreven in elke taal of code die wordt uitgevoerd in een container die wordt gehost op een Service Fabric cluster.

## <a name="guest-executables"></a>Uitvoerbare gastbestanden

Een [uitvoerbaar gast bestand](service-fabric-guest-executables-introduction.md) is een bestaand, wille keurig uitvoerbaar bestand (geschreven in een wille keurige taal) dat als een service in uw toepassing kan worden uitgevoerd. Met uitvoer bare bestanden voor gasten worden de Service Fabric SDK-Api's niet rechtstreeks aanroepen. Ze profiteren echter nog steeds van de functies van het platform, zoals detectie van services, aangepaste status en belasting rapportage door de aanroepen van REST Api's die door Service Fabric worden weer gegeven. Ze hebben ook volledige ondersteuning voor toepassings levenscyclus.

Ga aan de slag met uitvoer bare gast bestanden door uw eerste [uitvoer bare gast toepassing](service-fabric-deploy-existing-app.md)te implementeren.

## <a name="containers"></a>Containers

Service Fabric implementeert en activeert standaard services als processen. Service Fabric kunnen ook services in [containers](service-fabric-containers-overview.md)implementeren. Service Fabric ondersteunt de implementatie van Linux-containers en Windows-containers in Windows Server 2016 en hoger. Container installatie kopieën kunnen worden opgehaald uit elke container opslagplaats en op de machine worden geïmplementeerd. U kunt bestaande toepassingen implementeren als uitvoer bare gast bestanden, Service Fabric stateless of stateful betrouw bare Services of Reliable Actors in containers, en u kunt Services in processen en services in containers in dezelfde toepassing combi neren.

[Meer informatie over het waarmee van uw services in Windows of Linux](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services is een licht gewicht Framework voor het schrijven van services die zijn geïntegreerd met het Service Fabric platform en profiteren van de volledige set platform functies. Reliable Services bieden een minimale set Api's waarmee de Service Fabric runtime de levens cyclus van uw services kan beheren en waarmee uw services kunnen communiceren met de runtime. Het toepassings raamwerk is mini maal, waardoor u volledige controle hebt over opties voor ontwerpen en implementaties en kan worden gebruikt voor het hosten van elk ander toepassings raamwerk, zoals ASP.NET Core.

Reliable Services kan stateless zijn, vergelijkbaar met de meeste service platforms, zoals webservers, waarin elk exemplaar van de service is gemaakt en waarvan de status persistent is in een externe oplossing, zoals Azure DB of Azure Table Storage.

Exclusief voor Service Fabric kan Reliable Services ook stateful zijn, waarbij de status direct in de service zelf wordt gebruikt met behulp van betrouw bare verzamelingen. De status wordt Maxi maal beschikbaar gemaakt via replicatie en gedistribueerd via partitioneren, die allemaal automatisch worden beheerd door Service Fabric.

[Lees meer over reliable Services](service-fabric-reliable-services-introduction.md) of ga aan de slag door [uw eerste betrouw bare service te schrijven](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core is een open-source platform voor meerdere platforms voor het bouwen van moderne toepassingen met Internet verbinding op basis van de Cloud, zoals web apps, IoT apps en mobiele back-ends. Service Fabric integreert met ASP.NET Core zodat u zowel stateless als stateful ASP.NET Core toepassingen kunt schrijven die gebruikmaken van betrouw bare verzamelingen en de geavanceerde mogelijkheden van de indeling van Service Fabric.

Meer [informatie over ASP.net core in service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) of aan de slag gaan door [uw eerste ASP.net core service Fabric toepassing te schrijven](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Het reliable actor Framework is gebaseerd op Reliable Services en is een toepassings raamwerk dat het [virtuele actor](https://research.microsoft.com/en-us/projects/orleans/) -patroon implementeert op basis van het reken kundige [actor model](https://en.wikipedia.org/wiki/Actor_model). Het reliable actor-Framework maakt gebruik van onafhankelijke eenheden van Compute en State met uitvoering met één thread en ' *actors*'. Het reliable actor-Framework biedt ingebouwde communicatie voor actors en vooraf ingestelde status persistentie en scale-out configuraties.

Omdat Reliable Actors een toepassings raamwerk is dat is gebouwd op Reliable Services, is het volledig geïntegreerd met het Service Fabric platform en de voor delen van de volledige set functies die worden geboden door het platform.

[Lees meer over reliable actors](service-fabric-reliable-actors-introduction.md) of ga aan de slag door [uw eerste betrouw bare actor service te schrijven](service-fabric-reliable-actors-get-started.md)

[Een front-end-service maken met behulp van ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Service Fabric en containers](service-fabric-containers-overview.md)

[Overzicht van Reliable Services](service-fabric-reliable-services-introduction.md)

[Overzicht van Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric en ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
