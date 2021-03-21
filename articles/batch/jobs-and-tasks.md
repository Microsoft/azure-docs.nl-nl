---
title: Taken en taken in Azure Batch
description: Meer informatie over taken en taken en hoe deze worden gebruikt in een Azure Batch werk stroom vanuit een ontwikkelings oogpunt.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95808597"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Taken en taken in Azure Batch

In Azure Batch vertegenwoordigt een *taak* een reken kundige eenheid. Een *taak* is een verzameling van deze taken. Meer informatie over taken en taken, en hoe deze worden gebruikt in een Azure Batch werk stroom, wordt hieronder beschreven.

## <a name="jobs"></a>Taken

Een job is een verzameling taken. Deze beheert hoe de berekening door de taken op rekenknooppunten in een pool wordt uitgevoerd.

Een taak specificeert de [pool](nodes-and-pools.md#pools) waarin het werk moet worden uitgevoerd. U kunt voor elke job een nieuwe pool maken of één groep gebruiken voor een groot aantal jobs. U kunt een pool maken voor elke taak die is gekoppeld aan een [taak schema](#scheduled-jobs)of één pool voor alle taken die aan een taak schema zijn gekoppeld.

### <a name="job-priority"></a>Jobprioriteit

U kunt een optionele taak prioriteit toewijzen aan taken die u maakt. De batch-service gebruikt de prioriteits waarde van de taak om de volg orde van de planning (voor alle taken binnen de taak) wtihin elke groep te bepalen.

Als u de prioriteit van een taak wilt bijwerken, roept u de [Update de eigenschappen van een taak](/rest/api/batchservice/job/update) bewerking aan (batch rest) of wijzigt u de [eigenschap cloudjob. Priority](/dotnet/api/microsoft.azure.batch.cloudjob) (batch .net). Prioriteits waarden variëren van-1000 (laagste prioriteit) tot 1000 (hoogste prioriteit).

Binnen dezelfde groep hebben taken met een hogere prioriteit een plannings prioriteit boven taken met een lagere prioriteit. Taken in taken met een lagere prioriteit die al worden uitgevoerd, worden niet afgebroken door taken in een taak met een hogere prioriteit. Taken met hetzelfde prioriteits niveau hebben een gelijke kans om te worden gepland, en de volg orde van de taak uitvoering is niet gedefinieerd.

Een taak met een waarde met hoge prioriteit die in een groep wordt uitgevoerd, heeft geen invloed op de planning van taken die worden uitgevoerd in een afzonderlijke groep of in een ander batch-account. De taak prioriteit is niet van toepassing op [autopools](nodes-and-pools.md#autopools)die worden gemaakt wanneer de taak wordt verzonden.

### <a name="job-constraints"></a>Taak beperkingen

Met beperkingen voor jobs kunt u bepaalde limieten aan uw taken stellen:

- U kunt een **maximale kloktijd** instellen, zodat als de uitvoering van een job langer duurt dan de maximale kloktijd die is opgegeven, de job en alle bijbehorende taken worden beëindigd.
- U kunt het **maximum aantal nieuwe pogingen voor een taak** opgeven als een beperking, inclusief of een taak al dan niet opnieuw is geprobeerd. Als u een taak opnieuw probeert uit te voeren, wordt de taak opnieuw in de wachtrij geplaatst om opnieuw te worden uitgevoerd.

### <a name="job-manager-tasks-and-automatic-termination"></a>Taak beheer taken en automatische beëindiging

De clienttoepassing kan taken toevoegen aan een taak, maar u kunt ook een [jobbeheertaak](#job-manager-task) opgeven. Een jobbeheertaak bevat de benodigde informatie om de vereiste taken voor een job te maken, waarbij de jobbeheertaak wordt uitgevoerd op een van de rekenknooppunten binnen de pool. De taak taak beheer wordt specifiek verwerkt door batch; Zodra de taak is gemaakt, wordt deze in de wachtrij geplaatst en opnieuw opgestart als deze mislukt. Taak beheer taak is vereist voor taken die zijn gemaakt met een [taak planning](#scheduled-jobs), omdat dit de enige manier is om de taken te definiëren voordat de taak wordt geïnstantieerd.

Standaard blijven jobs in de actieve status wanneer alle taken binnen de job zijn voltooid. U kunt dit gedrag wijzigen zodat de job automatisch wordt beëindigd wanneer alle taken binnen de job zijn voltooid. Stel de eigenschap **onAllTasksComplete** van de taak ([onAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) in batch .net) in op `terminatejob` * om de taak automatisch te beëindigen wanneer alle taken de status voltooid hebben.

De batch-service beschouwt een taak *zonder taken om alle taken te* kunnen volt ooien. Daarom wordt deze optie meestal gebruikt met een [Jobbeheertaak](#job-manager-task). Als u automatische taak beëindiging wilt gebruiken zonder taak beheer, moet u eerst de eigenschap **onAllTasksComplete** van een nieuwe taak instellen op `noaction` en vervolgens instellen op `terminatejob` * ' alleen nadat u klaar bent met het toevoegen van taken aan de taak.

### <a name="scheduled-jobs"></a>Geplande jobs

Met behulp van [jobplanningen](/rest/api/batchservice/jobschedule) kunt u in de Batch-service terugkerende jobs maken. Een jobplanning geeft aan wanneer u jobs moet uitvoeren en bevat de specificaties voor de uit te voeren jobs. U kunt de duur van de planning opgeven (hoelang en wanneer de planning van kracht is) en hoe vaak taken worden gemaakt tijdens de geplande periode.

## <a name="tasks"></a>Taken

Een taak is een rekeneenheid die aan een job is gekoppeld. Deze wordt uitgevoerd op een knooppunt. Taken worden toegewezen aan een knooppunt om te worden uitgevoerd of in een wachtrij geplaatst tot een knooppunt beschikbaar is. Eenvoudig gesteld: een taak voert een of meer programma's of scripts uit op een rekenknooppunt om het benodigde werk uit te voeren.

Wanneer u een taak maakt, kunt u het volgende opgeven:

- De **opdrachtregel** van de taak. Dit is de opdrachtregel die uw toepassing of script op het rekenknooppunt uitvoert.

    Het is belang rijk te weten dat de opdracht regel niet onder een shell wordt uitgevoerd. Daarom kan deze niet op systeemeigen wijze profiteren van shell-functies zoals [omgevingsvariabele](#environment-settings-for-tasks)-uitbreiding (dit omvat het `PATH`). Als u gebruik wilt maken van dergelijke functies, moet u de shell aanroepen in de opdracht regel, zoals door te starten `cmd.exe` op Windows-knoop punten of `/bin/sh` op Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Als uw taken toepassingen of scripts moeten uitvoeren die zich niet in het `PATH` van het knooppunt bevinden of verwijzen naar omgevingsvariabelen, start u de shell expliciet in de opdrachtregel van de taak.
- **Bronbestanden** die de te verwerken gegevens bevatten. In een Azure Storage-account worden deze bestanden automatisch vanuit Blob Storage naar het knooppunt gekopieerd vóórdat de opdrachtregel van de taak wordt uitgevoerd. Zie taken en [bestanden en mappen](files-and-directories.md) [starten](#start-task) voor meer informatie.
- De **omgevingsvariabelen** die zijn vereist voor de toepassing. Zie [omgevings instellingen voor taken](#environment-settings-for-tasks)voor meer informatie.
- De **beperkingen** waaronder de taak moet worden uitgevoerd. Beperkingen zijn bijvoorbeeld de maximale duur dat de taak mag worden uitgevoerd, het maximumaantal keren dat een taak opnieuw moet worden uitgevoerd indien deze mislukt en de maximale duur dat bestanden in de werkmap van de taak behouden blijven.
- **Toepassingspakketten** voor het implementeren in het rekenknooppunt waarop de taak staat gepland voor uitvoering. [Toepassingspakketten](batch-application-packages.md) bieden vereenvoudigde implementatie en versies van de toepassingen die de taken uitvoeren. Toepassingspakketten op taakniveau zijn met name nuttig in omgevingen met gedeelde groepen. Verschillende jobs worden uitgevoerd op één groep en de groep wordt niet verwijderd wanneer een job is voltooid. Als de job minder taken dan knooppunten in de groep heeft, kunnen toepassingspakketten van taken gegevensoverdracht minimaliseren omdat uw toepassing alleen wordt geïmplementeerd op de knooppunten die taken uitvoeren.
- Een verwijzing naar een **containerinstallatiekopie** in Docker Hub of een persoonlijk register en extra instellingen voor het maken van een Docker-container waarin de taak wordt uitgevoerd op het knooppunt. U geeft deze informatie alleen op als de pool is ingesteld met een containerconfiguratie.

> [!NOTE]
> De maximale levensduur van een taak, van het toevoegen aan de job tot de voltooiing, bedraagt 180 dagen. Voltooide taken blijven gedurende zeven dagen aanwezig; gegevens voor taken die niet binnen de maximale levensduur zijn voltooid, zijn niet toegankelijk.

Naast de taken die u definieert om een berekening op een knoop punt uit te voeren, worden ook verschillende speciale taken door de batch-service verschaft:

- [Begintaak](#start-task)
- [Jobbeheertaak](#job-manager-task)
- [Jobvoorbereidingstaken en jobvrijgevingstaken](#job-preparation-and-release-tasks)
- [Taken met meerdere instanties](#multi-instance-task)
- [Taakafhankelijkheden](#task-dependencies)

### <a name="start-task"></a>Begintaak

Door een begintaak aan een pool te koppelen, kunt u de besturingsomgeving van de knooppunten ervan voorbereiden. U kunt bijvoorbeeld acties uitvoeren als het installeren van de toepassingen die de taken uitvoeren en het starten van achtergrondprocessen. De begin taak wordt uitgevoerd telkens wanneer een knoop punt wordt gestart, voor zolang deze in de pool blijft. Dit geldt ook wanneer het knoop punt voor het eerst wordt toegevoegd aan de groep en wanneer het wordt opnieuw gestart of een installatie kopie wordt gemaakt.

Een belangrijk voordeel van de begintaak is dat deze alle informatie bevat die nodig is voor het configureren van een rekenknooppunt en het installeren van de toepassingen die vereist zijn voor het uitvoeren van de taak. Het verhogen van het aantal knooppunten in een pool is daarom net zo eenvoudig als het opgeven van een nieuw aantal doelknooppunten. De begin taak bevat de informatie die nodig is voor de batch-service om de nieuwe knoop punten te configureren en ze gereed te krijgen voor het accepteren van taken.

Net als bij elke andere Azure Batch-taak kunt u een lijst met resourcebestanden in [Azure Storage](../storage/index.yml) opgeven, naast een uit te voeren opdrachtregel. De Batch-service kopieert eerst de resourcebestanden naar het knooppunt vanuit Azure Storage en voert vervolgens de opdrachtregel uit. Voor een pool-begintaak bevat de lijst met bestanden meestal de taaktoepassing en de bijbehorende afhankelijkheden.

De begintaak kan echter ook referentiegegevens bevatten die worden gebruikt door alle taken die op het rekenknooppunt worden uitgevoerd. De opdracht regel van een begin taak kan bijvoorbeeld een bewerking uitvoeren `robocopy` om toepassings bestanden (die zijn opgegeven als bron bestanden en gedownload naar het knoop punt) te kopiëren van de [werkmap](files-and-directories.md) van de begin taak naar de **gedeelde** map en vervolgens een MSI of uitvoeren `setup.exe` .

Het is doorgaans wenselijk dat de Batch-service wacht tot de begintaak is voltooid voordat het knooppunt als gereed wordt beschouwd om er taken aan toe te wijzen, maar dit kunt u configureren.

Als een begintaak op een rekenknooppunt mislukt, wordt de status van het knooppunt bijgewerkt om de fout aan te geven en is het knooppunt niet beschikbaar om taken toe te wijzen. Een begintaak kan mislukken als er een probleem optreedt bij het kopiëren van de bronbestanden van de begintaak uit de opslag, of als het proces dat door de opdrachtregel ervan wordt uitgevoerd een andere afsluitcode dan nul retourneert.

Als u de begintaak voor een bestaande pool toevoegt of bijwerkt, moet u de rekenknooppunten voor de begintaak opnieuw toepassen op de knooppunten.

>[!NOTE]
> Batch beperkt de totale grootte van een begintaak, inclusief bronbestanden en omgevingsvariabelen. Als u de grootte van een begintaak wilt beperken, kunt u dat op twee manieren doen:
>
> 1. U kunt toepassingspakketten gebruiken om toepassingen of gegevens te distribueren naar elk knooppunt in de Batch-pool. Zie [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Toepassingen implementeren naar rekenknooppunten met Batch-toepassingspakketten) voor meer informatie over toepassingspakketten.
> 2. U kunt handmatig een ZIP-archief maken dat uw toepassingsbestanden bevat. Upload het ZIP-archief als een blob naar Azure Storage. Geef het ZIP-archief op als bronbestand voor de begintaak. Voordat u de opdrachtregel voor de begintaak uitvoert, moet u het archief uitpakken vanaf de opdrachtregel. 
>
>    Daarvoor kunt u elk gewenst hulpprogramma voor archivering gebruiken. U moet het hulpprogramma dat u gebruikt om het archief uit te pakken, als bronbestand voor de begintaak opnemen.

### <a name="job-manager-task"></a>Jobbeheertaak

Normaal gesp roken gebruikt u een taak beheer taak om de uitvoering van taken te beheren en/of te controleren. Taak beheer taken worden bijvoorbeeld vaak gebruikt voor het maken en verzenden van taken voor een taak, het bepalen van aanvullende taken om uit te voeren en te bepalen wanneer het werk is voltooid.

Een jobbeheertaak is echter niet beperkt tot deze activiteiten. Het is een volledige taak waarmee acties kunnen worden uitgevoerd die vereist zijn voor de taak. Een jobbeheertaak kan bijvoorbeeld een bestand downloaden dat als een parameter is opgegeven, de inhoud van dat bestand analyseren en op basis van die inhoud aanvullende taken verzenden.

Een jobbeheertaak wordt vóór alle andere taken gestart. Deze biedt de volgende functies:

- Een jobbeheertaak wordt automatisch door de Batch-service verzonden als een taak wanneer de job wordt gemaakt.
- De uitvoering van een jobbeheertaak wordt gepland voor de andere taken in een job.
- Het hieraan gekoppelde knooppunt is het laatste dat moeten worden verwijderd uit een pool wanneer de pool wordt verkleind.
- De beëindiging ervan kan worden gekoppeld aan de beëindiging van alle taken in de job.
- Een jobbeheertaak krijgt de hoogste prioriteit wanneer deze opnieuw moet worden gestart. Als een niet-actief knooppunt niet beschikbaar is, kan de Batch-service een van de andere actieve taken in de pool beëindigen om ruimte te maken voor het uitvoeren van de jobbeheertaak.
- Een jobbeheertaak in de ene job heeft geen hogere prioriteit dan de taken van andere jobs. Tussen jobs worden alleen prioriteiten op jobniveau in acht genomen.

### <a name="job-preparation-and-release-tasks"></a>Jobvoorbereidingstaken en jobvrijgevingstaken

Batch biedt taak voorbereidings taken voor het uitvoeren van de uitvoering van voor bereidingen en taak release taken voor onderhoud na de taak of opschonen.

Een jobvoorbereidingstaak wordt uitgevoerd op alle rekenknooppunten die zijn gepland om taken uit te voeren, voordat een van de andere taken van de job wordt uitgevoerd. U kunt bijvoorbeeld een taak voorbereiding gebruiken om gegevens te kopiëren die door alle taken worden gedeeld, maar uniek zijn voor de taak.

Wanneer een job is voltooid, wordt er een jobvrijgevingstaak uitgevoerd op elk knooppunt in de pool dat ten minste één taak heeft uitgevoerd. Een taak release taak kan bijvoorbeeld gegevens die zijn gekopieerd door de taak voorbereidings taak verwijderen, of de diagnostische logboek gegevens comprimeren en uploaden.

Met zowel jobvoorbereidingstaken als jobvrijgevingstaken kunt u een opdrachtregel opgeven die moet worden uitgevoerd wanneer de taak wordt gestart. Ze bieden functies zoals het downloaden van bestanden, uitvoering met verhoogde bevoegdheden, aangepaste omgevingsvariabelen, maximale uitvoeringsduur, aantal pogingen en bewaartijd voor bestanden.

Zie [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md)  (Jobvoorbereidings- en jobvrijgevingstaken uitvoeren op Azure Batch-rekenknooppunten) voor meer informatie over jobvoorbereidings- en jobvrijgevingstaken.

### <a name="multi-instance-task"></a>Taak met meerdere instanties

Een [taak met meerdere instanties](batch-mpi.md) is een taak die is geconfigureerd om op meer dan één rekenknooppunt tegelijk te worden uitgevoerd. Met taken voor meerdere instanties kunt u High Performance Computing-scenario's inschakelen die een groep reken knooppunten vereisen die samen worden toegewezen om één workload te verwerken, zoals Message Passing Interface (MPI).

Zie [Taken met meerdere instanties gebruiken om Message Passing Interface (MPI)-toepassingen uit te voeren in Azure Batch](batch-mpi.md) voor gedetailleerde informatie over het uitvoeren van MPI-jobs in Batch met behulp van de Batch .NET-bibliotheek.

### <a name="task-dependencies"></a>Taakafhankelijkheden

Zoals de naam al aangeeft, kunt u met [taakafhankelijkheden](batch-task-dependencies.md) opgeven dat een taak afhangt van de voltooiing van andere taken voordat deze wordt uitgevoerd. Deze functie biedt ondersteuning voor situaties waarin een 'downstream'-taak gebruikmaakt van de uitvoer van een 'upstream'-taak, of wanneer een 'upstream'-taak initialisaties uitvoert die zijn vereist voor een 'downstream'-taak.

Als u deze functie wilt gebruiken, moet u eerst [taak afhankelijkheden inschakelen](batch-task-dependencies.md#enable-task-dependencies
) voor uw batch-taak. Daarna geeft u voor elke taak die afhankelijk is van een andere (of vele andere), de taken op waarvan die taak afhankelijk is.

Bij taakafhankelijkheden kunt u scenario's zoals de volgende configureren:

- *taakB* hangt af van *taakA* (*taakB* kan pas worden uitgevoerd nadat *taakA* is voltooid).
- *taakC* is afhankelijk van *taakA* én *taakB*.
- *taakD* is afhankelijk van een bereik van taken, zoals taken *1* t/m *10*, voordat deze wordt uitgevoerd.

Zie voor meer informatie [taak afhankelijkheden in azure batch](batch-task-dependencies.md) en het voor beeld van het programma voor de code [in de GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) -opslag plaats [Azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) .

### <a name="environment-settings-for-tasks"></a>Omgevingsinstellingen voor taken

Elke taak die wordt uitgevoerd door de Batch-service heeft toegang tot de omgevingsvariabelen die zijn ingesteld op de rekenknooppunten. Hieronder vallen omgevings variabelen die zijn gedefinieerd door de batch-service (door de[service gedefinieerde](./batch-compute-node-environment-variables.md) en aangepaste omgevings variabelen die u voor uw taken kunt definiëren. De toepassingen en scripts die door uw taken worden uitgevoerd, hebben tijdens de uitvoering toegang tot deze omgevingsvariabelen.

U kunt aangepaste omgevingsvariabelen instellen op het niveau van de taak of de job door voor deze entiteiten de eigenschap voor *omgevingsinstellingen* in te vullen. Zie de bewerking [een taak toevoegen aan een taak](/rest/api/batchservice/task/add?)(batch rest API) of de eigenschappen [CloudTask. EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) en [eigenschap cloudjob. CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) in batch .net voor meer informatie.

Uw clienttoepassing of -service kan de omgevingsvariabelen, zowel door de service gedefinieerde als aangepaste, verkrijgen met behulp van de bewerking [Get information about a task](/rest/api/batchservice/task/get) (Batch REST) of via de eigenschap [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) (Batch .NET). Processen die op een rekenknooppunt worden uitgevoerd, kunnen ook toegang krijgen tot deze en andere omgevingsvariabelen in het knooppunt, bijvoorbeeld met behulp van de vertrouwde syntaxis van `%VARIABLE_NAME%` (Windows) of `$VARIABLE_NAME` (Linux).

Een volledige lijst van alle gedefinieerde omgevingsvariabelen vindt u in [Compute node environment variables](batch-compute-node-environment-variables.md) (Knooppuntomgevingsvariabelen berekenen).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bestanden en mappen](files-and-directories.md).
