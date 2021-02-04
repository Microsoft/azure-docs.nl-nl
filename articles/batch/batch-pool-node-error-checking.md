---
title: Controleren op groeps-en knooppunt fouten
description: In dit artikel worden de achtergrond bewerkingen behandeld die zich kunnen voordoen, samen met fouten die moeten worden gecontroleerd en hoe u deze kunt voor komen bij het maken van Pools en knoop punten.
author: mscurrell
ms.author: markscu
ms.date: 02/03/2020
ms.topic: how-to
ms.openlocfilehash: 8901877ab3055c02dfc8c129fb35864418cd19d8
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549132"
---
# <a name="check-for-pool-and-node-errors"></a>Controleren op groeps-en knooppunt fouten

Wanneer u Azure Batch groepen maakt en beheert, vinden sommige bewerkingen onmiddellijk plaats. Het detecteren van fouten voor deze bewerkingen is doorgaans eenvoudig, omdat deze direct worden geretourneerd door de API, CLI of gebruikers interface. Sommige bewerkingen zijn echter asynchroon en worden uitgevoerd op de achtergrond. het duurt enkele minuten voordat de bewerking is voltooid.

Controleer of u uw toepassingen hebt ingesteld voor het implementeren van uitgebreide fout controle, met name voor asynchrone bewerkingen. Dit kan u helpen om problemen op te sporen en op te lossen.

In dit artikel worden manieren beschreven om fouten in de achtergrond bewerkingen te detecteren en te voor komen die kunnen optreden voor Pools en groeps knooppunten.

## <a name="pool-errors"></a>Groeps fouten

### <a name="resize-timeout-or-failure"></a>Grootte van time-out of fout wijzigen

Bij het maken van een nieuwe groep of het wijzigen van de grootte van een bestaande groep, geeft u het doel aantal knoop punten op. De bewerking maken of formaat wijzigen wordt onmiddellijk voltooid, maar de daad werkelijke toewijzing van nieuwe knoop punten of het verwijderen van bestaande knoop punten kan enkele minuten duren. U geeft de time-out voor de grootte van cam op in de API [Create](/rest/api/batchservice/pool/add) of [Resize](/rest/api/batchservice/pool/resize) . Als batch het doel aantal knoop punten niet kan verkrijgen tijdens de time-outperiode voor het wijzigen van de grootte, wordt de groep omgezet in een stabiele status en worden er fouten gerapporteerd.

De eigenschap [ResizeError](/rest/api/batchservice/pool/get#resizeerror) voor de meest recente evaluatie lijst bevat de fouten die zijn opgetreden.

Veelvoorkomende oorzaken voor het wijzigen van de grootte zijn:

- Grootte van time-out is te kort
  - In de meeste gevallen is de standaard time-out van 15 minuten lang genoeg voor het toewijzen of verwijderen van pool knooppunten.
  - Als u een groot aantal knoop punten toewijst, is het raadzaam om de time-outwaarde in te stellen op 30 minuten. Bijvoorbeeld wanneer u het formaat van een installatie kopie van een Azure Marketplace of van meer dan 300 knooppunten wijzigt in meer dan 1.000 knoop punten.
- Onvoldoende kern quotum
  - Een batch-account is beperkt in het aantal kernen dat kan worden toegewezen in alle groepen. Batch stopt met het toewijzen van knoop punten nadat het quotum is bereikt. U [kunt](./batch-quota-limit.md) het kern quotum verhogen zodat batch meer knoop punten kan toewijzen.
- Onvoldoende subnet-IP-adressen wanneer een [groep zich in een virtueel netwerk bevindt](./batch-virtual-network.md)
  - Een subnet van een virtueel netwerk moet voldoende niet-toegewezen IP-adressen hebben om aan elk aangevraagde groeps knooppunt toe te wijzen. Anders kunnen de knoop punten niet worden gemaakt.
- Onvoldoende resources wanneer een [groep zich in een virtueel netwerk bevindt](./batch-virtual-network.md)
  - U kunt resources zoals load-balancers, open bare Ip's en netwerk beveiligings groepen maken in hetzelfde abonnement als het batch-account. Controleer of de abonnements quota's voldoende zijn voor deze resources.
- Grote Pools met aangepaste VM-installatie kopieën
  - Grote Pools die gebruikmaken van aangepaste VM-installatie kopieën, kunnen langer duren en kunnen worden gewijzigd.  Zie [een groep maken met de galerie met gedeelde afbeeldingen](batch-sig-images.md) voor aanbevelingen over limieten en configuratie.

### <a name="automatic-scaling-failures"></a>Fouten bij automatisch schalen

U kunt Azure Batch zo instellen dat het aantal knoop punten in een pool automatisch wordt geschaald. U definieert de para meters voor de [formule voor automatisch schalen van een groep](./batch-automatic-scaling.md). De batch-service gebruikt de formule vervolgens om periodiek het aantal knoop punten in de pool te evalueren en een nieuw doel nummer in te stellen.

De volgende soorten problemen kunnen optreden bij het gebruik van automatisch schalen:

- De evaluatie van automatisch schalen mislukt.
- De resulterende grootte kan niet worden gewijzigd en er wordt een time-out opgetreden.
- Er is een probleem met de automatische schaal formule die leidt tot onjuiste knooppunt doel waarden. Het formaat kan worden gewijzigd of een time-out optreedt.

Gebruik de eigenschap [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun) om informatie te krijgen over de laatste automatische schaal aanpassing. Deze eigenschap rapporteert de evaluatie tijd, de waarden en het resultaat en prestatie fouten.

Met de gebeurtenis voor het [volt ooien van de groeps grootte](./batch-pool-resize-complete-event.md) worden gegevens over alle evaluaties vastgelegd.

### <a name="pool-deletion-failures"></a>Verwijderings fouten van groep

Wanneer u een pool verwijdert die knoop punten bevat, worden de knoop punten door de eerste batch verwijderd. Dit kan enkele minuten in beslag nemen. Daarna verwijdert batch het groeps object zelf.

Batch stelt de [groeps status](/rest/api/batchservice/pool/get#poolstate) in die tijdens het verwijderings proces moet worden **verwijderd** . De aanroepende toepassing kan detecteren of het verwijderen van de groep te lang duurt door de eigenschappen **status** en **stateTransitionTime** te gebruiken.

## <a name="node-errors"></a>Knooppunt fouten

Zelfs wanneer de batch knoop punten in een groep heeft toegewezen, kunnen verschillende problemen ertoe leiden dat sommige van de knoop punten niet meer in orde zijn en taken niet kunnen uitvoeren. Voor deze knoop punten zijn nog steeds kosten in rekening gebracht. het is dus belang rijk om problemen op te sporen om te voor komen dat er geen knoop punten kunnen worden gebruikt. Naast algemene knooppunt fouten is de huidige [taak status](/rest/api/batchservice/job/get#jobstate) nuttig voor het oplossen van problemen.

### <a name="start-task-failures"></a>Fouten bij starten van taken

U kunt desgewenst een optionele [begin taak](/rest/api/batchservice/pool/add#starttask) voor een groep opgeven. Net als bij elke taak kunt u een opdracht regel en bron bestanden gebruiken om te downloaden uit de opslag. De begin taak wordt uitgevoerd voor elk knoop punt nadat dit is gestart. De eigenschap **waitForSuccess** geeft aan of batch wacht totdat de begin taak is voltooid voordat taken worden gepland in een knoop punt.

Wat gebeurt er als u het knoop punt hebt geconfigureerd om te wachten op voltooiing van de voltooide begin taak, maar de begin taak mislukt? In dat geval kan het knoop punt niet worden gebruikt, maar worden er nog steeds kosten in rekening gebracht.

U kunt problemen met de begin taak detecteren met behulp van de eigenschappen [Result](/rest/api/batchservice/computenode/get#taskexecutionresult) en  [FailureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) van de [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) node-eigenschap op het hoogste niveau.

Een mislukte begin taak zorgt er ook voor dat de [status](/rest/api/batchservice/computenode/get#computenodestate) van het knoop punt door batch wordt ingesteld op **starttaskfailed** als  **waitForSuccess** is ingesteld op **True**.

Net als bij elke taak kunnen er veel oorzaken zijn voor het mislukken van een begin taak. Als u problemen wilt oplossen, controleert u de stdout, stderr en eventuele verdere taak-specifieke logboek bestanden.

Start taken moeten worden herhaald, omdat het mogelijk is dat de begin taak meerdere keren op hetzelfde knoop punt wordt uitgevoerd. de begin taak wordt uitgevoerd wanneer een knoop punt wordt geimageeerd of opnieuw wordt opgestart. In zeldzame gevallen wordt een begin taak uitgevoerd nadat een gebeurtenis heeft geleid tot het opnieuw opstarten van een knoop punt, waarbij een van de besturings systemen of tijdelijke schijven werd geimageeerd terwijl het andere niet was. Omdat batch-Start taken (zoals alle batch taken) vanaf de tijdelijke schijf worden uitgevoerd, is dit doorgaans geen probleem, maar in sommige gevallen waarbij de start taak een toepassing installeert op de besturingssysteem schijf en andere gegevens op de tijdelijke schijf bewaart, kunnen er problemen ontstaan omdat er geen synchronisaties zijn. Bescherm uw toepassing dienovereenkomstig als u beide schijven gebruikt.

### <a name="application-package-download-failure"></a>Fout bij het downloaden van het toepassings pakket

U kunt een of meer toepassings pakketten voor een groep opgeven. Batch downloadt de opgegeven pakket bestanden naar elk knoop punt en decomprimeert de bestanden nadat het knoop punt is gestart, maar voordat taken worden gepland. Het is gebruikelijk om een opdracht regel voor starten van de taak te gebruiken in combi natie met toepassings pakketten. Bijvoorbeeld om bestanden te kopiëren naar een andere locatie of om Setup uit te voeren.

De eigenschap knooppunt [fouten](/rest/api/batchservice/computenode/get#computenodeerror) rapporteert een fout bij het downloaden en het ongedaan maken van de compressie van een toepassings pakket; de status van het knoop punt is ingesteld op **onbruikbaar**.

### <a name="container-download-failure"></a>Fout bij downloaden van container

U kunt een of meer container verwijzingen opgeven voor een groep. Batch downloadt de opgegeven containers naar elk knoop punt. De eigenschap knooppunt [fouten](/rest/api/batchservice/computenode/get#computenodeerror) rapporteert een fout bij het downloaden van een container en stelt de status van het knoop punt in op **onbruikbaar**.

### <a name="node-os-updates"></a>Updates van het knoop punt-besturings systeem

Voor Windows-groepen `enableAutomaticUpdates` is standaard ingesteld op `true` . Het toestaan van automatische updates wordt aanbevolen, maar ze kunnen de taak voortgang onderbreken, met name als de taken langlopend zijn. U kunt deze waarde instellen op `false` Als u er zeker van wilt zijn dat een update van het besturings systeem onverwacht niet wordt uitgevoerd.

### <a name="node-in-unusable-state"></a>Het knoop punt kan niet worden gebruikt

Azure Batch kan de status van het [knoop punt](/rest/api/batchservice/computenode/get#computenodestate) om een groot aantal redenen worden ingesteld op **onbruikbaar** . Als de status van het knoop punt is ingesteld op **onbruikbaar**, kunnen taken niet worden gepland voor het knoop punt, maar worden er nog steeds kosten in rekening gebracht.

Knoop punten in een niet- **bruikbare** status, maar zonder [fouten](/rest/api/batchservice/computenode/get#computenodeerror) betekent dat de batch niet kan communiceren met de virtuele machine. In dit geval probeert batch altijd de virtuele machine te herstellen. Er wordt niet automatisch geprobeerd om Vm's te herstellen waarvoor geen toepassings pakketten of containers konden worden geïnstalleerd, ook al is de status **onbruikbaar**.

Als batch de oorzaak kan bepalen, wordt deze door de eigenschap voor knooppunt [fouten](/rest/api/batchservice/computenode/get#computenodeerror) gerapporteerd.

Aanvullende voor beelden van oorzaken voor niet- **bruikbare** knoop punten zijn:

- Een aangepaste VM-installatie kopie is ongeldig. Bijvoorbeeld een installatie kopie die niet goed is voor bereid.

- Een virtuele machine wordt verplaatst vanwege een infrastructuur fout of een upgrade op laag niveau. Batch herstelt het knoop punt.

- Er is een VM-installatie kopie geïmplementeerd op hardware die deze niet ondersteunt. U kunt bijvoorbeeld proberen om een CentOS HPC-installatie kopie uit te voeren op een [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM.

- De virtuele machines bevinden zich in een [virtueel Azure-netwerk](batch-virtual-network.md)en het verkeer is geblokkeerd voor de sleutel poorten.

- De virtuele machines bevinden zich in een virtueel netwerk, maar uitgaand verkeer naar Azure Storage wordt geblokkeerd.

- De virtuele machines bevinden zich in een virtueel netwerk met een DNS-configuratie van de klant en de DNS-server kan Azure Storage niet omzetten.

### <a name="node-agent-log-files"></a>Logboek bestanden van de knooppunt agent

Het batch agent-proces dat wordt uitgevoerd op elk pool knooppunt, kan logboek bestanden bieden die handig kunnen zijn als u contact moet opnemen met de ondersteuning van een probleem met een groeps knooppunt. Logboek bestanden voor een knoop punt kunnen worden geüpload via de Azure Portal, Batch Explorer of een [API](/rest/api/batchservice/computenode/uploadbatchservicelogs). Het is handig om de logboek bestanden te uploaden en op te slaan. Daarna kunt u het knoop punt of de groep verwijderen om de kosten van de actieve knoop punten op te slaan.

### <a name="node-disk-full"></a>De schijf van het knoop punt is vol

Het tijdelijke station voor een groeps knooppunt-VM wordt gebruikt door batch voor taak bestanden, taak bestanden en gedeelde bestanden, zoals de volgende:

- Bestanden van toepassings pakketten
- Resource bestanden van taak
- Toepassingsspecifieke bestanden die worden gedownload naar een van de batch-mappen
- Stdout-en stderr-bestanden voor elke uitvoering van een taak toepassing
- Toepassingsspecifieke uitvoer bestanden

Sommige van deze bestanden worden slechts eenmaal geschreven wanneer groeps knooppunten worden gemaakt, zoals groeps toepassings pakketten of resource bestanden voor het starten van de groep. Zelfs als er één keer wordt geschreven wanneer het knoop punt wordt gemaakt, kan het tijdelijke station worden gevuld als deze bestanden te groot zijn.

Andere bestanden worden geschreven voor elke taak die wordt uitgevoerd op een knoop punt, zoals stdout en stderr. Als een groot aantal taken wordt uitgevoerd op hetzelfde knoop punt en/of de taak bestanden te groot zijn, kunnen ze het tijdelijke station vullen.

De grootte van het tijdelijke station is afhankelijk van de grootte van de virtuele machine. Een overweging bij het kiezen van een VM-grootte is om ervoor te zorgen dat de tijdelijke schijf voldoende ruimte heeft.

- In de Azure Portal wanneer u een groep toevoegt, kan de volledige lijst met VM-grootten worden weer gegeven en de kolom ' bron schijf grootte '.
- De artikelen waarin alle VM-grootten worden beschreven, hebben tabellen met de kolom Temp Storage. voor beeld van [geoptimaliseerde VM-grootten](../virtual-machines/sizes-compute.md)

Voor bestanden die door elke taak zijn geschreven, kan een Bewaar periode worden opgegeven voor elke taak die bepaalt hoe lang de taak bestanden worden bewaard voordat ze automatisch worden opgeruimd. De retentie tijd kan worden gereduceerd om de opslag vereisten te verlagen.

Als de tijdelijke schijf bijna geen ruimte meer heeft (of bijna helemaal geen ruimte meer heeft), wordt de status van het knoop punt [onbruikbaar](/rest/api/batchservice/computenode/get#computenodestate) gemaakt en wordt er een knooppunt fout gerapporteerd met de melding dat de schijf vol is.

Als u niet zeker weet wat er ruimte is op het knoop punt, kunt u externe toegang tot het knoop punt proberen en hand matig onderzoeken waar de ruimte is verdwenen. U kunt ook de [API voor batch-lijst bestanden](/rest/api/batchservice/file/listfromcomputenode) gebruiken om bestanden in met batch beheerde mappen te onderzoeken (bijvoorbeeld taak uitvoer). Houd er rekening mee dat met deze API alleen bestanden in de door batch beheerde directory's worden weer gegeven. Als uw taken elders bestanden hebben gemaakt, worden ze niet weer geven.

Zorg ervoor dat alle gegevens die u nodig hebt, zijn opgehaald uit het knoop punt of naar een duurzame opslag zijn geüpload en verwijder vervolgens de gegevens naar behoefte om ruimte vrij te maken.

U kunt oude voltooide taken of oude voltooide taken verwijderen waarvan de taak gegevens zich nog op de knoop punten bevindt. Zoek in de [verzameling RecentTasks](/rest/api/batchservice/computenode/get#taskinformation) op het knoop punt of op de [bestanden in het knoop punt](/rest/api/batchservice/file/listfromcomputenode). Als u een taak verwijdert, worden alle taken in de taak verwijderd. Als u de taken in de taak verwijdert, worden de gegevens in de taak mappen op het knoop punt dat moet worden verwijderd geactiveerd en wordt er ruimte vrijgemaakt. Zodra u voldoende ruimte hebt vrijgemaakt, start u het knoop punt opnieuw op en moet u de status ' onbruikbaar ' verplaatsen en opnieuw instellen op ' inactief '.

Als u een niet-bruikbaar knoop punt in [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) -groepen wilt herstellen, kunt u een knoop punt uit de pool verwijderen met de [API knoop punten verwijderen](/rest/api/batchservice/pool/removenodes). Vervolgens kunt u de pool opnieuw verg Roten om het ongeldige knoop punt te vervangen door een nieuwe. Voor [CloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) -groepen kunt u het knoop punt opnieuw instellen met behulp van de API voor het opnieuw uitvoeren van een [batch](/rest/api/batchservice/computenode/reimage). Hiermee wordt de hele schijf opgeruimd. Opnieuw afbeelding wordt momenteel niet ondersteund voor [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) -groepen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [fout controle van taken en taken](batch-job-task-error-checking.md).
- Meer informatie over [Aanbevolen procedures](best-practices.md) voor het werken met Azure batch.
