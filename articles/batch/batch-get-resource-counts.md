---
title: Statussen voor taken en knoop punten tellen
description: Tel de status van Azure Batch taken en reken knooppunten op om batch-oplossingen te beheren en te bewaken.
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85960585"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Batch-oplossingen bewaken door taken en knoop punten te tellen per status

Als u grootschalige Azure Batch oplossingen wilt bewaken en beheren, moet u mogelijk het aantal resources in verschillende Staten bepalen. Azure Batch biedt efficiënte bewerkingen voor het ophalen van aantallen voor batch taken en reken knooppunten. U kunt deze bewerkingen gebruiken in plaats van mogelijk tijdrovende lijst query's die gedetailleerde informatie over grote verzamelingen taken of knoop punten retour neren.

- [Taak aantallen ophalen](/rest/api/batchservice/job/gettaskcounts) haalt een totaal aantal actieve, uitgevoerde en voltooide taken in een taak op en van taken die zijn geslaagd of mislukt. 

  Door taken in elke status te tellen, kunt u de taak voortgang gemakkelijker weer geven voor een gebruiker of onverwachte vertragingen of fouten detecteren die van invloed kunnen zijn op de taak. Taak aantallen ophalen is beschikbaar vanaf de API-versie 2017 van de batch-service, -06-01.5.1 en gerelateerde Sdk's en hulpprogram ma's.

- Aantallen van het aantal [weer geven](/rest/api/batchservice/account/listpoolnodecounts) van de lijst wordt opgehaald uit een van de bestemde en lage prioriteits knooppunten in elke groep die zich in verschillende statussen bevinden: maken, inactief, offline, afgebroken, opnieuw opstarten, opnieuw maken van installatie kopieën, starten en andere.

  Door knoop punten te tellen in elke status kunt u bepalen wanneer u beschikt over de juiste reken resources om uw taken uit te voeren en mogelijke problemen met uw Pools te identificeren. Het aantal verzamelingen van de lijst is beschikbaar vanaf de API-versie van de batch-service 2018 -03-01.6.1 en gerelateerde Sdk's en hulpprogram ma's.

Houd er rekening mee dat de getallen die worden geretourneerd door deze bewerkingen mogelijk niet up-to-date zijn. Als u er zeker van wilt zijn dat een aantal nauw keurig is, gebruikt u een lijst query om deze resources te tellen. Met een lijst met query's kunt u ook informatie krijgen over andere batch-resources, zoals toepassingen. Zie [Query's maken om batch resources efficiënt weer te geven](batch-efficient-list-queries.md)voor meer informatie over het Toep assen van filters op lijst query's.

## <a name="task-state-counts"></a>Aantal taak status

De bewerking taak aantal ophalen telt taken met de volgende statussen:

- **Actief** : een taak die in de wachtrij is geplaatst en kan worden uitgevoerd, maar die momenteel niet is toegewezen aan een reken knooppunt. Een taak is ook `active` als deze [afhankelijk is van een bovenliggende taak](batch-task-dependencies.md) die nog niet is voltooid. 
- **Uitvoeren** : een taak die is toegewezen aan een reken knooppunt, maar nog niet is voltooid. Een taak wordt meegeteld als `running` de status `preparing` of is `running` , zoals wordt aangegeven door de bewerking [informatie over een taak ophalen](/rest/api/batchservice/task/get) .
- **Voltooid** : een taak die niet langer in aanmerking komt voor uitvoering, omdat deze is voltooid, of als deze is voltooid en de limiet voor nieuwe pogingen is bereikt. 
- **Geslaagd** : een taak waarvan het resultaat van de uitvoering van de taak is `success` . Batch bepaalt of een taak is geslaagd of mislukt door de `TaskExecutionResult` eigenschap van de eigenschap [executionInfo](/rest/api/batchservice/task/get) te controleren.
- **Is mislukt** Een taak waarvan het resultaat van de uitvoering van de taak is `failure` .

In het volgende voor beeld van de .NET-code ziet u hoe u taak aantallen kunt ophalen per status:

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

U kunt een vergelijkbaar patroon voor REST en andere ondersteunde talen gebruiken om taak aantallen voor een taak te ontvangen. 

> [!NOTE]
> De API-versies van de batch-service vóór 2018 -08-01.7.0 retour neren ook een `validationStatus` eigenschap in het antwoord taak aantallen ophalen. Deze eigenschap geeft aan of batch de status aantallen heeft gecontroleerd op consistentie met de statussen die zijn gerapporteerd in de API lijst taken. Een waarde van `validated` geeft alleen aan dat de batch ten minste één keer voor de taak is gecontroleerd op consistentie. De waarde van de `validationStatus` eigenschap geeft niet aan of de aantallen die de telling van het aantal taken ophalen, op dit moment up-to-date zijn.

## <a name="node-state-counts"></a>Aantal knooppunt statussen

De bewerking aantal knoop punten van een lijst groep telt het aantal reken knooppunten op basis van de volgende statussen in elke groep. Er worden afzonderlijke statistische aantallen gegeven voor toegewezen knoop punten en knoop punten met lage prioriteit in elke groep.

- **Maken** : een door Azure toegewezen virtuele machine die nog niet is gestart om lid te worden van een groep.
- **Inactief** : er is een beschik bare Compute-knoop punt dat momenteel geen taak wordt uitgevoerd.
- **LeavingPool** : een knoop punt dat de pool verlaat, hetzij omdat de gebruiker deze expliciet verwijdert of omdat het formaat van de groep wordt gewijzigd of automatisch wordt geschaald.
- **Offline** : een knoop punt dat batch niet kan gebruiken om nieuwe taken te plannen.
- **Afgebroken** : een knoop punt met lage prioriteit dat uit de groep is verwijderd omdat Azure de virtuele machine heeft teruggewonnen. Een `preempted` knoop punt kan opnieuw worden geïnitialiseerd als er een VM-capaciteit met lage prioriteit beschikbaar is.
- **Opnieuw opstarten** : een knoop punt dat opnieuw wordt opgestart.
- **Reimaging** : een knoop punt waarop het besturings systeem opnieuw wordt geïnstalleerd.
- **Uitvoeren** : een knoop punt waarop een of meer taken worden uitgevoerd (anders dan de begin taak).
- **Starten** : een knoop punt waarop de batch-service wordt gestart. 
- **StartTaskFailed** : een knoop punt waarop de [begin taak](/rest/api/batchservice/pool/add#starttask) is mislukt en alle pogingen zijn uitgeput en op `waitForSuccess` de begin taak is ingesteld. Het knoop punt is niet bruikbaar voor het uitvoeren van taken.
- **Onbekend** : een knoop punt dat contact heeft gemaakt met de batch-service en waarvan de status niet bekend is.
- **Onbruikbaar** : een knoop punt dat niet kan worden gebruikt voor het uitvoeren van taken vanwege fouten.
- **WaitingForStartTask** : een knoop punt waarop de begin taak wordt uitgevoerd, maar `waitForSuccess` is ingesteld en de begin taak is niet voltooid.

Het volgende C#-code fragment laat zien hoe u het aantal knoop punten in het huidige account kunt weer geven voor alle Pools:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

Het volgende C#-code fragment laat zien hoe u het aantal knoop punten voor een bepaalde groep in het huidige account kunt weer geven.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

U kunt een vergelijkbaar patroon voor REST en andere ondersteunde talen gebruiken om het aantal knoop punten voor Pools te verkrijgen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie over het Toep assen van filters op query's met batch-resources Zie [Query's maken om batch-resources efficiënt weer te geven](batch-efficient-list-queries.md).
