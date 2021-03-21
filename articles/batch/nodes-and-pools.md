---
title: Knoop punten en Pools in Azure Batch
description: Meer informatie over reken knooppunten en Pools en hoe deze worden gebruikt in een Azure Batch werk stroom vanuit een ontwikkelings oogpunt.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 7d4c2d45849deb011498efe4c8a1ae91724b9acd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563892"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Knoop punten en Pools in Azure Batch

In een Azure Batch-werk stroom is een *reken knooppunt* (of een *knoop punt*) een virtuele machine die een deel van de werk belasting van uw toepassing verwerkt. Een *pool* is een verzameling van deze knoop punten waarop uw toepassing kan worden uitgevoerd. In dit artikel vindt u meer informatie over knoop punten en groepen, samen met overwegingen bij het maken en gebruiken van deze in een Azure Batch werk stroom.

## <a name="nodes"></a>Knooppunten

Een knoop punt is een virtuele machine (VM) of Cloud service-VM van Azure die is toegewezen aan het verwerken van een deel van de werk belasting van uw toepassing. De grootte van een knooppunt bepaalt het aantal CPU-kernen, de geheugencapaciteit en de grootte van het lokale bestandssysteem die aan het knooppunt worden toegewezen.

U kunt pools van Windows- of Linux-knooppunten maken met behulp van Azure Cloud Services, [Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)-installatiekopieën of aangepaste installatiekopieën die u voorbereidt.

Knooppunten kunnen elk uitvoerbaar bestand of script uitvoeren dat wordt ondersteund door de besturingssysteemomgeving van het knooppunt. Uitvoer bare bestanden of scripts zijn \* . exe, \* . cmd, \* . bat, en Power shell-scripts (voor Windows) en binaire bestanden, shell en python-scripts (voor Linux).

Alle rekenknooppunten in Batch omvatten ook:

- Een standaard[mapstructuur](files-and-directories.md) en daaraan gekoppelde [omgevingsvariabelen](jobs-and-tasks.md) die beschikbaar zijn voor verwijzing door taken.
- **Firewall** instellingen die zijn geconfigureerd om de toegang te beheren.
- [Externe toegang](error-handling.md#connect-to-compute-nodes) tot zowel Windows-(Remote Desktop Protocol (RDP)) als Linux-knoop punten (Secure Shell (SSH)) (tenzij u [uw groep maakt met externe toegang uitgeschakeld](pool-endpoint-configuration.md)).

Knoop punten kunnen standaard met elkaar communiceren, maar ze kunnen niet communiceren met virtuele machines die geen deel uitmaken van dezelfde groep. Als u knoop punten veilig met andere virtuele machines of met een on-premises netwerk wilt laten communiceren, kunt u de groep inrichten [in een subnet van een virtueel Azure-netwerk (VNet)](batch-virtual-network.md). Wanneer u dit doet, kunt u toegang krijgen tot uw knoop punten via open bare IP-adressen. Deze open bare IP-adressen worden gemaakt door batch en kunnen worden gewijzigd gedurende de levens duur van de groep. U kunt ook [een pool maken met een statisch openbaar IP-adres](create-pool-public-ip.md) dat u beheert. Dit zorgt ervoor dat ze niet onverwacht worden gewijzigd.

## <a name="pools"></a>Pools

Een pool is de verzameling knoop punten waarop uw toepassing wordt uitgevoerd.

Azure Batch-groepen worden gebouwd boven op het kernrekenplatform van Azure. Ze bieden grootschalige toewijzing, installatie van toepassingen, gegevens distributie, status controle en flexibele aanpassing ([schalen](#automatic-scaling-policy)) van het aantal reken knooppunten in een pool.

Aan elk knooppunt dat aan een pool wordt toegevoegd, wordt een unieke naam en een uniek IP-adres toegewezen. Wanneer een knooppunt uit een pool wordt verwijderd, gaan alle wijzigingen in het besturingssysteem of de bestanden verloren. De naam en het IP-adres van het verwijderde knooppunt worden vrijgegeven voor toekomstig gebruik. Wanneer een knooppunt een pool verlaat, is de levensduur ervan beëindigd.

Een pool kan alleen worden gebruikt door de Batch-account waarin deze is gemaakt. Een batch-account kan meerdere Pools maken om te voldoen aan de resource vereisten van de toepassingen die worden uitgevoerd.

De pool kan hand matig of [automatisch door de batch-service](#autopools) worden gemaakt wanneer u het werk opgeeft dat moet worden uitgevoerd. Wanneer u een pool maakt, kunt u de volgende kenmerken opgeven:

- [Besturings systeem en versie van knoop punt](#operating-system-and-version)
- [Knooppunt type en het doel aantal knoop punten](#node-type-and-target)
- [Knooppuntgrootte](#node-size)
- [Beleid voor automatisch schalen](#automatic-scaling-policy)
- [Taakplanningsbeleid](#task-scheduling-policy)
- [Communicatie status](#communication-status)
- [Taken starten](#start-tasks)
- [Toepassingspakketten](#application-packages)
- [Configuratie van virtuele netwerken (VNet) en firewalls](#virtual-network-vnet-and-firewall-configuration)
- [Dood](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Batch-accounts hebben een standaardquotum dat het aantal kernen in een Batch-account beperkt. Het aantal kernen komt overeen met het aantal rekenknooppunten. U vindt de standaardquota en instructies voor het [verhogen van een quotum](batch-quota-limit.md#increase-a-quota) in [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Quota en limieten voor de Azure Batch-service). Als een pool niet het beoogde aantal knooppunten bereikt, ligt dit mogelijk aan het kernquotum.

## <a name="operating-system-and-version"></a>Besturings systeem en versie

Wanneer u een batch-pool maakt, geeft u de configuratie van de virtuele Azure-machine en het type besturings systeem op dat u wilt uitvoeren op elk reken knooppunt in de pool.

## <a name="configurations"></a>Configuraties

Er zijn twee soorten pool configuraties beschikbaar in batch.

> [!IMPORTANT]
> Hoewel u momenteel Pools kunt maken met behulp van een van de configuraties, moeten nieuwe groepen worden geconfigureerd met de configuratie van de virtuele machine en niet Cloud Services configuratie. Alle huidige en nieuwe batch-functies worden ondersteund door de virtuele-machine configuratie groepen. Cloud Services configuratie Pools bieden geen ondersteuning voor alle functies en er worden geen nieuwe mogelijkheden gepland. U kunt geen nieuwe ' CloudServiceConfiguration-groepen maken of nieuwe knoop punten toevoegen aan bestaande Pools [na 29 februari 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

### <a name="virtual-machine-configuration"></a>Configuratie van virtuele machine

De **virtuele-machine configuratie** geeft aan dat de groep bestaat uit virtuele machines van Azure. Deze virtuele machines kunnen worden gemaakt met Linux- of Windows-installatiekopieën.

De [batch-knooppunt agent](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) is een programma dat wordt uitgevoerd op elk knoop punt in de pool en biedt de opdracht-en besturings interface tussen het knoop punt en de batch-service. Er zijn verschillende implementaties van de knooppunt agent, ook wel Sku's genoemd, voor verschillende besturings systemen. Wanneer u een pool maakt op basis van de virtuele-machineconfiguratie, moet u niet alleen de grootte van de knooppunten opgeven en de bron van de installatiekopieën waarmee u ze hebt gemaakt, maar ook de **verwijzing naar de installatiekopie van de virtuele machine** en de Batch-**knooppuntagent-SKU** die op de knooppunten moet worden geïnstalleerd. Zie [Linux-rekenknooppunten in Azure Batch-pools inrichten](batch-linux-nodes.md) voor meer informatie over het opgeven van deze pooleigenschappen. U kunt desgewenst een of meer lege gegevensschijven koppelen aan pool-VM's die zijn gemaakt op basis van Marketplace-installatiekopieën of gegevensschijven opnemen in aangepaste installatiekopieën die worden gebruikt voor het maken van de virtuele machines. Wanneer u gegevens schijven inneemt, moet u de schijven koppelen en Format teren vanuit een VM om ze te gebruiken.

### <a name="cloud-services-configuration"></a>Cloud Services configuratie

> [!WARNING]
> Cloud Services configuratie groepen zijn [afgeschaft](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Gebruik in plaats daarvan configuratie groepen voor virtuele machines. Zie de [batch pool configuratie migreren van Cloud Services naar de virtuele machine](batch-pool-cloud-service-to-virtual-machine-configuration.md)voor meer informatie.

De **configuratie** van de Cloud Services geeft aan dat de groep bestaat uit Azure Cloud Services knoop punten. Cloud Services biedt alleen Windows-reken knooppunten.

Beschik bare besturings systemen voor Cloud Services-configuratie groepen worden weer gegeven in de [Azure Guest OS releases en SDK Compatibility Matrix](../cloud-services/cloud-services-guestos-update-matrix.md)en de beschik bare grootte van reken knooppunten worden weer gegeven in [grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Wanneer u een pool maakt die Cloud Services knoop punten bevat, geeft u de grootte van het knoop punt en de bijbehorende *besturingssysteem familie* op (die bepaalt welke versies van .net worden geïnstalleerd met het besturings systeem). Cloud Services wordt sneller geïmplementeerd in azure dan virtuele machines waarop Windows wordt uitgevoerd. Als u pools met Windows-rekenknooppunten wilt, zult u merken dat Cloud Services prestatievoordelen biedt wat de implementatietijd betreft.

Net als bij werk rollen in Cloud Services kunt u een versie van het *besturings systeem* opgeven. We raden u `Latest (*)` aan voor de versie van het *besturings systeem* op te geven, zodat de knoop punten automatisch worden bijgewerkt en er geen werk vereist is om nieuwe versies vrij te maken. Er wordt voornamelijk voor een specifieke versie van een besturingssysteem gekozen om ervoor te zorgen dat toepassingen compatibel blijven, en om compatibiliteitstests met eerdere versies te kunnen uitvoeren alvorens toe te staan dat de versie mag worden bijgewerkt. Na validatie kan de *versie van het besturings systeem* voor de pool worden bijgewerkt en kan de nieuwe installatie kopie van het besturings systeem worden geïnstalleerd. Actieve taken worden onderbroken en opnieuw in de wachtrij geplaatst.

### <a name="node-agent-skus"></a>Node agent-Sku's

Wanneer u een pool maakt, moet u afhankelijk van het besturingssysteem van de basisinstallatiekopie van uw VHD de juiste **nodeAgentSkuId** selecteren. U kunt een toewijzing van beschik bare SKU-Id's van de knoop punt agent ophalen voor de bijbehorende verwijzingen naar de installatie kopie van het besturings systeem door de [lijst ondersteunde sku's-bewerking van knoop punt agent](/rest/api/batchservice/list-supported-node-agent-skus)

### <a name="custom-images-for-virtual-machine-pools"></a>Aangepaste installatiekopieën voor VM-pools

Zie [de galerie met gedeelde afbeeldingen gebruiken om een aangepaste groep te maken](batch-sig-images.md)voor meer informatie over het maken van een groep met aangepaste installatie kopieën.

U kunt ook een aangepaste pool met virtuele machines maken met behulp van een [beheerde installatie kopie](batch-custom-images.md) bron. Zie [How to create an image of a virtual machine or VHD ](../virtual-machines/linux/capture-image.md) (Een installatiekopie van een virtuele machine of VHD maken) voor meer informatie over het maken van aangepaste Linux-installatiekopieën van virtuele Azure-machines. Zie [Create a managed image of a generalized VM in Azure](../virtual-machines/windows/capture-image-resource.md) (Een beheerde VM-installatiekopie maken van een gegeneraliseerde VM in Azure) voor meer informatie over het maken van aangepaste Windows-installatiekopieën op basis van Azure VM's.

### <a name="container-support-in-virtual-machine-pools"></a>Ondersteuning voor containers in virtuele machine-pools

Bij het maken van een virtuele machine-pool met de Batch-API's, kunt u de pool instellen voor het uitvoeren van taken in Docker-containers. Momenteel moet u de pool maken met behulp van een installatiekopie die ondersteuning biedt voor Docker-containers. Gebruik Windows Server 2016 Datacenter met de Containers-installatiekopie van Azure Marketplace of geef een aangepaste VM-installatiekopie op die Docker Community Edition of Enterprise Edition en alle vereiste stuurprogramma's bevat. De poolinstellingen moeten een [containerconfiguratie](/rest/api/batchservice/pool/add) bevatten die containerinstallatiekopieën naar de virtuele machines kopieert wanneer de pool wordt gemaakt. Taken die worden uitgevoerd op de pool kunnen vervolgens verwijzen naar de containerinstallatiekopieën en uitvoeringsopties voor containers.

Zie voor meer informatie [Docker-containertoepassingen uitvoeren op Azure Batch](batch-docker-container-workloads.md).

## <a name="node-type-and-target"></a>Knooppunt type en doel

Wanneer u een pool maakt, kunt u opgeven welke typen knoop punten u wilt en wat het doel nummer voor elk van beide. De twee typen knoop punten zijn:

- **Toegewezen knoop punten.** Toegewezen rekenknooppunten zijn gereserveerd voor uw workloads. Ze zijn duurder dan de knooppunten met lage prioriteit, maar ze worden gegarandeerd nooit verschoven.
- **Knoop punten met een lage prioriteit.** Knooppunten met lage prioriteit profiteren van de overtollige capaciteit in Azure om uw Batch-workloads uit te voeren. Knoop punten met een lage prioriteit zijn minder duur per uur dan toegewezen knoop punten, en zorgen ervoor dat werk belastingen een aanzienlijke reken kracht vereisen. Zie voor meer informatie [VM's met lage prioriteit gebruiken met Batch](batch-low-pri-vms.md).

Knoop punten met een lage prioriteit kunnen worden afgebroken wanneer Azure onvoldoende capaciteit voor overschot heeft. Als een knooppunt wordt verschoven tijdens het uitvoeren van taken, worden de taken opnieuw ingepland en uitgevoerd zodra er weer een rekenknooppunt beschikbaar is. Knooppunten met lage prioriteit zijn een goede optie voor workloads waarbij de voltooiingstijd van de taak flexibel is en het werk over veel knooppunten wordt verdeeld. Voordat u besluit om knooppunten met lage prioriteit voor uw scenario te gebruiken, moet u ervoor zorgen dat het werk dat verloren gaat als gevolg van toe-eigening minimaal is en eenvoudig opnieuw kan worden uitgevoerd.

U kunt zowel rekenknooppunten met lage prioriteit als toegewezen rekenknooppunten hebben in dezelfde groep. Elk type knoop punt heeft een eigen doel instelling, waarvoor u het gewenste aantal knoop punten kunt opgeven.

Het aantal rekenknooppunten wordt aangeduid als *beoogd* omdat de pool in sommige gevallen mogelijk niet het gewenste aantal knooppunten bereikt. Een pool kan het doel bijvoorbeeld mogelijk niet bereiken als het eerst het [quotum voor kernen](batch-quota-limit.md) voor uw Batch-account bereikt. Het is ook mogelijk dat de groep het doel niet bereikt als u een formule voor automatisch schalen hebt toegepast op de groep die het maximum aantal knoop punten beperkt.

Zie [batch-prijzen](https://azure.microsoft.com/pricing/details/batch/)voor prijs informatie voor knoop punten met lage prioriteit en toegewezen.

## <a name="node-size"></a>Knooppuntgrootte

Wanneer u een Azure Batch-groep maakt, kunt u kiezen uit bijna alle reeksen en grootten virtuele machines die beschikbaar zijn in Azure. Azure biedt verscheidene VM-grootten voor verschillende workloads, met inbegrip van VM-grootten die speciaal zijn bedoeld voor [HPC](../virtual-machines/sizes-hpc.md) of [GPU](../virtual-machines/sizes-gpu.md). Houd er rekening mee dat knooppunt grootten alleen kunnen worden gekozen op het moment dat een pool wordt gemaakt. Met andere woorden, wanneer een pool is gemaakt, kan de knooppunt grootte niet meer worden gewijzigd.

Zie [Choose a VM size for compute nodes in an Azure Batch pool](batch-pool-vm-sizes.md) (Een VM-grootte voor rekenknooppunten in een Azure Batch-groep kiezen) voor meer informatie.

## <a name="automatic-scaling-policy"></a>Beleid voor automatisch schalen

Voor dynamische workloads kunt u een beleid voor automatisch schalen Toep assen op een groep. De batch-service evalueert periodiek de formule en past het aantal knoop punten in de pool dynamisch aan op basis van de huidige werk belasting en het resource gebruik van uw reken scenario. Zo kunt u de totale kosten van het uitvoeren van uw toepassing verlagen door alleen de benodigde resources te gebruiken en de resources die u niet nodig hebt, vrij te geven.

U schakelt automatische vergroting/verkleining in door een [formule voor automatisch vergroten/verkleinen](batch-automatic-scaling.md#autoscale-formulas) te schrijven en die formule te koppelen aan een pool. De Batch-service gebruikt deze formule om het doelaantal knooppunten in de pool te bepalen voor het volgende interval voor vergroten/verkleinen (een interval dat u kunt configureren). U kunt de instellingen voor automatisch vergroten/verkleinen voor een pool opgeven wanneer u deze maakt of op een later moment voor een pool inschakelen. U kunt de instellingen voor automatisch vergroten/verkleinen ook bijwerken in een pool waarvoor vergroten/verkleinen is ingeschakeld.

Een voor beeld: als u een taak wilt uitvoeren, moet u een groot aantal taken verzenden. U kunt aan de pool een formule voor vergroten/verkleinen toewijzen die het aantal knooppunten in de pool aanpast op basis van het huidige aantal taken in de wachtrij en de snelheid waarmee de taken in de job worden voltooid. De Batch-service evalueert periodiek de formule en vergroot/verkleint de pool op basis van de workload en uw andere formule-instellingen. De service voegt naar behoefte knooppunten toe, bijvoorbeeld wanneer er veel taken in de wachtrij staan. Ook worden er knooppunten verwijderd wanneer er geen taken actief zijn of in de wachtrij staan.

Een formule voor vergroten/verkleinen kan op de volgende metrische gegevens worden gebaseerd:

- **Metrische gegevens voor tijd** zijn gebaseerd op statistieken die om de vijf minuten worden verzameld binnen het opgegeven aantal uren.
- **Metrische gegevens voor resources** zijn gebaseerd op CPU-gebruik, bandbreedtegebruik, geheugengebruik en het aantal knooppunten.
- **Metrische gegevens voor taken** zijn gebaseerd op de taakstatus, zoals *Actief* (in de wachtrij) *Wordt uitgevoerd* of *Voltooid*.

Wanneer met automatisch vergroten/verkleinen het aantal rekenknooppunten in een groep vermindert, moet u bedenken hoe taken moeten worden afgehandeld die worden uitgevoerd op het moment van de verkleining. Om dit te kunnen doen, biedt batch een optie voor het [*detoewijzen van knoop punten*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) die u in uw formules kunt gebruiken. U kunt bijvoorbeeld opgeven dat actieve taken worden gestopt en vervolgens opnieuw in de wachtrij worden geplaatst om te worden uitgevoerd op een ander knooppunt, of moeten worden voltooid voordat het knooppunt uit de pool wordt verwijderd. Houd er rekening mee dat bij het instellen van de toewijzing van het knoop punt de optie voor het ongedaan maken `taskcompletion` `retaineddata` van de grootte van de groep wordt ingesteld, totdat alle taken zijn voltooid of alle Bewaar perioden van de taak zijn verlopen.

Zie [Automatically scale compute nodes in an Azure Batch pool](batch-automatic-scaling.md) (Rekenknooppunten in een Azure Batch-pool automatisch vergroten/verkleinen) voor meer informatie over het automatisch vergroten/verkleinen van een toepassing.

> [!TIP]
> Als u het gebruik van rekenresources wilt maximaliseren, stelt u het doelaantal knooppunten aan het einde van een job in op nul, maar staat u toe dat actieve taken mogen worden voltooid.

## <a name="task-scheduling-policy"></a>Taakplanningsbeleid

De configuratieoptie [Maximumaantal taken per knooppunt](batch-parallel-node-tasks.md) bepaalt het maximumaantal taken dat parallel kan worden uitgevoerd op elk rekenknooppunt in de pool.

Bij de standaardconfiguratie wordt er op een knooppunt één taak tegelijk uitgevoerd, maar er zijn scenario's waarin het nuttig is om op een knooppunt twee of meer taken tegelijk uit te voeren. Zie het [voorbeeldscenario](batch-parallel-node-tasks.md#example-scenario) in het artikel over [gelijktijdige knooppunttaken](batch-parallel-node-tasks.md) om te bekijken hoe u kunt profiteren van meerdere taken per knooppunt.

U kunt ook een *opvul type* opgeven dat bepaalt of batch de taken gelijkmatig verspreid over alle knoop punten in een pool, of dat elk knoop punt met het maximum aantal taken wordt gebundeld voordat taken aan een ander knoop punt worden toegewezen.

## <a name="communication-status"></a>Communicatie status

In de meeste gevallen functioneren taken onafhankelijk en hoeven ze niet met elkaar te communiceren. Maar mogelijk hebt u ook toepassingen waarin taken moeten communiceren, bijvoorbeeld [MPI-scenario's](batch-mpi.md).

U kunt een pool zodanig configureren dat **communicatie tussen knoop** punten is toegestaan, zodat knooppunten in een pool kunnen communiceren tijdens runtime. Wanneer communicatie tussen knooppunten is ingeschakeld, kunnen knooppunten in de configuratie voor Cloud Services-pools met elkaar communiceren op poorten die groter zijn dan 1100. Voor virtuele-machineconfiguratiepools is verkeer op geen enkele poort beperkt.

Het inschakelen van de communicatie tussen knoop punten is ook van invloed op de plaatsing van het knoop punt in clusters, waardoor het maximum aantal knoop punten in een groep kan worden beperkt vanwege implementatie beperkingen. Als voor uw toepassing geen communicatie tussen knooppunten is vereist, kan de Batch-service een potentieel groter aantal knooppunten aan de pool toewijzen vanuit vele verschillende clusters en datacenters, om meer parallelle verwerkingskracht mogelijk te maken.

## <a name="start-tasks"></a>Taken starten

Desgewenst kunt u een [begin taak](jobs-and-tasks.md#start-task) toevoegen die op elk knoop punt wordt uitgevoerd wanneer dat knoop punt aan de pool wordt toegevoegd en telkens wanneer een knoop punt opnieuw wordt opgestart of een installatie kopie wordt gemaakt. De begintaak is vooral handig voor de voorbereiding van rekenknooppunten voor het uitvoeren van taken, zoals het installeren van de toepassingen die door de taken worden uitgevoerd.

## <a name="application-packages"></a>Toepassingspakketten

U kunt toepassingspakketten opgeven die moeten worden geïmplementeerd in de rekenknooppunten in de groep. Toepassingspakketten bieden vereenvoudigde implementatie en versies van de toepassingen die de taken uitvoeren. Toepassingspakketten die u voor een groep van toepassingen opgeeft, worden geïnstalleerd op elk knooppunt dat lid wordt van de groep, en elke keer dat er een knooppunt opnieuw wordt opgestart of er een installatiekopie wordt hersteld.

Zie [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Toepassingen implementeren naar rekenknooppunten met Batch-toepassingspakketten) voor meer informatie over het gebruiken van toepassingspakketten om toepassingen te implementeren naar Batch-knooppunten.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Configuratie van virtuele netwerken (VNet) en firewalls

Wanneer u een pool rekenknooppunten inricht in Batch kunt u deze pool koppelen aan een subnet van een virtueel netwerk van [Azure (VNet)](../virtual-network/virtual-networks-overview.md). Het gebruik van een Azure VNet vereist dat de Batch-client-API gebruikmaakt van Azure Active Directory-verificatie (AD). Azure Batch-ondersteuning voor Azure AD wordt beschreven in [Oplossingen van Batch-service verifiëren met Active Directory](batch-aad-auth.md).

### <a name="vnet-requirements"></a>Vereisten voor VNet

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Zie [Een pool van virtuele machines maken met uw virtuele netwerk](batch-virtual-network.md) voor meer informatie over het instellen van een Batch-pool in een VNet.

> [!TIP]
> Om ervoor te zorgen dat de open bare IP-adressen die worden gebruikt voor toegang tot knoop punten, niet worden gewijzigd, kunt u [een pool maken met opgegeven open bare IP-adressen die u beheert](create-pool-public-ip.md).

## <a name="pool-and-compute-node-lifetime"></a>Levensduur van pool en rekenknooppunt

Wanneer u uw Azure Batch-oplossing ontwerpt, moet u opgeven hoe en wanneer groepen worden gemaakt en hoe lang Compute-knoop punten in deze groepen beschikbaar worden gehouden.

Aan het ene uiteinde van het spectrum kunt u een pool maken voor elke job wanneer deze wordt verzonden en kunt u de pool verwijderen zodra taken zijn uitgevoerd. Hiermee maximaliseert u het gebruik omdat de knoop punten alleen worden toegewezen wanneer dit nodig is, en ze worden afgesloten zodra ze inactief zijn. Hoewel dit betekent dat de taak moet wachten tot de knoop punten zijn toegewezen, is het belang rijk te weten dat de taken zijn gepland om te worden uitgevoerd zodra er afzonderlijke knoop punten zijn toegewezen en de begin taak is voltooid. Batch wacht *niet* tot alle knooppunten in een pool beschikbaar zijn alvorens taken aan de knooppunten toe te wijzen. Hiermee zorgt u voor maximaal gebruik van alle beschikbare knooppunten.

Als, aan het andere uiteinde van het spectrum, het onmiddellijk starten van jobs de hoogste prioriteit heeft, kunt u een pool voortijdig maken en de knooppunten ervan beschikbaar stellen voordat er jobs worden verzonden. In dit scenario kunnen taken onmiddellijk worden gestart, maar blijven knooppunten mogelijk inactief terwijl wordt gewacht tot er taken worden toegewezen.

Een gecombineerde benadering wordt meestal gebruikt voor het verwerken van een variabele, maar doorlopende belasting. U kunt een pool hebben waarin meerdere taken worden verzonden en u kunt het aantal knoop punten omhoog of omlaag schalen op basis van de taak belasting. U kunt dit reactief doen, op basis van de huidige workload, of proactief als de workload kan worden voorspeld. Zie het [beleid voor automatisch schalen](#automatic-scaling-policy)voor meer informatie.

## <a name="autopools"></a>Autogroepen

Een [autopool](/rest/api/batchservice/job/add#autopoolspecification) is een groep die wordt gemaakt door de batch-service wanneer een taak wordt verzonden, in plaats van vóór de taken die worden uitgevoerd in de groep. Met de batch-service wordt de levens duur van een automatisch groep beheerd volgens de kenmerken die u opgeeft. Meestal worden deze groepen ook automatisch verwijderd nadat de taken zijn voltooid.

## <a name="security-with-certificates"></a>Beveiliging met certificaten

Certificaten gebruikt u doorgaans bij het versleutelen of ontsleutelen van gevoelige gegevens voor taken, zoals de sleutel voor een [Azure Storage-account](accounts.md#azure-storage-accounts). Ter ondersteuning hiervan installeert u certificaten op knooppunten. Versleutelde geheimen worden via opdrachtregelparameters doorgegeven aan taken of worden ingesloten in een van de taakresources. De geïnstalleerde certificaten kunnen dan worden gebruikt om ze te ontsleutelen.

U gebruikt de bewerking [Certificaat toevoegen](/rest/api/batchservice/certificate/add) (Batch REST) of de methode [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) (Batch .NET) om een certificaat aan een Batch-account toe te voegen. Daarna kunt u het certificaat aan een nieuwe of bestaande pool koppelen.

Wanneer een certificaat aan een pool is gekoppeld, wordt het door de Batch-service in elk knooppunt in de pool geïnstalleerd. De batch-service installeert de juiste certificaten wanneer het knoop punt wordt gestart, vóór het starten van taken (inclusief de [taak taak en taak beheer](jobs-and-tasks.md#job-manager-task) [starten](jobs-and-tasks.md#start-task) ).

Als u een certificaat toevoegt aan een bestaande pool, moet u de reken knooppunten opnieuw opstarten om het certificaat toe te passen op de knoop punten.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [taken en taken](jobs-and-tasks.md).
- Meer informatie over het [detecteren en voor komen van storingen in de groep en achtergrond bewerkingen op knoop punten ](batch-pool-node-error-checking.md).
