---
title: Prestaties en schaal in Durable Functions-Azure
description: Meer informatie over de unieke schaal kenmerken van de Durable Functions extensie voor Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 120335a7bce83bc3d4771ea64f665d67c7d1079a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572796"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestaties en schaalbaarheid in Durable Functions (Azure Functions)

Als u de prestaties en schaal baarheid wilt optimaliseren, is het belang rijk dat u de unieke schaal kenmerken van [Durable functions](durable-functions-overview.md)begrijpt.

Om het schaal gedrag te begrijpen, moet u enkele details van de onderliggende Azure Storage Provider begrijpen.

## <a name="history-table"></a>Geschiedenistabel

De **geschiedenis** tabel is een Azure Storage tabel die de geschiedenis gebeurtenissen bevat voor alle Orchestration-instanties binnen een task hub. De naam van deze tabel bevindt zich in de vorm *TaskHubName* geschiedenis. Als instanties worden uitgevoerd, worden nieuwe rijen toegevoegd aan deze tabel. De partitie sleutel van deze tabel is afgeleid van de exemplaar-ID van de indeling. Een exemplaar-ID is in de meeste gevallen wille keurig. Dit zorgt ervoor dat interne partities in Azure Storage optimaal worden gedistribueerd.

Wanneer een Orchestration-exemplaar moet worden uitgevoerd, worden de juiste rijen van de geschiedenis tabel in het geheugen geladen. Deze *geschiedenis gebeurtenissen* worden vervolgens opnieuw afgespeeld in de Orchestrator-functie code om deze terug te krijgen in de status van het eerder vastgelegde controle punt. Het gebruik van de uitvoerings geschiedenis om de status op deze manier opnieuw op te bouwen, wordt beïnvloed door het [patroon gebeurtenis bronnen](/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabel met exemplaren

De tabel **instances** is een andere Azure Storage tabel die de statussen bevat van alle indelings-en entiteits instanties binnen een task hub. Als er exemplaren worden gemaakt, worden er nieuwe rijen aan deze tabel toegevoegd. De partitie sleutel van deze tabel is de indelings exemplaar-ID of entiteits sleutel en de rij is een lege teken reeks. Er is één rij per indelings-of entiteits exemplaar.

Deze tabel wordt gebruikt om te voldoen aan de instantie query aanvragen van de `GetStatusAsync` api's (.net) en `getStatus` (Java script) en de [http-API van de status query](durable-functions-http-api.md#get-instance-status). Het wordt uiteindelijk consistent met de inhoud van de eerder genoemde **geschiedenis** tabel. Het gebruik van een afzonderlijke Azure Storage tabel om op deze manier efficiënt te voldoen aan de instantie query bewerkingen, wordt beïnvloed door het [CQRS-patroon (Command and query Responsibility segregation)](/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Interne wachtrij Triggers

Orchestrator-functies en-activiteit functies worden beide geactiveerd door interne wacht rijen in de taak hub van de functie-app. Met behulp van wacht rijen op deze manier beschikt u over betrouw bare leverings garanties voor ' ten minste één keer '. Er zijn twee typen wacht rijen in Durable Functions: de **controle wachtrij** en de **wachtrij met werk items**.

### <a name="the-work-item-queue"></a>De wachtrij voor werk items

Er bevindt zich één werk item wachtrij per taak-hub in Durable Functions. Het is een eenvoudige wachtrij en gedraagt zich op dezelfde manier als een andere `queueTrigger` wachtrij in azure functions. Deze wachtrij wordt gebruikt voor het activeren van stateless *activiteiten functies* door één bericht tegelijk te delegeren. Elk van deze berichten bevat invoer van de functie activiteit en aanvullende meta gegevens, zoals welke functie moet worden uitgevoerd. Wanneer een Durable Functions toepassing wordt geschaald naar meerdere Vm's, zijn deze Vm's allemaal concurrerend om werk te verkrijgen vanuit de werk wachtrij.

### <a name="control-queues"></a>Controle wachtrij (en)

Er zijn meerdere *controle wachtrijen* per taak hub in Durable functions. Een *controle wachtrij* is geavanceerder dan de vereenvoudigde wachtrij voor werk items. Controle wachtrijen worden gebruikt voor het activeren van stateful Orchestrator-en Entity-functies. Omdat de functie instanties van Orchestrator en Entity stateful Singleton zijn, is het niet mogelijk een concurrerend consument model te gebruiken om de belasting over Vm's te verdelen. In plaats daarvan worden Orchestrator-en entiteits berichten verdeeld over de controle wachtrijen. Meer informatie over dit gedrag vindt u in de volgende secties.

Controle wachtrijen bevatten diverse indelings levenscyclus bericht typen. Voor beelden zijn [Orchestrator-besturings berichten](durable-functions-instance-management.md), *antwoord* berichten over de functie van de activiteit en timer berichten. Net als 32 berichten worden in een enkele poll van een controle wachtrij uit de wachtrij verwijderd. Deze berichten bevatten payload-gegevens en meta gegevens met inbegrip van de indeling waarvoor deze is bedoeld. Als meerdere uit de wachtrij geplaatste berichten zijn bedoeld voor hetzelfde exemplaar van de indeling, worden deze verwerkt als een batch.

### <a name="queue-polling"></a>Polling voor wachtrij

De uitbrei ding duurzame taak implementeert een wille keurig exponentiële uitstel algoritme om het effect van polling bij een niet-actieve wachtrij op kosten voor opslag transacties te verminderen. Wanneer een bericht wordt gevonden, controleert de runtime onmiddellijk op een ander bericht. Wanneer er geen bericht wordt gevonden, wordt gewacht tot een bepaalde tijd voordat u het opnieuw probeert. Na volgende mislukte pogingen om een wachtrij bericht op te halen, blijft de wacht tijd toenemen totdat de maximale wacht tijd is bereikt. de standaard waarde is 30 seconden.

De maximale polling vertraging kan worden geconfigureerd via de `maxQueuePollingInterval` eigenschap in de [host.jsvoor het bestand](../functions-host-json.md#durabletask). Als u deze eigenschap instelt op een hogere waarde, kan dit leiden tot hogere latenties voor bericht verwerking. Hogere latenties worden alleen verwacht na Peri Oden van inactiviteit. Als u deze eigenschap instelt op een lagere waarde, kan dit leiden tot hogere opslag kosten als gevolg van grotere opslag transacties.

> [!NOTE]
> Bij het uitvoeren van de Azure Functions verbruiks-en Premium-abonnementen, wordt elke regel van elk besturings element en elke werk item elke 10 seconden gecontroleerd door de [Azure functions Scale-controller](../event-driven-scaling.md) . Deze extra polling is nodig om te bepalen wanneer u functie-app-exemplaren wilt activeren en schaal beslissingen wilt maken. Op het moment van schrijven is dit 10 seconden interval constant en kan het niet worden geconfigureerd.

### <a name="orchestration-start-delays"></a>Start vertragingen voor orchestration
Indelings instanties worden gestart door een bericht te plaatsen `ExecutionStarted` in een van de beheer wachtrijen van de taak hub. Onder bepaalde omstandigheden kunt u vertragingen in meerdere seconden waarnemen tussen het moment waarop een indeling is gepland en wanneer deze daad werkelijk wordt uitgevoerd. Tijdens dit tijds interval blijft de Orchestration-instantie in de `Pending` status. Er zijn twee mogelijke oorzaken van deze vertraging:

1. **Wacht rijen voor achterstand**: als de controle wachtrij voor dit exemplaar een groot aantal berichten bevat, kan het enige tijd duren voordat het `ExecutionStarted` bericht door de runtime wordt ontvangen en verwerkt. Bericht achterstand kan gebeuren wanneer de indelingen gelijktijdig worden verwerkt. Gebeurtenissen die in de controle wachtrij worden opgenomen, zijn onder andere indelings begin gebeurtenissen, voltooiingen van activiteiten, duurzame timers, beëindiging en externe gebeurtenissen. Als deze vertraging onder normale omstandigheden optreedt, kunt u overwegen een nieuwe taak hub te maken met een groter aantal partities. Het configureren van meer partities zorgt ervoor dat de runtime meer controle wachtrijen maakt voor de verdeling van de belasting.

2. **Achterwaartse polling-vertragingen**: een andere veelvoorkomende oorzaak van de herstels vertraging is het [eerder beschreven polling gedrag van de back-up voor controle wachtrijen](#queue-polling). Deze vertraging wordt echter alleen verwacht wanneer een app wordt geschaald naar twee of meer exemplaren. Als er slechts één app-exemplaar is, of als het app-exemplaar dat de indeling start ook hetzelfde exemplaar is dat de doel beheer wachtrij navraagt, wordt er geen polling vertraging voor de wachtrij. Uitstel vertraging kan worden beperkt door de **host.jsop instellingen bij** te werken, zoals eerder beschreven.

## <a name="storage-account-selection"></a>Opslag account selecteren

De wacht rijen, tabellen en blobs die door Durable Functions worden gebruikt, worden gemaakt in een geconfigureerd Azure Storage-account. Het account dat moet worden gebruikt, kan worden opgegeven met behulp van de `durableTask/storageProvider/connectionStringName` instelling (of `durableTask/azureStorageConnectionStringName` instelling in Durable functions 1. x) in het bestand **host.js** .

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Als u niets opgeeft, wordt het standaard `AzureWebJobsStorage` opslag account gebruikt. Voor prestatie gevoelige workloads wordt het configureren van een niet-standaard opslag account echter aanbevolen. Durable Functions maakt gebruik van Azure Storage intensief, en met behulp van een toegewezen opslag account wordt het gebruik geïsoleerd van Durable Functions opslag van het interne gebruik door de Azure Functions-host.

## <a name="orchestrator-scale-out"></a>Orchestrator-uitschalen

Activiteit functies zijn stateless en worden automatisch geschaald door Vm's toe te voegen. Orchestrator-functies en-entiteiten zijn daarentegen *gepartitioneerd* over een of meer controle wachtrijen. Het aantal controle wachtrijen wordt gedefinieerd in de **host.jsvoor** het bestand. In het volgende voor beeld host.jsop fragment de `durableTask/storageProvider/partitionCount` eigenschap (of `durableTask/partitionCount` in Durable functions 1. x) ingesteld op `3` .

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Een task hub kan worden geconfigureerd met tussen de 1 en 16 partities. Als deze niet is opgegeven, is het standaard aantal partities **4**.

Bij het uitschalen naar meerdere functie-instanties (meestal op verschillende Vm's), krijgt elk exemplaar een vergren deling van een van de controle wachtrijen. Deze vergren delingen worden intern geïmplementeerd als Blob Storage-leases en zorgen ervoor dat een Orchestrator-exemplaar of-entiteit alleen op één exemplaar van een host tegelijk wordt uitgevoerd. Als een taak hub is geconfigureerd met drie controle wachtrijen, kunnen indelings instanties en entiteiten worden verdeeld over Maxi maal drie Vm's. Er kunnen extra Vm's worden toegevoegd om de capaciteit voor de uitvoering van de activiteit functie te verhogen.

In het volgende diagram ziet u hoe de Azure Functions host communiceert met de opslag entiteiten in een geschaalde omgeving.

![Diagram schalen](./media/durable-functions-perf-and-scale/scale-diagram.png)

Zoals u in het vorige diagram ziet, concurreren alle Vm's met berichten in de werk wachtrij. Er kunnen echter maar drie Vm's berichten van controle wachtrijen ophalen en elke VM vergrendelt één controle wachtrij.

Indelings instanties en entiteiten worden gedistribueerd over alle exemplaren van de controle wachtrij. De distributie wordt uitgevoerd door hashing van de exemplaar-ID van de indeling of de naam en sleutel paar van de entiteit. Indelings exemplaar-Id's zijn standaard wille keurige GUID'S, zodat exemplaren gelijkmatig over alle controle wachtrijen worden verdeeld.

Over het algemeen zijn Orchestrator-functies lichter en zijn ze niet vereist voor een grote hoeveelheid reken kracht. Daarom is het niet nodig om een groot aantal controle wachtrij partities te maken om een goede door Voer voor de integratie te verkrijgen. De meeste zware werkzaamheden moeten worden uitgevoerd in stateless activiteiten functies, die oneindig kunnen worden uitgebreid.

## <a name="auto-scale"></a>Automatisch schalen

Net als bij alle Azure Functions die worden uitgevoerd in het verbruiks-en elastische Premium-abonnement, Durable Functions ondersteunt automatisch schalen via de [Azure functions Scale-controller](../event-driven-scaling.md#runtime-scaling). De schaal controller bewaakt de latentie van alle wacht rijen door regel matig de opdracht _Peek_ te geven. Op basis van de latentie van de gepeekte berichten, bepaalt de schaal controller of Vm's moeten worden toegevoegd of verwijderd.

Als de schaal controller bepaalt dat de bericht latenties van de controle wachtrij te hoog zijn, worden er VM-exemplaren toegevoegd totdat de bericht latentie afneemt naar een acceptabel niveau of het aantal partities van de controle wachtrij bereikt. De schaal controller voegt voortdurend VM-exemplaren toe als wacht tijden voor werk items in de wachtrij hoog zijn, ongeacht het aantal partities.

> [!NOTE]
> Vanaf Durable Functions 2,0 kunnen functie-apps worden geconfigureerd om te worden uitgevoerd binnen met VNET beveiligde service-eind punten in het elastische Premium-abonnement. In deze configuratie activeert de Durable Functions schaal aanvragen te initiëren in plaats van de schaal controller.

## <a name="thread-usage"></a>Thread gebruik

Orchestrator-functies worden uitgevoerd op één thread om ervoor te zorgen dat de uitvoering deterministisch kan worden uitgevoerd in veel herhalingen. Als gevolg van deze uitvoering met één thread is het belang rijk dat Orchestrator functions-threads geen CPU-intensieve taken uitvoeren, I/O of blok keren om een wille keurige reden. Werk waarvoor I/O, blok keren of meerdere threads nodig zijn, moet worden verplaatst naar activiteiten functies.

Activiteit functies hebben hetzelfde gedrag als normale, door de wachtrij geactiveerde functies. Ze kunnen I/O, CPU-intensieve bewerkingen uitvoeren en meerdere threads gebruiken. Omdat activiteit triggers stateless zijn, kunnen ze vrij uitschalen naar een niet-gebonden aantal Vm's.

Entiteits functies worden ook uitgevoerd op één thread en bewerkingen worden één voor een tijd verwerkt. Entiteits functies hebben echter geen beperkingen voor het type code dat kan worden uitgevoerd.

## <a name="concurrency-throttles"></a>Gelijktijdigheids beperking

Azure Functions ondersteunt het uitvoeren van meerdere functies gelijktijdig in één app-exemplaar. Met deze gelijktijdige uitvoering wordt de parallelle afkorting verbeterd en wordt het aantal ' koude start ' geminimaliseerd dat een typische app in de loop van de tijd zal werken. Een hoge gelijktijdigheid kan echter per VM worden uitgeput systeem bronnen die netwerk verbindingen of beschik bare geheugen. Afhankelijk van de behoeften van de functie-app, kan het nodig zijn om de gelijktijdigheid per exemplaar te beperken om te voor komen dat er onvoldoende geheugen beschikbaar is in situaties met hoge belasting.

De limieten voor de functie voor gelijktijdige uitvoering van activiteiten, Orchestrator en entiteiten kunnen worden geconfigureerd in de **host.jsvoor** het bestand. De relevante instellingen zijn `durableTask/maxConcurrentActivityFunctions` voor activiteiten functies en `durableTask/maxConcurrentOrchestratorFunctions` voor functies van Orchestrator en Entity.

### <a name="functions-20"></a>Functies 2,0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

In het vorige voor beeld kunnen Maxi maal 10 Orchestrator-of entiteits functies en tien activiteit functies gelijktijdig worden uitgevoerd op één virtuele machine. Als dat niet is opgegeven, wordt het aantal uitvoeringen van gelijktijdige activiteit en Orchestrator of entiteits functies beperkt bij 10X het aantal kern geheugens op de virtuele machine.

> [!NOTE]
> Deze instellingen zijn handig voor het beheren van geheugen en CPU-gebruik op één virtuele machine. Als er echter meer dan meerdere Vm's zijn uitgebreid, heeft elke virtuele machine een eigen set limieten. Deze instellingen kunnen niet worden gebruikt voor het beheren van gelijktijdigheid op een globaal niveau.

## <a name="extended-sessions"></a>Uitgebreide sessies

Uitgebreide sessies is een instelling die ervoor zorgt dat de integratie en entiteiten in het geheugen worden bewaard, zelfs nadat de berichten zijn verwerkt. Het standaard effect van het inschakelen van uitgebreide sessies is dat I/O wordt beperkt tegen het Azure Storage-account en de algehele door voer te verbeteren.

U kunt uitgebreide sessies inschakelen door `durableTask/extendedSessionsEnabled` in te stellen op `true` in het **host.jsop** bestand. De `durableTask/extendedSessionIdleTimeoutInSeconds` instelling kan worden gebruikt om te bepalen hoe lang een niet-actieve sessie in het geheugen wordt gehouden:

**Functies 2,0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Functies 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Er zijn twee mogelijke nadelen van deze instelling om rekening te houden met het volgende:

1. Er is een algehele toename in het geheugen gebruik van de functie-app.
2. Als er veel gelijktijdige, langdurige Orchestrator-of entiteits functies worden uitgevoerd, kan dit een groot deel uitmaken van de door voer.

Als bijvoorbeeld `durableTask/extendedSessionIdleTimeoutInSeconds` is ingesteld op 30 seconden, is een korte functie van Orchestrator of entiteit die in minder dan 1 seconde wordt uitgevoerd, gedurende 30 seconden nog steeds geheugen in beslag neemt. Het telt ook af op basis `durableTask/maxConcurrentOrchestratorFunctions` van het eerder vermelde quotum, waardoor andere Orchestrator-of entiteits functies niet kunnen worden uitgevoerd.

De specifieke effecten van uitgebreide sessies voor Orchestrator-en entiteits functies worden in de volgende secties beschreven.

> [!NOTE]
> Uitgebreide sessies worden momenteel alleen ondersteund in .NET-talen, zoals C# of F #. De `extendedSessionsEnabled` instelling `true` voor andere platforms kan leiden tot runtime problemen, zoals het op de achtergrond mislukken van het uitvoeren van activiteiten en geactiveerde functies.


### <a name="orchestrator-function-replay"></a>Orchestrator-functie opnieuw afspelen

Zoals eerder vermeld, worden Orchestrator-functies opnieuw afgespeeld met de inhoud van de **geschiedenis** tabel. De functie code van Orchestrator wordt standaard elke keer dat een batch berichten uit een controle wachtrij wordt verwijderd, opnieuw afgespeeld. Zelfs als u gebruikmaakt van het uitwaaieren van de ventilator en wacht op het volt ooien van alle taken (bijvoorbeeld `Task.WhenAll` in .net of `context.df.Task.all` in Java script), worden er herhalingen weer gegeven die optreden als batches taak reacties na verloop van tijd worden verwerkt. Wanneer uitgebreide sessies zijn ingeschakeld, worden Orchestrator-functie exemplaren in het geheugen langer bewaard en kunnen er nieuwe berichten worden verwerkt zonder dat een volledige geschiedenis wordt afgespeeld.

De verbeterde prestaties van uitgebreide sessies worden meestal in de volgende situaties waargenomen:

* Wanneer er een beperkt aantal Orchestrator-exemplaren gelijktijdig wordt uitgevoerd.
* Wanneer indelingen een groot aantal sequentiële acties hebben (bijvoorbeeld honderden activiteiten functie aanroepen) die snel zijn voltooid.
* Wanneer de indelingen uitwaaieren en uitwaaieren, in een groot aantal acties die rond hetzelfde tijdstip zijn voltooid.
* Wanneer de Orchestrator-functies grote berichten moeten verwerken of een CPU-intensieve gegevens verwerking moeten uitvoeren.

In alle andere situaties is er doorgaans geen waarneem bare prestatie verbetering voor Orchestrator-functies.

> [!NOTE]
> Deze instellingen mogen alleen worden gebruikt nadat een Orchestrator-functie volledig is ontwikkeld en getest. Het standaard gedrag voor agressief opnieuw afspelen kan handig zijn voor het detecteren van [Orchestrator-functie code beperkingen](durable-functions-code-constraints.md) tijdens de ontwikkelings tijd en is daarom standaard uitgeschakeld.

### <a name="entity-function-unloading"></a>Entiteit functie uit het geheugen verwijderen

Met entiteits functies worden Maxi maal 20 bewerkingen in één batch verwerkt. Zodra een entiteit klaar is met de verwerking van een batch met bewerkingen, wordt de status ervan persistent gemaakt en uit het geheugen verwijderd. U kunt het verwijderen van entiteiten uit het geheugen uitstellen met de instelling uitgebreide sessies. Entiteiten blijven hun status wijzigingen behouden als voorheen, maar blijven in het geheugen voor de geconfigureerde periode om het aantal belastingen van Azure Storage te verminderen. Deze verlaging van de belasting van Azure Storage kan de algemene door Voer van veelgebruikte entiteiten verbeteren.

## <a name="performance-targets"></a>Prestatie doelen

Bij het plannen van het gebruik van Durable Functions voor een productie toepassing is het belang rijk om de prestatie vereisten vroeg in het plannings proces in overweging te nemen. In deze sectie worden enkele eenvoudige gebruiks scenario's en de verwachte maximum doorvoer aantallen besproken.

* **Uitvoering van sequentiële activiteit**: in dit scenario wordt een Orchestrator-functie beschreven waarmee een reeks activiteit wordt uitgevoerd, één na de andere. Het lijkt veel op het voor beeld van een [functie](durable-functions-sequence.md) koppeling.
* **Uitvoering van parallelle activiteit**: in dit scenario wordt een Orchestrator-functie beschreven waarmee veel activiteit functies parallel worden uitgevoerd met behulp van het [uitwaaieren van](durable-functions-cloud-backup.md) de ventilator.
* **Verwerking van parallelle antwoorden**: dit scenario is de tweede helft van het [ventilator patroon.](durable-functions-cloud-backup.md) Het is gericht op de prestaties van de ventilator. Het is belang rijk te weten dat in tegens telling tot uitwaaiers de ventilator wordt uitgevoerd door één functie-exemplaar van Orchestrator en daarom alleen op één virtuele machine kan worden uitgevoerd.
* **Externe gebeurtenis verwerking**: dit scenario is een exemplaar van een Orchestrator-functie dat op een bepaald moment op [externe gebeurtenissen](durable-functions-external-events.md)wacht.
* **Verwerking van entiteits bewerkingen**: in dit scenario wordt getest hoe snel een _enkele_ [item entiteit](durable-functions-entities.md) een constante stroom van bewerkingen kan verwerken.

> [!TIP]
> In tegens telling tot uitwaaiers zijn ventilatoren beperkt tot één virtuele machine. Als uw toepassing gebruikmaakt van het uitwaaieren, een ventilator patroon en u zich zorgen maakt over de prestaties van de ventilator, kunt u overwegen om de activiteit functie te verdelen over meerdere [onderliggende](durable-functions-sub-orchestrations.md)indelingen.

De volgende tabel bevat de verwachte *maximum* waarden voor door Voer voor de eerder beschreven scenario's. ' Instance ' verwijst naar één exemplaar van een Orchestrator-functie die wordt uitgevoerd op een enkele kleine ([a1](../../virtual-machines/sizes-previous-gen.md)) VM in azure app service. In alle gevallen wordt ervan uitgegaan dat [uitgebreide sessies](#orchestrator-function-replay) zijn ingeschakeld. De werkelijke resultaten kunnen variëren, afhankelijk van de CPU of het I/O-werk dat door de functie code wordt uitgevoerd.

| Scenario | Maximale doorvoer |
|-|-|
| Uitvoering van sequentiële activiteit | 5 activiteiten per seconde, per instantie |
| Uitvoering van parallelle activiteit (uitwaaieren) | 100 activiteiten per seconde, per instantie |
| Verwerking van parallelle antwoorden (ventilator-in) | 150 reacties per seconde, per instantie |
| Externe gebeurtenis verwerking | 50 gebeurtenissen per seconde, per instantie |
| Verwerking van entiteits bewerkingen | 64 bewerkingen per seconde |

> [!NOTE]
> Deze nummers zijn actueel vanaf de release van de v 1.4.0 (GA) van de uitbrei ding Durable Functions. Deze getallen kunnen na verloop van tijd veranderen naarmate het onderdeel verloopt en als er optimalisaties worden uitgevoerd.

Als u de door u verwachte doorvoer nummers niet ziet en het CPU-en geheugen gebruik op de juiste manier wordt weer gegeven, controleert u of de oorzaak is gerelateerd aan [de status van uw opslag account](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). De uitbrei ding van de Durable Functions kan een Azure Storage account aanzienlijk laden en voldoende hoge belasting kan leiden tot beperking van het opslag account.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over herstel na nood gevallen en geo-distributie](durable-functions-disaster-recovery-geo-distribution.md)
