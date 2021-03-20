---
title: Bericht overdrachten, vergren delingen en afwikkeling van Azure Service Bus
description: Dit artikel bevat een overzicht van Azure Service Bus bericht overdrachten, vergrendelingen en vereffenings bewerkingen.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: fd71edd12e478bcd5f14815c105c14482cf7e2bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89020028"
---
# <a name="message-transfers-locks-and-settlement"></a>Berichten overdragen, vergrendelen en verwerken

De centrale mogelijkheid van een Message Broker, zoals Service Bus, is het accepteren van berichten in een wachtrij of onderwerp en om ze beschikbaar te houden voor later ophalen. *Send* is de term die meestal wordt gebruikt voor de overdracht van een bericht naar de Message Broker. *Ontvangen* is de term die wordt gebruikt voor de overdracht van een bericht naar een client die wordt opgehaald.

Wanneer een client een bericht verzendt, wil het meestal weten of het bericht juist is overgedragen aan en wordt geaccepteerd door de Broker, of dat er een fout is opgetreden bij het sorteren van het probleem. Deze positieve of negatieve bevestiging bezinkt de client en de broker die de overdrachts status van het bericht kennen en wordt daarom *vereffend* genoemd.

Evenzo, wanneer de broker een bericht naar een client overdraagt, willen de Broker en client weten of het bericht is verwerkt en daarom kan worden verwijderd, of dat het bericht niet kan worden bezorgd of verwerkt, en dat het bericht mogelijk opnieuw moet worden bezorgd.

## <a name="settling-send-operations"></a>Verzend bewerkingen afwikkelen

Bij het gebruik van een van de ondersteunde Service Bus API-clients worden verzend bewerkingen in Service Bus altijd expliciet worden vereffend, wat inhoudt dat de API-bewerking wacht op een acceptatie resultaat van Service Bus om te arriveren, waarna de verzend bewerking wordt voltooid.

Als het bericht is geweigerd door Service Bus, bevat de weigering een fout indicator en tekst met een ' tracking-ID ' erin. De weigering bevat ook informatie over de vraag of de bewerking opnieuw kan worden uitgevoerd met een verwachte uitkomst. In de client wordt deze informatie omgezet in een uitzonde ring en verhoogd naar de aanroeper van de verzend bewerking. Als het bericht is geaccepteerd, wordt de bewerking op de achtergrond uitgevoerd.

Bij gebruik van het AMQP-protocol, dat het exclusieve protocol voor de .NET Standard-client en de Java-client is en [die een optie is voor de .NET Framework-client](service-bus-amqp-dotnet.md), worden bericht overdrachten en-afwikkelingen in de pipeline en volledig asynchroon uitgevoerd en wordt aanbevolen dat u de asynchrone programmeer model-API varianten gebruikt.

Een afzender kan meerdere berichten op de kabel snel achter elkaar plaatsen zonder te hoeven wachten op elk bericht dat moet worden bevestigd, zoals in andere gevallen het SBMP-protocol of met HTTP 1,1. Deze asynchrone verzend bewerkingen zijn voltooid als de respectieve berichten worden geaccepteerd en opgeslagen in gepartitioneerde entiteiten of wanneer de verzend bewerking naar verschillende entiteiten overlapt. De voltooiingen kunnen ook uit de oorspronkelijke verzend volgorde optreden.

De strategie voor het afhandelen van het resultaat van verzend bewerkingen kan onmiddellijke en aanzienlijke invloed hebben op de prestaties van uw toepassing. De voor beelden in deze sectie zijn geschreven in C# en zijn gelijk voor Java-futures.

Als de toepassing bursts van berichten produceert, die hier worden geïllustreerd met een gewone lus en moesten wachten op het volt ooien van elke verzend bewerking voordat het volgende bericht, synchrone of asynchrone API-shapes op dezelfde wijze worden verzonden, worden 10 berichten verzonden na tien opeenvolgende volledige ronde trips voor de vereffening.

De volgende lus neemt ten minste 8 seconden in beslag, waarbij geen Payload-overdrachts tijd of mogelijke problemen met route overbelasting worden geteld, waarbij wordt aangenomen dat de TCP-retour latentie van 70 milliseconden van een on-premises site Service Bus is en alleen 10 MS heeft voor Service Bus om elk bericht te accepteren en op te slaan.

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

Het is belang rijk te weten dat alle asynchrone programmeer modellen een vorm van op geheugen gebaseerde, verborgen werk wachtrij gebruiken die in behandeling zijnde bewerkingen bevat. Wanneer [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) of **Send** (Java) retourneert, wordt de verzend taak in de wachtrij geplaatst in die werk wachtrij, maar wordt de protocol-penbeweging pas gestart zodra de taak is uitgeschakeld. Voor code die doorgaans bursts van berichten pusht en wanneer de betrouw baarheid een probleem is, moet ervoor worden gezorgd dat niet te veel berichten in één keer worden geplaatst, omdat alle verzonden berichten geheugen in beslag nemen totdat ze feitelijk op de kabel zijn geplaatst.

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

De modus [ontvangen en verwijderen](/dotnet/api/microsoft.servicebus.messaging.receivemode) laat de Broker weten dat alle berichten die worden verzonden naar de ontvangende client, moeten worden beschouwd als ze worden verzonden. Dit betekent dat het bericht wordt beschouwd als verbruikt zodra de Broker het op de kabel heeft gezet. Als de bericht overdracht mislukt, gaat het bericht verloren.

Aan de zijkant van deze modus is dat de ontvanger geen verdere actie hoeft uit te voeren op het bericht en dat deze ook niet wordt vertraagd door te wachten op het resultaat van de vereffening. Als de gegevens in de afzonderlijke berichten een lage waarde hebben en/of alleen zinvol zijn voor een zeer korte periode, is deze modus een redelijk keuze.

### <a name="peeklock"></a>PeekLock

In de modus [Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) wordt de Broker aangegeven dat de ontvangende client expliciet ontvangen berichten wil vereffenen. Het bericht wordt beschikbaar gesteld voor verwerking van de ontvanger, terwijl deze wordt gehouden onder een exclusieve vergren deling in de service, zodat andere, concurrerende ontvangers deze niet kunnen zien. De duur van de vergren deling wordt in eerste instantie gedefinieerd op het niveau van de wachtrij of het abonnement en kan worden uitgebreid door de client die eigenaar is van de vergren deling, via de [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) -bewerking.

Wanneer een bericht is vergrendeld, kunnen andere clients die ontvangen van dezelfde wachtrij of hetzelfde abonnement, de volgende beschik bare berichten ophalen, niet onder actieve vergren deling. Wanneer de vergren deling van een bericht expliciet wordt vrijgegeven of wanneer de vergren deling is verlopen, wordt er een back-up van het bericht weer gegeven aan of aan het begin van de ophaal volgorde voor het opnieuw bezorgen.

Wanneer het bericht herhaaldelijk is vrijgegeven door ontvangers of als ze de vergren deling voor een gedefinieerd aantal keren ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)) laten verlopen, wordt het bericht automatisch uit de wachtrij of het abonnement verwijderd en in de bijbehorende wachtrij met onbestelbare berichten geplaatst.

De ontvangende client initieert de afwikkeling van een ontvangen bericht met een positieve bevestiging wanneer het [is voltooid](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) op API-niveau. Dit geeft aan de Broker aan dat het bericht is verwerkt en het bericht wordt verwijderd uit de wachtrij of het abonnement. De Broker reageert op de afwikkeling van de ontvanger met een antwoord dat aangeeft of de vereffening kan worden uitgevoerd.

Wanneer de ontvangende client een bericht niet kan verwerken, maar wil dat het bericht opnieuw wordt bezorgd, kan het expliciet worden gevraagd of het bericht onmiddellijk moet worden vrijgegeven en ontgrendeld door het aanroepen van [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) of kan niets doen en de vergren deling laten verlopen.

Als een ontvangende client een bericht niet kan verwerken en weet dat het bericht opnieuw moet worden bezorgd en de bewerking opnieuw kan worden uitgevoerd, kan het bericht worden geweigerd, waardoor het wordt verplaatst naar de wachtrij voor onbestelbare berichten door [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter)aan te roepen, waardoor ook een aangepaste eigenschap kan worden ingesteld, inclusief een reden code die kan worden opgehaald met het bericht uit de wachtrij voor onbestelbare berichten.

Een speciaal geval van afwikkeling is uitstel, dat in een afzonderlijk artikel wordt besproken.

De **volledige** -of **Deadletter** -bewerkingen en de **RenewLock** -bewerkingen kunnen mislukken als gevolg van netwerk problemen, als de vergren deling is verlopen of er andere service voorwaarden zijn die de trans actie verhinderen. In een van de laatste gevallen verzendt de service een negatieve bevestiging als een uitzonde ring op de API-clients. Als de reden een verbroken netwerk verbinding is, wordt de vergren deling verwijderd omdat Service Bus geen ondersteuning biedt voor het herstellen van bestaande AMQP-koppelingen op een andere verbinding.

Als **volt ooien** mislukt. dit gebeurt doorgaans aan het eind van de verwerking van berichten en in sommige gevallen na de verwerking van het werk, kan de ontvangende toepassing beslissen of de status van het werk wordt behouden en dat hetzelfde bericht wordt genegeerd wanneer het een tweede keer wordt geleverd, of dat er een nieuwe poging wordt gedaan om het werk resultaat te tosses en opnieuw te proberen wanneer het bericht opnieuw wordt bezorgd.

Het gebruikelijke mechanisme voor het identificeren van dubbele bericht leveringen is door het controleren van de bericht-id, die door de afzender kan worden ingesteld op een unieke waarde, mogelijk afgestemd op een id van het oorspronkelijke proces. Met een job scheduler zou de bericht-id waarschijnlijk worden ingesteld op de id van de taak die wordt toegewezen aan een werk nemer met de opgegeven werk nemer. de werk nemer negeert het tweede exemplaar van de taak toewijzing als deze taak al is voltooid.

> [!IMPORTANT]
> Het is belang rijk te weten dat de vergren deling die PeekLock op het bericht verkrijgt, vluchtig is en mogelijk verloren gaat in de volgende omstandigheden
>   * Service-Update
>   * Update van het besturings systeem
>   * De eigenschappen van de entiteit (wachtrij, onderwerp, abonnement) worden gewijzigd terwijl de vergren deling is ingeschakeld.
>
> Wanneer de vergren deling is verbroken, wordt door Azure Service Bus een LockLostException gegenereerd dat wordt weer gegeven op de client toepassings code. In dit geval moet de standaard logica van de client automatisch worden gestart en de bewerking opnieuw proberen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
