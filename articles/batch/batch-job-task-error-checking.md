---
title: Controleren op taakfouten
description: Meer informatie over fouten voor het controleren op en het oplossen van problemen met taken en taken.
author: mscurrell
ms.topic: how-to
ms.date: 11/23/2020
ms.author: markscu
ms.openlocfilehash: d8cf3b5e28d4455e00e0bdcbae2063771d3e8acd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95736796"
---
# <a name="job-and-task-error-checking"></a>Fout controle taak en taak

Er zijn verschillende fouten die kunnen optreden bij het toevoegen van taken en taken. Het detecteren van fouten voor deze bewerkingen is eenvoudig, omdat eventuele fouten direct door de API, CLI of de gebruikers interface worden geretourneerd. Er zijn echter ook fouten die later kunnen optreden wanneer taken en taken worden gepland en uitgevoerd.

In dit artikel worden de fouten behandeld die zich kunnen voordoen nadat taken en taken zijn verzonden en hoe ze moeten worden gecontroleerd en afgehandeld.

## <a name="jobs"></a>Taken

Een taak is een groepering van een of meer taken, waarbij de taken daad werkelijk de opdracht regels opgeven die moeten worden uitgevoerd.

Bij het toevoegen van een taak kunnen de volgende para meters worden opgegeven die van invloed kunnen zijn op hoe de taak kan mislukken:

- [Taak beperkingen](/rest/api/batchservice/job/add#jobconstraints)
  - De `maxWallClockTime` eigenschap kan eventueel worden opgegeven om de maximale hoeveelheid tijd in te stellen waarmee een taak actief of actief kan zijn. Als deze wordt overschreden, wordt de taak beëindigd met de `terminateReason` eigenschap die is ingesteld in de [executionInfo](/rest/api/batchservice/job/get#cloudjob) voor de taak.
- [Taak voor het voorbereiden van taken](/rest/api/batchservice/job/add#jobpreparationtask)
  - Indien opgegeven wordt een taak voorbereidings taak uitgevoerd wanneer een taak voor de eerste keer wordt uitgevoerd voor een taak op een knoop punt. De taak voor het voorbereiden van de taak kan mislukken, waardoor de taak niet wordt uitgevoerd en de taak niet wordt voltooid.
- [Taak voor taak release](/rest/api/batchservice/job/add#jobreleasetask)
  - Er kan alleen een taak vrijgave taak worden opgegeven als een taak voorbereidings taak is geconfigureerd. Wanneer een taak wordt beëindigd, wordt de taak vrijgave uitgevoerd op elk van de groeps knooppunten waarop een taak voorbereidings taak is uitgevoerd. Een taak vrijgave taak kan mislukken, maar de taak zal nog steeds worden verplaatst naar een `completed` status.

### <a name="job-properties"></a>Taak eigenschappen

De volgende taak eigenschappen moeten worden gecontroleerd op fouten:

- '[executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation)':
  - De `terminateReason` eigenschap kan waarden bevatten om aan te geven dat de `maxWallClockTime` , opgegeven in de taak beperkingen, is overschreden en dat de taak is beëindigd. Het kan ook worden ingesteld om aan te geven dat een taak is mislukt als de taak eigenschap op de `onTaskFailure` juiste wijze is ingesteld.
  - De eigenschap [schedulingError](/rest/api/batchservice/job/get#jobschedulingerror) wordt ingesteld als er een plannings fout is opgetreden.

### <a name="job-preparation-tasks"></a>Taak voorbereidings taken

Als een taak voorbereidings taak voor een taak is opgegeven, wordt een exemplaar van die taak uitgevoerd wanneer een taak voor de taak voor het eerst op een knoop punt wordt uitgevoerd. De taak voor taak voorbereiding die is geconfigureerd voor de taak kan worden beschouwd als een taak sjabloon, waarbij meerdere taak voorbereidings taken worden uitgevoerd, tot het aantal knoop punten in een pool.

De taak exemplaren voor taak voorbereiding moeten worden gecontroleerd om te bepalen of er fouten zijn opgetreden:

- Wanneer een taak voorbereidings taak wordt uitgevoerd, wordt de taak die de taak voorbereidings taken heeft geactiveerd, verplaatst naar een [toestand](/rest/api/batchservice/task/get#taskstate) van `preparing` ; als de taak voorbereidings taken vervolgens mislukken, wordt de activerings taak teruggezet naar de `active` status en wordt deze niet uitgevoerd.
- Alle exemplaren van de taak voorbereidings taak die zijn uitgevoerd, kunnen worden verkregen van de taak met behulp van de status-API van de [lijst voorbereiding en release taak](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Net als bij elke taak is er [uitvoerings informatie](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) beschikbaar met eigenschappen zoals `failureInfo` , `exitCode` en `result` .
- Als taak voorbereidings taken mislukken, worden de activerings taak taken niet uitgevoerd. de taak wordt niet voltooid en blijft actief. De pool kan worden gebruikt als er geen andere taken zijn met taken die kunnen worden gepland.

### <a name="job-release-tasks"></a>Taak release taken

Als een taak release taak is opgegeven voor een taak en een taak wordt beëindigd, wordt een exemplaar van de taak vrijgave uitgevoerd op elk pool knooppunt waar een taak voorbereidings taak werd uitgevoerd. De taak exemplaren van de taak release moeten worden gecontroleerd om te bepalen of er fouten zijn opgetreden:

- Alle exemplaren van de taak die wordt uitgevoerd, kunnen worden opgehaald uit de taak met behulp van de voor bereiding van de API- [lijst en de status van de taak release](/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Net als bij elke taak is er [uitvoerings informatie](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) beschikbaar met eigenschappen zoals `failureInfo` , `exitCode` en `result` .
- Als een of meer taken voor de taak release mislukken, wordt de taak nog steeds beëindigd en verplaatst naar een `completed` status.

## <a name="tasks"></a>Taken

Taak taken kunnen om verschillende redenen mislukken:

- De opdracht regel van de taak is mislukt, retour neren met een afsluit code die niet gelijk is aan nul.
- Er zijn `resourceFiles` opgegeven voor een taak, maar er is een fout opgetreden waardoor een of meer bestanden niet zijn gedownload.
- Er zijn `outputFiles` opgegeven voor een taak, maar er is een fout opgetreden waardoor een of meer bestanden niet zijn geüpload.
- De verstreken tijd voor de taak, opgegeven door de `maxWallClockTime` eigenschap in de taak [beperkingen](/rest/api/batchservice/task/add#taskconstraints), is overschreden.

In alle gevallen moeten de volgende eigenschappen worden gecontroleerd op fouten en informatie over de fouten:

- De eigenschap tasks [executionInfo](/rest/api/batchservice/task/get#taskexecutioninformation) bevat meerdere eigenschappen die informatie geven over een fout. [resultaat](/rest/api/batchservice/task/get#taskexecutionresult) geeft aan of de taak om een of andere reden is mislukt, met `exitCode` en `failureInfo` meer informatie over de fout.
- De taak wordt altijd verplaatst naar de `completed` [status](/rest/api/batchservice/task/get#taskstate), ongeacht of deze is geslaagd of mislukt.

De impact van taak fouten op de taak en eventuele taak afhankelijkheden moeten worden overwogen. De eigenschap [exitConditions](/rest/api/batchservice/task/add#exitconditions) kan worden opgegeven voor een taak voor het configureren van een actie voor afhankelijkheden en voor de taak.

- Voor afhankelijkheden bepaalt [DependencyAction](/rest/api/batchservice/task/add#dependencyaction) of de taken die afhankelijk zijn van de mislukte taak worden geblokkeerd of worden uitgevoerd.
- Voor de taak bepaalt [JobAction](/rest/api/batchservice/task/add#jobaction) of de mislukte taak naar de taak wordt uitgeschakeld, wordt beëindigd of ongewijzigd blijft.

### <a name="task-command-line-failures"></a>Opdracht regel fouten taak

Wanneer de opdracht regel van de taak wordt uitgevoerd, wordt uitvoer geschreven naar `stderr.txt` en `stdout.txt` . Daarnaast kan de toepassing schrijven naar toepassingsspecifieke logboek bestanden.

Als het groeps knooppunt waarop een taak is uitgevoerd nog bestaat, kunnen de logboek bestanden worden verkregen en weer gegeven. De Azure Portal lijsten en kunnen bijvoorbeeld logboek bestanden voor een taak of een groeps knooppunt weer geven. Meerdere Api's staan ook toe dat taak bestanden worden weer gegeven en opgehaald, zoals [ophalen van taak](/rest/api/batchservice/file/getfromtask).

Omdat Pools en groeps knooppunten regel matig kortstondig zijn, en als knoop punten voortdurend worden toegevoegd en verwijderd, is het raadzaam om logboek bestanden op te slaan. [Taak uitvoer bestanden](./batch-task-output-files.md) zijn een handige manier om logboek bestanden op te slaan in azure Storage.

De opdracht regels die door taken op reken knooppunten worden uitgevoerd, worden niet uitgevoerd onder een shell, zodat ze geen systeem eigen gebruik kunnen maken van shell-functies zoals omgevings variabele uitbrei ding. Als u gebruik wilt maken van deze functies, moet u [de shell aanroepen in de opdracht regel](batch-compute-node-environment-variables.md#command-line-expansion-of-environment-variables).

### <a name="output-file-failures"></a>Fouten in het uitvoer bestand

Bij elke upload van het bestand schrijft batch twee logboek bestanden naar het reken knooppunt `fileuploadout.txt` en `fileuploaderr.txt` . U kunt deze logboek bestanden controleren om meer te weten te komen over een specifieke fout. In gevallen waarin de upload van het bestand nooit is geslaagd, bijvoorbeeld omdat de taak zelf niet kan worden uitgevoerd, zijn deze logboek bestanden niet aanwezig.  

## <a name="next-steps"></a>Volgende stappen

- Controleer of uw toepassing uitgebreide fout controle implementeert. het kan van cruciaal belang zijn om problemen op te sporen en op te sporen.
- Meer informatie over [taken en taken](jobs-and-tasks.md).