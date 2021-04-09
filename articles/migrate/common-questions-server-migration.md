---
title: Veelgestelde vragen over Azure Migrate server migratie
description: Krijg antwoorden op veelgestelde vragen over het gebruik van Azure Migrate server migratie om machines te migreren.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 63c7f226dcd99ec8040f2078ce12be0fe3c594df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548808"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migratie van Azure Migrate server: veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over de Azure Migrate: hulp programma voor server migratie. Als u andere vragen hebt, raadpleegt u deze bronnen:

- [Algemene vragen](resources-faq.md) over Azure migrate
- Vragen over het [Azure migrate apparaat](common-questions-appliance.md)
- Vragen over de [visualisatie van detectie, beoordeling en afhankelijkheid](common-questions-discovery-assessment.md)
- Beantwoorde vragen in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum) ontvangen

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Worden Azure Migrate UEFI-machines naar een BIOS-computer geconverteerd en naar Azure gemigreerd als Vm's van Azure Generation 1?
Azure Migrate: met het hulp programma voor migratie van servers worden alle UEFI-machines naar Azure gemigreerd als Vm's van Azure Generation 2. We bieden geen ondersteuning meer voor de conversie van virtuele machines op basis van UEFI naar BIOS-Vm's. Houd er rekening mee dat alle BIOS-computers worden gemigreerd naar Azure als alleen Vm's van Azure-generatie 1.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Hoe kan ik UEFI-machines migreren naar Azure als Vm's van Azure Generation 1?
Azure Migrate: met het hulp programma voor server migratie worden UEFI-machines naar Azure gemigreerd als Vm's van Azure Generation 2. Als u ze wilt migreren naar virtuele machines van Azure-generatie 1, converteert u het opstart type naar BIOS voordat u begint met de replicatie, en gebruikt u vervolgens het Azure Migrate: hulp programma voor server migratie om naar Azure te migreren.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Welke besturings systemen worden ondersteund voor migratie van UEFI-machines naar Azure?

| **Besturings systemen die worden ondersteund voor UEFI-machines** | **VMware naar Azure zonder agent**                                                                                                             | **Hyper-V naar Azure zonder agent** | **VMware-, fysieke en andere Clouds op basis van agents naar Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 2012                | J                                                                                                                                         | J                              | J                                                          |
| Windows 10 Pro, Windows 10 Enter prise                   | J                                                                                                                                         | J                              | J                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | J                                                                                                                                         | J                              | J                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | J                                                                                                                                         | J                              | J                                                          |
| Ubuntu Server 16,04, 18,04, 19,04, 19,10                | J                                                                                                                                         | J                              | J                                                          |
| RHEL 8,1, 8,0, 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x        | J<br>                 _RHEL 8. x vereist [hand matige voor bereiding](./prepare-for-migration.md#linux-machines)_   | J                              | J                                                          |
| Cent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 6. x               | J<br>_Cent OS 8. x vereist [hand matige voor bereiding](./prepare-for-migration.md#linux-machines)_ | J                              | J                                                          |
| Oracle Linux 7.7, 7.7CI                                |  J                                                                                                                                        | J                              | J                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Kan ik de Recovery Services-kluis gebruiken die is gemaakt door Azure Migrate voor scenario's voor herstel na nood gevallen?
Het is niet raadzaam om de Recovery Services-kluis te gebruiken die is gemaakt door Azure Migrate voor scenario's voor herstel na nood gevallen. Dit kan leiden tot problemen met de replicatie in Azure Migrate. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Waar moet ik het replicatie apparaat installeren voor migraties op basis van een agent?

Het replicatie apparaat moet op een toegewezen computer worden geïnstalleerd. Het replicatieapparaat mag niet worden geïnstalleerd op een bronmachine die u wilt repliceren of op het detectie- en beoordelingsapparaat voor Azure Migrate dat u eerder hebt geïnstalleerd. Volg de [zelf studie](./tutorial-migrate-physical-virtual-machines.md) voor meer informatie.

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Hoe kan ik mijn AWS EC2-exemplaren migreren naar Azure?

Lees dit [artikel](./tutorial-migrate-aws-virtual-machines.md) voor het detecteren, evalueren en migreren van uw AWS EC2-instanties naar Azure.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Kan ik AWS-Vm's waarop het besturings systeem van Amazon Linux wordt uitgevoerd, migreren?

Vm's waarop Amazon Linux wordt uitgevoerd, kunnen niet worden gemigreerd alsof het besturings systeem van Amazon Linux alleen wordt ondersteund op AWS.
Om workloads die op Amazon Linux draaien te migreren, kunt u een CentOS/RHEL VM in Azure opstarten en de workload die op de AWS Linux-machine draait migreren met een relevante workload-migratiebenadering. Afhankelijk van de werkbelasting kunnen er bijvoorbeeld werkbelastingspecifieke tools zijn om de migratie te ondersteunen, zoals databases of implementatietools in het geval van webservers.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Welke geografische gebieden worden ondersteund voor migratie met Azure Migrate?

Bekijk de ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Kunnen we hetzelfde Azure Migrate project gebruiken om te migreren naar meerdere regio's?

Hoewel u evaluaties kunt maken voor meerdere regio's in een Azure Migrate project, kan één Azure Migrate project worden gebruikt voor het migreren van servers naar slechts één Azure-regio. U kunt aanvullende Azure Migrate projecten maken voor elke regio waarnaar u wilt migreren.

- Voor VMware-migraties zonder agent is de doel regio vergrendeld wanneer u de eerste replicatie inschakelt.
- Voor migraties op basis van een agent (VMware, fysieke servers en servers uit andere Clouds) wordt de doel regio vergrendeld zodra op de knop resources maken wordt geklikt op de portal tijdens het instellen van het replicatie apparaat.
- Voor Hyper-V-migraties zonder agent wordt de doel regio vergrendeld zodra op de knop resources maken wordt geklikt op de portal tijdens het instellen van de Hyper-V-replicatie provider.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Kunnen we hetzelfde Azure Migrate project gebruiken om te migreren naar meerdere abonnementen? 

Ja, u kunt migreren naar meerdere abonnementen in dezelfde doel regio voor een Azure Migrate-project. U kunt het doel abonnement selecteren terwijl u replicatie inschakelt voor een machine of een set machines. De doel regio is vergrendeld na de eerste replicatie voor VMware-migraties zonder agent en tijdens de installatie van het replicatie apparaat en de Hyper-V-provider voor migraties op basis van agents en Hyper-V-migraties zonder agent.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Wat zijn de migratie opties in Azure Migrate: Server migratie?

Het Azure Migrate: hulp programma voor server migratie biedt twee opties voor het uitvoeren van migraties van uw bron servers/Vm's naar Azure, migratie zonder agents en migratie op basis van een agent.

Ongeacht de gekozen migratie optie, de eerste stap voor het migreren van een server met Azure Migration: Server migratie is het inschakelen van replicatie voor de server. Dit voert een initiële replicatie van uw VM/Server-gegevens uit naar Azure. Nadat de initiële replicatie is voltooid, wordt er een voortdurende replicatie (voortdurende Delta-synchronisatie) tot stand gebracht om incrementele gegevens naar Azure te migreren. Zodra de bewerking de Delta-synchronisatie fase bereikt, kunt u op elk gewenst moment migreren naar Azure.  

Hier volgen enkele aandachtspunten waarmee u rekening moet houden bij het kiezen van de migratie optie.

Voor **migraties zonder agent** zijn geen software (agents) vereist die worden geïmplementeerd op de bron-vm's en-servers die worden gemigreerd. De optie zonder agent organiseert de replicatie door te integreren met de functionaliteit van de virtualisatiehost.
De replicatie opties zonder agent zijn beschikbaar voor [virtuele VMware-machines](./tutorial-migrate-vmware.md) en [virtuele Hyper-V-machines](./tutorial-migrate-hyper-v.md).

Voor **migraties op basis van een agent** moeten Azure migrate software (agents) worden geïnstalleerd op de bron-vm's en machines die moeten worden gemigreerd. De op agents gebaseerde optie is niet afhankelijk van het virtualisatieplatform voor de replicatie functionaliteit en kan daarom worden gebruikt met elke server met een x86/x64-architectuur en een versie van een besturings systeem die wordt ondersteund door de replicatie methode op basis van een agent.

Migratie op basis van een agent kan worden gebruikt voor [virtuele VMware-machines](./tutorial-migrate-vmware-agent.md), [Hyper-V-vm's](./tutorial-migrate-physical-virtual-machines.md), [fysieke servers](./tutorial-migrate-physical-virtual-machines.md), [virtuele machines die worden uitgevoerd op AWS](./tutorial-migrate-aws-virtual-machines.md), virtuele machines die worden uitgevoerd op GCP of vm's die worden uitgevoerd op een andere virtualisatiehost. De migratie op basis van een agent behandelt uw computers als fysieke servers voor het doel van de migratie.

Hoewel de migratie zonder agent een extra gemak en eenvoud biedt over de replicatie opties op basis van de agent voor de ondersteunde scenario's (VMWare en Hyper-V), kunt u overwegen het scenario op basis van de agent te gebruiken voor de volgende gebruiks voorbeelden:

- Beperkte IOPS-omgeving: replicatie zonder agent maakt gebruik van moment opnamen en verbruikt opslag-IOPS/band breedte. We raden u aan de migratie methode op basis van een agent uit te voeren als er beperkingen gelden voor opslag/IOPS in uw omgeving.
- Als u geen vCenter Server hebt, kunt u uw virtuele VMware-machines als fysieke servers behandelen en de op agents gebaseerde migratie werk stroom gebruiken.

Raadpleeg dit [artikel](./server-migrate-overview.md) voor meer informatie over het vergelijken van migratie opties voor VMware-migraties.

## <a name="how-does-agentless-migration-work"></a>Hoe werkt migratie zonder agent?

Azure Migrate: Server migratie biedt replicatie opties zonder agent voor de migratie van virtuele VMware-machines en virtuele Hyper-V-machines met Windows of Linux. Het hulp programma biedt ook een extra replicatie optie op basis van een agent voor Windows-en Linux-servers die kunnen worden gebruikt voor het migreren van fysieke servers, evenals x86/x64 virtuele machines op VMware, Hyper-V, AWS, GCP, enzovoort. De replicatie optie op basis van een agent vereist de installatie van de agent software op de server/virtuele machine die wordt gemigreerd, terwijl in de optie zonder agent geen software moet worden geïnstalleerd op de virtuele machines zelf, waardoor er extra gemak en eenvoud worden geboden over de replicatie optie op basis van een agent.

De optie replicatie zonder agent werkt met behulp van mechanismen van de Virtualization provider (VMware, Hyper-V). In het geval van virtuele VMware-machines maakt het replicatie mechanisme zonder agent gebruik van VMware-moment opnamen en de VMware-wijzigings technologie voor blok tracering om gegevens van virtuele-machine schijven te repliceren. Dit mechanisme is vergelijkbaar met de methode die wordt gebruikt door veel back-upproducten. In het geval van virtuele Hyper-V-machines, maakt het replicatie mechanisme zonder agent gebruik van VM-moment opnamen en de mogelijkheid voor het bijhouden van wijzigingen van de Hyper-V replica om gegevens van schijven van virtuele machines te repliceren.

Wanneer replicatie voor een virtuele machine is geconfigureerd, wordt eerst een initiële replicatie fase door lopen. Tijdens de eerste replicatie wordt een VM-moment opname gemaakt en een volledige kopie van gegevens van de momentopname schijven worden gerepliceerd naar beheerde schijven in uw abonnement. Nadat de initiële replicatie voor de virtuele machine is voltooid, wordt het replicatie proces overgezet naar een incrementele replicatie fase (Delta replicatie). In de incrementele replicatie fase worden gegevens wijzigingen die zich hebben voorgedaan sinds de laatste voltooide replicatie cyclus periodiek gerepliceerd en toegepast op de door de replica beheerde schijven, waardoor replicatie synchroon wordt met de wijzigingen die op de virtuele machine optreden. In het geval van virtuele VMware-machines wordt de VMware-functie voor het bijhouden van blokken gebruikt om wijzigingen tussen replicatie cycli bij te houden. Aan het begin van de replicatie cyclus wordt een moment opname van de virtuele machine gemaakt en wordt de functie voor het bijhouden van wijzigingen gebruikt om de veranderingen tussen de huidige moment opname en de laatste geslaagde moment opname op te halen. Op die manier moeten alleen gegevens die zijn gewijzigd sinds de laatste voltooide replicatie cyclus worden gerepliceerd om de replicatie voor de virtuele machine synchroon te laten blijven. Aan het einde van elke replicatie cyclus wordt de moment opname vrijgegeven en wordt er momentopname consolidatie uitgevoerd voor de virtuele machine. Op dezelfde manier wordt, in het geval van virtuele Hyper-V-machines, de Hyper-V replica-engine voor het bijhouden van replica's gebruikt om wijzigingen tussen opeenvolgende replicatie cycli bij te houden.
Wanneer u de migratie bewerking uitvoert op een virtuele machine die wordt gerepliceerd, hebt u de optie om de on-premises virtuele machine af te sluiten en een definitieve incrementele replicatie uit te voeren om ervoor te zorgen dat er geen gegevens verloren gaan. Bij het uitvoeren van de optie voor migreren worden de beheerde replica schijven die overeenkomen met de virtuele machine, gebruikt om de virtuele machine in azure te maken.

Als u aan de slag wilt gaan, raadpleegt u de zelf studies [VMware-agentloze migratie](./tutorial-migrate-vmware.md) en [Hyper-V zonder agent migratie](./tutorial-migrate-hyper-v.md) .

## <a name="how-does-agent-based-migration-work"></a>Hoe werkt de migratie op basis van een agent?

Naast de migratie opties zonder agent voor virtuele VMware-machines en Hyper-V-machines, biedt het hulp programma voor server migratie een migratie optie op basis van een agent voor het migreren van Windows-en Linux-servers die worden uitgevoerd op fysieke servers of die worden uitgevoerd als x86/x64-machines op VMware, Hyper-V, AWS, Google Cloud Platform, enzovoort.

De op agents gebaseerde migratie methode maakt gebruik van agent software die is geïnstalleerd op de server die wordt gemigreerd om Server gegevens te repliceren naar Azure. Het replicatie proces maakt gebruik van een offload-architectuur waarbij de agent replicatie gegevens doorstuurt naar een toegewezen replicatie server, het replicatie apparaat of de configuratie server (of een scale-out proces server). Meer [informatie](./agent-based-migration-architecture.md) over hoe de op agents gebaseerde migratie optie werkt. 

Opmerking: het replicatie apparaat verschilt van het Azure Migrate detectie apparaat en moet worden geïnstalleerd op een afzonderlijke/toegewezen computer.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Hoe kan ik de bandbreedte vereiste voor mijn migraties meten?

Band breedte voor de replicatie van gegevens naar Azure is afhankelijk van een aantal factoren en is een functie van hoe snel het on-premises Azure Migrate apparaat de gegevens kan lezen en repliceren naar Azure. Replicatie heeft twee fasen: initiële replicatie en replicatie van verschillen.

Wanneer replicatie voor een virtuele machine wordt gestart, treedt er een initiële replicatie cyclus op waarin volledige kopieën van de schijven worden gerepliceerd. Nadat de initiële replicatie is voltooid, worden incrementele replicatie cycli (Delta cycli) periodiek gepland voor het overdragen van wijzigingen die hebben plaatsgevonden sinds de vorige replicatie cyclus.

### <a name="agentless-vmware-vm-migration"></a>Migratie van VMware VM zonder agent

U kunt de vereiste band breedte op basis van het volume van de gegevens die moeten worden verplaatst in de Wave en de tijd waarop de eerste replicatie moet worden voltooid (in het ideale geval moet de initiële replicatie ten minste 3-4 dagen voorafgaand aan het huidige migratie venster) hebben voltooid, zodat u voldoende tijd hebt om een test migratie vóór het werkelijke venster uit te voeren en de uitval tijd tot een minimum te beperken

Met de volgende formule kunt u een schatting maken van de band breedte of tijd die nodig is voor de migratie van agentloze VMware-VM'S:

Tijd voor het volt ooien van de initiële replicatie = {grootte van de schijven (of de gebruikte grootte indien beschikbaar) * 0,7 (uitgaande van een gemiddelde compressie van 30 procent – conservatieve schatting)}/Bandwidth beschikbaar voor replicatie.

### <a name="agent-based-vmware-vm-migration"></a>Migratie van VMware VM op basis van een agent

Voor een replicatie methode op basis van een agent kan de implementatie Planner u helpen bij het profileren van de omgeving voor het verloop van de gegevens en helpt u de benodigde bandbreedte vereisten te voors pellen. Raadpleeg dit [artikel](./agent-based-migration-architecture.md#plan-vmware-deployment)voor meer informatie. 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Hoe kan ik beperking van de replicatie in het gebruik van Azure Migrate apparaat voor VMware-replicatie zonder agent?  

U kunt dit beperken met behulp van NetQosPolicy. Bijvoorbeeld:

De AppNamePrefix die in de NetQosPolicy moet worden gebruikt, is "GatewayWindowsService.exe". U kunt een beleid maken op het Azure Migrate apparaat om het replicatie verkeer van het apparaat te beperken door een beleid te maken, bijvoorbeeld:

New-NetQosPolicy naam ' ThrottleReplication '-AppPathNameMatchCondition ' GatewayWindowsService.exe '-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Hoe worden de gegevens verzonden vanuit een on-premises omgeving naar Azure? Versleuteld vóór verzen ding?

Het Azure Migrate apparaat in de replicatie zonder agent zorgt ervoor dat gegevens worden gecomprimeerd en versleuteld voordat ze worden geüpload. Gegevens worden verzonden via een beveiligd communicatie kanaal via https en maakt gebruik van TLS 1,2 of hoger. Daarnaast worden uw gegevens automatisch door Azure Storage versleuteld wanneer deze persistent worden gemaakt in de Cloud (versleuteling-at-rest).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Wat is de invloed van het verloop tempo op replicatie zonder agent?

Het *verloop patroon* is belang rijker dan het *verloop tempo*, omdat replicatie van zonder agents in gegevens wordt gevouwen. Wanneer een bestand opnieuw wordt geschreven, heeft de frequentie geen veel invloed meer. Een patroon waarbij elke andere sector wordt geschreven, veroorzaakt echter een hoog verloop in de volgende cyclus. Omdat we de hoeveelheid gegevens die we overdragen minimaliseert, kunnen we de gegevens zo veel mogelijk vouwen voordat we de volgende cyclus plannen.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Hoe vaak is een replicatie cyclus gepland?

De formule voor het plannen van de volgende replicatie cyclus is (vorige cyclus tijd/2) of één uur, afhankelijk van wat hoger is.

Als een virtuele machine bijvoorbeeld vier uur duurt voor een Delta cyclus, wordt de volgende cyclus gepland in twee uur en niet in het volgende uur. Het proces wijkt direct na de initiële replicatie af wanneer de eerste Delta cyclus onmiddellijk wordt gepland.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Hoe kan ik Windows Server 2003 die op VMware/Hyper-V wordt uitgevoerd, migreren naar Azure?

De [uitgebreide ondersteuning voor Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) is gestopt op 14 juli 2015.  Het ondersteunings team van Azure blijft hulp bij het oplossen van problemen met het uitvoeren van Windows Server 2003 op Azure. Deze ondersteuning is echter beperkt tot problemen waarvoor geen probleem oplossing of patches op besturingssysteem niveau is vereist.
Het migreren van uw toepassingen naar Azure-exemplaren met een nieuwere versie van Windows Server is de aanbevolen methode om ervoor te zorgen dat u de flexibiliteit en betrouw baarheid van de Azure-Cloud effectief kunt benutten.

Als u echter nog steeds uw Windows Server 2003 naar Azure migreert, kunt u de Azure Migrate: hulp programma voor server migratie gebruiken als uw Windows Server een VM is die wordt uitgevoerd op VMware of Hyper-V raadpleegt u dit artikel om [uw Windows Server 2003-computers voor te bereiden op migratie](./prepare-windows-server-2003-migration.md).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Wat is het verschil tussen de migratie-en migratie bewerkingen voor de test?

Test migratie biedt een manier om migraties te testen en te valideren vóór de daad werkelijke migratie. Test migratie werkt door het maken van test kopieën van het repliceren van Vm's in een sandbox-omgeving in Azure. De sandbox-omgeving wordt afgebakend door een test-virtueel netwerk dat u opgeeft. De test migratie bewerking is niet storend, terwijl toepassingen worden uitgevoerd in de bron, terwijl u tests uitvoert op een gekloonde kopie in een geïsoleerde sandbox-omgeving. U kunt zo nodig meerdere tests uitvoeren voor het valideren van de migratie, het testen van apps uitvoeren en eventuele problemen oplossen vóór de daad werkelijke migratie.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Worden Windows Server 2008 en 2008 R2 ondersteund in azure na de migratie?

U kunt uw on-premises Windows Server 2008-en 2008 R2-servers naar Azure virtual machines migreren en uitgebreide beveiligings updates voor drie jaar na het einde van de ondersteunings datums ophalen zonder extra kosten boven de kosten van het uitvoeren van de virtuele machine. U kunt het Azure Migrate: hulp programma voor server migratie gebruiken om de workloads van Windows Server 2008 en 2008 R2 te migreren.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Is er een terugdraai optie voor Azure Migrate?

U kunt de optie migratie testen gebruiken om de functionaliteit en prestaties van uw toepassing in azure te valideren. U kunt een wille keurig aantal test migraties uitvoeren en de definitieve migratie uitvoeren nadat u het vertrouwen hebt ingesteld via de test migratie bewerking. Een test migratie heeft geen invloed op de on-premises computer. deze blijft operationeel en gaat door met repliceren totdat u de daad werkelijke migratie uitvoert. Als er fouten zijn opgetreden tijdens de test migratie bedoeld, kunt u ervoor kiezen de definitieve migratie uit te stellen en de bron-VM/-server actief te laten zijn en te repliceren naar Azure. U kunt de definitieve migratie opnieuw proberen zodra de fouten zijn opgelost.  
Opmerking: wanneer u een definitieve migratie naar Azure hebt uitgevoerd en de on-premises bron machine is afgesloten, kunt u geen terugdraaien van Azure uitvoeren naar uw on-premises omgeving.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Kan ik de Virtual Network en het subnet selecteren dat moet worden gebruikt voor test migraties?

U kunt een Virtual Network selecteren voor test migraties. Het subnet wordt automatisch geselecteerd op basis van de volgende logica:

- Als er een doel-subnet (anders dan standaard) is opgegeven als invoer tijdens het inschakelen van replicatie, Azure Migrate prioriteits aanduidingen met een subnet met dezelfde naam in de Virtual Network geselecteerd voor de test migratie.
- Als het subnet met dezelfde naam niet wordt gevonden, selecteert Azure Migrate het eerste beschik bare subnet alfabetisch dat geen gateway/Application Gateway/firewall-Bastion-subnet is.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Waarom is de knop migratie testen uitgeschakeld voor mijn server?

De test migratie knop kan in de volgende scenario's een uitgeschakelde status hebben:

- U kunt pas een test migratie starten als er een initiële replicatie (IR) is voltooid voor de virtuele machine. De test migratie knop wordt uitgeschakeld totdat het IR-proces is voltooid. U kunt een test migratie uitvoeren wanneer de virtuele machine zich in een Delta-synchronisatie fase bevindt.
- De knop kan worden uitgeschakeld als er al een test migratie is voltooid, maar er is geen opruiming voor de test migratie uitgevoerd voor die VM. Voer een opschoon bewerking voor het testen van de migratie uit en probeer het opnieuw.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>Wat gebeurt er als ik mijn test migratie niet opschoon?

Met test migratie wordt de werkelijke migratie gesimuleerd door een Azure VM met een test te maken met gerepliceerde gegevens. De server wordt geïmplementeerd met een Point-in-time kopie van de gerepliceerde gegevens naar de doel resource groep (geselecteerd tijdens het inschakelen van de replicatie) met het achtervoegsel '-test '. Test migraties zijn bedoeld voor het valideren van de server functionaliteit, zodat bericht migratie problemen worden geminimaliseerd. Als de test migratie niet wordt opgeruimd, wordt de test-virtuele machine nog steeds uitgevoerd in Azure en worden er kosten in rekening gebracht. Als u een test migratie wilt opschonen, gaat u naar de weer gave replicerende computers in het hulp programma voor migratie van servers en gebruikt u de actie test migratie opschonen op de computer.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Kan ik Active Directory domein controllers met behulp van Azure Migrate migreren?

Het hulp programma voor server migratie is neutraal van toepassingen en werkt voor de meeste toepassingen. Wanneer u een server migreert met het hulp programma voor server migratie, worden alle toepassingen die op de server zijn geïnstalleerd, samen met de-service gemigreerd. Voor sommige toepassingen kunnen alternatieve migratie methoden dan server migratie echter beter geschikt zijn voor de migratie.  Voor Active Directory, in het geval van hybride omgevingen waarin de on-premises site is verbonden met uw Azure-omgeving, kunt u uw Directory uitbreiden naar Azure door extra domein controllers toe te voegen aan Azure en Active Directory-replicatie in te stellen. Als u migreert naar een geïsoleerde omgeving in azure waarvoor een eigen domein controller (of het testen van toepassingen in een sandbox-omgeving) is vereist, kunt u servers migreren met het hulp programma voor server migratie.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>Wat gebeurt er als de replicatie na de migratie niet wordt gestopt?

Wanneer u de replicatie stopt, worden met de Azure Migrate: hulp programma voor server migratie de beheerde schijven opgeschoond in het abonnement dat voor replicatie is gemaakt. Als u replicatie na een migratie niet stopt, worden er nog steeds kosten in rekening gebracht voor deze schijven. Stoppen van replicatie heeft geen invloed op de schijven die zijn gekoppeld aan computers die al zijn gemigreerd.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Heb ik VMware vCenter nodig voor het migreren van virtuele VMware-machines?

Als u [virtuele VMware-machines wilt migreren](server-migrate-overview.md) met behulp van een VMware-agent of een agentloze migratie, moeten ESXi-hosts waarop vm's zich bevinden, worden beheerd door vCenter Server. Als u niet beschikt over vCenter Server, kunt u virtuele VMware-machines migreren door ze te migreren als fysieke servers. [Meer informatie](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>Kan ik mijn besturings systeem upgraden tijdens de migratie?

Het Azure Migrate: hulp programma voor server migratie ondersteunt op dit moment alleen migraties zoals-like. Het hulp programma biedt geen ondersteuning voor de upgrade van de versie van het besturings systeem tijdens de migratie. De gemigreerde machine heeft hetzelfde besturings systeem als de bron machine.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Ik heb twee (of meer) apparaten geïmplementeerd om Vm's in mijn vCenter Server te detecteren. Als ik de Vm's echter probeer te migreren, zie ik alleen Vm's die overeenkomen met een van de apparaten.

Als er meerdere apparaten zijn ingesteld, mag er geen overlap bestaan tussen de virtuele machines op de opgegeven vCenter-accounts. Een detectie met een dergelijke overlap is een niet-ondersteund scenario.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Hoe kan ik weet of mijn virtuele machine is gemigreerd?

Nadat u uw virtuele machine/server hebt gemigreerd, kunt u de virtuele machine bekijken en beheren vanaf de pagina Virtual Machines. Maak verbinding met de gemigreerde VM om te valideren.
U kunt ook de ' taak status ' controleren om te controleren of de migratie is voltooid. Als er fouten worden weer geven, lost u deze op en voert u de migratie bewerking opnieuw uit.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Kan ik meerdere bron-Vm's samen voegen tot één virtuele machine tijdens de migratie?

Azure Migrate mogelijkheden voor server migratie ondersteunen, zoals migraties op dit moment. We bieden geen ondersteuning voor het samen voegen van servers of het upgraden van het besturings systeem als onderdeel van de migratie. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Hoe heeft replicatie zonder agent invloed op VMware-servers?

Replicatie zonder agent resulteert in een invloed op de prestaties van VMware vCenter Server en VMware ESXi hosts. Omdat replicatie zonder agent gebruik maakt van moment opnamen, verbruikt de service IOPS op opslag, zodat er een aantal IOPS-opslag bandbreedte vereist is. Het wordt niet aangeraden replicatie zonder agent te gebruiken als u beperkingen hebt voor opslag of IOPs in uw omgeving.


## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).
