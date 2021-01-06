---
title: Aanbevolen procedures
description: Leer de aanbevolen procedures en handige tips voor het ontwikkelen van uw Azure Batch oplossingen.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 7e2a49c8307af89fb3898f5f2513fb493d0f5d90
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934285"
---
# <a name="azure-batch-best-practices"></a>Aanbevolen procedures Azure Batch

In dit artikel wordt een verzameling aanbevolen procedures en handige tips beschreven voor het effectief gebruik van de Azure Batch-service, op basis van de praktijk ervaring met batch. Deze tips kunnen u helpen bij het verbeteren van de prestaties en Vermijd het ontwerpen van Valk uilen in uw Azure Batch oplossingen.

> [!TIP]
> Zie voor meer informatie over beveiliging in Azure Batch de [Aanbevolen procedures voor batch beveiliging en naleving](security-best-practices.md).

## <a name="pools"></a>Pools

[Pools](nodes-and-pools.md#pools) zijn de reken resources voor het uitvoeren van taken voor de batch-service. De volgende secties bevatten aanbevelingen voor het werken met batch-Pools.

### <a name="pool-configuration-and-naming"></a>Groeps configuratie en-naamgeving

- **Pool toewijzings modus** Bij het maken van een batch-account kunt u kiezen uit twee pool toewijzings modi: **batch-service** of **gebruikers abonnement**. In de meeste gevallen moet u de standaard batch-service modus gebruiken, waarin Pools achter de schermen worden toegewezen in door batch beheerde abonnementen. In de alternatieve modus Gebruikersabonnement worden Batch-VM's en andere resources rechtstreeks in uw abonnement gemaakt wanneer er een groep wordt gemaakt. Abonnements accounts voor gebruikers worden voornamelijk gebruikt om een belang rijke, maar kleine subset van scenario's mogelijk te maken. Meer informatie over de modus gebruikers abonnement vindt u op [aanvullende configuratie voor de modus gebruikers abonnement](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **' cloudServiceConfiguration ' of ' virtualMachineConfiguration '.**
    ' virtualMachineConfiguration ' moet worden gebruikt. Alle batch-functies worden ondersteund door groepen van ' virtualMachineConfiguration '. Niet alle functies worden ondersteund voor cloudServiceConfiguration-groepen en er worden geen nieuwe mogelijkheden gepland.

- **Denk na over taak-en taak uitvoerings tijd bij het bepalen van de taak voor pool toewijzing.**
    Als u taken hebt die voornamelijk uit korte uitvoeringen bestaan, en het verwachte totale aantal taken klein is, zodat de totale verwachte uitvoerings tijd van de taak niet lang is, kunt u geen nieuwe groep voor elke taak toewijzen. De toewijzings tijd van de knoop punten vermindert de uitvoerings tijd van de taak.

- **Pools moeten meer dan één reken knooppunt hebben.**
    Afzonderlijke knoop punten zijn niet gegarandeerd altijd beschikbaar. Hoewel ongebruikelijk, hardwarestoringen, updates van het besturings systeem en een andere fout optreden, kunnen afzonderlijke knoop punten offline zijn. Als uw batch-workload deterministisch, gegarandeerde voortgang vereist, moet u Pools met meerdere knoop punten toewijzen.

- **Resource namen niet opnieuw gebruiken.**
    Batch-resources (taken, Pools, enz.) zijn vaak beschikbaar en zijn in de loop van de tijd. U kunt bijvoorbeeld een pool maken op maandag, deze verwijderen op dinsdag en vervolgens een andere pool maken op donderdag. Elke nieuwe resource die u maakt, moet een unieke naam hebben die u nog niet eerder hebt gebruikt. Dit kan worden gedaan met behulp van een GUID (als de volledige resource naam of als onderdeel ervan) of het insluiten van de tijd waarin de resource is gemaakt in de resource naam. Batch ondersteunt [DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname), die kan worden gebruikt om een resource een door de mens lees bare naam te geven, zelfs als de werkelijke resource-id iets anders is dan dat van mensen vriendelijke. Door gebruik te maken van unieke namen is het gemakkelijker om te onderscheiden welke resource iets in Logboeken en metrische gegevens heeft gedaan. Er wordt ook dubbel zinnigheid verwijderd als u ooit een ondersteunings aanvraag voor een resource moet opslaan.

- **Continuïteit tijdens het onderhoud van de groep en de fout.**
    Het is raadzaam om uw taken dynamisch gebruik van Pools te laten doen. Als uw taken dezelfde pool gebruiken voor alles, is er een kans dat uw taken niet worden uitgevoerd als er iets mis gaat met de groep. Dit is vooral belang rijk voor tijd gevoelige workloads. Om dit probleem op te lossen, selecteert of maakt u een pool dynamisch wanneer u een taak plant of een manier om de naam van de groep te overschrijven zodat u een beschadigde pool kunt overs Laan.

- **Bedrijfs continuïteit tijdens het onderhoud van Pools en storingen** Er zijn veel mogelijke oorzaken waardoor een pool niet groter kan worden dan de vereiste grootte die u wenst, zoals interne fouten, capaciteits beperkingen, enzovoort. Daarom moet u de taken in een andere groep kunnen richten (mogelijk met een andere VM-grootte-batch ondersteunt deze via [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)), indien nodig. Vermijd het gebruik van een statische groeps-ID met de verwachting dat deze nooit wordt verwijderd en nooit wordt gewijzigd.

### <a name="pool-lifetime-and-billing"></a>Levens duur van groep en facturering

De levens duur van de groep kan variëren, afhankelijk van de toewijzings methode en opties die worden toegepast op de groeps configuratie. Pools kunnen op elk moment een wille keurige levens duur hebben en een verschillend aantal reken knooppunten in de pool. Het is uw verantwoordelijkheid om de reken knooppunten in de groep expliciet te beheren of door de functies van de service ([automatisch schalen](nodes-and-pools.md#automatic-scaling-policy) of [autogroepen](nodes-and-pools.md#autopools)).

- **Bewaar Pools nieuw.**
    Wijzig de grootte van uw Pools in nul om de paar maanden om ervoor te zorgen dat u de [nieuwste updates voor de knooppunt agent en](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md)oplossingen krijgt. Uw pool ontvangt geen updates voor de knooppunt agent, tenzij deze opnieuw worden gemaakt of het formaat van 0 reken knooppunten wordt gewijzigd. Voordat u de groep opnieuw maakt of het formaat ervan wijzigt, is het raadzaam om de logboeken van de-agent te downloaden voor fout opsporing, zoals beschreven in de sectie [knoop punten](#nodes) .

- **Groep opnieuw maken** Het wordt afgeraden om uw Pools dagelijks te verwijderen en opnieuw te maken op een vergelijk bare opmerking. Maak in plaats daarvan een nieuwe pool, werk uw bestaande taken bij zodat deze naar de nieuwe groep wijzen. Wanneer alle taken zijn verplaatst naar de nieuwe groep, verwijdert u de oude groep.

- **Efficiëntie en facturering van groep** Batch zelf maakt geen extra kosten in rekening, maar u kunt wel kosten betalen voor de gebruikte reken resources. U wordt gefactureerd voor elk reken knooppunt in de pool, onafhankelijk van de status waarin het knoop punt zich bevindt. Dit omvat alle kosten die nodig zijn om het knoop punt uit te voeren, zoals opslag-en netwerk kosten. Zie [kosten analyse en budgetten voor Azure batch voor](budget.md)meer informatie over best practices.

### <a name="pool-allocation-failures"></a>Fouten bij pool toewijzing

Groeps toewijzings fouten kunnen zich voordoen op elk moment tijdens de eerste toewijzing of de volgende grootte. Dit kan worden veroorzaakt door tijdelijke capaciteits uitputting in een regio of fouten in andere Azure-Services waarop batch van afhankelijk is. Uw kern quotum is geen garantie, maar een limiet.

### <a name="unplanned-downtime"></a>Niet-geplande uitvaltijd

Het is mogelijk dat batch-Pools downtime-gebeurtenissen in azure kunnen ervaren. Houd dit in acht wanneer u uw scenario of werk stroom voor batch plant en ontwikkelt.

In het geval dat een knoop punt uitvalt, probeert batch automatisch deze reken knooppunten te herstellen namens u. Dit kan leiden tot het opnieuw plannen van elke actieve taak op het knoop punt dat wordt hersteld. Zie [ontwerpen voor nieuwe pogingen voor](#design-for-retries-and-re-execution) meer informatie over onderbroken taken.

### <a name="custom-image-pools"></a>Aangepaste installatie kopie groepen

Wanneer u een Azure Batch groep maakt met behulp van de configuratie van de virtuele machine, geeft u een VM-installatie kopie op die het besturings systeem levert voor elk reken knooppunt in de pool. U kunt de pool maken met een ondersteunde Azure Marketplace-installatie kopie, of u kunt [een aangepaste installatie kopie maken met een afbeelding van de galerie met gedeelde installatie kopieën](batch-sig-images.md). Hoewel u ook een [beheerde installatie kopie](batch-custom-images.md) kunt gebruiken om een aangepaste installatie kopie groep te maken, is het raadzaam om, indien mogelijk, aangepaste installatie kopieën te maken met behulp van de galerie met gedeelde afbeeldingen. Met de galerie gedeelde afbeeldingen kunt u Pools sneller inrichten, grotere aantallen Vm's schalen en de betrouw baarheid verbeteren bij het inrichten van Vm's.

### <a name="third-party-images"></a>Installatie kopieën van derden

Pools kunnen worden gemaakt met installatie kopieën van derden die naar Azure Marketplace worden gepubliceerd. In het geval van een batch-account in de gebruikers abonnements modus ziet u mogelijk de fout ' toewijzing is mislukt vanwege een Marketplace-aankoop geschiktheids controle ' bij het maken van een groep met bepaalde installatie kopieën van derden. Ga akkoord met de voor waarden die zijn ingesteld door de uitgever van de installatie kopie om deze fout op te lossen. U kunt dit doen met behulp van [Azure PowerShell](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) of [Azure cli](/cli/azure/vm/image/terms).

### <a name="azure-region-dependency"></a>Azure-regio afhankelijkheid

Als u een tijd gevoelige of productie werk belasting hebt, is het raadzaam om niet afhankelijk te zijn van één Azure-regio. Soms zijn er problemen die invloed kunnen hebben op een hele regio. Als uw verwerking bijvoorbeeld op een specifiek tijdstip moet worden gestart, kunt u overwegen om de groep in uw primaire regio goed te schalen voor *de start tijd*. Als deze pool schaal mislukt, kunt u terugvallen om een pool omhoog te schalen in een back-upgebied (of regio's). Pools over meerdere accounts in verschillende regio's bieden een kant-en-klare back-up als er iets mis is met een andere groep. Zie [uw toepassing ontwerpen voor hoge Beschik baarheid](high-availability-disaster-recovery.md)voor meer informatie.

## <a name="jobs"></a>Taken

Een [taak](jobs-and-tasks.md#jobs) is een container die is ontworpen om honderden, duizenden of zelfs miljoenen taken te bevatten. Volg deze richt lijnen bij het maken van taken.

### <a name="fewer-jobs-more-tasks"></a>Minder taken, meer taken

Het gebruik van een taak voor het uitvoeren van één taak is inefficiënt. Het is bijvoorbeeld efficiënter om één taak te gebruiken met 1000 taken in plaats van dat u 100 taken maakt die elk 10 taken bevatten. Het uitvoeren van 1000-taken, elk met één taak, is de minst efficiënte, langzaamste en meest dure aanpak.

Zorg er daarom voor dat u niet een batch-oplossing ontwerpt waarvoor duizenden gelijktijdig actieve taken vereist zijn. Er zijn geen quota voor taken, dus het uitvoeren van veel taken onder zo weinig mogelijk taken maakt gebruik van uw [taak-en taak schema quota's](batch-quota-limit.md#resource-quotas).

### <a name="job-lifetime"></a>Taak levensduur

Een batch-taak heeft een onbeperkte levens duur tot deze uit het systeem is verwijderd. De status geeft aan of er meer taken kunnen worden geaccepteerd voor de planning of niet.

Een taak wordt niet automatisch verplaatst naar de status voltooid, tenzij deze expliciet is beëindigd. Dit kan automatisch worden geactiveerd via de eigenschap [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) of [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints).

Er is een standaard quotum voor taak- [en taak planning](batch-quota-limit.md#resource-quotas). Taken en taak planningen in de status voltooid tellen niet mee voor dit quotum.

## <a name="tasks"></a>Taken

[Taken](jobs-and-tasks.md#tasks) zijn afzonderlijke werk eenheden waaruit een taak bestaat. Taken worden door de gebruiker verzonden en gepland door batch op reken knooppunten. Er zijn verschillende ontwerp overwegingen voor het maken en uitvoeren van taken. In de volgende secties worden veelvoorkomende scenario's beschreven en wordt uitgelegd hoe u taken kunt ontwerpen om problemen op te lossen en efficiënt uit te voeren.

### <a name="save-task-data"></a>Taak gegevens opslaan

Reken knooppunten zijn van nature. Er zijn veel functies in batch, zoals automatisch [groeperen](nodes-and-pools.md#autopools) en [automatisch schalen](nodes-and-pools.md#automatic-scaling-policy) , waarmee knoop punten eenvoudig kunnen worden verwijderd. Wanneer knoop punten een pool verlaten (vanwege het wijzigen van de grootte of het verwijderen van een pool), worden alle bestanden op deze knoop punten ook verwijderd. Als gevolg hiervan moet een taak de uitvoer van het knoop punt waarop het wordt uitgevoerd, verplaatsen naar een duurzame Store voordat deze wordt voltooid. Als een taak mislukt, moeten de logboeken worden verplaatst die nodig zijn om de fout in een duurzame opslag te diagnosticeren.

Batch heeft geïntegreerde ondersteunings Azure Storage voor het uploaden van gegevens via [OutputFiles](batch-task-output-files.md), evenals een groot aantal gedeelde bestands systemen, of u kunt de upload zelf in uw taken uitvoeren.

### <a name="manage-task-lifetime"></a>Levens duur van taken beheren

Verwijder taken wanneer ze niet meer nodig zijn of stel een beperking voor een [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) -taak in. Als er een `retentionTime` is ingesteld, wordt door batch automatisch de schijf ruimte opgeschoond die door de taak wordt gebruikt wanneer de `retentionTime` verloopt.

Als u taken verwijdert, worden twee dingen uitgevoerd. Het zorgt ervoor dat u niet beschikt over een opgebouwde taak in de taak, waardoor het lastig is om een query uit te voeren/te zoeken naar de taken die u wilt uitvoeren (omdat u de voltooide taken moet filteren). Ook worden de bijbehorende taak gegevens op het knoop punt opgeschoond (de waarde `retentionTime` is nog niet al bereikt). Dit helpt ervoor te zorgen dat uw knoop punten niet worden gevuld met taak gegevens en dat er geen schijf ruimte meer beschikbaar is.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Een groot aantal taken in de verzameling verzenden

Taken kunnen worden verzonden op individuele basis of in verzamelingen. Verzend taken in [verzamelingen](/rest/api/batchservice/task/addcollection) van maxi maal 100 tegelijk bij het bulksgewijs verzenden van taken om de overhead-en inzendings tijd te verminderen.

### <a name="set-max-tasks-per-node-appropriately"></a>Het juiste aantal taken per knoop punt instellen

Batch ondersteunt het overabonneren van taken op knoop punten (het uitvoeren van meer taken dan een knoop punt heeft kern geheugens). Het is aan u om ervoor te zorgen dat de taken in de knoop punten in uw pool passen. U kunt bijvoorbeeld een gedegradeerde ervaring hebben als u probeert acht taken te plannen die elk een CPU-gebruik van 25% op één knoop punt (in een groep met) verbruikt `taskSlotsPerNode = 8` .

### <a name="design-for-retries-and-re-execution"></a>Ontwerpen voor nieuwe pogingen en opnieuw uitvoeren

Taken kunnen automatisch opnieuw worden uitgevoerd op batch. Er zijn twee soorten nieuwe pogingen: door de gebruiker beheerd en intern. Door de gebruiker beheerde nieuwe pogingen worden opgegeven door de [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)van de taak. Wanneer een programma dat is opgegeven in de taak wordt afgesloten met een afsluit code die niet gelijk is aan nul, wordt de taak opnieuw uitgevoerd tot de waarde van `maxTaskRetryCount` .

Hoewel zeldzame gevallen kan een taak intern opnieuw worden geprobeerd vanwege storingen op het reken knooppunt, zoals het niet mogelijk is interne status of een fout op het knoop punt bij te werken terwijl de taak wordt uitgevoerd. De taak wordt indien mogelijk opnieuw geprobeerd op hetzelfde reken knooppunt, tot een interne limiet voordat de taak wordt uitgevoerd en de taak moet worden uitgesteld op basis van een batch, mogelijk op een ander reken knooppunt.

Er zijn geen ontwerp verschillen bij het uitvoeren van uw taken op knoop punten met specifieke of lage prioriteit. Of een taak al dan niet wordt uitgevoerd op een knoop punt met lage prioriteit of wordt onderbroken als gevolg van een storing op een toegewezen knoop punt, maar beide situaties worden mogelijk verholpen door de taak te ontwerpen en de fout te achterhalen.

### <a name="build-durable-tasks"></a>Duurzame taken bouwen

Taken moeten zodanig zijn ontworpen dat ze zich kunnen voordoen en het opnieuw proberen. Dit is vooral belang rijk voor langlopende taken. Als u dit wilt doen, moet u ervoor zorgen dat taken hetzelfde resultaat genereren, zelfs als ze meer dan één keer worden uitgevoerd. Een manier om dit te doen is het maken van de taken ' Doelzoeken '. Een andere manier is om ervoor te zorgen dat uw taken idempotent zijn (taken hebben hetzelfde resultaat, ongeacht het aantal keren dat ze worden uitgevoerd).

Een veelvoorkomend voor beeld is een taak voor het kopiëren van bestanden naar een reken knooppunt. Een eenvoudige benadering is een taak waarmee alle opgegeven bestanden elke keer dat deze wordt uitgevoerd, wordt gekopieerd, wat inefficiënt is en niet is opgebouwd om de fout op te vangen. Maak in plaats daarvan een taak om ervoor te zorgen dat de bestanden zich op het reken knooppunt bevinden. een taak waarmee bestanden die al aanwezig zijn, niet opnieuw worden gekopieerd. Op deze manier wordt de taak opgehaald waar deze wordt afgebroken als deze is onderbroken.

### <a name="avoid-short-execution-time"></a>Korte uitvoerings tijd voor komen

Taken die slechts een tot twee seconden worden uitgevoerd, zijn niet ideaal. Probeer een aanzienlijke hoeveelheid werk uit te voeren in een afzonderlijke taak (mini maal 10 seconden, Maxi maal uur of dagen). Als elke taak gedurende één minuut (of meer) wordt uitgevoerd, is de plannings overhead als Fractie van de totale reken tijd klein.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Groeps bereik voor korte taken op Windows-knoop punten gebruiken

Bij het plannen van een taak op batch knooppunten kunt u kiezen of u deze wilt uitvoeren met het taak bereik of groeps bereik. Als de taak alleen voor een korte tijd wordt uitgevoerd, kan het taak bereik inefficiënt zijn als gevolg van de resources die nodig zijn voor het maken van het account voor automatische gebruikers voor die taak. Voor een grotere efficiëntie kunt u deze taken instellen op groeps bereik. Zie [een taak uitvoeren als een automatische gebruiker met een groeps bereik](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope)voor meer informatie.

## <a name="nodes"></a>Knooppunten

Een [reken knooppunt](nodes-and-pools.md#nodes) is een virtuele machine (VM) of Cloud service-VM van Azure die is toegewezen aan het verwerken van een deel van de werk belasting van uw toepassing. Volg deze richt lijnen bij het werken met knoop punten.

### <a name="idempotent-start-tasks"></a>Idempotent Start taken

Net als bij andere taken moet de [begin taak](jobs-and-tasks.md#start-task) van het knoop punt idempotent zijn, omdat deze elke keer dat het knoop punt wordt opgestart opnieuw wordt uitgevoerd. Een idempotent-taak is slechts een, wat een consistent resultaat oplevert wanneer er meerdere keren worden uitgevoerd.

### <a name="isolated-nodes"></a>Geïsoleerde knoop punten

Overweeg het gebruik van geïsoleerde VM-grootten voor workloads met nalevings-of regelgevings vereisten. Ondersteunde, geïsoleerde grootten in de configuratie van de virtuele machine zijn onder andere,,,, `Standard_E80ids_v4` `Standard_M128ms` `Standard_F72s_v2` `Standard_G5` `Standard_GS5` en `Standard_E64i_v3` . Zie [Virtual machine-isolatie in azure](https://docs.microsoft.com/azure/virtual-machines/isolation)voor meer informatie over geïsoleerde VM-grootten.

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>Langlopende Services beheren via de interface van het besturings systeem-services

Soms moet er een andere agent naast de batch-agent in het knoop punt worden uitgevoerd. U kunt bijvoorbeeld gegevens verzamelen van het knoop punt en dit rapport rapporteren. U wordt aangeraden deze agents als OS-Services te implementeren, zoals een Windows-service of een Linux- `systemd` service.

Bij het uitvoeren van deze services mogen ze geen bestands vergrendeling hebben op bestanden in door batch beheerde directory's op het knoop punt, omdat in andere gevallen deze directory's niet kunnen worden verwijderd vanwege de vergren deling van het bestand. Als u bijvoorbeeld een Windows-service in een begin taak installeert in plaats van de service rechtstreeks vanuit de werkmap voor het starten van de taak te starten, kopieert u de bestanden ergens anders (of als de bestanden zich alleen over de kopie bevinden). Installeer de service vervolgens vanaf die locatie. Wanneer uw start taak door batch opnieuw wordt uitgevoerd, wordt de werkmap voor het starten van de taak verwijderd en opnieuw gemaakt. Dit werkt omdat de service Bestands vergrendelingen heeft op de andere map, niet op de werkmap voor het starten van de taak.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Vermijd het maken van adreslijst koppelingen in Windows

Directory-koppelingen, ook wel vaste koppelingen naar mappen genoemd, zijn moeilijk te verwerken tijdens het opschonen van taken en taken. Gebruik symlinks (soft-koppelingen) in plaats van vaste koppelingen.

### <a name="collect-the-batch-agent-logs"></a>De batch Agent-logboeken verzamelen

Als u een probleem ondervindt met betrekking tot het gedrag van een knoop punt of taken die worden uitgevoerd op een knoop punt, verzamelt u de logboeken van de batch agent voordat u de desbetreffende knoop punten ongedaan maakt. U kunt de batch Agent-logboeken verzamelen met de API voor het uploaden van batch-service Logboeken. Deze logboeken kunnen worden verstrekt als onderdeel van een ondersteunings ticket voor micro soft en helpt u bij het oplossen van problemen en oplossingen.

### <a name="manage-os-upgrades"></a>Upgrades van besturings systemen beheren

Bij batch-accounts voor gebruikers abonnementen kan automatische besturingssysteem upgrades de taak voortgang onderbreken, met name als de taken langlopend zijn. Het [maken van idempotent-taken](#build-durable-tasks) kan helpen bij het verminderen van fouten die zijn veroorzaakt door deze onderbrekingen. U wordt ook aangeraden [upgrades van installatie kopieën van besturings systemen te plannen voor tijden waarop de taken niet naar verwachting worden uitgevoerd](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades).

## <a name="isolation-security"></a>Isolatie beveiliging

Als voor uw scenario het isoleren van taken van elkaar is vereist, moet u deze in afzonderlijke groepen laten staan. Een pool is de grens van beveiligings isolatie in batch, en twee groepen zijn standaard niet zichtbaar of kunnen met elkaar communiceren. Vermijd het gebruik van afzonderlijke batch-accounts als isolatie methode.

## <a name="moving-batch-accounts-across-regions"></a>Batch-accounts verplaatsen tussen regio's

Er zijn scenario's waarin het nuttig kan zijn om een bestaand batch-account van de ene regio naar een andere te verplaatsen. U kunt bijvoorbeeld overschakelen naar een andere regio als onderdeel van de planning voor nood herstel.

Azure Batch accounts kunnen niet rechtstreeks worden verplaatst van de ene regio naar een andere. U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie van uw batch-account te exporteren. U kunt de resource vervolgens in een andere regio zetten door het batch-account te exporteren naar een sjabloon, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjabloon te implementeren in de nieuwe regio.

Nadat u de sjabloon naar de nieuwe regio hebt geüpload, moet u certificaten, taak schema's en toepassings pakketten opnieuw maken. Als u de wijzigingen wilt door voeren en het batch-account wilt verplaatsen, moet u het oorspronkelijke batch-account of de resource groep verwijderen.

Voor meer informatie over Resource Manager en sjablonen raadpleegt [u Quick Start: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="connectivity"></a>Connectiviteit

Raadpleeg de volgende richt lijnen met betrekking tot de connectiviteit in uw batch-oplossingen.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Netwerk beveiligings groepen (Nsg's) en door de gebruiker gedefinieerde routes (Udr's)

Bij het inrichten van [batch-Pools in een virtueel netwerk](batch-virtual-network.md), moet u ervoor zorgen dat u voldoet aan de richt lijnen met betrekking tot het gebruik van de `BatchNodeManagement` service tags, poorten, protocollen en richting van de regel.
Het gebruik van het servicetag wordt sterk aanbevolen, in plaats van het gebruik van de onderliggende batch service-IP-adressen. Dit komt doordat de IP-adressen na verloop van tijd kunnen worden gewijzigd. Het rechtstreeks gebruiken van IP-adressen van batch Services kan leiden tot instabiliteit, onderbrekingen of storingen voor uw batch-Pools.

Voor door de gebruiker gedefinieerde routes (Udr's), moet u ervoor zorgen dat u over een proces beschikt om de batch service-IP-adressen periodiek bij te werken in uw route tabel, omdat deze adressen in de loop van de tijd veranderen. Zie [on-premises service Tags](../virtual-network/service-tags-overview.md)voor meer informatie over het verkrijgen van de lijst met IP-adressen van batch-service. De IP-adressen van de batch-service worden gekoppeld aan de servicetag `BatchNodeManagement` (of de regionale variant die overeenkomt met de regio van uw batch-account).

### <a name="honoring-dns"></a>DNS naleven

Zorg ervoor dat uw systemen DNS-time-to-Live (TTL) door lopen voor de URL van uw batch-account service. Bovendien moet u ervoor zorgen dat uw batch-service-clients en andere verbindings mechanismen voor de batch-service niet afhankelijk zijn van IP-adressen (of [een groep maken met statische open bare IP-adressen](create-pool-public-ip.md) , zoals hieronder wordt beschreven).

Als uw aanvragen een HTTP-reactie van het 5xx-niveau ontvangen en er in het antwoord een ' Connection: Close-header is, moet uw batch-serviceclient de aanbeveling volgen door de bestaande verbinding te sluiten, DNS opnieuw te verhelpen voor de service-URL van de batch-account en om een nieuwe verbinding te kunnen volgen.

### <a name="retry-requests-automatically"></a>Aanvragen automatisch opnieuw proberen

Zorg ervoor dat uw batch-service-clients over het juiste beleid voor nieuwe pogingen beschikken om uw aanvragen automatisch opnieuw uit te voeren, zelfs tijdens normale werking en niet alleen tijdens een service-onderhouds periode. Deze beleids regels voor opnieuw proberen moeten een interval van ten minste vijf minuten omvatten. Automatische mogelijkheden voor opnieuw proberen worden geboden bij verschillende batch-Sdk's, zoals de [.net RetryPolicyProvider-klasse](/dotnet/api/microsoft.azure.batch.retrypolicyprovider).

### <a name="static-public-ip-addresses"></a>Statische openbare IP-adressen

Doorgaans worden virtuele machines in een batch-pool geopend via open bare IP-adressen die kunnen worden gewijzigd gedurende de levens duur van de groep. Hierdoor kan het lastig zijn om te communiceren met een Data Base of een andere externe service die de toegang tot bepaalde IP-adressen beperkt. Om ervoor te zorgen dat de open bare IP-adressen in uw pool onverwacht niet worden gewijzigd, kunt u een groep maken met behulp van een set statische open bare IP-adressen die u beheert. Zie [een Azure batch groep met opgegeven open bare IP-adressen maken](create-pool-public-ip.md)voor meer informatie.

### <a name="testing-connectivity-with-cloud-services-configuration"></a>De connectiviteit met Cloud Services configuratie testen

U kunt het normale ' ping '/ICMP-protocol niet gebruiken met Cloud Services, omdat het ICMP-protocol niet is toegestaan via de Azure-load balancer. Zie [connectiviteit en netwerken voor Azure Cloud Services](../cloud-services/cloud-services-connectivity-and-networking-faq.md#can-i-ping-a-cloud-service)voor meer informatie.

## <a name="batch-node-underlying-dependencies"></a>Onderliggende afhankelijkheden van het batch knooppunt

Houd rekening met de volgende afhankelijkheden en beperkingen bij het ontwerpen van uw batch-oplossingen.

### <a name="system-created-resources"></a>Door het systeem gemaakte resources

Azure Batch maakt en beheert een set gebruikers en groepen op de VM, wat niet mag worden gewijzigd. De verschillen zijn als volgt:

Windows:

- Een gebruiker met de naam **PoolNonAdmin**
- Een gebruikers groep met de naam **WATaskCommon**

Linux:

- Een gebruiker met de naam **_azbatch**

### <a name="file-cleanup"></a>Bestanden opschonen

Batch probeert actief de werkmap op te schonen waarin de taken worden uitgevoerd, zodra de retentie tijd verloopt. Bestanden die buiten deze map zijn geschreven, zijn [uw verantwoordelijkheid om op te schonen](#manage-task-lifetime) om te voor komen dat u schijf ruimte kunt invullen.

De automatische opschoning voor de werkmap wordt geblokkeerd als u een service uitvoert in Windows vanuit de werkmap startTask, omdat de map nog in gebruik is. Dit leidt tot slechte prestaties. Om dit probleem op te lossen, wijzigt u de map voor die service in een afzonderlijke map die niet wordt beheerd door batch.

## <a name="next-steps"></a>Volgende stappen

- [Maak een Azure batch-account met behulp van de Azure Portal](batch-account-create-portal.md).
- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie over de [standaard Azure batch quota's, limieten en beperkingen en het aanvragen van quotum verhogingen](batch-quota-limit.md).
