---
title: Problemen met de visualisatie van analyses en afhankelijkheden in Azure Migrate oplossen
description: Krijg hulp bij de visualisatie van beoordeling en afhankelijkheid in Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 78e54543c94cd6e8434023b61516242c2491f353
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863595"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Problemen met de evaluatie/afhankelijkheid oplossen

Dit artikel helpt u bij het oplossen van problemen met de visualisatie van evaluatie en afhankelijkheid met [Azure migrate: detectie en evaluatie](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemen met de gereedheids beoordeling

Los problemen met de voorbereidings voorbereiding op als volgt:

**Name** | **Herstellen**
--- | ---
Niet-ondersteund opstart type | Azure biedt geen ondersteuning voor Vm's met een EFI-opstart type. U wordt aangeraden het opstart type te converteren naar BIOS voordat u een migratie uitvoert. <br/><br/>U kunt Azure Migrate server migratie gebruiken om de migratie van dergelijke Vm's te verwerken. Tijdens de migratie wordt het opstart type van de VM naar het BIOS geconverteerd.
Voorwaardelijk ondersteund Windows-besturings systeem | Het besturings systeem heeft de eind datum van de ondersteuning door gegeven en heeft een aangepaste ondersteunings overeenkomst (CSA) nodig voor [ondersteuning in azure](/troubleshoot/azure/virtual-machines/server-software-support). Overweeg om te upgraden voordat u naar Azure migreert. Lees de informatie over het [voorbereiden van servers met Windows Server 2003](prepare-windows-server-2003-migration.md) voor migratie naar Azure.
Niet-ondersteund Windows-besturings systeem | Azure ondersteunt alleen [geselecteerde versies van Windows-besturings systemen](/troubleshoot/azure/virtual-machines/server-software-support). U kunt de server upgraden voordat u naar Azure migreert.
Voorwaardelijk goedgekeurd Linux-besturings systeem | Azure bevestigt alleen [geselecteerde Linux-besturingssysteem versies](../virtual-machines/linux/endorsed-distros.md). U kunt de server upgraden voordat u naar Azure migreert. Raadpleeg ook [hier](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) voor meer informatie.
Niet-goedgekeurd Linux-besturings systeem | De server kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. U kunt een upgrade uitvoeren naar een [officiële versie van Linux](../virtual-machines/linux/endorsed-distros.md) voordat u naar Azure migreert.
Onbekend besturings systeem | Het besturings systeem van de virtuele machine is opgegeven als ' andere ' in vCenter Server. Dit gedrag blokkeert Azure Migrate van het controleren van de Azure-gereedheid van de virtuele machine. Zorg ervoor dat het besturings systeem wordt [ondersteund](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) door Azure voordat u de server migreert.
Niet-ondersteunde bits versie | Vm's met een 32-bits besturings systeem kunnen worden opgestart in azure, maar het wordt aanbevolen dat u een upgrade uitvoert naar 64-bits voordat u naar Azure migreert.
Vereist een micro soft Visual Studio-abonnement | Op de server wordt een Windows client-besturings systeem uitgevoerd, dat alleen wordt ondersteund via een Visual Studio-abonnement.
Er is geen VM gevonden voor de vereiste opslag prestaties | De opslag prestaties (invoer/uitvoer-bewerkingen per seconde [IOPS] en door Voer) die voor de server zijn vereist, overschrijden de ondersteuning voor Azure-VM'S. Verminder de opslag vereisten voor de server vóór de migratie.
Er is geen VM gevonden voor de vereiste netwerk prestaties | De netwerk prestaties (in/uit) die voor de server zijn vereist, overschrijden de ondersteuning voor Azure-VM'S. Verminder de netwerk vereisten voor de server.
De virtuele machine is niet gevonden op de opgegeven locatie | Gebruik een andere doel locatie vóór de migratie.
Een of meer niet-geschikte schijven | Een of meer schijven die zijn gekoppeld aan de virtuele machine voldoen niet aan de vereisten van Azure. Één<br/><br/> Azure Migrate: detectie en evaluatie biedt momenteel geen ondersteuning voor Ultra-SSD schijven en de schijven worden geëvalueerd op basis van de schijf limieten voor Premium Managed disks (32 TB).<br/><br/> Zorg ervoor dat de grootte van de schijf < 64 TB (ondersteund door Ultra-SSD schijven) voor elke schijf die aan de VM is gekoppeld.<br/><br/> Als dat niet het geval is, vermindert u de schijf grootte voordat u naar Azure migreert, of gebruikt u meerdere schijven in Azure en [stript u deze samen](../virtual-machines/premium-storage-performance.md#disk-striping) om hogere opslag limieten te krijgen. Zorg ervoor dat de prestaties (IOPS en door Voer) die nodig zijn voor elke schijf worden ondersteund door door Azure [beheerde virtuele-machine schijven](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits).
Een of meer niet-geschikte netwerk adapters. | Verwijder ongebruikte netwerk adapters van de server vóór de migratie.
Aantal schijven overschrijdt de limiet | Verwijder ongebruikte schijven van de server vóór de migratie.
De schijf grootte overschrijdt de limiet | Azure Migrate: detectie en evaluatie biedt momenteel geen ondersteuning voor Ultra-SSD schijven en controleert de schijven op basis van Premium-schijf limieten (32 TB).<br/><br/> Azure ondersteunt echter schijven met een grootte van Maxi maal 64 TB (ondersteund door Ultra-SSD schijven). Verklein schijven tot minder dan 64 TB vóór de migratie of gebruik meerdere schijven in Azure en [strip deze samen](../virtual-machines/premium-storage-performance.md#disk-striping) om hogere opslag limieten te krijgen.
De schijf is niet beschikbaar op de opgegeven locatie | Zorg ervoor dat de schijf zich op de doel locatie bevindt voordat u migreert.
De schijf is niet beschikbaar voor de opgegeven redundantie | De schijf moet het opslag type redundantie gebruiken dat is gedefinieerd in de instellingen voor evaluatie (standaard LRS).
Kan de schijf geschiktheid niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
VM met vereiste kernen en geheugen niet gevonden | Azure kan geen geschikt VM-type vinden. Verminder het geheugen en het aantal kernen van de on-premises server voordat u migreert.
Kan de geschiktheid van de virtuele machine niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Kan de geschiktheid van een of meer schijven niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Kan de geschiktheid voor een of meer netwerk adapters niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Er is geen VM-grootte gevonden voor het gereserveerde exemplaar van de aanbiedings valuta | De server is gemarkeerd als niet geschikt omdat de VM-grootte niet is gevonden voor de geselecteerde combi natie van RI, offer en Currency. Bewerk de eigenschappen van de beoordeling om de geldige combi Naties te kiezen en de beoordeling opnieuw te berekenen. 
Voorwaardelijk gereed Internet Protocol | Alleen van toepassing op AVS-evaluaties (Azure VMware-oplossing). AVS biedt geen ondersteuning voor IPv6-Internet adressen. Neem contact op met het AVS-team voor herstel richtlijnen als uw server wordt gedetecteerd met IPv6.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Aanbevolen migratie hulpmiddel in op import gebaseerde AVS-evaluatie is gemarkeerd als onbekend

Voor servers die worden geïmporteerd via een CSV-bestand, is het standaard hulp programma voor migratie in en AVS-evaluatie onbekend. Voor servers in VMware-omgevingen is het echter raadzaam de VMware Hybrid Cloud extension (HCX)-oplossing te gebruiken. [Meer informatie](../azure-vmware/tutorial-deploy-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux-Vm's zijn ' conditioneel Ready ' in een Azure VM-evaluatie

In het geval van VMware-en Hyper-V-Vm's, markeert Azure VM-evaluatie Linux Vm's als ' voorwaardelijk gereed ' vanwege een bekende tussen ruimte. 

- De tussen ruimte voor komt dat de secundaire versie van het Linux-besturings systeem dat is geïnstalleerd op de on-premises Vm's kan worden gedetecteerd.
- Voor RHEL 6,10 wordt momenteel alleen RHEL 6 door de Azure VM-evaluatie gedetecteerd als de versie van het besturings systeem. Dit komt doordat de vCenter Server ar de Hyper-V-host niet de kernel-versie van de Linux-VM-besturings systemen bevat.
-  Omdat Azure alleen specifieke versies van Linux afschrijft, zijn de virtuele Linux-machines momenteel gemarkeerd als voorwaardelijk gereed in de Azure VM-evaluatie.
- U kunt bepalen of het Linux-besturings systeem dat wordt uitgevoerd op de on-premises VM, wordt goedgekeurd in azure door [Azure Linux-ondersteuning](../virtual-machines/linux/endorsed-distros.md)te controleren.
-  Nadat u de geviseerde distributie hebt gecontroleerd, kunt u deze waarschuwing negeren.

Dit gat kan worden opgelost door [toepassings detectie](./how-to-discover-applications.md) in te scha kelen op de virtuele VMware-machines. De evaluatie van Azure VM maakt gebruik van het besturings systeem dat is gedetecteerd van de virtuele machine met behulp van de gast referenties. Deze besturingssysteem gegevens identificeren de juiste besturingssysteem gegevens in het geval van zowel Windows-als Linux-Vm's.

## <a name="operating-system-version-not-available"></a>De versie van het besturings systeem is niet beschikbaar

Voor fysieke servers moet de informatie over de secundaire versie van het besturings systeem beschikbaar zijn. Neem contact op met Microsoft Ondersteuning als deze niet beschikbaar is. Voor servers in de VMware-omgeving gebruikt Azure Migrate de gegevens van het besturings systeem die zijn opgegeven voor de virtuele machine in vCenter Server. VCenter Server biedt echter niet de secundaire versie voor besturings systemen. Als u de secundaire versie wilt detecteren, moet u [toepassings detectie](./how-to-discover-applications.md)instellen. Voor virtuele Hyper-V-machines wordt het doorzoeken van de secundaire versie van het besturings systeem niet ondersteund. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure-Sku's die groter zijn dan on-premises in een Azure VM-evaluatie

Azure VM-evaluatie kan Azure VM Sku's aanbevelen met meer kernen en geheugen dan de huidige on-premises toewijzing op basis van het type evaluatie:

- De aanbeveling van de VM-SKU is afhankelijk van de evaluatie-eigenschappen.
- Dit wordt beïnvloed door het type evaluatie dat u in de Azure VM-evaluatie uitvoert: *op prestaties gebaseerd* of *als on-premises*.
- Voor evaluaties op basis van prestaties beschouwt de Azure VM-evaluatie de gebruiks gegevens van de on-premises Vm's (CPU, geheugen, schijf en netwerk gebruik) om de juiste doel-VM-SKU voor uw on-premises Vm's te bepalen. Er wordt ook een comfortfactor toegevoegd bij het bepalen van effectief gebruik.
- Voor on-premises grootte worden de prestatie gegevens niet in overweging genomen en wordt de doel-SKU aanbevolen op basis van on-premises toewijzing.

Om te laten zien hoe dit kan invloed hebben op aanbevelingen, gaan we een voor beeld doen:

We hebben een on-premises VM met vier kernen en acht GB geheugen, met een CPU-gebruik van 50% en een geheugen gebruik van 50% en een opgegeven comfort factor van 1,3.

-  Als de evaluatie **on-premises** is, wordt een Azure VM-SKU met vier kernen en 8 GB geheugen aanbevolen.
- Als de evaluatie op basis van prestaties is gebaseerd op een effectief CPU-en geheugen gebruik (50% van 4 kernen * 1,3 = 2,6 kernen en 50% van 8 GB geheugen * 1,3 = 5,3-GB geheugen), wordt de goedkoopste VM-SKU van vier kernen (dichtstbijgelegen ondersteunde kernen) en acht GB aan geheugen (dichtstbijgelegen ondersteunde geheugen grootte) aanbevolen.
- Meer [informatie](concepts-assessment-calculation.md#types-of-assessments) over de beoordelings grootte.

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Waarom is de aanbevolen Azure Disk-Sku's groter dan on-premises in een Azure VM-evaluatie?

De evaluatie van de Azure-VM kan een grotere schijf aanbevelen op basis van het type evaluatie.
- De schijf grootte is afhankelijk van twee evaluatie-eigenschappen: grootte criteria en opslag type.
- Als de grootte criteria **op basis van prestaties** zijn en het opslag type is ingesteld op **automatisch**, worden de IOPS-en doorvoer waarden van de schijf beschouwd bij het identificeren van het doel schijf type (Standard-HDD, Standard-SSD of Premium). Er wordt vervolgens een schijf-SKU van het schijf type aanbevolen en de aanbeveling houdt rekening met de grootte vereisten van de on-premises schijf.
- Als de grootte criteria **op basis van prestaties** zijn en het opslag type **Premium** is, wordt een Premium-schijf-SKU in azure aanbevolen op basis van de IOPS-, doorvoer-en grootte vereisten van de on-premises schijf. Dezelfde logica wordt gebruikt voor het uitvoeren van schijf grootte wanneer de grootte criteria **op locatie** zijn en het opslag type is **Standard-HDD**, **Standard-SSD** of **Premium**.

Als u bijvoorbeeld een on-premises schijf met 32 GB geheugen hebt, maar de geaggregeerde Lees-en schrijf-IOPS voor de schijf 800 IOPS is, raadt Azure VM-evaluatie een Premium-schijf aan (vanwege de hogere IOPS-vereisten). vervolgens wordt een schijf-SKU aanbevolen die de vereiste IOPS en grootte kan ondersteunen. In dit voorbeeld komen we dan uit bij P15 (256 GB, 1100 IOPS). Hoewel de grootte die de on-premises schijf vereist, 32 GB was, raadt Azure VM-evaluatie een grotere schijf aan vanwege de hoge IOPS-vereiste van de on-premises schijf.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Waarom ontbreken er prestatiegegevens voor sommige/alle VM's in mijn evaluatierapport?

Voor evaluaties Op basis van prestaties staat in de export van het evaluatierapport PercentageOfCoresUtilizedMissing of PercentageOfMemoryUtilizedMissing, wanneer er geen prestatiegegevens voor de on-premises VM's kunnen worden verzameld op het Azure Migrate-apparaat. Controleer het volgende:

- Of de VM's zijn ingeschakeld gedurende de periode waarvoor u de evaluatie maakt
- Als er alleen geheugenitems ontbreken en u virtuele Hyper-V-machines probeert te evalueren, controleert u of er dynamisch geheugen is ingeschakeld op deze virtuele machines. Er is momenteel een bekend probleem als gevolg waarvan het Azure Migrate-apparaat geen geheugengebruik kan verzamelen voor dergelijke VM's.
- Als alle prestatie meter items ontbreken, controleert u of aan de toegangs vereisten voor de poort voor evaluatie is voldaan. Meer informatie over de toegangs vereisten voor de poort voor [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) en [fysieke](./migrate-support-matrix-physical.md#port-access) evaluatie.
Opmerking: als een of meer van de prestatie meter items ontbreken, wordt Azure Migrate gedetecteerd en geëvalueerd de toegewezen kernen/geheugen on-premises terug en wordt de VM-grootte dienovereenkomstig aanbevolen.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Waarom ontbreken prestatie gegevens voor sommige/alle servers in mijn Azure VM-en/of AVS-evaluatie rapport?

Voor de evaluatie op basis van prestaties geeft de export rapport uitvoer ' PercentageOfCoresUtilizedMissing ' of ' PercentageOfMemoryUtilizedMissing ' als het Azure Migrate apparaat geen prestatie gegevens kan verzamelen voor de on-premises servers. Controleer het volgende:

- Als de servers zijn ingeschakeld voor de duur waarvoor u de evaluatie maakt
- Als er alleen geheugen items ontbreken en u probeert servers in de Hyper-V-omgeving te beoordelen. In dit scenario schakelt u dynamisch geheugen op de servers in en herberekenen de evaluatie om de meest recente wijzigingen weer te geven. Het apparaat kan alleen geheugen gebruiks waarden voor servers in de Hyper-V-omgeving verzamelen wanneer dynamisch geheugen is ingeschakeld op de server.

- Als alle prestatie meter items ontbreken, moet u ervoor zorgen dat uitgaande verbindingen op poort 443 (HTTPS) zijn toegestaan.

    > [!Note]
    > Als een van de prestatie meter items ontbreekt, Azure Migrate: detectie en evaluatie terugvallen op de toegewezen kernen/geheugen on-premises en wordt de VM-grootte dienovereenkomstig aanbevolen.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Waarom ontbreken prestatie gegevens voor sommige/alle SQL-exemplaren/data bases in mijn Azure SQL-evaluatie?

Controleer het volgende om zeker te weten dat de prestatiegegevens zijn verzameld:

- Als de SQL-servers zijn ingeschakeld voor de duur waarvoor u de evaluatie maakt
- Als de verbindings status van de SQL-Agent in Azure Migrate verbonden is en de laatste heartbeat controleren 
- Als Azure Migrate verbindings status voor alle SQL-exemplaren ' connected ' is op de Blade gedetecteerde SQL-instantie
- Als alle prestatiemeteritems ontbreken, moet u ervoor zorgen dat uitgaande verbindingen op poort 443 (HTTPS) zijn toegestaan

Als een van de prestatie meter items ontbreekt, raadt Azure SQL assessment de kleinste Azure SQL-configuratie voor die instantie/data base aan.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Waarom is de betrouwbaarheidsclassificatie van mijn evaluatie laag?

De betrouwbaarheidsclassificatie wordt berekend voor evaluaties Op basis van prestaties, op basis van het percentage [beschikbare gegevenspunten](./concepts-assessment-calculation.md#ratings) dat nodig is om de evaluatie te berekenen. Hieronder ziet u de redenen waarom een evaluatie een lage betrouwbaarheidsclassificatie kan krijgen:

- U hebt uw omgeving niet geprofileerd gedurende de periode waarvoor u de evaluatie maakt. Als u bijvoorbeeld een evaluatie maakt waarbij de duur van de prestaties is ingesteld op één week, moet u na het starten van de detectie minstens een week wachten voordat alle gegevenspunten zijn verzameld. Als u niet kunt wachten op de duur, wijzigt u de duur van de prestaties in een kleinere periode en berekent u de evaluatie **opnieuw** .
 
- Bij de evaluatie kunnen de prestatiegegevens voor sommige of alle servers in de evaluatieperiode niet worden verzameld. Voor een hoge betrouwbaarheids classificatie moet u het volgende doen: 
    - Servers zijn ingeschakeld voor de duur van de evaluatie
    - Uitgaande verbindingen op poort 443 zijn toegestaan
    - Voor Hyper-V-servers is dynamisch geheugen ingeschakeld 
    - De verbindings status van agents in Azure Migrate is verbonden en controleer de laatste heartbeat
    - Voor Azure SQL-evaluaties is Azure Migrate verbindings status voor alle SQL-exemplaren verbonden op de Blade gedetecteerde SQL-instantie

    **Bereken de evaluatie opnieuw** om de meest recente wijzigingen in de betrouwbaarheidsclassificatie mee te nemen.

- Voor Azure VM-en AVS-evaluaties zijn er weinig servers gemaakt nadat de detectie is gestart. Als u bijvoorbeeld een evaluatie maakt voor de prestatie geschiedenis van de laatste maand, maar weinig servers in de omgeving is slechts een week geleden gemaakt. In dit geval zijn de prestatie gegevens voor de nieuwe servers niet beschikbaar voor de hele duur en is de betrouwbaarheids classificatie laag. [Meer informatie](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Voor Azure SQL-evaluaties zijn enkele SQL-exemplaren of -databases gemaakt nadat de detectie is gestart. Als u bijvoorbeeld een evaluatie maakt voor de prestatie geschiedenis van de laatste maand, maar weinig SQL-exemplaren of data bases zijn in de omgeving slechts een week geleden gemaakt. In dit geval zijn de prestatie gegevens voor de nieuwe servers niet beschikbaar voor de hele duur en is de betrouwbaarheids classificatie laag. [Meer informatie](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Is de licentie voor het besturings systeem opgenomen in een Azure VM-evaluatie?

De Azure VM-evaluatie beschouwt momenteel alleen de licentie kosten van het besturings systeem voor Windows-servers. De licentie kosten voor Linux-servers worden momenteel niet in overweging genomen.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Hoe werkt de grootte van de prestaties in een Azure VM-evaluatie?

Azure VM-evaluatie verzamelt voortdurend prestatie gegevens van on-premises servers en gebruikt deze om de SKU van de VM en schijf in azure aan te bevelen. [Meer informatie over het](concepts-assessment-calculation.md#calculate-sizing-performance-based) verzamelen van gegevens op basis van prestaties.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Waarom toont mijn evaluatie een waarschuwing dat deze is gemaakt met een ongeldige combi natie van gereserveerde instanties, VM-uptime en korting (%)?
Wanneer u gereserveerde instanties selecteert, wordt de korting (%) de eigenschappen van de uptime van de VM zijn niet van toepassing. Als uw evaluatie is gemaakt met een ongeldige combi natie van deze eigenschappen, worden de knoppen bewerken en opnieuw berekenen uitgeschakeld. Maak een nieuwe evaluatie. [Meer informatie](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Ik zie geen prestatie gegevens voor sommige netwerk adapters op mijn fysieke servers

Dit kan gebeuren als Hyper-V-virtualisatie is ingeschakeld op de fysieke server. Als gevolg van een product hiaat worden op deze servers de fysieke en virtuele netwerk adapters door Azure Migrate gedetecteerd. De netwerk doorvoer wordt alleen vastgelegd op de gedetecteerde virtuele netwerk adapters.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>De aanbevolen SKU van Azure VM voor mijn fysieke server is te klein

Dit kan gebeuren als Hyper-V-virtualisatie is ingeschakeld op de fysieke server. Op deze servers detecteert Azure Migrate momenteel de fysieke en virtuele netwerk adapters. Daarom is het nr. van gedetecteerde netwerk adapters is hoger dan werkelijk. Tijdens de evaluatie van Azure-VM'S wordt een virtuele machine van Azure gepickt die het vereiste aantal netwerk adapters kan ondersteunen. Dit kan mogelijk leiden tot een grote VM. Meer [informatie](./concepts-assessment-calculation.md#calculating-sizing) over de impact van Nee. van netwerk adapters bij grootte. Dit is een product hiaat dat vooruit gaat.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>De gereedheids categorie is niet gereed voor mijn fysieke server

De gereedheids categorie kan onjuist zijn gemarkeerd als ' niet gereed ' in het geval van een fysieke server waarop Hyper-V-virtualisatie is ingeschakeld. Op deze servers, vanwege een product hiaat, detecteert Azure Migrate momenteel zowel de fysieke als de virtuele adapters. Daarom is het nr. van gedetecteerde netwerk adapters is hoger dan werkelijk. In zowel on-premises als op prestaties gebaseerde beoordelingen pickt Azure VM-evaluatie een Azure-VM die het vereiste aantal netwerk adapters kan ondersteunen. Als het aantal netwerk adapters wordt gedetecteerd dat deze hoger is dan 32, wordt de maximum waarde Nee. van Nic's die worden ondersteund op virtuele machines van Azure, wordt de server gemarkeerd als ' niet gereed '.  Meer [informatie](./concepts-assessment-calculation.md#calculating-sizing) over de impact van Nee. van Nic's in grootte.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Aantal gedetecteerde Nic's hoger dan werkelijk voor fysieke servers

Dit kan gebeuren als Hyper-V-virtualisatie is ingeschakeld op de fysieke server. Op deze servers detecteert Azure Migrate momenteel zowel de fysieke als de virtuele adapters. Daarom is het nr. van gedetecteerde Nic's is hoger dan werkelijk.

## <a name="dependency-visualization-in-azure-government"></a>Afhankelijkheids visualisatie in Azure Government

Afhankelijkheids analyse op basis van een agent wordt niet ondersteund in Azure Government. Gebruik afhankelijkheids analyse zonder agent.


## <a name="dependencies-dont-show-after-agent-install"></a>Afhankelijkheden worden niet weer gegeven na installatie van agent

Nadat u de afhankelijkheids visualisatie agenten hebt geïnstalleerd op on-premises Vm's, duurt Azure Migrate doorgaans 15-30 minuten om de afhankelijkheden in de portal weer te geven. Als u langer dan 30 minuten hebt gewacht, moet u ervoor zorgen dat de micro soft Monitoring Agent (MMA) verbinding kan maken met de Log Analytics-werk ruimte.

Voor Windows-Vm's:
1. Start MMA in het configuratie scherm.
2. Controleer in de **Eigenschappen van micro soft Monitoring Agent**  >  **Azure log Analytics (OMS)** of de **status** van de werk ruimte groen is.
3. Als de status niet groen is, probeert u de werk ruimte te verwijderen en toe te voegen aan MMA.

    ![MMA-status](./media/troubleshoot-assessment/mma-properties.png)

Voor virtuele Linux-machines moet u ervoor zorgen dat de installatie opdrachten voor MMA en de afhankelijkheids agent zijn geslaagd. Raadpleeg [hier](../azure-monitor/vm/service-map.md#post-installation-issues)voor meer richt lijnen voor probleem oplossing.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- **MMS-agent**: Bekijk de ondersteunde [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)-en [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) -besturings systemen.
- **Afhankelijkheids agent**: de ondersteunde [Windows-en Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) -besturings systemen.

## <a name="visualize-dependencies-for--1-hour"></a>Afhankelijkheden voor > 1 uur visualiseren

Met een afhankelijkheids analyse zonder agent kunt u afhankelijkheden visualiseren of ze exporteren in een kaart voor een duur van Maxi maal 30 dagen.

Met afhankelijkheids analyse op basis van een agent, hoewel Azure Migrate u in staat stelt om terug te gaan naar een bepaalde datum in de afgelopen maand, is de maximale duur waarvoor u de afhankelijkheden kunt visualiseren één uur. U kunt bijvoorbeeld de functie tijd duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt ze alleen bekijken voor een periode van één uur. U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [de afhankelijkheids gegevens](./how-to-create-group-machine-dependencies.md) op te vragen.

## <a name="visualized-dependencies-for--10-servers"></a>Gevisualiseerde afhankelijkheden voor > 10-servers

In Azure Migrate, met afhankelijkheids analyse op basis van een agent, kunt u [afhankelijkheden voor groepen](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) met Maxi maal tien vm's visualiseren. Voor grotere groepen wordt u aangeraden de Vm's te splitsen in kleinere groepen om afhankelijkheden te visualiseren.


## <a name="servers-show-install-agent"></a>Servers ' agent installeren ' weer geven

Nadat u servers met afhankelijkheids visualisatie hebt gemigreerd naar Azure, kunnen servers de actie ' agent installeren ' in plaats van ' afhankelijkheden weer geven ' weer geven vanwege het volgende gedrag:

- Na de migratie naar Azure zijn on-premises servers uitgeschakeld en zijn er vergelijk bare Vm's in Azure. Deze servers halen een ander MAC-adres op.
- Servers kunnen ook een ander IP-adres hebben, op basis van het feit of u het on-premises IP-adres hebt behouden of niet.
- Als zowel MAC-als IP-adressen verschillen van on-premises, Azure Migrate de on-premises servers niet koppelen aan gegevens die Servicetoewijzing afhankelijk zijn. In dit geval wordt de optie voor het installeren van de agent weer gegeven in plaats van het weer geven van afhankelijkheden.
- Na een test migratie naar Azure blijven on-premises servers ingeschakeld zoals verwacht. Gelijkwaardige servers maken deel uit van een ander MAC-adres in Azure en kunnen verschillende IP-adressen verkrijgen. Tenzij u uitgaand Azure Monitor logboek verkeer van deze servers blokkeert, Azure Migrate de on-premises servers niet koppelen aan een Servicetoewijzing afhankelijkheids gegevens, en wordt dus de optie voor het installeren van agents weer gegeven in plaats van afhankelijkheden weer te geven.

## <a name="dependencies-export-csv-shows-unknown-process"></a>CSV-export bevat ' onbekend proces '
In afhankelijkheids analyse zonder agent worden de proces namen op basis van de beste inspanningen vastgelegd. In bepaalde scenario's, hoewel de namen van de bron-en doel server en de doel poort worden vastgelegd, is het niet haalbaar om de proces namen aan beide uiteinden van de afhankelijkheid te bepalen. In dergelijke gevallen wordt het proces als ' onbekend proces ' gemarkeerd.

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate"></a>Mijn Log Analytics-werk ruimte wordt niet weer gegeven bij het configureren van de werk ruimte in Azure Migrate
Azure Migrate biedt momenteel ondersteuning voor het maken van OMS-werkruimte in de regio's VS - oost, Azië - zuidoost en Europa - west. Als de werk ruimte wordt gemaakt buiten Azure Migrate in een andere regio, kan deze momenteel niet worden gekoppeld aan een project.


## <a name="capture-network-traffic"></a>Netwerk verkeer vastleggen

Verzamel logboeken voor netwerk verkeer als volgt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Druk op F12 om Ontwikkelhulpprogramma's te starten. Als dat nodig is, schakelt u de instelling  **vermeldingen wissen bij navigatie** uit.
3. Selecteer het tabblad **netwerk** en begin met het vastleggen van netwerk verkeer:
   - In Chrome selecteert u **logboek behouden**. De opname moet automatisch worden gestart. Een rode cirkel geeft aan dat verkeer wordt vastgelegd. Als de rode cirkel niet wordt weer gegeven, selecteert u de zwarte cirkel om te starten.
   - In micro soft Edge en Internet Explorer moet de opname automatisch worden gestart. Als dat niet het geval is, selecteert u de groene knop afspelen.
4. Probeer de fout te reproduceren.
5. Nadat u de fout hebt aangetroffen tijdens het opnemen, stoppen met opnemen en opslaan van een kopie van de geregistreerde activiteit:
   - Klik in Chrome met de rechter muisknop en selecteer **Opslaan als har met inhoud**. Met deze actie worden de logboeken gecomprimeerd en geëxporteerd als een. har-bestand.
   - Selecteer in micro soft Edge of Internet Explorer de optie **vastgelegd verkeer exporteren** . Met deze actie wordt het logboek gecomprimeerd en geëxporteerd.
6. Selecteer het tabblad **console** om te controleren of er waarschuwingen of fouten zijn. Het console logboek opslaan:
   - Klik in Chrome met de rechter muisknop op een wille keurige plaats in het console logboek. Selecteer **Opslaan als**, om het logboek te exporteren en uit te voeren.
   - Klik in micro soft Edge of Internet Explorer met de rechter muisknop op de fouten en selecteer **Alles kopiëren**.
7. Ontwikkelhulpprogramma's sluiten.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Waar worden de gegevens van het besturings systeem in mijn beoordeling gedetecteerd?

- Voor VMware-Vm's zijn standaard de gegevens van het besturings systeem die door de vCenter worden verschaft. 
   - Als toepassings detectie is ingeschakeld voor VMware Linux-Vm's, worden de details van het besturings systeem opgehaald van de gast-VM. Als u de details van het besturings systeem in de beoordeling wilt controleren, gaat u naar de weer gave gedetecteerde servers en klikt u op de waarde in de kolom besturings systeem. In de tekst die wordt weer gegeven, kunt u zien of de besturingssysteem gegevens die u ziet, worden verzameld van de vCenter-Server of van de gast-VM met behulp van de VM-referenties. 
   - Voor Windows-Vm's worden de details van het besturings systeem altijd opgehaald uit de vCenter Server.
- Voor virtuele Hyper-V-machines worden de gegevens van het besturings systeem verzameld vanaf de Hyper-V-host
- Voor fysieke servers wordt deze opgehaald van de server.

## <a name="next-steps"></a>Volgende stappen

Een evaluatie [maken](how-to-create-assessment.md) of [aanpassen](how-to-modify-assessment.md) .