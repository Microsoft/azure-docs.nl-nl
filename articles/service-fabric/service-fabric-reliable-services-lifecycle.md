---
title: Overzicht van de levens cyclus van Reliable Services
description: Meer informatie over de levens cyclus gebeurtenissen in een Azure Service Fabric Reliable Services toepassing voor stateful en stateless Services.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6ea8fa6933052374721d8d205d5b07386c807ae2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784593"
---
# <a name="reliable-services-lifecycle-overview"></a>Overzicht van Reliable Services levenscyclus
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java op Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Wanneer u op de hoogte bent van de levens cycli van Azure Service Fabric Reliable Services, zijn de basis principes van de levens cyclus het belangrijkst. In het algemeen omvat de levens cyclus het volgende:

- Tijdens het opstarten:
  - Services worden samengesteld.
  - De services hebben de mogelijkheid om nul of meer listeners te maken en te retour neren.
  - Alle geretourneerde listeners worden geopend, waardoor communicatie mogelijk is met de service.
  - De methode **RunAsync** van de service wordt aangeroepen, waardoor de service langlopende taken of achtergrond werkzaamheden kan uitvoeren.
- Tijdens afsluiten:
  - Het annulerings token dat is door gegeven aan **RunAsync** is geannuleerd en de listeners zijn gesloten.
  - Nadat de listeners zijn gesloten, wordt het Service object zelf afgezet.

Er zijn details over de exacte volg orde van deze gebeurtenissen. De volg orde van gebeurtenissen kan enigszins veranderen, afhankelijk van het feit of de betrouw bare service stateless of stateful is. Daarnaast moeten we voor stateful Services het primaire swap scenario afhandelen. Tijdens deze reeks wordt de rol van primaire naar een andere replica overgedragen (of wordt deze weer gegeven) zonder dat de service wordt afgesloten. Ten slotte moeten we nadenken over fout-of fout voorwaarden.

## <a name="stateless-service-startup"></a>Stateless service opstarten
De levens cyclus van een stateless service is eenvoudig. Dit is de volg orde van gebeurtenissen:

1. De service is gebouwd.
2. Twee dingen gebeuren vervolgens parallel:
    - `StatelessService.CreateServiceInstanceListeners()` wordt aangeroepen en alle geretourneerde listeners worden geopend. `ICommunicationListener.OpenAsync()` wordt aangeroepen voor elke listener.
    - De methode van de service `StatelessService.RunAsync()` wordt aangeroepen.
3. Indien aanwezig, wordt de methode van de service `StatelessService.OnOpenAsync()` aangeroepen. Deze aanroep is een ongebruikelijke onderdrukking, maar is wel beschikbaar. Uitgebreide service-initialisatie taken kunnen op dit moment worden gestart.

Denk eraan dat er geen volg orde is tussen de aanroepen om de listeners en **RunAsync** te maken en te openen. De listeners kunnen worden geopend voordat **RunAsync** wordt gestart. Op dezelfde manier kunt u **RunAsync** aanroepen voordat de communicatie-listeners zijn geopend of zelfs zijn gebouwd. Als een synchronisatie vereist is, wordt deze overgelaten als oefening voor de uitvoerder. Hier volgen enkele veelvoorkomende oplossingen:

  - Soms werken listeners niet totdat er een andere informatie is gemaakt of wordt uitgevoerd. Voor stateless Services kan dat werk doorgaans worden uitgevoerd op andere locaties, zoals de volgende: 
    - In de constructor van de service.
    - Tijdens de `CreateServiceInstanceListeners()` aanroep.
    - Als onderdeel van de constructie van de listener zelf.
  - Soms wordt de code in **RunAsync** pas gestart als de listeners zijn geopend. In dit geval is verdere coördinatie nood zakelijk. Een veelvoorkomende oplossing is dat er in de listeners een markering is die aangeeft wanneer deze zijn voltooid. Deze markering wordt vervolgens ingecheckt in **RunAsync** voordat u doorgaat met de werkelijke hoeveelheid werk.

## <a name="stateless-service-shutdown"></a>Stateless service afsluiten
Voor het afsluiten van een stateless service wordt hetzelfde patroon gevolgd, net zo omgekeerd:

1. Parallel:
    - Alle geopende listeners worden gesloten. `ICommunicationListener.CloseAsync()` wordt aangeroepen voor elke listener.
    - Het annulerings token dat is door gegeven aan `RunAsync()` , is geannuleerd. Een controle van de eigenschap van het annulerings token `IsCancellationRequested` retourneert True en als de token methode wordt aangeroepen, wordt `ThrowIfCancellationRequested` een gegenereerd `OperationCanceledException` .
2. Na `CloseAsync()` voltooiing van elke listener, `RunAsync()` wordt de methode van de service `StatelessService.OnCloseAsync()` aangeroepen, indien aanwezig.  OnCloseAsync wordt aangeroepen wanneer de stateless service-instantie zonder problemen wordt afgesloten. Dit kan gebeuren wanneer de code van de service wordt bijgewerkt, het service-exemplaar wordt verplaatst vanwege taak verdeling of als er een tijdelijke fout wordt gedetecteerd. Het is ongebruikelijk dat ze `StatelessService.OnCloseAsync()` worden overschreven, maar dit kan worden gebruikt om resources veilig te sluiten, de achtergrond verwerking te stoppen, de externe status op te slaan of bestaande verbindingen te sluiten.
3. Na `StatelessService.OnCloseAsync()` voltooiing wordt het Service object afgestructd.

## <a name="stateful-service-startup"></a>Stateful service starten
Stateful Services hebben een vergelijkbaar patroon voor stateless Services, met een paar wijzigingen. Voor het starten van een stateful service is de volg orde van gebeurtenissen als volgt:

1. De service is gebouwd.
2. `StatefulServiceBase.OnOpenAsync()` wordt aangeroepen. Deze aanroep wordt niet vaak overschreven in de service.
3. De volgende acties worden parallel uitgevoerd:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` is aangeroepen. 
      - Als de service een primaire service is, worden alle geretourneerde listeners geopend. `ICommunicationListener.OpenAsync()` wordt aangeroepen voor elke listener.
      - Als de service een secundaire service is, worden alleen de listeners die zijn gemarkeerd als `ListenOnSecondary = true` geopend. Luister aars die open zijn op de secundaire zones, zijn minder gangbaar.
    - Als de service momenteel een primair is, wordt de methode van de service `StatefulServiceBase.RunAsync()` aangeroepen.
4. Nadat alle aanroepen van de replica-listener zijn `OpenAsync()` voltooid en `RunAsync()` wordt aangeroepen, `StatefulServiceBase.OnChangeRoleAsync()` wordt aangeroepen. Deze aanroep wordt niet vaak overschreven in de service.

Net als stateless Services is er geen coördinatie tussen de volg orde waarin de listeners worden gemaakt en geopend en wanneer **RunAsync** wordt aangeroepen. Als u coördinatie nodig hebt, zijn de oplossingen veel hetzelfde. Er is één extra geval voor stateful service. Stel dat de aanroepen die binnenkomen bij de communicatie-listeners, gegevens moeten bewaren in enkele [betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Omdat de communicatie-listeners kunnen worden geopend voordat de betrouw bare verzamelingen Lees bare of beschrijfbaar zijn, en voordat **RunAsync** kan worden gestart, is een andere coördinatie nood zakelijk. De eenvoudigste en meest voorkomende oplossing is voor de communicatie-listeners om een fout code te retour neren die de client gebruikt om de aanvraag opnieuw uit te voeren.

## <a name="stateful-service-shutdown"></a>Stateful service afsluiten
Net als stateless Services zijn de levenscyclus gebeurtenissen tijdens het afsluiten hetzelfde als tijdens het opstarten, maar omgekeerd. Wanneer een stateful service wordt afgesloten, treden de volgende gebeurtenissen op:

1. Parallel:
    - Alle geopende listeners worden gesloten. `ICommunicationListener.CloseAsync()` wordt aangeroepen voor elke listener.
    - Het annulerings token dat is door gegeven aan `RunAsync()` , is geannuleerd. Een controle van de eigenschap van het annulerings token `IsCancellationRequested` retourneert True en als de token methode wordt aangeroepen, wordt `ThrowIfCancellationRequested` een gegenereerd `OperationCanceledException` .
2. Nadat `CloseAsync()` de service is voltooid op elke listener `RunAsync()` , wordt deze ook `StatefulServiceBase.OnChangeRoleAsync()` wel voltooid. Deze aanroep wordt niet vaak overschreven in de service.

   > [!NOTE]  
   > De nood zaak te wachten tot **RunAsync** is voltooid, is alleen nodig als deze replica een primaire replica is.

3. Nadat de `StatefulServiceBase.OnChangeRoleAsync()` methode is voltooid, `StatefulServiceBase.OnCloseAsync()` wordt de methode aangeroepen. Deze aanroep is een ongebruikelijke onderdrukking, maar is wel beschikbaar.
3. Na `StatefulServiceBase.OnCloseAsync()` voltooiing wordt het Service object afgestructd.

## <a name="stateful-service-primary-swaps"></a>Primaire swaps van stateful service
Terwijl een stateful service wordt uitgevoerd, hebben alleen de primaire replica's van die stateful Services hun communicatie-listeners geopend en wordt de **RunAsync** -methode aangeroepen. Secundaire replica's zijn gebouwd, maar er worden geen verdere aanroepen weer geven. Terwijl er een stateful service wordt uitgevoerd, kan de replica die op dat moment primair is, veranderen als gevolg van een fout of optimalisatie van cluster Balancing. Wat betekent dit voor de levens cyclus gebeurtenissen die een replica kan zien? Het gedrag dat de stateful replica ziet, is afhankelijk van het feit of de replica tijdens de wisseling wordt gedegradeerd of gepromoveerd.

### <a name="for-the-primary-thats-demoted"></a>Voor de primaire laag die is gedegradeerd
Voor de primaire replica die wordt gedegradeerd, heeft Service Fabric deze replica nodig om de verwerking van berichten te stoppen en het op de achtergrond uitgevoerde werk af te sluiten. Als gevolg hiervan lijkt deze stap op het moment dat de service wordt afgesloten. Een verschil is dat de service niet is gestructd of gesloten omdat deze als een secundaire server blijft. De volgende Api's worden aangeroepen:

1. Parallel:
    - Alle geopende listeners worden gesloten. `ICommunicationListener.CloseAsync()` wordt aangeroepen voor elke listener.
    - Het annulerings token dat is door gegeven aan `RunAsync()` , is geannuleerd. Een controle van de eigenschap van het annulerings token `IsCancellationRequested` retourneert True en als de token methode wordt aangeroepen, wordt `ThrowIfCancellationRequested` een gegenereerd `OperationCanceledException` .
2. Nadat `CloseAsync()` de service is voltooid op elke listener `RunAsync()` , wordt deze ook `StatefulServiceBase.OnChangeRoleAsync()` wel voltooid. Deze aanroep wordt niet vaak overschreven in de service.

### <a name="for-the-secondary-thats-promoted"></a>Voor het secundaire niveau dat is bevorderd
Op dezelfde manier moet Service Fabric de secundaire replica die wordt bevorderd, worden gepromoveerd om te Luis teren naar berichten op de kabel en alle achtergrond taken te starten die moeten worden voltooid. Als gevolg hiervan lijkt dit proces op het moment dat de service wordt gemaakt, behalve dat de replica zelf al bestaat. De volgende Api's worden aangeroepen:

1. Parallel:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` wordt aangeroepen en alle geretourneerde listeners worden geopend. `ICommunicationListener.OpenAsync()` wordt aangeroepen voor elke listener.
    - De methode van de service `StatefulServiceBase.RunAsync()` wordt aangeroepen.
2. Nadat alle aanroepen van de replica-listener zijn `OpenAsync()` voltooid en `RunAsync()` wordt aangeroepen, `StatefulServiceBase.OnChangeRoleAsync()` wordt aangeroepen. Deze aanroep wordt niet vaak overschreven in de service.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Veelvoorkomende problemen tijdens het afsluiten van stateful service en primaire degradatie
Service Fabric de primaire van een stateful service om verschillende redenen wijzigt. Het meest voorkomende is [cluster herverdeling](service-fabric-cluster-resource-manager-balancing.md) en [toepassings upgrade](service-fabric-application-upgrade.md). Tijdens deze bewerkingen (en tijdens de normale afsluiting van de service, zoals u kunt zien of de service is verwijderd), is het belang rijk dat de service de `CancellationToken` . 

Services die de annulering niet op de juiste manier afhandelen, kunnen diverse problemen ondervinden. Deze bewerkingen zijn traag omdat Service Fabric wacht totdat de services op de juiste wijze worden gestopt. Dit kan uiteindelijk leiden tot mislukte upgrades die een time-out veroorzaken en terugdraaien. Het niet voldoen aan het annulerings token kan ook een niet-sluitende cluster veroorzaken. Clusters worden niet in rekening gebalanceerd omdat knoop punten warm zijn, maar de services kunnen niet worden gebalanceerd omdat het te lang duurt om ze elders te verplaatsen. 

Omdat de services stateful zijn, is het ook waarschijnlijk dat ze de [betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md)gebruiken. In Service Fabric, wanneer een primaire laag wordt gedegradeerd, is een van de eerste dingen die schrijf toegang tot de onderliggende status is ingetrokken. Dit leidt tot een tweede set problemen die van invloed kunnen zijn op de levens cyclus van de service. De verzamelingen retour uitzonde ringen op basis van de timing en of de replica wordt verplaatst of afgesloten. Deze uitzonde ringen moeten correct worden afgehandeld. Uitzonde ringen die worden veroorzaakt door service Fabric vallen in permanente [( `FabricException` )](/dotnet/api/system.fabric.fabricexception) en tijdelijke [( `FabricTransientException` )](/dotnet/api/system.fabric.fabrictransientexception) categorieën. Permanente uitzonde ringen moeten worden vastgelegd en gegenereerd terwijl de tijdelijke uitzonde ringen opnieuw kunnen worden uitgevoerd op basis van een poging tot logica.

Het afhandelen van de uitzonde ringen die afkomstig zijn van het gebruik van de `ReliableCollections` in combi natie met Service levenscyclus gebeurtenissen is een belang rijk onderdeel van het testen en valideren van een betrouw bare service. U wordt aangeraden uw service altijd onder belasting te plaatsen tijdens het uitvoeren van upgrades en [chaos tests](service-fabric-controlled-chaos.md) voordat u de productie implementeert. Met deze eenvoudige stappen kunt u ervoor zorgen dat uw service correct is geïmplementeerd en levenscyclus gebeurtenissen correct verwerkt.


## <a name="notes-on-the-service-lifecycle"></a>Opmerkingen over de levens cyclus van de service
  - Zowel de `RunAsync()` methode als de `CreateServiceReplicaListeners/CreateServiceInstanceListeners` aanroepen zijn optioneel. Een service kan een van beide of geen van beide hebben. Als de service bijvoorbeeld alle werk in reactie op gebruikers aanroepen doet, is het niet nodig om deze te implementeren `RunAsync()` . Alleen de communicatie-listeners en de bijbehorende code zijn nood zakelijk. Op dezelfde manier is het maken en retour neren van communicatie listeners optioneel, omdat de service alleen achtergrond taken kan uitvoeren, en dat daarom alleen moet worden geïmplementeerd `RunAsync()` .
  - Het is geldig voor het volt ooien van een service `RunAsync()` en het retour neren ervan. Volt ooien is geen fout voorwaarde. Met volt ooien `RunAsync()` wordt aangegeven dat het achtergrond werk van de service is voltooid. Voor stateful reliable Services `RunAsync()` wordt opnieuw aangeroepen als de replica van de primaire naar de secundaire laag wordt gedegradeerd en vervolgens weer wordt teruggedraaid naar primair.
  - Als een service wordt afgesloten `RunAsync()` door een onverwachte uitzonde ring te genereren, vormt dit een fout. Het Service object is afgesloten en er wordt een status fout gerapporteerd.
  - Hoewel er geen tijds limiet is voor het retour neren van deze methoden, verliest u onmiddellijk de mogelijkheid om te schrijven naar betrouw bare verzamelingen en kunnen daarom geen echte werkzaamheden worden uitgevoerd. Het wordt aanbevolen dat u zo snel mogelijk terugkeert wanneer u de annulerings aanvraag ontvangt. Als uw service niet binnen een redelijke hoeveelheid tijd reageert op deze API-aanroepen, kan Service Fabric uw service geforceerd beëindigen. Dit gebeurt gewoonlijk alleen tijdens het bijwerken van de toepassing of wanneer een service wordt verwijderd. Deze time-out is standaard 15 minuten.
  - Storingen in het `OnCloseAsync()` pad resulteren in het `OnAbort()` aanroepen van de service, wat een laatste mogelijkheid is om de services op te schonen die ze hebben geclaimd. Dit wordt meestal opgeroepen wanneer een permanente fout op het knoop punt wordt gedetecteerd of wanneer Service Fabric de levens cyclus van het service-exemplaar niet betrouwbaar kunt beheren als gevolg van interne storingen.
  - `OnChangeRoleAsync()` wordt aangeroepen wanneer de stateful service replica een andere rol heeft, bijvoorbeeld primair of secundair. Primaire replica's krijgen een schrijf status (mogen geen betrouw bare verzamelingen maken en ernaar schrijven). Secundaire replica's krijgen de status lezen (kan alleen lezen van bestaande betrouw bare verzamelingen). De meeste werk in een stateful service wordt uitgevoerd op de primaire replica. Secundaire replica's kunnen alleen-lezen validatie, rapporten genereren, gegevens analyse of andere alleen-lezen taken uitvoeren.

## <a name="next-steps"></a>Volgende stappen
- [Inleiding tot Reliable Services](service-fabric-reliable-services-introduction.md)
- [Reliable Services snel starten](service-fabric-reliable-services-quick-start.md)
- [Replica's en exemplaren](service-fabric-concepts-replica-lifecycle.md)
