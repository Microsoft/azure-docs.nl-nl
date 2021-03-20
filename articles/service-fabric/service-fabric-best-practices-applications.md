---
title: Aanbevolen procedures voor het ontwerpen van Azure Service Fabric-toepassingen
description: Aanbevolen procedures en ontwerp overwegingen voor het ontwikkelen van toepassingen en services met behulp van Azure Service Fabric.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: ddf846e9e3ac6add7cf3f584b702de5accfb22af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91538495"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Aanbevolen procedures voor het ontwerpen van Azure Service Fabric-toepassingen

Dit artikel bevat best practice richt lijnen voor het bouwen van toepassingen en services op Azure Service Fabric.
 
## <a name="get-familiar-with-service-fabric"></a>Vertrouwd raken met Service Fabric
* Lees het artikel [wat u graag wilt weten over service Fabric?](service-fabric-content-roadmap.md) .
* Meer informatie over [service Fabric toepassings scenario's](service-fabric-application-scenarios.md).
* Meer informatie over de programmeer model opties kunt u lezen [service Fabric model overzicht](service-fabric-choose-framework.md).



## <a name="application-design-guidance"></a>Richt lijnen voor het ontwerpen van toepassingen
Vertrouwd raken met de [algemene architectuur](/azure/architecture/reference-architectures/microservices/service-fabric) van service Fabric toepassingen en hun [ontwerp overwegingen](/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations).

### <a name="choose-an-api-gateway"></a>Een API-gateway kiezen
Gebruik een API-Gateway Service die communiceert met back-end-services die vervolgens kunnen worden uitgeschaald. De meest gebruikte API-Gateway Services zijn:

- [Azure API Management](./service-fabric-api-management-overview.md), dat is [geïntegreerd met Service Fabric](./service-fabric-tutorial-deploy-api-management.md).
- [Azure IOT hub](../iot-hub/index.yml) of [Azure Event hubs](../event-hubs/index.yml)met behulp van de [ServiceFabricProcessor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.ServiceFabricProcessor) om te lezen uit Event hub-partities.
- [Træfik reverse proxy](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric), met behulp van de [Azure service Fabric-provider](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/).
- [Azure-toepassing gateway](../application-gateway/index.yml).

   > [!NOTE] 
   > Azure-toepassing gateway is niet rechtstreeks geïntegreerd met Service Fabric. Azure API Management is doorgaans de voorkeurs optie.
- Uw eigen aangepaste [ASP.net core](./service-fabric-reliable-services-communication-aspnetcore.md) webapplication gateway.

### <a name="stateless-services"></a>Stateless Services
We raden u aan om altijd stateless services te bouwen met behulp van [reliable Services](./service-fabric-reliable-services-introduction.md) en de opslag status in een Azure-data base, Azure Cosmos DB of Azure Storage. De externe status is de vertrouwdste benadering van de meeste ontwikkel aars. Met deze aanpak kunt u ook gebruikmaken van query mogelijkheden in de Store.  

### <a name="when-to-use-stateful-services"></a>Wanneer stateful Services gebruiken
Overweeg stateful Services wanneer u een scenario voor een lage latentie hebt en de gegevens dicht bij de berekening wilt houden. Enkele voor beelden van scenario's zijn IoT Digital dubbele apparaten, spel status, sessie status, gegevens uit een data base in de cache opslaan en langlopende werk stromen voor het bijhouden van aanroepen naar andere services.

Bepaal het tijds bestek voor gegevens retentie:

- **Gegevens in cache**. Gebruik caching als latentie voor externe winkels een probleem is. Gebruik een stateful service als uw eigen gegevens cache of overweeg de [open-source SoCreate service Fabric gedistribueerde cache](https://github.com/SoCreate/service-fabric-distributed-cache)te gebruiken. In dit scenario hoeft u zich geen zorgen te maken als alle gegevens in de cache verloren zijn gegaan.
- **Tijdgebonden gegevens**. In dit scenario moet u gegevens dichtbij houden om gedurende een bepaalde periode voor een latentie te berekenen, maar kunt u de gegevens in een *nood* geval kwijt raken. Zo moeten gegevens in veel IoT-oplossingen dicht bij de hand worden gebracht, bijvoorbeeld wanneer de gemiddelde Tempe ratuur gedurende de afgelopen paar dagen wordt berekend, maar als deze gegevens verloren zijn gegaan, zijn de specifieke gegevens punten die worden geregistreerd niet die belang rijk. In dit scenario is het meestal niet belang rijk dat u een back-up maakt van de afzonderlijke gegevens punten. U kunt alleen een back-up maken van berekende gemiddelde waarden die regel matig worden geschreven naar externe opslag.  
- **Lange termijn gegevens**. Met betrouw bare verzamelingen kunt u uw gegevens permanent opslaan. Maar in dit geval moet u [voor bereidingen treffen voor herstel na nood gevallen](./service-fabric-disaster-recovery.md), waaronder het [configureren van beleid voor periodieke back-ups](./service-fabric-backuprestoreservice-configure-periodic-backup.md) voor uw clusters. In feite configureert u wat er gebeurt als uw cluster wordt vernietigd in een nood geval, waar u een nieuw cluster zou moeten maken en nieuwe toepassings exemplaren moet implementeren en herstellen van de laatste back-up.

Bespaar kosten en verbeter de beschik baarheid:
- U kunt kosten reduceren door stateful services te gebruiken, omdat u geen gegevens toegang hebt tot de kosten en trans acties in de externe Store, en omdat u geen andere service hoeft te gebruiken, zoals Azure cache voor redis.
- Het gebruik van stateful Services is voornamelijk voor opslag en niet voor reken kracht en wordt niet aanbevolen. U beschouwt stateful Services als Compute met de lokale goedkope opslag.
- Door afhankelijkheden op andere services te verwijderen, kunt u de beschik baarheid van uw service verbeteren. Als u de status met HA in het cluster beheert, worden de problemen met de uitval tijd van andere services of latenties geïsoleerd.

## <a name="how-to-work-with-reliable-services"></a>Werken met Reliable Services
Met Service Fabric Reliable Services kunt u eenvoudig stateless en stateful Services maken. Zie de [Inleiding tot reliable Services](./service-fabric-reliable-services-introduction.md)voor meer informatie.
- Altijd het [annulerings token](./service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps) in de `RunAsync()` methode voor stateless en stateful Services en de `ChangeRole()` methode voor stateful Services. Als u dit niet doet, weet Service Fabric niet of uw service kan worden gesloten. Als u bijvoorbeeld niet voldoet aan het annulerings token, kunnen er veel langere tijden voor de upgrade van toepassingen optreden.
-    Open en sluit [communicatie-listeners](./service-fabric-reliable-services-communication.md) tijdig en honoreer de annulerings tokens.
-    Combi neer synchronisatie code nooit met async-code. Gebruik bijvoorbeeld niet `.GetAwaiter().GetResult()` in uw async-aanroepen. Gebruik async *all de methode* via de aanroep stack.

## <a name="how-to-work-with-reliable-actors"></a>Werken met Reliable Actors
Met Service Fabric Reliable Actors kunt u eenvoudig stateful, virtuele actors maken. Zie de [Inleiding tot reliable actors](./service-fabric-reliable-actors-introduction.md)voor meer informatie.

- Het is belang rijk dat u pub/submessa ging tussen uw actoren gebruikt voor het schalen van uw toepassing. Hulpprogram ma's voor deze service zijn onder andere [open-source SoCreate service Fabric pub/sub](https://service-fabric-pub-sub.socreate.it/) en [Azure service bus](/azure/service-bus/).
- Maak de actor status zo [nauw keurig mogelijk](./service-fabric-reliable-actors-state-management.md#best-practices).
- De [levens cyclus van de actor](./service-fabric-reliable-actors-state-management.md#best-practices)beheren. Verwijder actors als u ze niet meer wilt gebruiken. Het verwijderen van overbodige actors is vooral belang rijk wanneer u de [veranderlijke State-provider](./service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)gebruikt, omdat alle statussen in het geheugen worden opgeslagen.
- Vanwege hun [op hun beurt gebaseerde gelijktijdigheid](./service-fabric-reliable-actors-introduction.md#concurrency)worden actors het beste gebruikt als onafhankelijke objecten. Maak geen grafieken van multi actor-, synchrone-methode aanroepen (die elk waarschijnlijk een afzonderlijke netwerk aanroep worden) of maak circulaire actor-aanvragen. Dit is van invloed op de prestaties en schaal baarheid.
- Combi neer de synchronisatie code niet met een async-code. Gebruik async consistent om prestatie problemen te voor komen.
- Maak geen langlopende aanroepen in actors. Met langlopende aanroepen worden andere aanroepen naar dezelfde actor geblokkeerd vanwege de gelijktijdigheid op basis van een op te slaan.
- Als u communiceert met andere services met [service Fabric externe toegang](./service-fabric-reliable-services-communication-remoting.md) en u een maakt `ServiceProxyFactory` , maakt u de Factory op het niveau van de [actor-service](./service-fabric-reliable-actors-using.md) en *niet* op het actor niveau.


## <a name="application-diagnostics"></a>Application Diagnostics
Wees uitgebreid met het toevoegen van [toepassings logboeken](./service-fabric-diagnostics-event-generation-app.md) in service aanroepen. Het helpt u bij het vaststellen van scenario's waarin Services elkaar aanroepen. Wanneer een aanroepen B bijvoorbeeld C-aanroepen aanroept, kan de aanroep nergens worden uitgevoerd. Als u onvoldoende logboek registratie hebt, zijn fouten moeilijk te onderzoeken. Als de services te veel worden geregistreerd vanwege aanroepende volumes, moet u ervoor zorgen dat u ten minste logboek fouten en waarschuwingen meldt.

## <a name="iot-and-messaging-applications"></a>IoT-en Messa ging-toepassingen
Gebruik [ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/ServiceFabricProcessor)als u berichten leest van [Azure IoT Hub](../iot-hub/index.yml) of [Azure Event hubs](../event-hubs/index.yml). ServiceFabricProcessor kan worden geïntegreerd met Service Fabric Reliable Services om de status van het lezen van de Event Hub partities te behouden en nieuwe berichten naar uw services te pushen via de- `IEventProcessor::ProcessEventsAsync()` methode.


## <a name="design-guidance-on-azure"></a>Ontwerp richtlijnen voor Azure
* Ga naar het [Azure Architecture Center](/azure/architecture/microservices/) voor ontwerp richtlijnen voor het bouwen van micro [Services in azure](/azure/architecture/microservices/).

* Ga aan de [slag met Azure voor gaming](/gaming/azure/) voor ontwerp richtlijnen over het [gebruik van service fabric in gaming services](/gaming/azure/reference-architectures/multiplayer-synchronous-sf).
