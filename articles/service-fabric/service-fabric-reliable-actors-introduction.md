---
title: Overzicht van Service Fabric Reliable Actors
description: Inleiding tot het programmeer model Service Fabric Reliable Actors, op basis van het patroon van de virtuele actor.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a8a7003a69deaf6b74d6fbb8a3cf84b0a78eecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576380"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Inleiding tot Service Fabric Reliable Actors
Reliable Actors is een Service Fabric Application Framework op basis van het patroon van de [virtuele actor](https://research.microsoft.com/en-us/projects/orleans/) . De Reliable Actors-API biedt een programmeer model met één thread dat is gebaseerd op de mogelijkheden voor schaal baarheid en betrouw baarheid van Service Fabric.

## <a name="what-are-actors"></a>Wat zijn actors?
Een actor is een geïsoleerde, onafhankelijke eenheid van Compute en State met uitvoering met één thread. Het [actor-patroon](https://en.wikipedia.org/wiki/Actor_model) is een reken kundig model voor gelijktijdige of gedistribueerde systemen waarin een groot aantal van deze actors gelijktijdig en onafhankelijk van elkaar kan worden uitgevoerd. Actors kunnen met elkaar communiceren en er kunnen meer actors worden gemaakt.

### <a name="when-to-use-reliable-actors"></a>Wanneer moet ik Reliable Actors gebruiken?
Service Fabric Reliable Actors is een implementatie van het ontwerp patroon actor. Net als bij elk wille keurig software ontwerp patroon wordt beslist of een specifiek patroon moet worden gebruikt, op basis van het feit of een probleem met het software-ontwerp aan het patroon voldoet.

Hoewel het ontwerp patroon actor kan worden aangepast aan een aantal gedistribueerde systeem problemen en scenario's, moet zorgvuldig rekening worden gehouden met de beperkingen van het patroon en het Framework dat wordt geïmplementeerd. Als algemene richt lijn kunt u het actor-patroon beschouwen om uw probleem of scenario te model leren als:

* Uw probleem ruimte bestaat uit een groot aantal (duizenden of meer) kleine, onafhankelijke en geïsoleerde eenheden van staat en logica.
* U wilt werken met objecten met één thread waarvoor geen significante interactie nodig is van externe onderdelen, waaronder het uitvoeren van een query status in een set actors.
* De actor-exemplaren blok keren bellers met onvoorspelbare vertragingen door I/O-bewerkingen uit te voeren.

## <a name="actors-in-service-fabric"></a>Actors in Service Fabric
In Service Fabric worden actors geïmplementeerd in het Reliable Actors Framework: een toepassings raamwerk op basis van actor dat is gebouwd op [Service Fabric reliable Services](service-fabric-reliable-services-introduction.md). Elke reliable actor service die u schrijft, is in feite een gepartitioneerde, stateful betrouw bare service.

Elke actor wordt gedefinieerd als een exemplaar van een actor-type, identiek aan de manier waarop een .NET-object een exemplaar van een .NET-type is. Het is bijvoorbeeld mogelijk dat er een actor-type is dat de functionaliteit van een reken machine implementeert en dat er veel actors van dat type worden gedistribueerd op verschillende knoop punten in een cluster. Elke actor wordt uniek geïdentificeerd door een actor-ID.

## <a name="actor-lifetime"></a>Actor-levens duur
Service Fabric Actors zijn virtueel, wat inhoudt dat hun levens duur niet is gekoppeld aan de weer gave in het geheugen. Als gevolg hiervan hoeven ze niet expliciet te worden gemaakt of vernietigd. De Reliable Actors-runtime activeert automatisch een actor de eerste keer dat er een aanvraag voor die actor-ID wordt ontvangen. Als een actor niet voor een bepaalde tijd wordt gebruikt, wordt het in-memory-object verzameld door de Reliable Actors runtime-opschoon bewerking. Er wordt ook kennis van het bestaan van actors bijgehouden. dit moet later opnieuw worden geactiveerd. Zie voor meer informatie [actor levenscyclus en garbagecollection](service-fabric-reliable-actors-lifecycle.md).

Deze samen vatting van de virtuele actor-levens duur bevat enkele voor behoud van het resultaat van het virtuele actor model, en in feite wordt de Reliable Actors-implementatie van het ene naar het andere tijdstip afwijkt van dit model.

* Er wordt automatisch een actor geactiveerd (waardoor een actor-object wordt geconstrueerd) wanneer een bericht voor het eerst naar de actor-ID wordt verzonden. Na enige tijd wordt het actor-object opgeruimd. In de toekomst wordt met de actor-ID opnieuw een nieuw actor-object gemaakt. De status van een actor is de levens duur van het object wanneer het wordt opgeslagen in de status Manager.
* Als u een actor-methode aanroept voor een actor-ID, wordt die actor geactiveerd. Daarom hebben actor typen hun constructor impliciet aangeroepen door de runtime. Client code kan daarom geen para meters door geven aan de constructor van het actor-type, hoewel para meters kunnen worden door gegeven aan de constructor van de actor door de service zelf. Het resultaat is dat actors in een gedeeltelijk geïnitialiseerde toestand kunnen worden gebouwd op basis van de tijd dat er andere methoden worden aangeroepen, als de actor initialisatie parameters van de client vereist. Er is geen enkel toegangs punt voor de activering van een actor van de client.
* Hoewel Reliable Actors impliciet actor-objecten maken; u hebt de mogelijkheid om een actor en de status expliciet te verwijderen.

## <a name="distribution-and-failover"></a>Distributie en failover
Service Fabric zorgt voor schaal baarheid en betrouw baarheid, distribueert actors in het hele cluster en migreert deze automatisch van mislukte knoop punten naar een goede status als vereist. Dit is een abstractie van een [gepartitioneerde, stateful betrouw bare service](service-fabric-concepts-partitioning.md). Distributie, schaal baarheid, betrouw baarheid en automatische failover worden allemaal verschaft op grond van het feit dat actors worden uitgevoerd in een stateful betrouw bare service met de naam *actor service*.

Actors worden gedistribueerd over de partities van de actor-service en deze partities worden gedistribueerd over de knoop punten in een Service Fabric cluster. Elke service partitie bevat een set actors. Service Fabric beheert de distributie en failover van de service partities.

Een actor service met negen partities die zijn geïmplementeerd op drie knoop punten met behulp van de standaard plaatsing van de actor-partitie, wordt dus gedistribueerd:

![Reliable Actors distributie][2]

Het actor-Framework beheert de instellingen voor het partitie schema en het sleutel bereik. Dit vereenvoudigt enkele keuzes, maar voert ook een zekere overweging uit:

* Met Reliable Services kunt u een partitie schema, sleutel bereik (bij gebruik van een bereik partitie schema) en het aantal partities kiezen. Reliable Actors is beperkt tot het schema voor het partitioneren van het bereik (het uniforme Int64-schema) en vereist dat u het volledige Int64-sleutel bereik gebruikt.
* Actors worden standaard wille keurig in partities geplaatst, wat resulteert in een uniforme distributie.
* Omdat actors wille keurig worden geplaatst, moet worden verwacht dat actor bewerkingen altijd netwerk communicatie vereisen, inclusief serialisatie en deserialisatie van methode aanroep gegevens, latentie en overhead kosten.
* In geavanceerde scenario's is het mogelijk om de plaatsing van de actor-partitie te beheren met behulp van Int64 actor-Id's die worden toegewezen aan specifieke partities. Dit kan echter leiden tot een niet-sluitende distributie van actors over partities.

Raadpleeg voor meer informatie over hoe actor Services zijn gepartitioneerd de [partitie concepten voor actors](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Actor communicatie
Actor-interacties worden gedefinieerd in een interface die wordt gedeeld door de actor die de interface implementeert, en de client die een proxy naar een actor via dezelfde interface haalt. Omdat deze interface wordt gebruikt om actor-methoden asynchroon aan te roepen, moet elke methode op de interface taak retour neren.

Het aanroepen van de methode en hun antwoorden resulteren uiteindelijk in netwerk aanvragen over het cluster, dus de argumenten en de resultaat typen van de taken die ze retour neren moeten serialiseerbaar zijn door het platform. In het bijzonder moet het [gegevens contract serialiseerbaar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)zijn.

### <a name="the-actor-proxy"></a>De actor-proxy
De Reliable Actors client-API biedt communicatie tussen een actor-exemplaar en een actor-client. Een-client kan alleen communiceren met een actor door een actor-proxy object te maken dat de actor-interface implementeert. De client communiceert met de actor door het aanroepen van methoden voor het proxy-object. De actor-proxy kan worden gebruikt voor communicatie tussen de client en actor en actor-to-actor.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Houd er rekening mee dat de twee stukjes informatie die worden gebruikt voor het maken van het actor-proxy object de actor-ID en de toepassings naam zijn. Met de actor-ID wordt de actor uniek geïdentificeerd, terwijl de naam van de toepassing de [service Fabric toepassing](service-fabric-reliable-actors-platform.md#application-model) identificeert waarin de actor wordt geïmplementeerd.

De `ActorProxy` klasse (C#)/ `ActorProxyBase` (Java) aan de client zijde voert de nood zakelijke oplossing uit om de actor met de id te vinden en een communicatie kanaal te openen. Ook wordt geprobeerd om de actor te vinden in het geval van communicatie fouten en failovers. Als gevolg hiervan heeft de bezorging van berichten de volgende kenmerken:

* Bericht bezorging is het beste.
* Actors kunnen dubbele berichten ontvangen van dezelfde client.

## <a name="concurrency"></a>Gelijktijdigheid
De Reliable Actors runtime biedt een eenvoudig op te zetten toegangs model voor het verkrijgen van toegang tot actor-methoden. Dit betekent dat er op elk gewenst moment niet meer dan één thread actief kan zijn binnen de code van een actor-object. Op basis van toegangs rechten worden gelijktijdige systemen aanzienlijk vereenvoudigd omdat er geen synchronisatie mechanismen voor gegevens toegang nodig zijn. Het betekent ook dat systemen moeten worden ontworpen met speciale overwegingen voor de toegangs aard met één thread van elk actor-exemplaar.

* Eén actor-exemplaar kan niet meer dan één aanvraag tegelijk verwerken. Een actor-exemplaar kan leiden tot een doorvoer knelpunt als wordt verwacht dat er gelijktijdige aanvragen worden afgehandeld.
* Actors kunnen op elkaar deadlock maken als er een kring verwijzing tussen twee actors bestaat, terwijl een externe aanvraag gelijktijdig naar een van de actors wordt verzonden. De actor-runtime neemt automatisch een time-out op bij actor-aanroepen en genereert een uitzonde ring voor de aanroeper om mogelijke deadlock-situaties te onderbreken.

![Communicatie Reliable Actors][3]

### <a name="turn-based-access"></a>Toegang op basis van een toegangs
Een turn bestaat uit de volledige uitvoering van een actor-methode als reactie op een aanvraag van andere actors of clients of de volledige uitvoering van een [Timer/herinnerings](service-fabric-reliable-actors-timers-reminders.md) retour aanroep. Hoewel deze methoden en retour aanroepen asynchroon zijn, worden ze niet door de actors-runtime ondersteund. Een turn moet volledig worden voltooid voordat een nieuwe bocht wordt toegestaan. Met andere woorden, een actor-methode of timer/herinnering-call back die momenteel wordt uitgevoerd, moet volledig worden voltooid voordat een nieuwe aanroep naar een methode of call back is toegestaan. Een methode of retour aanroep wordt geacht te zijn voltooid als de uitvoering is geretourneerd door de methode of call back en de taak die door de methode is geretourneerd of de aanroep is voltooid. Het is belang rijk om te benadrukken dat op elkaar gebaseerde gelijktijdigheid wordt geëerbiedigd, zelfs over verschillende methoden, timers en retour aanroepen.

De actors-runtime dwingt gelijktijdige gelijktijdigheid af door een vergren deling per actor aan het begin van een bocht te verkrijgen en de vergren deling aan het einde van de beurt op te heffen. Op deze manier wordt gelijktijdigheid op basis van een op-actor gebaseerd en niet tussen actors. Actor-methoden en timer-en herinnering-retour aanroepen kunnen gelijktijdig worden uitgevoerd namens verschillende actors.

In het volgende voor beeld worden de bovenstaande concepten geïllustreerd. Overweeg een actor-type dat twee asynchrone methoden (zeg, *Method1* en *Method2*), een timer en een herinnering implementeert. In het onderstaande diagram ziet u een voor beeld van een tijd lijn voor het uitvoeren van deze methoden en retour aanroepen namens twee actors (*ActorId1* en *ActorId2*) die horen bij dit type actor.

![Gelijktijdigheid en toegang op basis van Reliable Actors runtime][1]

Dit diagram volgt deze conventies:

* Elke verticale lijn toont de logische stroom van het uitvoeren van een methode of een call back namens een bepaalde actor.
* De gebeurtenissen die op elke verticale lijn zijn gemarkeerd, komen in chronologische volg orde voor, met nieuwere gebeurtenissen die onder oudere versies vallen.
* Verschillende kleuren worden gebruikt voor tijd lijnen die overeenkomen met verschillende actors.
* Markeren wordt gebruikt om de duur aan te geven waarvoor de vergren deling per actor namens een methode of retour aanroep wordt gehouden.

Enkele belang rijke punten om rekening mee te houden:

* Hoewel *Method1* wordt uitgevoerd namens *ActorId2* als reactie op client aanvragen *xyz789*, arriveert een andere client aanvraag (*abc123*) die ook *Method1* moet uitvoeren door *ActorId2*. De tweede uitvoering van *Method1* begint echter pas als de vorige uitvoering is voltooid. Op dezelfde manier wordt een herinnering geregistreerd door *ActorId2* geactiveerd terwijl *Method1* wordt uitgevoerd in reactie op de *xyz789* van de client aanvraag. De retour aanroep van de herinnering wordt pas uitgevoerd nadat de uitvoering van *Method1* is voltooid. Dit komt door dat op basis van een op zichzelf gebaseerde gelijktijdigheid wordt afgedwongen voor *ActorId2*.
* Op deze manier wordt ook gelijktijdigheid op basis van een op- *ActorId1* afgedwongen, zoals gedemonstreerd door de uitvoering van *Method1*, *Method2* en de timer-Call back uit naam van *ActorId1* . dit gebeurt op een seriële manier.
* Het uitvoeren van *Method1* namens *ActorId1* overlapt met uitvoering namens *ActorId2*. Dit komt doordat gelijktijdigheid op basis van lagen alleen binnen een actor en niet tussen actors wordt afgedwongen.
* In sommige van de methode/call back-uitvoeringen, de `Task` (C#)/ `CompletableFuture` (Java) geretourneerd door de methode/call back is voltooid nadat de methode is geretourneerd. In sommige andere gevallen is de asynchrone bewerking al voltooid op het moment dat de methode/retour aanroep wordt geretourneerd. In beide gevallen wordt de vergren deling per actor pas vrijgegeven nadat de methode/retour aanroep is geretourneerd en de asynchrone bewerking is voltooid.

### <a name="reentrancy"></a>Herintreding
De actors-runtime maakt standaard herbetreedbaarheid mogelijk. Dit betekent dat als een actor-methode van *actor a* een methode aanroept op *actor B*, die op zijn beurt een andere methode op *actor A* aanroept. deze methode mag worden uitgevoerd. Dit is omdat deze deel uitmaakt van dezelfde logische aanroep keten context. Alle timer-en herinnerings aanroepen beginnen met de nieuwe logische aanroep context. Zie de [reliable actors herbetreedbaarheid](service-fabric-reliable-actors-reentrancy.md) voor meer informatie.

### <a name="scope-of-concurrency-guarantees"></a>Bereik van gelijktijdigheids garanties
De actors-runtime biedt deze gelijktijdigheids garanties in situaties waarin deze de aanroep van deze methoden regelt. Het biedt bijvoorbeeld deze garanties voor de aanroepen van de methode die worden uitgevoerd als reactie op een client aanvraag, en voor timer-en herinnering-retour aanroepen. Als de actor code deze methoden echter rechtstreeks aanroept buiten de mechanismen die worden geleverd door de actors-runtime, kan de runtime geen garanties bieden voor gelijktijdigheid. Als de methode bijvoorbeeld wordt aangeroepen in de context van een taak die niet is gekoppeld aan de taak die wordt geretourneerd door de actor-methoden, kan de runtime geen gelijktijdigheids garanties bieden. Als de methode wordt aangeroepen vanuit een thread die de actor zelf maakt, kan de runtime ook geen gelijktijdigheids garanties bieden. Om achtergrond bewerkingen uit te voeren, moeten actors voor [actor-timers en actor-herinneringen](service-fabric-reliable-actors-timers-reminders.md) die gebruikmaken van op beurt gebaseerde gelijktijdigheid, worden gebruikt.

## <a name="next-steps"></a>Volgende stappen
Ga aan de slag door uw eerste Reliable Actors-service te bouwen:
   * [Aan de slag met Reliable Actors op .NET](service-fabric-reliable-actors-get-started.md)
   * [Aan de slag met Reliable Actors in Java](./service-fabric-create-your-first-linux-application-with-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
