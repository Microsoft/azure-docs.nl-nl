---
title: Bericht overdrachten, vergren delingen en afwikkeling van Azure Service Bus
description: Dit artikel bevat een overzicht van Azure Service Bus bericht overdrachten, vergrendelingen en vereffenings bewerkingen.
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6fbbcbf4a1920ee0e66a956443dcfb8a6a17af43
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306769"
---
# <a name="message-transfers-locks-and-settlement"></a>Berichten overdragen, vergrendelen en verwerken

De centrale mogelijkheid van een Message Broker, zoals Service Bus, is het accepteren van berichten in een wachtrij of onderwerp en om ze beschikbaar te houden voor later ophalen. *Send* is de term die meestal wordt gebruikt voor de overdracht van een bericht naar de Message Broker. *Ontvangen* is de term die wordt gebruikt voor de overdracht van een bericht naar een client die wordt opgehaald.

Wanneer een client een bericht verzendt, wil het meestal weten of het bericht juist is overgedragen aan en wordt geaccepteerd door de Broker, of dat er een fout is opgetreden bij het sorteren van het probleem. Deze positieve of negatieve bevestiging bezinkt de client en de Broker over de overdrachts status van het bericht. Dit wordt dus ' *settlement*' genoemd.

Evenzo, wanneer de broker een bericht naar een client overdraagt, willen de Broker en client weten of het bericht is verwerkt en daarom kan worden verwijderd, of dat het bericht niet kan worden bezorgd of verwerkt, en dat het bericht mogelijk opnieuw moet worden bezorgd.

## <a name="settling-send-operations"></a>Verzend bewerkingen afwikkelen

Bij het gebruik van een van de ondersteunde Service Bus API-clients worden verzend bewerkingen in Service Bus altijd expliciet worden vereffend, wat inhoudt dat de API-bewerking wacht op een acceptatie resultaat van Service Bus om te arriveren, waarna de verzend bewerking wordt voltooid.

Als het bericht is geweigerd door Service Bus, bevat de weigering een fout indicator en tekst met een **tracerings-ID** . De weigering bevat ook informatie over de vraag of de bewerking opnieuw kan worden uitgevoerd met een verwachte uitkomst. In de client wordt deze informatie omgezet in een uitzonde ring en verhoogd naar de aanroeper van de verzend bewerking. Als het bericht is geaccepteerd, wordt de bewerking op de achtergrond uitgevoerd.

Bij gebruik van het AMQP-protocol, dat het exclusieve protocol voor de .NET Standard-, Java-, java script-, python-en go-clients is, en [een optie voor de .NET Framework-client](service-bus-amqp-dotnet.md), worden bericht overdrachten en-afwikkelingen in de pipeline en asynchroon uitgevoerd. U wordt aangeraden de asynchrone programmeer model-API-varianten te gebruiken.

Een afzender kan meerdere berichten op de kabel snel achter elkaar plaatsen zonder te hoeven wachten op elk bericht dat moet worden bevestigd, zoals in andere gevallen het SBMP-protocol of met HTTP 1,1. Deze asynchrone verzend bewerkingen zijn voltooid als de respectieve berichten worden geaccepteerd en opgeslagen in gepartitioneerde entiteiten of wanneer de verzend bewerking naar verschillende entiteiten overlapt. De voltooiingen kunnen ook uit de oorspronkelijke verzend volgorde optreden.

De strategie voor het afhandelen van het resultaat van verzend bewerkingen kan onmiddellijke en aanzienlijke invloed hebben op de prestaties van uw toepassing. De voor beelden in deze sectie zijn geschreven in C# en zijn van toepassing op Java futures, Java mono, java script-belofte en vergelijk bare concepten in andere talen.

Als de toepassing bursts van berichten produceert, die hier worden geïllustreerd met een gewone lus en moesten wachten op het volt ooien van elke verzend bewerking voordat het volgende bericht, synchrone of asynchrone API-shapes op dezelfde wijze worden verzonden, worden 10 berichten verzonden na tien opeenvolgende volledige ronde trips voor de vereffening.

Met een veronderstelde wacht tijd van 70-milliseconden voor TCP retour latentie van een on-premises site naar Service Bus en alleen 10 MS voor Service Bus om elk bericht te accepteren en op te slaan, neemt de volgende lus ten minste 8 seconden in beslag, waarbij geen Payload-overdrachts tijd of mogelijke route congestie effecten worden geteld:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Als de toepassing de tien asynchrone verzend bewerkingen onmiddellijk achter elkaar start en de respectieve voltooiing afzonderlijk afwacht, wordt de retour tijd voor deze 10 verzend bewerkingen overlapt. De 10 berichten worden direct achter elkaar overgebracht, mogelijk zelfs door TCP-frames te delen en de totale overdrachts duur is grotendeels afhankelijk van de tijd die nodig is om de berichten op te halen die worden overgedragen naar de Broker.

Als u dezelfde hypo Thesen als voor de voor gaande lus maakt, kan het totale aantal overlappende uitvoerings tijd voor de volgende lus goed onder één seconde blijven:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Het is belang rijk te weten dat alle asynchrone programmeer modellen een vorm van op geheugen gebaseerde, verborgen werk wachtrij gebruiken die in behandeling zijnde bewerkingen bevat. Wanneer de Send API retourneert, wordt de verzend taak in de wachtrij geplaatst in die werk wachtrij, maar wordt de protocol-penbeweging pas gestart zodra de taak is uitgeschakeld. Voor code die doorgaans bursts van berichten pusht en wanneer de betrouw baarheid een probleem is, moet ervoor worden gezorgd dat niet te veel berichten in één keer worden geplaatst, omdat alle verzonden berichten geheugen in beslag nemen totdat ze feitelijk op de kabel zijn geplaatst.

Semaforen, zoals wordt weer gegeven in het volgende code fragment in C#, zijn synchronisatie objecten die op toepassings niveau beperking toestaan wanneer dat nodig is. Door dit gebruik van een semafoor kunnen Maxi maal 10 berichten tegelijk in de vlucht worden uitgevoerd. Een van de 10 beschik bare semafoor vergren delingen wordt gemaakt voordat de verzen ding wordt vrijgegeven, omdat de verzen ding is voltooid. De elfde Pass-Through-lus wacht totdat ten minste één van de voor gaande verzen dingen is voltooid en de vergren deling vervolgens beschikbaar maakt:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Toepassingen mogen **nooit** een asynchrone verzend bewerking in een ' brand-en '-' manier initiëren zonder het resultaat van de bewerking op te halen. Als u dit doet, kan de interne en onregelmatige taak wachtrij worden geladen, waardoor de toepassing geen verzend fouten kan detecteren:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Met een AMQP-client op laag niveau Service Bus accepteert ook "vooraf afgerekende" overdrachten. Een vooraf gestarte overdracht is een brand-en-vergeet-bewerking waarvoor het resultaat, hetzij in de richting, niet wordt gemeld aan de client en het bericht wordt beschouwd als verrekend wanneer het wordt verzonden. Het ontbreken van feedback op de client betekent ook dat er geen bruikbare gegevens beschikbaar zijn voor diagnoses, wat betekent dat deze modus niet in aanmerking komt voor Help via ondersteuning voor Azure.

## <a name="settling-receive-operations"></a>Ontvangst bewerkingen afwikkelen

Voor ontvangst bewerkingen maken de Service Bus-API-clients twee verschillende expliciete modi: *Receive-and-Delete* en *Peek-Lock*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

De modus **ontvangen en verwijderen** laat de Broker weten dat alle berichten die worden verzonden naar de ontvangende client, moeten worden beschouwd als ze worden verzonden. Dit betekent dat het bericht wordt beschouwd als verbruikt zodra de Broker het op de kabel heeft gezet. Als de bericht overdracht mislukt, gaat het bericht verloren.

Aan de zijkant van deze modus is dat de ontvanger geen verdere actie hoeft uit te voeren op het bericht en dat deze ook niet wordt vertraagd door te wachten op het resultaat van de vereffening. Als de gegevens in de afzonderlijke berichten een lage waarde hebben en/of alleen zinvol zijn voor een zeer korte periode, is deze modus een redelijk keuze.

### <a name="peeklock"></a>PeekLock

In de modus **Peek-Lock** wordt de Broker aangegeven dat de ontvangende client expliciet ontvangen berichten wil vereffenen. Het bericht wordt beschikbaar gesteld voor verwerking van de ontvanger, terwijl deze wordt gehouden onder een exclusieve vergren deling in de service, zodat andere, concurrerende ontvangers deze niet kunnen zien. De duur van de vergren deling wordt in eerste instantie gedefinieerd op het niveau van de wachtrij of het abonnement en kan worden uitgebreid door de client die eigenaar is van de vergren deling. Zie de sectie [vergren delingen vernieuwen](#renew-locks) in dit artikel voor meer informatie over het vernieuwen van vergren delingen. 

Wanneer een bericht is vergrendeld, kunnen andere clients die ontvangen van dezelfde wachtrij of hetzelfde abonnement, de volgende beschik bare berichten ophalen, niet onder actieve vergren deling. Wanneer de vergren deling van een bericht expliciet wordt vrijgegeven of wanneer de vergren deling is verlopen, wordt er een back-up van het bericht weer gegeven aan of aan het begin van de ophaal volgorde voor het opnieuw bezorgen.

Wanneer het bericht herhaaldelijk is vrijgegeven door ontvangers of als de vergren deling voor een gedefinieerd aantal keren is verstreken ([Max. aantal leveringen](service-bus-dead-letter-queues.md#maximum-delivery-count)), wordt het bericht automatisch uit de wachtrij of het abonnement verwijderd en in de bijbehorende wachtrij met onbestelbare berichten geplaatst.

De ontvangende client initieert de beslechting van een ontvangen bericht met een positieve bevestiging wanneer het de `Complete` API voor het bericht aanroept. Dit geeft aan de Broker aan dat het bericht is verwerkt en het bericht wordt verwijderd uit de wachtrij of het abonnement. De Broker reageert op de afwikkeling van de ontvanger met een antwoord dat aangeeft of de vereffening kan worden uitgevoerd.

Wanneer de ontvangende client een bericht niet kan verwerken, maar wil dat het bericht opnieuw wordt bezorgd, kan het expliciet worden gevraagd of het bericht onmiddellijk moet worden vrijgegeven en ontgrendeld door de `Abandon` API voor het bericht aan te roepen of niets te doen en de vergren deling te laten verlopen.

Als een ontvangende client een bericht niet kan verwerken en weet dat het opnieuw verzenden van het bericht en het opnieuw proberen van de bewerking niet helpt, kan het bericht afwijzen, waardoor het wordt verplaatst naar de wachtrij voor onbestelbare berichten door de `DeadLetter` API in het bericht aan te roepen, waardoor ook een aangepaste eigenschap kan worden opgehaald met het bericht uit de wachtrij met onbestelbare berichten.

Een speciaal geval van afwikkeling is uitstel, dat in een [afzonderlijk artikel](message-deferral.md)wordt besproken.

De `Complete` , `Deadletter` , of `RenewLock` bewerkingen kunnen mislukken als gevolg van netwerk problemen, als de vergren deling is verlopen, of er zijn andere service voorwaarden voor voor waarden die de vereffening verhinderen. In een van de laatste gevallen verzendt de service een negatieve bevestiging als een uitzonde ring op de API-clients. Als de reden een verbroken netwerk verbinding is, wordt de vergren deling verwijderd omdat Service Bus geen ondersteuning biedt voor het herstellen van bestaande AMQP-koppelingen op een andere verbinding.

Als er `Complete` een fout optreedt, die meestal aan het eind van de bericht afhandeling plaatsvindt, en in sommige gevallen na de verwerking van het werk, kan de ontvangende toepassing beslissen of de status van het werk wordt behouden en dat hetzelfde bericht wordt genegeerd wanneer het een tweede keer wordt geleverd, of dat er een nieuwe poging wordt gedaan om het werk resultaat te tossesen wanneer het bericht opnieuw wordt bezorgd.

Het gebruikelijke mechanisme voor het identificeren van dubbele bericht leveringen is door het controleren van de bericht-id, die door de afzender kan worden ingesteld op een unieke waarde, mogelijk afgestemd op een id van het oorspronkelijke proces. Met een job scheduler zou de bericht-id waarschijnlijk worden ingesteld op de id van de taak die wordt toegewezen aan een werk nemer met de opgegeven werk nemer. de werk nemer negeert het tweede exemplaar van de taak toewijzing als deze taak al is voltooid.

> [!IMPORTANT]
> Het is belang rijk te weten dat de vergren deling die PeekLock op het bericht verkrijgt, vluchtig is en mogelijk verloren gaat in de volgende omstandigheden
>   * Service-Update
>   * Update van het besturings systeem
>   * De eigenschappen van de entiteit (wachtrij, onderwerp, abonnement) worden gewijzigd terwijl de vergren deling is ingeschakeld.
>
> Wanneer de vergren deling is verbroken, wordt door Azure Service Bus een LockLostException gegenereerd dat wordt weer gegeven op de client toepassings code. In dit geval moet de standaard logica van de client automatisch worden gestart en de bewerking opnieuw proberen.

## <a name="renew-locks"></a>Vergren delingen vernieuwen
De standaard waarde voor de vergrendelings duur is **30 seconden**. U kunt een andere waarde voor de vergrendelings duur opgeven op het niveau van de wachtrij of het abonnement. De client die eigenaar is van de vergren deling kan de bericht vergrendeling vernieuwen met behulp van methoden op het receiver-object. In plaats daarvan kunt u de functie voor automatisch vergren delen gebruiken, waarbij u de tijds duur kunt opgeven waarvoor u de vergren deling wilt blijven verlengen. 

## <a name="next-steps"></a>Volgende stappen
- Een speciaal geval van vereffening wordt uitgesteld. Zie het [bericht](message-deferral.md) uitstellen voor meer informatie. 
- Zie [wacht rijen](service-bus-dead-letter-queues.md)voor onbestelbare berichten voor meer informatie over onbestelbare berichten.
- Zie [service bus wacht rijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md) voor meer informatie over Service Bus berichten in het algemeen.