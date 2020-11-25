---
title: ReliableConcurrentQueue in azure Service Fabric
description: ReliableConcurrentQueue is een wachtrij met hoge door Voer waarmee parallelle in en dequeues zijn toegestaan.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 423ef3d1898176d7c25c596ad186a9c000108aa4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997117"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Inleiding tot ReliableConcurrentQueue in azure Service Fabric
Betrouw bare, gelijktijdige wachtrij is een asynchrone, transactionele en gerepliceerde wachtrij, die een hoge gelijktijdigheid voor bewerkingen in de wachtrij plaatsen en verwijderen. Het is ontworpen om hoge door Voer en lage latentie te bieden door de strikte FIFO-volg orde die wordt geleverd door een [betrouw bare wachtrij](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) te verminderen en in plaats daarvan een best mogelijke volg orde te bieden.

## <a name="apis"></a>API's

|Gelijktijdige wachtrij                |Betrouwbare gelijktijdige wachtrij                                         |
|--------------------------------|------------------------------------------------------------------|
| in wachtrij plaatsen (T item)           | Taak EnqueueAsync (ITransaction TX, T item)                       |
| BOOL TryDequeue (uitgaand resultaat)  | Taak< ConditionalValue < T > > TryDequeueAsync (ITransaction TX)  |
| Aantal int ()                    | Lange telling ()                                                     |

## <a name="comparison-with-reliable-queue"></a>Vergelijking met [betrouw bare wachtrij](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1)

Betrouw bare gelijktijdige wachtrij wordt aangeboden als alternatief voor een [betrouw bare wachtrij](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1). Het moet worden gebruikt in gevallen waarin strikte FIFO-ordening niet is vereist, omdat voor het garanderen van FIFO een afweging met gelijktijdigheid is vereist.  Een [betrouw bare wachtrij](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) maakt gebruik van vergren delingen voor het afdwingen van FIFO-bestelling, met Maxi maal één trans actie die in de wachtrij mag worden geplaatst en Maxi maal één trans actie tegelijk mag worden verwijderd. In vergelijking zorgt een betrouw bare, gelijktijdige wachtrij voor een versoepeling van de ordenings beperking en kan elk aantal gelijktijdige trans acties interacties uitvoeren om de bewerkingen in de wachtrij te plaatsen en Best mogelijke ordening is beschikbaar, maar de relatieve volg orde van twee waarden in een betrouw bare, gelijktijdige wachtrij kan nooit worden gegarandeerd.

Betrouw bare gelijktijdige wachtrij biedt hogere door Voer en een lagere latentie dan [betrouw bare wachtrij](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1) wanneer er meerdere gelijktijdige trans acties zijn die in en/of dewachtrijen uitvoeren.

Een voor beeld van een use-case voor ReliableConcurrentQueue is het scenario voor de [berichten wachtrij](https://en.wikipedia.org/wiki/Message_queue) . In dit scenario maken en toevoegen een of meer bericht producenten items aan de wachtrij en kunnen een of meer gebruikers berichten uit de wachtrij halen en verwerken. Meerdere producenten en consumenten kunnen onafhankelijk werken, met behulp van gelijktijdige trans acties om de wachtrij te verwerken.

## <a name="usage-guidelines"></a>Gebruiks richtlijnen
* De wachtrij verwacht dat de items in de wachtrij een lage Bewaar periode hebben. Dat wil zeggen dat de items gedurende een lange periode niet in de wachtrij blijven.
* De wachtrij garandeert geen strikte FIFO-bestelling.
* De wachtrij leest geen eigen schrijf bewerkingen. Als een item binnen een trans actie in de wachtrij wordt geplaatst, wordt het niet weer gegeven in een dewachtrij binnen dezelfde trans actie.
* Dewachtrijen zijn niet van elkaar geïsoleerd. Als item *a* wordt verwijderd uit de *txnA* van de trans actie, zelfs als *txnA* niet is doorgevoerd, zou item *a* niet zichtbaar zijn voor een gelijktijdige trans actie *txnB*.  Als *txnA* afbreekt, wordt *A* onmiddellijk zichtbaar voor *txnB* .
* *TryPeekAsync* -gedrag kan worden geïmplementeerd met behulp van een *TryDequeueAsync* en de trans actie af te breken. Een voor beeld van dit gedrag vindt u in de sectie programmeer patronen.
* Aantal is niet-transactioneel. Het kan worden gebruikt om een idee te krijgen van het aantal elementen in de wachtrij, maar dit is een tijdgebonden punt en kan niet worden vertrouwd op.
* De dure verwerking van items in de wachtrij mag niet worden uitgevoerd terwijl de trans actie actief is, om langlopende trans acties te voor komen die invloed kunnen hebben op de prestaties van het systeem.

## <a name="code-snippets"></a>Codefragmenten
Laat ons enkele code fragmenten zien en de verwachte uitvoer. Afhandeling van uitzonde ringen wordt in deze sectie genegeerd.

### <a name="instantiation"></a>Instantiëring
Het maken van een exemplaar van een betrouw bare, gelijktijdige wachtrij is vergelijkbaar met een andere betrouw bare verzameling.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Hier volgen enkele code fragmenten voor het gebruik van EnqueueAsync, gevolgd door de verwachte uitvoer.

- *Voor beeld 1: één taak in wachtrij plaatsen*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Stel dat de taak is voltooid en dat er geen gelijktijdige trans acties zijn die de wachtrij wijzigen. De gebruiker kan de wachtrij de items in een van de volgende orders laten bevatten:

> 10, 20
> 
> 20, 10


- *Voor beeld 2: taak parallel in wachtrij plaatsen*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Stel dat de taken zijn voltooid, dat de taken parallel werden uitgevoerd en dat er geen andere gelijktijdige trans acties zijn die de wachtrij wijzigen. Er kan geen interferentie worden gemaakt over de volg orde van items in de wachtrij. Voor dit code fragment kunnen de items worden weer gegeven in een van de vier! mogelijke ordeningen.  Er wordt geprobeerd de items in de oorspronkelijke volg orde (in de wachtrij) te houden, maar dit kan worden afgesteld als gevolg van gelijktijdige bewerkingen of fouten.


### <a name="dequeueasync"></a>DequeueAsync
Hier volgen enkele code fragmenten voor het gebruik van TryDequeueAsync, gevolgd door de verwachte uitvoer. Stel dat de wachtrij al is gevuld met de volgende items in de wachtrij:
> 10, 20, 30, 40, 50, 60

- *Voor beeld 1: enkele taak uit wachtrij*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Stel dat de taak is voltooid en dat er geen gelijktijdige trans acties zijn die de wachtrij wijzigen. Omdat er geen interferentie kan worden gemaakt over de volg orde van de items in de wachtrij, kunnen drie van de items in een wille keurige volg orde worden verwijderd. Er wordt geprobeerd de items in de oorspronkelijke volg orde (in de wachtrij) te houden, maar dit kan worden afgesteld als gevolg van gelijktijdige bewerkingen of fouten.  

- *Voor beeld 2: taak parallel dewachtrij*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Stel dat de taken zijn voltooid, dat de taken parallel werden uitgevoerd en dat er geen andere gelijktijdige trans acties zijn die de wachtrij wijzigen. Omdat er geen interferentie kan worden gemaakt over de volg orde van de items in de wachtrij, bevatten de lijsten *dequeue1* en *dequeue2* elk twee items, in een wille keurige volg orde.

Hetzelfde item wordt *niet* weer gegeven in beide lijsten. Als dequeue1 *10*, *30* heeft, is dequeue2 dus *20*, *40*.

- *Case 3: het ordenen van een wachtrij met een trans actie afbreken*

Als een trans actie wordt afgebroken met een in-Flight wachtrij, worden de items weer gegeven in de kop van de wachtrij. De volg orde waarin de items worden geplaatst op het hoofd van de wachtrij is niet gegarandeerd. We kijken naar de volgende code:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Stel dat de items in de wachtrij zijn geplaatst in de volgende volg orde:
> 10, 20

Wanneer de trans actie wordt afgebroken, worden de items in een van de volgende orders weer toegevoegd aan de kop van de wachtrij:
> 10, 20
> 
> 20, 10

Dit geldt ook voor alle gevallen waarin de trans actie niet is *doorgevoerd*.

## <a name="programming-patterns"></a>Programmeer patronen
In deze sectie kijken we naar een paar programmeer patronen die handig kunnen zijn bij het gebruik van ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Batch-dewachtrijen
Een aanbevolen programmerings patroon is dat de Consumer-taak de dewachtrij van de klant uit een batch heeft verwijderd in plaats van één wachtrij tegelijk uit te voeren. De gebruiker kan ervoor kiezen om vertragingen tussen elke batch of de Batch grootte te beperken. Het volgende code fragment toont dit programmeer model. Houd er rekening mee dat in dit voor beeld de verwerking wordt uitgevoerd nadat de trans actie is doorgevoerd, dus als er een fout optreedt tijdens de verwerking, gaan de niet-verwerkte items verloren zonder dat ze zijn verwerkt.  De verwerking kan ook worden uitgevoerd binnen het bereik van de trans actie, maar dit kan een negatieve invloed hebben op de prestaties en vereist het verwerken van de items die al zijn verwerkt.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Best-Effort Notification-Based verwerking
Een ander interessant programmeer patroon maakt gebruik van de API Count. Hier kunnen we de op berichten gebaseerde verwerking op basis van meldingen voor de wachtrij implementeren. Het aantal wacht rijen kan worden gebruikt om een taak in de wachtrij te plaatsen of uit te zetten.  Zoals in het vorige voor beeld, omdat de verwerking buiten de trans actie plaatsvindt, kunnen niet-verwerkte items verloren gaan als er een fout optreedt tijdens de verwerking.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Best-Effort afvoer
Een afvoer van de wachtrij kan niet worden gegarandeerd vanwege de gelijktijdige aard van de gegevens structuur.  Het is mogelijk dat, zelfs als er geen gebruikers bewerkingen zijn in de vlucht, een bepaalde aanroep naar TryDequeueAsync mogelijk geen item retourneert dat eerder in de wachtrij is geplaatst en doorgevoerd.  Het in de wachtrij geplaatste item moet *uiteindelijk* zichtbaar worden gemaakt voor het dequeueren, maar zonder een out-of-band-communicatie mechanisme kan een onafhankelijke consument niet weten dat de wachtrij een stationaire status heeft bereikt, zelfs als alle producenten zijn gestopt en er geen nieuwe bewerkingen voor plaatsen zijn toegestaan. De afvoer bewerking is dus de beste werk wijze, zoals hieronder is geïmplementeerd.

De gebruiker moet alle verdere producer-en Consumer taken stoppen en wachten tot trans acties die in de vlucht zijn doorgevoerd of afgebroken, voordat wordt geprobeerd om de wachtrij leeg te laten.  Als de gebruiker het verwachte aantal items in de wachtrij kent, kunnen ze een melding instellen die aangeeft dat alle items zijn verwijderd uit de wachtrij.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Piek
ReliableConcurrentQueue biedt geen *TryPeekAsync* -API. Gebruikers kunnen de semantische semantiek weer geven met behulp van een *TryDequeueAsync* en de trans actie vervolgens afbreken. In dit voor beeld worden de wachtrij alleen verwerkt als de waarde van het item groter is dan *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Moet worden gelezen
* [Snelstartgids Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Werken met betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Reliable Services meldingen](service-fabric-reliable-services-notifications.md)
* [Back-up en herstel (nood herstel) Reliable Services](service-fabric-reliable-services-backup-restore.md)
* [Configuratie van betrouw bare status Manager](service-fabric-reliable-services-configuration.md)
* [Aan de slag met Service Fabric Web API-services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Geavanceerd gebruik van het Reliable Services-programmeer model](./service-fabric-reliable-services-lifecycle.md)
* [Naslag informatie voor ontwikkel aars voor betrouw bare verzamelingen](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
