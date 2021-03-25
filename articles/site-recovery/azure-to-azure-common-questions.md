---
title: Veelgestelde vragen over herstel na nood gevallen voor Azure VM met Azure Site Recovery
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure VM-nood herstel wanneer u Azure Site Recovery gebruikt.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: 5309fd60640c45ade42bab4c5727cf1f0a8d9d70
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025472"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Veelgestelde vragen: Herstel na noodgeval van Azure naar Azure

In dit artikel vindt u antwoorden op veelgestelde vragen over nood herstel van virtuele Azure-machines naar een andere Azure-regio, met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.

## <a name="general"></a>Algemeen

### <a name="how-is-site-recovery-priced"></a>Hoe wordt Site Recovery geprijsd?

Meer informatie over de [kosten](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) voor herstel na nood gevallen voor Azure VM.

### <a name="how-does-the-free-tier-work"></a>Hoe werkt de gratis laag?

Elk exemplaar dat wordt beveiligd met Site Recovery is gratis gedurende de eerste 31 dagen van de beveiliging. Na deze periode heeft de beveiliging voor elke instantie de tarieven die worden vermeld in de [prijs informatie](https://azure.microsoft.com/pricing/details/site-recovery/). U kunt kosten ramen met de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/?service=site-recovery).

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Moet ik in de eerste 31 dagen andere Azure-kosten in rekening brengen?

Ja. Hoewel Azure Site Recovery gratis is gedurende de eerste 31 dagen van een beschermd exemplaar, worden er mogelijk kosten in rekening gebracht voor Azure Storage, opslag transacties en gegevens overdrachten. Een herstelde virtuele machine kan ook Azure-reken kosten opleveren. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Hoe kan ik aan de slag met nood herstel voor Azure VM?

1. Meer [informatie](azure-to-azure-architecture.md) over de Azure VM-nood herstel architectuur.
2. [Bekijk](azure-to-azure-support-matrix.md) de ondersteunings vereisten.
3. Herstel na nood geval [instellen](azure-to-azure-how-to-enable-replication.md) voor virtuele Azure-machines.
4. [Voer een nood herstel analyse uit](azure-to-azure-tutorial-dr-drill.md) met een testfailover.
5. [Voer een volledige failover uit](azure-to-azure-tutorial-failover-failback.md) naar een secundaire Azure-regio.
6. [Failback](azure-to-azure-tutorial-failback.md) van de secundaire regio naar de primaire regio.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hoe kan de capaciteit van de doel regio worden gegarandeerd?

Het Site Recovery-team en het Azure capacity management-team plannen voldoende capaciteit voor de infra structuur. Wanneer u een failover start, zorgen de teams er ook voor dat VM-exemplaren die worden beveiligd door Site Recovery worden geïmplementeerd in de doel regio.

## <a name="replication"></a>Replicatie

### <a name="can-i-replicate-vms-with-disk-encryption"></a>Kan ik Vm's repliceren met schijf versleuteling?

Ja. Site Recovery ondersteunt herstel na nood gevallen van Vm's waarvoor Azure Disk Encryption (ADE) is ingeschakeld. Wanneer u replicatie inschakelt, kopieert Azure alle vereiste schijf versleutelings sleutels en geheimen van de bron regio naar de doel regio in de gebruikers context. Als u niet over de vereiste machtigingen beschikt, kan uw beveiligings beheerder een script gebruiken om de sleutels en geheimen te kopiëren.

- Site Recovery ondersteunt ADE voor virtuele Azure-machines waarop Windows wordt uitgevoerd.
- Site Recovery ondersteunt:
    - ADE-versie 0,1, die een schema heeft waarvoor Azure Active Directory (Azure AD) is vereist.
    - ADE-versie 1,1, waarvoor Azure AD niet is vereist. Voor versie 1,1 moeten virtuele Windows Azure-machines beheerde schijven hebben.
    - [Meer informatie](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). over de extensie schema's.

[Meer informatie](azure-to-azure-how-to-enable-replication-ade-vms.md) over het inschakelen van replicatie voor versleutelde vm's.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Kan ik een Automation-account selecteren uit een andere resource groep?

Wanneer u Site Recovery toestaan updates te beheren voor de extensie van de Mobility-service die wordt uitgevoerd op de gerepliceerde Azure-Vm's, implementeert deze een globaal runbook (gebruikt door Azure-Services) via een Azure Automation-account. U kunt het Automation-account gebruiken dat Site Recovery maakt of selecteren dat u een bestaand Automation-account wilt gebruiken. 

In de portal kunt u op dit moment alleen een Automation-account in dezelfde resource groep selecteren als de kluis. U kunt een Automation-account selecteren uit een andere resource groep met behulp van Power shell. [Meer informatie](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Als ik een klant Automation-account gebruik dat zich niet in de kluis resource groep bevindt, kan ik dan het standaard-runbook verwijderen?

Ja, u kunt de app verwijderen als u deze niet nodig hebt. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan ik virtuele machines repliceren naar een ander abonnement?

Ja, u kunt virtuele Azure-machines repliceren naar elk abonnement binnen dezelfde Azure AD-Tenant. Wanneer u herstel na nood gevallen voor virtuele machines inschakelt, wordt het doel abonnement dat wordt weer gegeven, standaard die van de bron-VM. U kunt het doel abonnement wijzigen en andere instellingen (zoals de resource groep en het virtuele netwerk) worden automatisch ingevuld op basis van het geselecteerde abonnement.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Kan ik virtuele machines in een beschikbaarheids zone repliceren naar een andere regio?

Ja, u kunt Vm's in beschikbaarheids zones repliceren naar een andere Azure-regio. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Kan ik virtuele machines buiten de zone repliceren naar een zone binnen dezelfde regio? 

Dit wordt niet ondersteund in de portal. U kunt hiervoor de REST API/Power shell gebruiken.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Kan ik virtuele machines in een zone repliceren naar een andere zone in dezelfde regio?

Ondersteuning hiervoor is beperkt tot een aantal regio's. [Meer informatie](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>Kan ik schijven uitsluiten van replicatie?

Ja, u kunt schijven uitsluiten wanneer u de replicatie instelt met behulp van Power shell. [Meer informatie](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Kan ik nieuwe schijven die zijn toegevoegd aan gerepliceerde Vm's repliceren?

Voor gerepliceerde Vm's met Managed disks kunt u nieuwe schijven toevoegen en replicatie inschakelen. Wanneer u een nieuwe schijf toevoegt, toont de gerepliceerde VM een waarschuwing dat een of meer schijven op de virtuele machine beschikbaar zijn voor beveiliging. 

- Als u replicatie inschakelt voor de toegevoegde schijven, verdwijnt de waarschuwing na de initiële replicatie.
- Als u geen replicatie voor de schijf wilt inschakelen, kunt u de waarschuwing sluiten.
- Als u een virtuele machine met toegevoegde schijven failover, worden de schijven weer gegeven die beschikbaar zijn voor herstel. Als u bijvoorbeeld een tweede schijf aan een virtuele machine met één schijf toevoegt, wordt een replicatie punt gemaakt voordat u dit hebt toegevoegd als ' 1 van 2 schijven '.

Site Recovery biedt geen ondersteuning voor ' hot remove ' van schijven van een gerepliceerde VM. Als u een VM-schijf verwijdert, moet u de replicatie voor de virtuele machine uitschakelen en vervolgens opnieuw inschakelen.

### <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?

Replicatie is doorlopend bij het repliceren van virtuele Azure-machines naar een andere Azure-regio. Meer [informatie](./azure-to-azure-architecture.md#replication-process) over de werking van replicatie.

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Kan ik virtuele machines binnen een regio repliceren? 

U kunt Site Recovery niet gebruiken om schijven binnen een regio te repliceren.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Kan ik VM-exemplaren repliceren naar een Azure-regio?

U kunt virtuele machines repliceren en herstellen tussen twee regio's binnen hetzelfde geografische cluster. Geografische clusters worden gedefinieerd met gegevens latentie en soevereiniteit. Meer [informatie](./azure-to-azure-support-matrix.md#region-support) over regio ondersteuning.

### <a name="does-site-recovery-need-internet-connectivity"></a>Heeft Site Recovery verbinding met internet nodig?

Nee, maar Vm's moeten toegang hebben tot Site Recovery-Url's en IP-adresbereiken. [Meer informatie](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Kan ik een toepassings niveau repliceren tussen resource groepen?

Ja, u kunt de app repliceren en de configuratie voor herstel na nood gevallen in een afzonderlijke resource groep blijven gebruiken.

Als de apps bijvoorbeeld drie lagen (toepassing/data base/web) in verschillende resource groepen hebben, moet u de replicatie drie keer inschakelen om alle lagen te beveiligen. Site Recovery repliceert de drie lagen in drie verschillende resource groepen.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Kan ik opslag accounts verplaatsen over resource groepen?

Nee, dit wordt niet ondersteund. Als u opslag accounts per ongeluk naar een andere resource groep verplaatst en de oorspronkelijke resource groep verwijdert, kunt u een nieuwe resource groep maken met dezelfde naam als de oude resource groep en vervolgens het opslag account verplaatsen naar deze resource groep.

## <a name="replication-policy"></a>Beleid voor replicatie

### <a name="what-is-a-replication-policy"></a>Wat is een replicatie beleid?

Een replicatie beleid definieert de Bewaar geschiedenis van herstel punten en de frequentie van de app-consistente moment opnamen.  Site Recovery maakt als volgt een standaard beleid voor replicatie:

- Herstel punten worden gedurende 24 uur bewaard.
- Maak elke vier uur app-consistente moment opnamen.

Meer [informatie](azure-to-azure-how-to-enable-replication.md#customize-target-resources) over replicatie-instellingen.

### <a name="whats-a-crash-consistent-recovery-point"></a>Wat is een crash-consistent herstel punt?

Een crash consistent herstel punt bevat gegevens op de schijf, alsof u tijdens de moment opname het netsnoer van de server hebt opgehaald. Het bevat geen items die zich in het geheugen bevond toen de moment opname werd gemaakt.

Vandaag kunnen de meeste apps goed worden hersteld met crash-consistente moment opnamen. Een crash-consistent herstel punt is doorgaans voldoende voor andere besturings systemen dan data bases en apps zoals bestands servers, DHCP-servers en afdruk servers.

Site Recovery maakt automatisch elke vijf minuten een crash-consistent herstel punt.

### <a name="whats-an-application-consistent-recovery-point"></a>Wat is een toepassings consistent herstel punt?

App-consistente herstel punten worden gemaakt op basis van app-consistente moment opnamen. Ze vastleggen dezelfde gegevens als crash-consistente moment opnamen, en kunnen ook gegevens in het geheugen vastleggen en alle trans acties die worden uitgevoerd.

Vanwege extra inhoud zijn app-consistente moment opnamen het meest geschikt en nemen we het langst in de praktijk. We raden u aan toepassings consistente herstel punten te gebruiken voor database besturingssystemen en apps zoals SQL Server. Voor Windows kunnen app-consistente moment opnamen gebruikmaken van de Volume Shadow Copy Service (VSS).

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Beïnvloeden de prestaties van de app-consistente herstel punten?

 Omdat de app-consistente herstel punten alle gegevens in het geheugen en proces vastleggen, kunnen ze de prestaties beïnvloeden wanneer de werk belasting al bezig is. Het wordt niet aanbevolen dat u te vaak vastlegt voor niet-database workloads. Zelfs voor werk belasting van de data base moet één uur voldoende zijn.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Wat is de minimale frequentie voor het genereren van toepassings consistente herstel punten?

Site Recovery kunt toepassings consistente herstel punten maken met een minimum frequentie van één uur.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Kan ik app-consistente replicatie voor Linux Vm's inschakelen?

Ja. De Mobility-agent voor Linux ondersteunt aangepaste scripts voor app-consistentie. De agent gebruikt een aangepast script met vooraf en post opties. [Meer informatie](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Hoe worden herstel punten gegenereerd en opgeslagen?

Laten we een voor beeld gebruiken om te begrijpen hoe Site Recovery herstel punten genereert. 

- Een replicatie beleid behoudt herstel punten gedurende 24 uur en neemt elk uur een app-consistente frequentie-moment opname.
- Site Recovery maakt een crash-consistent herstel punt om de vijf minuten. U kunt deze frequentie niet wijzigen.
- Site Recovery herstel punten worden verwijderd na een uur, waarbij één punt per uur wordt bespaard.

In het afgelopen uur kunt u kiezen uit 12 crash-consistente punten en één app-consistent punt, zoals in de afbeelding wordt weer gegeven.

   ![Lijst met gegenereerde herstel punten](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Hoe ver terug kan ik herstellen?

Het oudste herstel punt dat u kunt gebruiken, is 72 uur.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Wat gebeurt er als Site Recovery niet langer dan 24 uur herstel punten kan genereren? 

Als u een replicatie beleid van 24 uur hebt en Site Recovery geen herstel punten meer dan 24 uur kan genereren, blijven uw oude herstel punten. Site Recovery vervangt alleen het oudste punt als het nieuwe punten genereert. Totdat er nieuwe herstel punten zijn, blijven alle oude punten nadat u het retentie venster hebt bereikt.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Kan ik het replicatie beleid wijzigen nadat replicatie is ingeschakeld?

Ja. Selecteer en bewerk het beleid in de kluis > **site Recovery infrastructuur**  >  **replicatie beleidsregels**. Wijzigingen zijn ook van toepassing op bestaande beleids regels.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Is alle herstel punten een volledige VM-kopie?

Het eerste herstel punt dat wordt gegenereerd, heeft de volledige kopie. Opeenvolgende herstel punten bevatten Delta wijzigingen.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Nemen de Bewaar periode van het herstel punt toe om de opslag kosten te verhogen?

Ja. Als u bijvoorbeeld de Bewaar periode van 24 uur tot 72 verhoogt, Site Recovery worden herstel punten voor een extra 48 uur opgeslagen. De toegevoegde tijd van de opslag wijzigingen. Als u alleen een voor beeld hebt van een enkel herstel punt met een Delta wijziging van 10 GB, met een kosten per GB van $0,16 per maand, worden extra kosten $1,60 × 48 per maand.

## <a name="multi-vm-consistency"></a>Multi-VM-consistentie

### <a name="what-is-multi-vm-consistency"></a>Wat is consistentie tussen meerdere VM'S?

Met consistentie tussen meerdere VM'S zorgt u ervoor dat herstel punten consistent zijn voor de gerepliceerde virtuele machines.

- Wanneer u multi-VM-consistentie inschakelt, maakt Site Recovery een replicatie groep van alle computers met de optie ingeschakeld. 
- Wanneer u een failover voor de computers in de replicatie groep hebt uitgevoerd, hebben ze gedeelde crash-consistente en toepassings consistente herstel punten.

[Meer informatie](azure-to-azure-tutorial-enable-replication.md#enable-replication) over het inschakelen van consistentie tussen meerdere vm's.

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Kan ik een failover uitvoeren voor één virtuele machine in een replicatie groep?

Nee. Wanneer u multi-VM-consistentie inschakelt, wordt er een app voor een afhankelijkheid van alle virtuele machines in de replicatie groep, en is de failover van één virtuele machine niet toegestaan.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Hoeveel virtuele machine kan ik samen in een groep repliceren?

U kunt 16 Vm's samen repliceren in een replicatie groep.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Wanneer moet ik de consistentie tussen meerdere VM'S inschakelen?

Multi-VM-consistentie is CPU-intensief, en het inschakelen van deze functie kan de prestaties van de werk belasting beïnvloeden. Schakel deze optie alleen in als Vm's dezelfde werk belasting uitvoeren en u consistentie op meerdere computers nodig hebt. Als u bijvoorbeeld twee SQL Server exemplaren en twee webservers in een toepassing hebt, schakelt u de consistentie tussen meerdere VM'S alleen in voor de SQL Server-exemplaren.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Kan ik een replicerende virtuele machine aan een replicatie groep toevoegen?

Wanneer u replicatie voor een virtuele machine inschakelt, kunt u deze toevoegen aan een nieuwe replicatie groep of aan een bestaande groep. U kunt geen virtuele machine toevoegen die al wordt gerepliceerd naar een groep. 
 
## <a name="failover"></a>Failover

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hoe kan de capaciteit van de doel regio worden gegarandeerd?

Het Site Recovery-team en het Azure capacity management-team kunnen voldoende infrastructuur capaciteit plannen. Wanneer u een failover start, zorgen de teams er ook voor dat VM-exemplaren die worden beveiligd door Site Recovery kunnen worden geïmplementeerd in de doel regio.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?

Failover wordt niet automatisch uitgevoerd. U kunt een failover starten met één klik in de portal of  [Power shell](azure-to-azure-powershell.md) gebruiken om een failover te activeren.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Kan ik een openbaar IP-adres houden na een failover?

U kunt het open bare IP-adres voor een productie-app niet hand haven na een failover.

Wanneer u een werk belasting ophaalt als onderdeel van het failoverproces, moet u een open bare Azure IP-adres resource aan het proces toewijzen. De resource moet beschikbaar zijn in de doel regio. U kunt de open bare IP-adres resource van Azure hand matig toewijzen of u kunt deze automatiseren met een herstel plan. [Meer informatie](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) over het instellen van open bare IP-adressen na een failover.

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Kan ik een privé-IP-adres houden na een failover?

Ja. Wanneer u herstel na nood gevallen voor virtuele Azure-machines inschakelt, maakt Site Recovery doel bronnen op basis van bron bron instellingen. Voor virtuele Azure-machines die zijn geconfigureerd met statische IP-adressen, probeert Site Recovery hetzelfde IP-adres in te richten voor de doel-VM als deze niet in gebruik is.
Meer [informatie over](site-recovery-retain-ip-azure-vm-failover.md) het bewaren van IP-adressen na een failover.

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Waarom is er na de failover een nieuwe IP-adres toegewezen aan een VM?

Site Recovery probeert het IP-adres op te geven op het moment van de failover. Als een andere virtuele machine dat adres gebruikt, Site Recovery het volgende beschik bare IP-adres als doel instellen.

Meer [informatie over](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) het instellen van netwerk toewijzing en IP-adres sering voor virtuele netwerken.

### <a name="whats-the-latest-recovery-point"></a>Wat is het *meest recente* herstel punt?

De *meest recente (laagste RPO)* herstel punt optie doet het volgende:

1. Eerst worden alle gegevens verwerkt die naar Site Recovery zijn verzonden.
2. Nadat de gegevens zijn verwerkt door de service, maakt deze een herstel punt voor elke virtuele machine voordat failover wordt uitgevoerd naar de virtuele machine. Deze optie biedt de laagste Recovery Point Objective (RPO).
3. De virtuele machine die is gemaakt na een failover, bevat alle gegevens die worden gerepliceerd naar Site Recovery, vanaf het moment dat de failover werd geactiveerd.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>Hebben de *laatste* herstel punten een FAILOVER-RTO?

Ja. Site Recovery alle in behandeling zijnde gegevens verwerkt voordat de failover wordt uitgevoerd. deze optie heeft daarom een hogere herstel tijd (RTO) dan andere opties.

### <a name="whats-the-latest-processed-recovery-option"></a>Wat is de *meest recente verwerkte* herstel optie?

De *meest recente verwerkings* optie doet het volgende:

1. Er wordt een failover uitgevoerd van alle Vm's naar het laatste herstel punt dat is verwerkt door Site Recovery. Deze optie biedt een laag RTO, omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>Wat gebeurt er als er sprake is van een onverwachte onderbreking in de primaire regio?

U kunt de failover starten. Site Recovery hebt geen verbinding nodig van de primaire regio om de failover uit te voeren.

### <a name="what-is-the-rto-of-a-vm-failover"></a>Wat is de RTO van een VM-failover?

Site Recovery heeft een RTO-SLA van [twee uur](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). De meeste tijd, Site Recovery failover van Vm's binnen enkele minuten. Als u de RTO wilt berekenen, controleert u de failover-taak, die de tijd toont die nodig is om een virtuele machine te openen. 

## <a name="recovery-plans"></a>Herstelplannen

### <a name="whats-a-recovery-plan"></a>Wat is een herstel plan?

Een [herstel plan](site-recovery-create-recovery-plans.md) in site Recovery organiseert de failover en het herstel van vm's. Het helpt herstel consistent te maken, herhaalbaar en geautomatiseerd. Dit doet het volgende:

- Hiermee wordt een groep virtuele machines gedefinieerd die samen een failover uitvoeren
- Hiermee worden de afhankelijkheden tussen Vm's gedefinieerd, zodat de toepassing nauw keurig is.
- Automatiseert herstel, met de optie aangepaste hand matige acties voor andere taken dan VM-failover. 


### <a name="how-does-sequencing-work"></a>Hoe werkt sequentiëren?

In een herstel plan kunt u meerdere groepen VM'S maken voor sequentiëren. Failover van groepen mislukt één voor één, zodat de Vm's die deel uitmaken van dezelfde groep samen worden uitgevoerd. [Meer informatie](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hoe kan ik de RTO van een herstel plan vinden?

Als u de RTO van een herstel plan wilt controleren, voert u een testfailover uit voor het herstel plan. Controleer in **site Recovery taken** de duur van de testfailover. In de scherm afbeelding voor beeld duurde de taak **SAPTestRecoveryPlan** -testfailover acht minuten en 59 seconden.

![Taken weer geven met de duur van de testfailover voor RTO](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Kan ik Automation-runbooks toevoegen aan herstel plannen?

Ja. [Meer informatie](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Opnieuw beveiligen en failback

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Worden virtuele machines in de secundaire regio automatisch beveiligd na een failover? 

Nee. Wanneer u virtuele machines van de ene naar de andere regio doorwerkt, worden de Vm's in de doel regio voor nood herstel in een niet-beveiligde status gestart. Als u virtuele machines in de secundaire regio opnieuw wilt [beveiligen](./azure-to-azure-how-to-reprotect.md) , schakelt u replicatie terug naar de primaire regio.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Worden alle gegevens van de secundaire regio naar primair gerepliceerd wanneer ik opnieuw Beveilig? 

Dit is afhankelijk van. Als er een VM van de bron regio bestaat, worden alleen de wijzigingen tussen de bron schijf en de doel schijf gesynchroniseerd. Site Recovery vergelijkt de schijven met wat er anders is en brengt vervolgens de gegevens over. Dit proces duurt meestal enkele uren. [Meer informatie](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>Hoe lang duurt het om een failback uit te voeren?

Na de herbeveiliging neemt failback ongeveer dezelfde hoeveelheid tijd in beslag voor failover van de primaire regio naar een secundaire regio.

## <a name="capacity"></a><a name="capacity"></a>Capaciteit

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hoe kan de capaciteit van de doel regio worden gegarandeerd?

Het team van de Site Recovery-team en het Azure Capacity Management-abonnement voor voldoende infrastructuur capaciteit. Wanneer u een failover start, zorgen de teams er ook voor dat VM-exemplaren die worden beveiligd door Site Recovery kunnen worden geïmplementeerd in de doel regio.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Werkt Site Recovery met gereserveerde instanties?

Ja, u kunt [gereserveerde Azure-vm's](https://azure.microsoft.com/pricing/reserved-vm-instances/) kopen in de nood herstel regio en site Recovery failover-bewerkingen gebruiken. Er is geen aanvullende configuratie nodig.

## <a name="security"></a>Beveiliging

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Worden er replicatiegegevens verzonden naar de Site Recovery-service?

Nee, Site Recovery worden geen gerepliceerde gegevens onderschept en er is geen informatie over wat er op uw Vm's wordt uitgevoerd. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.

Site Recovery is ISO 27001:2013, 27018, HIPAA en DPA Certified. De service ondergaat SOC2-en FedRAMP JAB-evaluaties.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?

Ja, de versleuteling van door Voer en [versleuteling op rest in azure](../storage/common/storage-service-encryption.md) wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk de vereisten voor Azure-naar-Azure-ondersteuning](azure-to-azure-support-matrix.md).
- [Azure-naar-Azure-replicatie instellen](azure-to-azure-tutorial-enable-replication.md).
- Als u vragen hebt na het lezen van dit artikel, plaatst u deze op de [pagina micro soft Q&een vraag voor Azure Recovery Services](/answers/topics/azure-site-recovery.html).
