---
title: Azure Service Fabric Reliable Services levenscyclus
description: Meer informatie over de levens cyclus gebeurtenissen in een Azure Service Fabric Reliable Services-toepassing met behulp van Java voor stateful en stateless Services.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: b22c78a0259e4430ac6bfae1c0a9379c4a832cd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87324604"
---
# <a name="reliable-services-lifecycle"></a>Levenscyclus van Reliable Services
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java op Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services is een van de programmeer modellen die beschikbaar zijn in azure Service Fabric. Bij het leren over de levens cyclus van Reliable Services, is het belang rijk om inzicht te krijgen in de algemene levenscyclus gebeurtenissen. De exacte volg orde van gebeurtenissen is afhankelijk van de configuratie gegevens. 

Over het algemeen bevat de Reliable Services levenscyclus de volgende gebeurtenissen:

* Tijdens het opstarten:
  * Services worden samengesteld.
  * Services hebben de mogelijkheid om nul of meer listeners te maken en te retour neren.
  * Alle geretourneerde listeners worden geopend, voor communicatie met de service.
  * De methode van de service `runAsync` wordt aangeroepen, zodat de service langlopende of achtergrond taken kan uitvoeren.
* Tijdens afsluiten:
  * Het annulerings token dat is door gegeven aan `runAsync` is geannuleerd en de listeners zijn gesloten.
  * Het Service object zelf is ontstructd.

De volg orde van gebeurtenissen in Reliable Services kan enigszins veranderen, afhankelijk van het feit of de betrouw bare service stateless of stateful is. 

Voor stateful Services moet u ook het primaire swap scenario aanpakken. Tijdens deze reeks wordt de rol van primaire naar een andere replica overgedragen (of wordt deze weer gegeven) zonder dat de service wordt afgesloten. 

Ten slotte moet u nadenken over fout-of fout voorwaarden.

## <a name="stateless-service-startup"></a>Stateless service opstarten
De levens cyclus van een stateless service is redelijk eenvoudig. Dit is de volg orde van gebeurtenissen:

1. De service is gebouwd.
2. Deze gebeurtenissen treden parallel op:
    - `StatelessService.createServiceInstanceListeners()` wordt aangeroepen en alle geretourneerde listeners worden geopend. `CommunicationListener.openAsync()` wordt aangeroepen voor elke listener.
    - De methode van de service `runAsync` ( `StatelessService.runAsync()` ) wordt aangeroepen.
3. Indien aanwezig, wordt de eigen methode van de service `onOpenAsync` aangeroepen. Dit `StatelessService.onOpenAsync()` wordt specifiek genoemd. Dit is een ongebruikelijke onderdrukking, maar deze is wel beschikbaar.

Het is belang rijk te weten dat er geen volg orde is tussen de oproep om de listeners te maken en te openen en de aanroep naar `runAsync` . De listeners worden mogelijk geopend voordat deze `runAsync` wordt gestart. `runAsync`Dit kan ook worden opgeroepen voordat de communicatie-listeners zijn geopend of voordat ze zelfs zijn gebouwd. Als een synchronisatie is vereist, moet deze door de implementeerder worden uitgevoerd. Hier volgen enkele veelvoorkomende oplossingen:

* Soms werken listeners niet totdat andere informatie is gemaakt of andere werkzaamheden worden uitgevoerd. Voor stateless Services kan dat werk doorgaans worden uitgevoerd in de constructor van de service. Dit kan tijdens de aanroep worden gedaan `createServiceInstanceListeners()` , of als onderdeel van de constructie van de listener zelf.
* Soms wordt de code in `runAsync` niet gestart totdat de listeners zijn geopend. In dit geval is verdere coördinatie nood zakelijk. Een veelvoorkomende oplossing is het toevoegen van een vlag in de listeners. De vlag geeft aan wanneer de listeners zijn voltooid. `runAsync`Deze methode controleert dit voordat de werkelijke hoeveelheid werk wordt voortgezet.

## <a name="stateless-service-shutdown"></a>Stateless service afsluiten
Wanneer u een stateless service afsluit, wordt hetzelfde patroon gevolgd, maar in omgekeerde volg orde:

1. Deze gebeurtenissen treden parallel op:
    - Alle geopende listeners worden gesloten. `CommunicationListener.closeAsync()` wordt aangeroepen voor elke listener.
    - Het annulerings token dat is door gegeven aan `runAsync()` , is geannuleerd. Het controleren van de eigenschap van het annulerings token `isCancelled` retourneert `true` , en als deze wordt aangeroepen, genereert de methode van het token `throwIfCancellationRequested` een `CancellationException` .
2. Wanneer `closeAsync()` op elke listener is voltooid en `runAsync()` ook is voltooid, wordt de methode van de service `StatelessService.onCloseAsync()` aangeroepen als deze aanwezig is. Opnieuw is dit geen veelvoorkomende onderdrukking, maar kan ook worden gebruikt om resources veilig te sluiten, de achtergrond verwerking te stoppen, de externe status op te slaan of bestaande verbindingen te sluiten.
3. Na `StatelessService.onCloseAsync()` voltooiing wordt het Service object afgestructd.

## <a name="stateful-service-startup"></a>Stateful service starten
Stateful Services hebben een patroon dat vergelijkbaar is met stateless Services, met een paar wijzigingen.  Dit is de volg orde van gebeurtenissen voor het starten van een stateful service:

1. De service is gebouwd.
2. `StatefulServiceBase.onOpenAsync()` wordt aangeroepen. Deze aanroep wordt niet vaak overschreven in de service.
3. Deze gebeurtenissen treden parallel op:
    - `StatefulServiceBase.createServiceReplicaListeners()` is aangeroepen. 
      - Als de service een primaire service is, worden alle geretourneerde listeners geopend. `CommunicationListener.openAsync()` wordt aangeroepen voor elke listener.
      - Als de service een secundaire service is, worden alleen listeners die zijn gemarkeerd als `listenOnSecondary = true` geopend. Luister aars die open zijn op de secundaire zones, zijn minder gangbaar.
    - Als de service momenteel een primair is, wordt de methode van de service `StatefulServiceBase.runAsync()` aangeroepen.
4. Nadat alle aanroepen van de replica-listener zijn `openAsync()` voltooid en `runAsync()` wordt aangeroepen, `StatefulServiceBase.onChangeRoleAsync()` wordt aangeroepen. Deze aanroep wordt niet vaak overschreven in de service.

Net als stateless Services, in stateful service is er geen coördinatie tussen de volg orde waarin de listeners worden gemaakt en geopend en wanneer `runAsync` wordt aangeroepen. Als u coördinatie nodig hebt, zijn de oplossingen veel hetzelfde. Maar er is nog een extra geval voor stateful service. Stel dat de aanroepen die binnenkomen bij de communicatie-listeners, gegevens moeten bewaren in enkele [betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md). Omdat de communicatie-listeners mogelijk worden geopend voordat de betrouw bare verzamelingen kunnen worden gelezen of beschrijfbaar zijn en voordat ze worden `runAsync` gestart, is een extra coördinatie nood zakelijk. De eenvoudigste en meest voorkomende oplossing is voor de communicatie-listeners om een fout code te retour neren. De client gebruikt de fout code om te weten dat de aanvraag opnieuw moet worden uitgevoerd.

## <a name="stateful-service-shutdown"></a>Stateful service afsluiten
Net als stateless Services zijn de levenscyclus gebeurtenissen tijdens het afsluiten hetzelfde als tijdens het opstarten, maar omgekeerd. Wanneer een stateful service wordt afgesloten, treden de volgende gebeurtenissen op:

1. Deze gebeurtenissen treden parallel op:
    - Alle geopende listeners worden gesloten. `CommunicationListener.closeAsync()` wordt aangeroepen voor elke listener.
    - Het annulerings token dat is door gegeven aan `runAsync()` , is geannuleerd. Een aanroep van de methode van het annulerings token `isCancelled()` retourneert `true` , en als deze wordt aangeroepen, genereert de methode van het token `throwIfCancellationRequested()` een `OperationCanceledException` .
2. Nadat `closeAsync()` de service is voltooid op elke listener `runAsync()` , wordt deze ook `StatefulServiceBase.onChangeRoleAsync()` wel voltooid. Deze aanroep wordt niet vaak overschreven in de service.

   > [!NOTE]  
   > Wachten op `runAsync` volt ooien is alleen nodig als deze replica een primaire replica is.

3. Nadat de `StatefulServiceBase.onChangeRoleAsync()` methode is voltooid, `StatefulServiceBase.onCloseAsync()` wordt de methode aangeroepen. Deze aanroep is een ongebruikelijke onderdrukking, maar is wel beschikbaar.
3. Na `StatefulServiceBase.onCloseAsync()` voltooiing wordt het Service object afgestructd.

## <a name="stateful-service-primary-swaps"></a>Primaire swaps van stateful service
Terwijl een stateful service wordt uitgevoerd, worden communicatie-listeners geopend en `runAsync` wordt de methode alleen aangeroepen voor de primaire replica's van die stateful Services. Secundaire replica's zijn gebouwd, maar er worden geen verdere aanroepen weer geven. Terwijl een stateful service wordt uitgevoerd, kan de replica die op dat moment primair is, worden gewijzigd. De levens cyclus gebeurtenissen die een stateful replica kan zien, zijn afhankelijk van het feit of de replica tijdens de wisseling wordt gedegradeerd of bevorderd.

### <a name="for-the-demoted-primary"></a>Voor de laagst primaire
Service Fabric moet de primaire replica die wordt gedegradeerd, voor het stoppen van het verwerken van berichten en het stoppen van achtergrond werk. Deze stap is vergelijkbaar met wanneer de service wordt afgesloten. Het enige verschil is dat de service niet is gestructd of gesloten, omdat deze zich als een secundaire server bevindt. De volgende gebeurtenissen vinden plaats:

1. Deze gebeurtenissen treden parallel op:
    - Alle geopende listeners worden gesloten. `CommunicationListener.closeAsync()` wordt aangeroepen voor elke listener.
    - Het annulerings token dat is door gegeven aan `runAsync()` , is geannuleerd. Een controle van de methode van het annulerings token `isCancelled()` retourneert `true` . Als wordt aangeroepen, genereert de methode van het token `throwIfCancellationRequested()` een `OperationCanceledException` .
2. Nadat `closeAsync()` de service is voltooid op elke listener `runAsync()` , wordt deze ook `StatefulServiceBase.onChangeRoleAsync()` wel voltooid. Deze aanroep wordt niet vaak overschreven in de service.

### <a name="for-the-promoted-secondary"></a>Voor de bevorderde secundaire
Op dezelfde manier moet Service Fabric de secundaire replica die wordt bevorderd om te beginnen met Luis teren naar berichten op de kabel en om eventuele achtergrond taken te starten die moeten worden voltooid. Dit proces is vergelijkbaar met wanneer de service wordt gemaakt. Het verschil is dat de replica zelf al bestaat. De volgende gebeurtenissen vinden plaats:

1. Deze gebeurtenissen treden parallel op:
    - `StatefulServiceBase.createServiceReplicaListeners()` wordt aangeroepen en alle geretourneerde listeners worden geopend. `CommunicationListener.openAsync()` wordt aangeroepen voor elke listener.
    - De methode van de service `StatefulServiceBase.runAsync()` wordt aangeroepen.
2. Nadat alle aanroepen van de replica-listener zijn `openAsync()` voltooid en `runAsync()` wordt aangeroepen, `StatefulServiceBase.onChangeRoleAsync()` wordt aangeroepen. Deze aanroep wordt niet vaak overschreven in de service.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Veelvoorkomende problemen tijdens het afsluiten van stateful service en primaire degradatie
Service Fabric de primaire van een stateful service om meerdere redenen wijzigt. De meest voorkomende oorzaken zijn [cluster herverdeling](service-fabric-cluster-resource-manager-balancing.md) en [toepassings upgrade](service-fabric-application-upgrade.md). Tijdens deze bewerkingen is het belang rijk dat de service de `cancellationToken` . Dit geldt ook bij normaal afsluiten van de service, bijvoorbeeld als de service is verwijderd.

Services die de annulering niet op de juiste manier afhandelen, kunnen diverse problemen ondervinden. Deze bewerkingen zijn traag omdat Service Fabric wacht totdat de services op de juiste wijze worden gestopt. Dit kan uiteindelijk leiden tot mislukte upgrades die een time-out en terugdraai actie hebben. Het niet voldoen aan het annulerings token kan ook een niet-sluitende cluster veroorzaken. Clusters worden niet in rekening gebalanceerd omdat knoop punten warm zijn. De services kunnen echter niet worden gebalanceerd omdat het te lang duurt om ze elders te verplaatsen. 

Omdat de services stateful zijn, is het ook waarschijnlijk dat de services gebruikmaken van [betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md). In Service Fabric, wanneer een primaire laag wordt gedegradeerd, is een van de eerste dingen die schrijf toegang tot de onderliggende status is ingetrokken. Dit leidt tot een tweede set problemen die van invloed kunnen zijn op de levens cyclus van de service. De verzamelingen retour uitzonde ringen op basis van de timing en of de replica wordt verplaatst of afgesloten. Het is belang rijk om deze uitzonde ringen correct af te handelen. 

Uitzonde ringen die worden veroorzaakt door service Fabric zijn permanent [( `FabricException` )](/java/api/system.fabric.exception) of tijdelijk [( `FabricTransientException` )](/java/api/system.fabric.exception.fabrictransientexception). Permanente uitzonde ringen moeten worden vastgelegd en gegenereerd. Tijdelijke uitzonde ringen kunnen opnieuw worden uitgevoerd op basis van de logica voor opnieuw proberen.

Een belang rijk onderdeel van het testen en valideren van Reliable Services is het afhandelen van de uitzonde ringen die afkomstig zijn van het gebruik `ReliableCollections` in combi natie met Service Lifecycle-gebeurtenissen. U wordt aangeraden uw service altijd onder belasting te voeren. U moet ook upgrades uitvoeren en [chaos testen](service-fabric-controlled-chaos.md) voordat u de productie implementeert. Met deze eenvoudige stappen kunt u ervoor zorgen dat uw service correct wordt geïmplementeerd en dat er levenscyclus gebeurtenissen correct worden verwerkt.

## <a name="notes-on-service-lifecycle"></a>Opmerkingen over service levenscyclus
* Zowel de `runAsync()` methode als de `createServiceInstanceListeners/createServiceReplicaListeners` aanroepen zijn optioneel. Een service kan een van beide of geen van beide hebben. Als de service bijvoorbeeld alle werk in reactie op gebruikers aanroepen doet, hoeft u deze niet te implementeren `runAsync()` . Alleen de communicatie-listeners en de bijbehorende code zijn nood zakelijk.  Daarnaast is het maken en retour neren van communicatie-listeners optioneel. De service heeft mogelijk alleen achtergrond taken, dus hoeft alleen maar te worden geïmplementeerd `runAsync()` .
* Het is geldig om een service te volt ooien `runAsync()` en terug te keren. Dit wordt niet beschouwd als een fout voorwaarde. Het vertegenwoordigt het achtergrond werk van de service wordt voltooid. Voor stateful Reliable Services `runAsync()` zou opnieuw worden aangeroepen als de service wordt gedegradeerd van primair en vervolgens weer wordt teruggedraaid naar primair.
* Als een service wordt afgesloten `runAsync()` door een onverwachte uitzonde ring te genereren, is dit een fout. Het Service object wordt afgesloten en er wordt een status fout gerapporteerd.
* Hoewel er geen tijds limiet is voor het retour neren van deze methoden, verliest u onmiddellijk de mogelijkheid om te schrijven. Daarom kunt u geen echt werk volt ooien. U wordt aangeraden zo snel mogelijk terug te keren wanneer de annulerings aanvraag wordt ontvangen. Als uw service niet binnen een redelijke hoeveelheid tijd reageert op deze API-aanroepen, kan het zijn dat Service Fabric uw service geforceerd beëindigt. Dit gebeurt gewoonlijk alleen tijdens het bijwerken van de toepassing of bij het verwijderen van een service. Deze time-out is standaard 15 minuten.
* Er zijn fouten opgetreden in het `onCloseAsync()` pad naar de `onAbort()` aanroep. Deze aanroep is een kans op een beste kans voor de service om alle resources die ze hebben geclaimd, op te schonen en vrij te geven. Dit wordt meestal opgeroepen wanneer een permanente fout op het knoop punt wordt gedetecteerd of wanneer Service Fabric de levens cyclus van het service-exemplaar niet betrouwbaar kunt beheren als gevolg van interne storingen.
* `OnChangeRoleAsync()` wordt aangeroepen wanneer de stateful service replica een andere rol heeft, bijvoorbeeld primair of secundair. Primaire replica's krijgen een schrijf status (mogen geen betrouw bare verzamelingen maken en ernaar schrijven). Secundaire replica's krijgen de status lezen (kan alleen lezen van bestaande betrouw bare verzamelingen). De meeste werk in een stateful service wordt uitgevoerd op de primaire replica. Secundaire replica's kunnen alleen-lezen validatie, rapporten genereren, gegevens analyse of andere alleen-lezen taken uitvoeren.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Reliable Services](service-fabric-reliable-services-introduction.md)
* [Snelstartgids Reliable Services](service-fabric-reliable-services-quick-start-java.md)
