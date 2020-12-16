---
title: Veelgestelde vragen over VMware-nood herstel met Azure Site Recovery
description: Krijg antwoorden op veelgestelde vragen over herstel na nood gevallen van on-premises virtuele VMware-machines naar Azure met behulp van Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: a272486eea111ab8c8e489556986f12f382e3f65
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587789"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Algemene vragen over replicatie van VMware naar Azure

In dit artikel vindt u antwoorden op veelgestelde vragen die mogelijk worden gemaakt wanneer u herstel na nood gevallen van on-premises virtuele VMware-machines (Vm's) naar Azure implementeert.

## <a name="general"></a>Algemeen

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Wat heb ik nodig voor een nood herstel van de virtuele VMware-machines?

[Meer informatie over de onderdelen die zijn betrokken](vmware-azure-architecture.md) bij herstel na nood gevallen van virtuele VMware-machines.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Kan ik Site Recovery gebruiken om virtuele VMware-machines te migreren naar Azure?

Ja. Naast het gebruik van Site Recovery voor het instellen van een volledig herstel na nood geval voor virtuele VMware-machines, kunt u ook Site Recovery gebruiken om on-premises virtuele VMware-machines te migreren naar Azure. In dit scenario repliceert u on-premises VMware-Vm's naar Azure Storage. Vervolgens voert u een failover uit van on-premises naar Azure. Na een failover zijn uw apps en workloads beschikbaar en worden deze uitgevoerd op virtuele Azure-machines. Het proces is vergelijkbaar met het instellen van een volledige nood herstel bewerking, behalve dat in een migratie geen failback kan worden uitgevoerd vanuit Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Heeft mijn Azure-account machtigingen nodig om Vm's te maken?

Als u een abonnements beheerder bent, hebt u de benodigde replicatie machtigingen. Als u geen beheerder bent, hebt u machtigingen nodig om de volgende acties uit te voeren:

- Maak een Azure VM in de resource groep en het virtuele netwerk dat u opgeeft wanneer u Site Recovery configureert.
- Schrijven naar het geselecteerde opslag account of de beheerde schijf op basis van uw configuratie.

Meer [informatie](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) over de vereiste machtigingen.

### <a name="what-applications-can-i-replicate"></a>Welke toepassingen kan ik repliceren?

U kunt elke app of workload repliceren die wordt uitgevoerd op een virtuele VMware-machine die voldoet aan de [replicatie vereisten](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery ondersteunt toepassings bewuste replicatie, zodat er failover kan worden uitgevoerd voor apps en een intelligente status kan worden hersteld.
- Site Recovery integreert met micro soft-toepassingen, zoals share point, Exchange, Dynamics, SQL Server en Active Directory. Het werkt ook nauw samen met toonaangevende leveranciers zoals Oracle, SAP, IBM en Red Hat.

Lees [hier](site-recovery-workload.md) meer informatie over workloadbeveiliging.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Kan ik een gast besturingssysteem Server licentie gebruiken in azure?

Ja, klanten met micro soft-Software Assurance kunnen [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) besparen op licentie kosten voor Windows Server-computers die worden gemigreerd naar Azure, of Azure gebruiken voor herstel na nood gevallen.

## <a name="security"></a>Beveiliging

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Welke toegang tot VMware-servers heeft Site Recovery nodig?

Site Recovery moet toegang hebben tot de VMware-servers om het volgende te kunnen doen:

- Stel een virtuele VMware-machine in waarop de Site Recovery-configuratie server wordt uitgevoerd.
- Vm's automatisch detecteren voor replicatie.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Welke toegang tot de VMware-Vm's heeft Site Recovery nodig?

- Als u wilt repliceren, moet de Site Recovery Mobility-service zijn geïnstalleerd en worden uitgevoerd op een virtuele VMware-machine. U kunt het hulp programma hand matig implementeren of u kunt opgeven dat Site Recovery een push-installatie van de service uitvoert wanneer u replicatie inschakelt voor een virtuele machine.
- Tijdens de replicatie communiceert Vm's met Site Recovery als volgt:
    - Vm's communiceren met de configuratie server op HTTPS-poort 443 voor replicatie beheer.
    - Vm's verzenden replicatie gegevens naar de proces server op HTTPS-poort 9443. (Deze instelling kan worden gewijzigd.)
    - Als u multi-VM-consistentie inschakelt, communiceren Vm's met elkaar via poort 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Worden er replicatie gegevens verzonden naar Site Recovery?

Nee, Site Recovery worden geen gerepliceerde gegevens onderschept en geen informatie over wat er op uw Vm's wordt uitgevoerd. Replicatie gegevens worden uitgewisseld tussen VMware-Hyper visors en Azure Storage. Met Site Recovery kunnen deze gegevens niet worden onderschept. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.

Site Recovery is gecertificeerd voor ISO 27001:2013 en 27018, HIPAA en DPA. Het is in het proces van SOC2-en FedRAMP JAB-evaluaties.

## <a name="pricing"></a>Prijzen

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hoe kan ik geschatte kosten voor VMware-nood herstel berekenen?

Gebruik de [prijs calculator](https://aka.ms/asr_pricing_calculator) om kosten te ramen tijdens het gebruik van site Recovery.

Voor een gedetailleerde schatting van de kosten voert u het hulp programma Deployment planner voor [VMware](./site-recovery-deployment-planner.md) uit en gebruikt u het [rapport kosten raming](./site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Is er sprake van een verschil in kosten tussen replicatie naar opslag of rechtstreeks naar Managed disks?

Beheerde schijven worden iets anders in rekening gebracht dan opslag accounts. Meer [informatie](https://azure.microsoft.com/pricing/details/managed-disks/) over prijzen voor beheerde schijven.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Zijn er kosten verbonden aan het repliceren van Algemeen v2-opslag account?

Normaal gesp roken ziet u een toename van de kosten voor trans acties die zijn gemaakt voor GPv2-opslag accounts, omdat Azure Site Recovery trans acties zwaar is. [Meer informatie](../storage/common/storage-account-upgrade.md#pricing-and-billing) over het schatten van de wijziging.

## <a name="mobility-service"></a>Mobility-service

### <a name="where-can-i-find-the-mobility-service-installers"></a>Waar vind ik de installatie Programma's van de Mobility-service?

De installatie Programma's bevinden zich in de map%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository op de configuratie server.

## <a name="how-do-i-install-the-mobility-service"></a>Hoe kan ik de Mobility-service installeren?

Op elke virtuele machine die u wilt repliceren, installeert u de service op een van de volgende manieren:

- [Push-installatie](vmware-physical-mobility-service-overview.md#push-installation)
- [Hand matige installatie](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) vanuit de gebruikers interface of Power shell
- Implementatie met behulp van een implementatie programma zoals [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Managed Disks

### <a name="where-does-site-recovery-replicate-data-to"></a>Waar worden gegevens Site Recovery gerepliceerd?

Site Recovery repliceert on-premises virtuele VMware-machines en fysieke servers naar Managed disks in Azure.

- De Site Recovery process server schrijft replicatie logboeken naar een cache-opslag account in de doel regio.
- Deze logboeken worden gebruikt voor het maken van herstel punten op Azure-beheerde schijven met een voor voegsel van **asrseeddisk**.
- Als er een failover optreedt, wordt het door u geselecteerde herstel punt gebruikt voor het maken van een nieuwe beheerde doel schijf. Deze beheerde schijf is gekoppeld aan de virtuele machine in Azure.
- Vm's die eerder zijn gerepliceerd naar een opslag account (vóór maart 2019), worden niet beïnvloed.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Kan ik nieuwe machines repliceren naar opslag accounts?

Nee. Vanaf maart 2019 kunt u in de Azure Portal alleen repliceren naar Azure Managed disks.

De replicatie van nieuwe Vm's naar een opslag account is alleen beschikbaar via Power shell ([AZ. Recovery Services module versie 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) of de rest API (versie 2018-01-10 of 2016-08-10). [Meer informatie over](./vmware-azure-disaster-recovery-powershell.md) het instellen van replicatie met behulp van de Power shell-opdrachten.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Wat zijn de voor delen van repliceren naar Managed disks?

[Meer informatie](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery vereenvoudigt nood herstel met beheerde schijven.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Kan ik het type van de beheerde schijf wijzigen nadat een machine is beveiligd?

Ja, u kunt eenvoudig [het type beheerde schijf wijzigen](../virtual-machines/windows/convert-disk-storage.md) voor doorlopende replicaties. Voordat u het type wijzigt, moet u ervoor zorgen dat er geen URL voor gedeelde toegangs handtekening wordt gegenereerd op de beheerde schijf:

1. Ga naar de resource voor **beheerde schijven** op de Azure Portal en controleer of u een URL-banner voor gedeelde toegang hebt op de Blade **overzicht** .
1. Als de banner aanwezig is, selecteert u deze om de lopende export te annuleren.
1. Wijzig het type van de schijf in de volgende paar minuten. Als u het type van de beheerde schijf wijzigt, wacht u totdat nieuwe herstel punten zijn gegenereerd door Azure Site Recovery.
1. Gebruik de nieuwe herstel punten voor een testfailover of failover in de toekomst.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Kan ik replicatie van beheerde schijven naar onbeheerde schijven overschakelen?

Nee. Overschakelen van beheerd naar niet-beheerd wordt niet ondersteund.

## <a name="replication"></a>Replicatie

### <a name="what-are-the-replicated-vm-requirements"></a>Wat zijn de vereisten voor de replicatie van de virtuele machine?

Meer [informatie](vmware-physical-azure-support-matrix.md#replicated-machines) over de ondersteunings vereisten voor virtuele VMware-machines en fysieke servers.

### <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?

Replicatie is doorlopend bij het repliceren van virtuele VMware-machines naar Azure.

### <a name="can-i-extend-replication"></a>Kan ik replicatie uitbreiden?

Uitgebreide of gekoppelde replicatie wordt niet ondersteund. Vraag deze functie aan in het [Feedback forum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="how-can-i-track-progress-of-initial-replicationsynchronization"></a>Hoe kan ik de voortgang van de initiële replicatie/synchronisatie volgen?

Deze mogelijkheid is onlangs Site Recovery Services. Werk uw Site Recovery-infra structuur (configuratie servers, scale-out proces servers) en de Mobility-agent bij naar versie 9,36 of hoger om nauw keurige details te krijgen. Meer informatie over hoe u [de voortgang kunt volgen.](vmware-azure-enable-replication.md#monitor-initial-replication)

### <a name="can-i-do-an-offline-initial-replication"></a>Kan ik een offline initiële replicatie uitvoeren?

Offline replicatie wordt niet ondersteund. Vraag deze functie aan in het [Feedback forum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Wat is asrseeddisk?

Voor elke bron schijf worden gegevens gerepliceerd naar een beheerde schijf in Azure. Deze schijf heeft het voor voegsel van **asrseeddisk**. Hiermee wordt de kopie van de bron schijf en alle moment opnamen van het herstel punt opgeslagen.

### <a name="can-i-exclude-disks-from-replication"></a>Kan ik schijven uitsluiten van replicatie?

Ja, u kunt schijven uitsluiten.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Kan ik virtuele machines met dynamische schijven repliceren?

Dynamische schijven kunnen worden gerepliceerd. De schijf met het besturings systeem moet een standaard schijf zijn.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Als ik replicatie groepen voor multi-VM-consistentie gebruik, kan ik een nieuwe virtuele machine toevoegen aan een bestaande replicatie groep?

Ja, u kunt nieuwe virtuele machines toevoegen aan een bestaande replicatie groep wanneer u replicatie inschakelt. Indien

- U kunt geen virtuele machine toevoegen aan een bestaande replicatie groep nadat de replicatie is gestart.
- U kunt geen replicatie groep maken voor bestaande Vm's.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan ik virtuele machines die repliceren, wijzigen door schijven toe te voegen of te wijzigen?

Voor VMware-replicatie naar Azure kunt u de schijf grootte van de bron-Vm's wijzigen. Als u nieuwe schijven wilt toevoegen, moet u de schijf toevoegen en de beveiliging voor de virtuele machine opnieuw inschakelen.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Kan ik on-premises machines migreren naar een nieuwe vCenter Server zonder dat dit van invloed is op de continue replicatie?

Raadpleeg onze [richt lijnen](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server) voor het migreren van machines naar een nieuwe vCenter

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Kan ik repliceren naar een cache-of het doel-opslag account dat een virtueel netwerk (met Azure-firewalls) heeft geconfigureerd?

Nee, Site Recovery biedt geen ondersteuning voor replicatie naar Azure Storage op virtuele netwerken.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>Wat is de frequentie van het genereren van crash-consistente herstel punten?

Site Recovery worden elke vijf minuten crash-consistente herstel punten gegenereerd.

## <a name="component-upgrade"></a>Upgrade van onderdelen

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Mijn versie van de Mobility Services-agent of configuratie server is verouderd en mijn upgrade is mislukt. Wat moet ik doen?

Site Recovery volgt het ondersteunings model N-4. Meer [informatie](./service-updates-how-to.md#support-statement-for-azure-site-recovery) over het uitvoeren van een upgrade van zeer oudere versies.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Waar vind ik de release opmerkingen en update pakketten voor Azure Site Recovery?

[Meer informatie over nieuwe updates](site-recovery-whats-new.md)en het [ophalen van Rollup-informatie](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Waar vind ik upgrade-informatie voor herstel na nood gevallen naar Azure?

[Meer informatie over upgraden](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Moet ik de bron machines voor elke upgrade opnieuw opstarten?

Opnieuw opstarten wordt aanbevolen, maar niet verplicht voor elke upgrade. [Meer informatie](./service-updates-how-to.md#reboot-after-mobility-service-upgrade).

## <a name="configuration-server"></a>Configuratieserver

### <a name="what-does-the-configuration-server-do"></a>Wat doet de configuratie server?

De configuratie server voert de on-premises Site Recovery onderdelen uit, waaronder:

- De configuratie server zelf. De server coördineert de communicatie tussen on-premises onderdelen en Azure, en beheert de gegevens replicatie.
- De proces server die fungeert als replicatie gateway. Deze server:
    1. Ontvangt replicatiegegevens.
    2. Optimaliseert de gegevens met caching, compressie en versleuteling.
    3. De gegevens worden naar Azure Storage verzonden.
  De proces server installeert ook een push-installatie van de Mobility-service op Vm's en voert automatische detectie uit van on-premises VMware-Vm's.
- De hoofddoel server die replicatie gegevens verwerkt tijdens de failback vanuit Azure.

Meer [informatie](vmware-azure-architecture.md) over de onderdelen en processen van de configuratie server.

### <a name="where-do-i-set-up-the-configuration-server"></a>Waar kan ik de configuratie server instellen?

U hebt een enkele, Maxi maal beschik bare, on-premises VMware-VM nodig voor de configuratie server. Installeer de configuratie server op een fysieke computer voor herstel na nood gevallen voor fysieke servers.

### <a name="what-do-i-need-for-the-configuration-server"></a>Wat heb ik nodig voor de configuratie server?

Controleer de [vereisten](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan ik de configuratie server hand matig instellen in plaats van een sjabloon te gebruiken?

We raden u aan [de VM van de configuratie server te maken](vmware-azure-deploy-configuration-server.md) met behulp van de meest recente versie van de sjabloon open VIRTUALIZATION Format (OVF). Als u de sjabloon niet kunt gebruiken (bijvoorbeeld als u geen toegang hebt tot de VMware-Server), [downloadt](physical-azure-set-up-source.md) u het installatie bestand vanuit de portal en stelt u de configuratie server in.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Kan een configuratie server naar meer dan één regio repliceren?

Nee. Als u naar meer dan één regio wilt repliceren, moet u in elke regio een configuratie server hebben.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan ik een configuratie Server hosten in azure?

Hoewel het mogelijk is, moet de virtuele machine van Azure waarop de configuratie server wordt uitgevoerd, communiceren met uw on-premises VMware-infra structuur en virtuele machines. Deze communicatie voegt latentie toe en heeft gevolgen voor de continue replicatie.

### <a name="how-do-i-update-the-configuration-server"></a>De configuratie server Hoe kan ik bijwerken?

[Meer informatie](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) over het bijwerken van de configuratie server.

- U vindt de meest recente update-informatie op de [pagina met Azure-updates](https://azure.microsoft.com/updates/?product=site-recovery).
- U kunt de meest recente versie downloaden via de portal. Of u kunt de nieuwste versie van de configuratie server rechtstreeks downloaden vanuit het [micro soft Download centrum](https://aka.ms/asrconfigurationserver).
- Als uw versie meer dan vier versies ouder is dan de huidige versie, raadpleegt u de [ondersteunings verklaring](./service-updates-how-to.md#support-statement-for-azure-site-recovery) voor upgrade richtlijnen.

### <a name="should-i-back-up-the-configuration-server"></a>Moet ik een back-up maken van de configuratie server?

U wordt aangeraden regel matig geplande back-ups van de configuratie server te maken.

- Voor een geslaagde failback moet er een back-up van de virtuele machine aanwezig zijn in de data base van de configuratie server.
- De configuratie server moet actief zijn en een verbonden status hebben.
- Meer [informatie](vmware-azure-manage-configuration-server.md) over algemene beheer taken voor configuratie servers.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Kan ik MySQL hand matig downloaden en installeren bij het instellen van de configuratie server?

Ja. Down load MySQL en plaats deze in de map C:\Temp\ASRSetup. Installeer het vervolgens hand matig. Wanneer u de virtuele machine van de configuratie server instelt en de voor waarden accepteert, wordt MySQL vermeld als **reeds geïnstalleerd** in **downloaden en installeren**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kan ik het downloaden van MySQL voor komen maar Site Recovery installeren?

Ja. Down load het MySQL-installatie programma en plaats het in de map C:\Temp\ASRSetup. Wanneer u de virtuele machine van de configuratie server instelt, accepteert u de voor waarden en selecteert u **downloaden en installeren**. De portal maakt gebruik van het installatie programma dat u hebt toegevoegd om MySQL te installeren.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Kan ik de VM van de configuratie server voor iets anders gebruiken?

Nee. Gebruik de VM alleen voor de configuratie server.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Kan ik een configuratie server klonen en gebruiken voor indeling?

Nee. Stel een nieuwe configuratie server in om registratie problemen te voor komen.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Kan ik de kluis wijzigen waarin de configuratie server is geregistreerd?

Nee. Nadat een kluis is gekoppeld aan de configuratie server, kan deze niet meer worden gewijzigd. [Meer informatie](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) over het registreren van een configuratie server met een andere kluis.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Kan ik dezelfde configuratie server gebruiken voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers?

Ja, maar houd er rekening mee dat de fysieke machine alleen kan worden teruggezet naar een virtuele VMware-machine.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Waar kan ik de wachtwoordzin voor de configuratie server downloaden?

[Meer informatie](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) over het downloaden van de wachtwoordzin.

### <a name="where-can-i-download-vault-registration-keys"></a>Waar kan ik een kluis registratie sleutel downloaden?

Selecteer in de Recovery Services kluis **configuratie servers** in **site Recovery-infra structuur**  >  **beheren**. Selecteer vervolgens bij **servers** de optie **registratie sleutel downloaden** om het bestand met kluis referenties te downloaden.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Kan één configuratie server worden gebruikt voor het beveiligen van meerdere vCenter-instanties?

Ja, een enkele configuratie server kan Vm's beveiligen over meerdere vCenter.  Er is geen limiet voor het aantal vCenter-instanties dat kan worden toegevoegd aan de configuratie server, maar de limieten voor het aantal Vm's dat door één configuratie server kan worden beveiligd, zijn van toepassing.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Kan één configuratie server meerdere clusters in vCenter beveiligen?

Ja, Azure Site Recovery kunnen Vm's in verschillende clusters beveiligen.

## <a name="process-server"></a>Proces server

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Waarom kan ik de proces server niet selecteren wanneer ik replicatie inschakel?

Updates in versie 9,24 en hoger tonen nu de [status van de proces server wanneer u replicatie inschakelt](vmware-azure-enable-replication.md#enable-replication). Deze functie helpt proces server beperking te voor komen en om het gebruik van beschadigde proces servers te minimaliseren.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Hoe kan ik de proces server bijwerken naar versie 9,24 of hoger voor nauw keurige status informatie?

Vanaf [versie 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups)zijn er meer waarschuwingen toegevoegd om de status van de proces server aan te geven. [Werk uw site Recovery-onderdelen bij naar versie 9,24 of hoger](service-updates-how-to.md#links-to-currently-supported-update-rollups) zodat alle waarschuwingen worden gegenereerd.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Hoe kan ik zorgen voor een hoge Beschik baarheid van de proces server?

Door meer dan één proces server te configureren, biedt het ontwerp flexibiliteit voor het verplaatsen van beveiligde computers van een slechte proces server naar werk proces server. Verplaatsing van een machine van de ene proces server naar een andere moet expliciet/hand matig worden gestart via de gedefinieerde stappen hier: [vm's verplaatsen tussen proces servers](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Failover en failback

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Kan ik de on-premises proces server voor failback gebruiken?

We raden u ten zeerste aan om een proces server in azure te maken voor failback-doel einden om latentie van gegevens overdracht te voor komen. Als u het bron-Vm's netwerk echter hebt gescheiden met het Azure-netwerk in de configuratie server, is het essentieel om de proces server te gebruiken die in Azure is gemaakt voor failback.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Kan ik het IP-adres op failover houden?

Ja, u kunt het IP-adres op failover houden. Zorg ervoor dat u het doel-IP-adres opgeeft in de **reken-en netwerk** instellingen voor de virtuele machine voordat de failover wordt doorvoerd. Sluit ook de computer af op het moment van de failover om IP-adres conflicten te voor komen tijdens het failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Kan ik de grootte of het VM-type van de doel-VM wijzigen vóór de failover?

Ja, u kunt het type of de grootte van de virtuele machine op elk gewenst moment vóór de failover wijzigen. Gebruik in de Portal de **reken-en netwerk** instellingen voor de gerepliceerde VM.

### <a name="how-far-back-can-i-recover"></a>Hoe ver terug kan ik herstellen?

Voor VMware naar Azure is het oudste herstel punt dat u kunt gebruiken 72 uur.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hoe kan ik toegang tot Azure-Vm's na een failover?

Na een failover kunt u toegang krijgen tot Azure-Vm's via een beveiligde Internet verbinding, via een site-naar-site-VPN of via Azure ExpressRoute. Als u verbinding wilt maken, moet u verschillende dingen voorbereiden. [Meer informatie](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Is er een fout opgetreden: de gegevens zijn niet meer flexibel?

Azure is ontworpen voor herstelbaarheid. Site Recovery is ontworpen voor failover naar een secundair Azure-Data Center, zoals vereist door de Azure Service-Level Agreement (SLA). Als er een failover optreedt, zorgen we ervoor dat uw meta gegevens en kluizen blijven in dezelfde geografische regio die u hebt gekozen voor uw kluis.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?

[Failover](site-recovery-failover.md) is niet automatisch. U start een failover door één selectie te maken in de portal of u kunt [Power shell](/powershell/module/az.recoveryservices) gebruiken om een failover te activeren.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan ik een failback uitvoeren naar een andere locatie?

Ja. Als u een failover naar Azure hebt uitgevoerd, kunt u een failback uitvoeren naar een andere locatie als het oorspronkelijke exemplaar niet beschikbaar is. [Meer informatie](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Waarom heb ik een VPN-of ExpressRoute met privé-peering nodig om failback uit te voeren?

Wanneer u een failback van Azure hebt uitgevoerd, worden de gegevens van Azure naar uw on-premises VM gekopieerd en is de persoonlijke toegang vereist.


## <a name="automation-and-scripting"></a>Automation en scripting

### <a name="can-i-set-up-replication-with-scripting"></a>Kan ik replicatie met scripting instellen?

Ja. U kunt Site Recovery werk stromen automatiseren met behulp van de rest-API, Power shell of de Azure SDK. [Meer informatie](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestaties en capaciteit

### <a name="can-i-throttle-replication-bandwidth"></a>Kan ik de replicatie bandbreedte beperken?

Ja. [Meer informatie](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Volgende stappen

- [Bekijk](vmware-physical-azure-support-matrix.md) de ondersteunings vereisten.
- [Instellen](vmware-azure-tutorial.md) Replicatie van VMware naar Azure.