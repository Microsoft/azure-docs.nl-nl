---
title: Algemene vragen over de Azure Site Recovery-service
description: In dit artikel worden populaire algemene vragen over Azure Site Recovery beschreven.
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 9db91a15c0ee5c982f73f36a36f12b38b969a125
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99820193"
---
# <a name="general-questions-about-azure-site-recovery"></a>Algemene vragen over Azure Site Recovery

In dit artikel vindt u een overzicht van veelgestelde vragen over Azure Site Recovery. Raadpleeg deze artikelen voor specifieke scenario's

- [Vragen over herstel na noodgeval van virtuele Azure-machines naar Azure](azure-to-azure-common-questions.md)
- [Vragen over herstel na noodgeval van VMware-VM naar Azure](vmware-azure-common-questions.md)
- [Vragen over herstel na noodgeval van Hyper-V-VM naar Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Algemeen

### <a name="what-does-site-recovery-do"></a>Wat doet Site Recovery?

Site Recovery draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) door de replicatie van Azure-Vm's tussen regio's, on-premises virtuele machines en fysieke servers naar Azure en on-premises machines naar een secundair Data Center te organiseren en te automatiseren. [Meer informatie](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Kan ik een virtuele machine beveiligen die een docker-schijf heeft?

Nee, dit is een niet-ondersteund scenario.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Wat doet Site Recovery om de gegevens integriteit te waarborgen?

Er zijn verschillende maat regelen door Site Recovery om gegevens integriteit te garanderen. Er wordt een beveiligde verbinding tot stand gebracht tussen alle services met behulp van het HTTPS-protocol. Dit zorgt ervoor dat alle malware of externe entiteiten de gegevens niet kunnen knoeien. Er wordt een andere meting uitgevoerd met behulp van controle sommen. De gegevens overdracht tussen de bron en het doel wordt uitgevoerd door controle sommen van gegevens ertussen te berekenen. Dit zorgt ervoor dat de overgebrachte gegevens consistent zijn.

## <a name="service-providers"></a>Service providers

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Ik ben een service provider. Werkt Site Recovery voor specifieke en gedeelde infrastructuur modellen?
Ja, Site Recovery werkt voor zowel toegewezen als gedeelde infrastructuurmodellen.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Voor een service provider is de identiteit van mijn Tenant gedeeld met de Site Recovery-service?
Nee. De Tenant-id blijft anoniem. Uw tenants hebben geen toegang nodig tot de Site Recovery-portal. Alleen de beheerder van de serviceprovider communiceert met de portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Gaat de gegevens van de Tenant toepassing ooit naar Azure?
Bij replicatie tussen sites van serviceproviders worden er nooit toepassingsgegevens naar Azure overgezet. Gegevens worden in transit versleuteld en direct gerepliceerd tussen de sites van de service provider.

Als u naar Azure repliceert, worden er toepassingsgegevens verzonden naar Azure-opslag, maar niet naar de Site Recovery-service. Gegevens worden in transit versleuteld en blijven versleuteld in Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Ontvangen mijn tenants een factuur voor Azure-services?
Nee. Azure-services worden rechtstreeks aan de serviceprovider gefactureerd. Serviceproviders zijn zelf verantwoordelijk voor het genereren van facturen voor hun tenants.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Als ik naar Azure repliceer, moet ik dan altijd een virtuele machine in Azure uitvoeren?
Nee, gegevens worden gerepliceerd naar Azure Storage in uw abonnement. Wanneer u een failovertest (details voor DR) of een werkelijke failover uitvoert, maakt Site Recovery automatisch virtuele machines in uw abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Wordt er gezorgd voor isolatie op tenantniveau wanneer ik naar Azure repliceer?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Welke platforms worden er momenteel ondersteund?
We ondersteunen implementaties van Azure Pack, Cloud platform System en System Center op basis van (2012 en hoger). Meer [informatie](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) over Azure Pack en integratie van site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Worden implementaties van één Azure Pack en één VMM-server ondersteund?
Ja, u kunt virtuele Hyper-V-machines repliceren naar Azure of tussen sites van service providers.  Houd er rekening mee dat als u tussen sites van service providers repliceert Azure runbook-integratie niet beschikbaar is.

## <a name="pricing"></a>Prijzen

### <a name="where-can-i-find-pricing-information"></a>Waar vind ik prijs informatie?
Bekijk de [site Recovery prijs](https://azure.microsoft.com/pricing/details/site-recovery/) gegevens.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hoe kan ik geschatte kosten berekenen tijdens het gebruik van Site Recovery?

U kunt de [prijs calculator](https://aka.ms/asr_pricing_calculator) gebruiken om de kosten te ramen tijdens het gebruik van site Recovery.

Voor een gedetailleerde schatting van de kosten voert u het hulp programma Deployment planner voor [VMware](./site-recovery-deployment-planner.md) of [Hyper-V](https://aka.ms/asr-deployment-planner)uit en gebruikt u het [rapport kosten raming](./site-recovery-vmware-deployment-planner-cost-estimation.md).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Beheerde schijven worden nu gebruikt voor het repliceren van virtuele VMware-machines en fysieke servers. Worden er extra kosten in rekening gebracht voor het cache-opslag account met Managed disks?

Nee, er zijn geen extra kosten voor de cache. Wanneer u naar een standaard-opslag account repliceert, maakt deze cache-opslag deel uit van hetzelfde doel-opslag account.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Ik ben al meer dan een maand Azure Site Recovery gebruiker geweest. Krijg ik nog steeds de eerste 31 dagen gratis voor elk beschermd exemplaar?

Ja. Voor elk beschermd exemplaar worden gedurende de eerste 31 dagen geen Azure Site Recovery kosten in rekening gebracht. Als u bijvoorbeeld 10 instanties voor de afgelopen zes maanden hebt beveiligd en u een elfde exemplaar verbindt met Azure Site Recovery, zijn er gedurende de eerste 31 dagen geen kosten in rekening gebracht voor het elfde exemplaar. Voor de eerste tien exemplaren blijven Azure Site Recovery kosten in rekening gebracht omdat ze al meer dan 31 dagen zijn beveiligd.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Worden er gedurende de eerste 31 dagen andere Azure-kosten in rekening gebracht?

Ja, hoewel Site Recovery gratis is gedurende de eerste 31 dagen van een beschermd exemplaar, worden er mogelijk kosten in rekening gebracht voor Azure Storage, opslag transacties en gegevens overdracht. Een herstelde virtuele machine kan ook Azure-reken kosten in rekening worden gebracht.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Zijn er kosten verbonden aan het uitvoeren van nood herstel oefeningen/testfailover?

Er zijn geen afzonderlijke kosten voor DR-oefeningen. Er worden kosten berekend nadat de virtuele machine is gemaakt na de testfailover.



## <a name="security"></a>Beveiliging

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Worden er replicatiegegevens verzonden naar de Site Recovery-service?
Nee, Site Recovery worden geen gerepliceerde gegevens onderschept en bevatten geen informatie over wat er op uw virtuele machines of fysieke servers wordt uitgevoerd.
Er worden alleen replicatiegegevens uitgewisseld tussen uw on-premises Hyper-V-hosts, VMware-hypervisors of fysieke servers en de Azure-opslag of uw secundaire site. Met Site Recovery kunnen deze gegevens niet worden onderschept. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.  

Site Recovery is ISO 27001:2013, 27018, HIPAA, DPA gecertificeerd en is het proces van SOC2-en FedRAMP JAB-evaluaties.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Voor nalevings redenen moeten zelfs onze on-premises meta gegevens binnen dezelfde geografische regio blijven. Kan Site Recovery ons helpen?
Ja. Wanneer u een Site Recovery kluis maakt in een regio, zorgen we ervoor dat alle meta gegevens die we nodig hebben om de replicatie en failover in te scha kelen en te organiseren, binnen de geografische grens van die regio blijven.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?
Voor virtuele machines en fysieke servers moet replicatie tussen on-premises sites worden gerepliceerd-in-transit wordt ondersteund. Voor virtuele machines en fysieke servers die worden gerepliceerd naar Azure, wordt zowel versleuteling als door Voer [(in Azure)](../storage/common/storage-service-encryption.md) ondersteund.

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Gebruikt Azure-to-Azure Site Recovery TLS 1,2 voor alle communicatie tussen micro services van Azure?
Ja, het TLS 1,2-protocol wordt standaard afgedwongen voor Azure-to-Azure Site Recovery scenario. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Hoe kan ik TLS 1,2 afdwingen voor scenario's met VMware-naar-Azure en fysieke server-naar-Azure Site Recovery?
Mobility-agents die zijn geïnstalleerd op de gerepliceerde items communiceren alleen met de proces server op TLS 1,2. Communicatie van configuratie server naar Azure en van proces server naar Azure kan echter worden uitgevoerd op TLS 1,1 of 1,0. Volg de [richt lijnen](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) voor het afdwingen van TLS 1,2 op alle configuratie servers en proces servers die door u zijn ingesteld.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Hoe kan ik TLS 1,2 afdwingen voor Hyper-to-Azure Site Recovery-scenario's?
Alle communicatie tussen de micro services van Azure Site Recovery gebeurt op het TLS 1,2-protocol. Site Recovery gebruikt beveiligings providers die zijn geconfigureerd in het systeem (OS) en maakt gebruik van het meest recente beschik bare TLS-protocol. U moet de TLS 1,2 expliciet inschakelen in het REGI ster en vervolgens Site Recovery gaan gebruiken van TLS 1,2 voor communicatie met Services. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Hoe kan ik beperkte toegang afdwingen voor mijn opslag accounts die worden gebruikt door Site Recovery service voor het lezen/schrijven van replicatie gegevens?
U kunt overschakelen op de beheerde identiteit van de Recovery Services-kluis door te gaan naar de *identiteits* instelling. Zodra de kluis is geregistreerd bij Azure Active Directory, kunt u naar uw opslag accounts gaan en de volgende roltoewijzingen aan de kluis geven:

- Op Resource Manager gebaseerde opslag accounts (standaard type):
  - [Inzender](../role-based-access-control/built-in-roles.md#contributor)
  - [Inzender voor Storage Blob-gegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Op Resource Manager gebaseerde opslag accounts (Premium-type):
  - [Inzender](../role-based-access-control/built-in-roles.md#contributor)
  - [Eigenaar van opslagblobgegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klassieke opslag accounts:
  - [Inzender voor klassieke opslag accounts](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Service functie voor de sleutel operator voor klassieke opslag accounts](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>Herstel na noodgeval

### <a name="what-can-site-recovery-protect"></a>Wat kan Site Recovery beschermen?
* **Azure vm's**: site Recovery kunt elke workload repliceren die wordt uitgevoerd op een ondersteunde Azure VM
* **Virtuele Hyper-v-machines**: site Recovery kunnen elke werk belasting die wordt uitgevoerd op een hyper-v-VM, beveiligen.
* **Fysieke servers**: site Recovery kunt fysieke servers met Windows of Linux beveiligen.
* **Virtuele VMware-machines**: site Recovery kunt elke werk belasting die wordt uitgevoerd in een VMware-VM, beveiligen.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Welke workloads kan ik met Site Recovery beveiligen?
U kunt Site Recovery gebruiken om de meeste werk belastingen te beveiligen die worden uitgevoerd op een ondersteunde virtuele machine of op een fysieke server. Site Recovery biedt ondersteuning voor toepassings bewuste replicatie, zodat apps kunnen worden hersteld naar een intelligente status. Het wordt geïntegreerd met micro soft-toepassingen, zoals share point, Exchange, Dynamics, SQL Server en Active Directory, en werkt nauw samen met toonaangevende leveranciers, waaronder Oracle, SAP, IBM en Red Hat. Lees [hier](site-recovery-workload.md) meer informatie over workloadbeveiliging.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kan ik met Site Recovery herstel na noodgeval voor mijn filialen beheren?
Ja. Wanneer u Site Recovery gebruikt om replicatie en failover in uw filialen te organiseren, krijgt u een uniforme indeling en weer gave van al uw filialen op een centrale locatie. Vanuit het hoofdkantoor kunt u eenvoudig failovers uitvoeren en herstel na noodgeval beheren voor alle filialen. Het is niet nodig de afzonderlijke filialen te bezoeken.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Wordt nood herstel ondersteund voor virtuele Azure-machines?

Ja, Site Recovery ondersteunt nood gevallen voor Azure-Vm's tussen Azure-regio's. [Bekijk veelgestelde vragen](azure-to-azure-common-questions.md) over nood herstel voor Azure VM. Als u tussen twee Azure-regio's op hetzelfde continent wilt repliceren, gebruikt u onze Azure naar Azure DR-aanbieding. U hoeft geen configuratie server-of proces server-en ExpressRoute-verbindingen in te stellen.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Wordt nood herstel ondersteund voor virtuele VMware-machines?

Ja, Site Recovery ondersteunt herstel na nood gevallen van on-premises VMware-Vm's. [Bekijk veelgestelde vragen](vmware-azure-common-questions.md) over nood herstel van virtuele VMware-machines.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Wordt herstel na nood gevallen ondersteund voor virtuele Hyper-V-machines?
Ja, Site Recovery ondersteunt herstel na nood gevallen van on-premises Hyper-V-Vm's. [Bekijk veelgestelde vragen](hyper-v-azure-common-questions.md) voor herstel na nood gevallen voor virtuele Hyper-V-machines.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>Wordt nood herstel ondersteund voor fysieke servers?
Ja, Site Recovery ondersteunt herstel na nood gevallen van on-premises fysieke servers met Windows en Linux naar Azure of naar een secundaire site. Meer informatie over vereisten voor herstel na nood gevallen naar [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)en naar[een secundaire site](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Houd er rekening mee dat fysieke servers na een failover worden uitgevoerd als virtuele machines in Azure. Failback van Azure naar een on-premises fysieke server wordt momenteel niet ondersteund. U kunt alleen een failback uitvoeren naar een virtuele VMware-machine.





## <a name="replication"></a>Replicatie

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kan ik een site-naar-site-VPN-verbinding naar Azure repliceren?
Azure Site Recovery gegevens repliceren naar een Azure-opslag account of beheerde schijven, via een openbaar eind punt. Replicatie is niet meer dan een site-naar-site-VPN. 

### <a name="why-cant-i-replicate-over-vpn"></a>Waarom kan ik niet repliceren via VPN?

Wanneer u naar Azure repliceert, bereikt het replicatie verkeer de open bare eind punten van een Azure Storage. Daarom kunt u alleen repliceren via het open bare Internet of via ExpressRoute (micro soft-peering of een bestaande open bare peering).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Kan ik Riverbed SteelHeads gebruiken voor replicatie?

Onze partner, Riverbed, biedt gedetailleerde richt lijnen voor het werken met Azure Site Recovery. Bekijk de [hand leiding](https://community.riverbed.com/s/article/DOC-4627)voor de oplossing.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kan ik ExpressRoute gebruiken om virtuele machines te repliceren naar Azure?
Ja, [ExpressRoute kan worden gebruikt](concepts-expressroute-with-site-recovery.md) om on-premises virtuele machines te repliceren naar Azure.

- Azure Site Recovery repliceert gegevens naar een Azure Storage over een openbaar eind punt. U moet [micro soft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) instellen of een bestaande [open bare peering](../expressroute/about-public-peering.md) (afgeschaft voor nieuwe circuits) gebruiken om ExpressRoute te gebruiken voor de replicatie van site Recovery.
- Micro soft-peering is het aanbevolen routerings domein voor replicatie.
- Replicatie wordt niet ondersteund voor persoonlijke peering.
- Als u VMware-machines of fysieke machines beveiligt, moet u ervoor zorgen dat ook aan de [netwerk vereisten](vmware-azure-configuration-server-requirements.md#network-requirements) voor de configuratie server wordt voldaan. De configuratie server voor de indeling van Site Recovery replicatie vereist dat er verbinding wordt met specifieke Url's. ExpressRoute kan niet worden gebruikt voor deze verbinding.
- Nadat de failover van de virtuele machines naar een virtueel Azure-netwerk is uitgevoerd, kunt u deze openen met behulp van de instelling voor [persoonlijke peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) met het virtuele netwerk van Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Als ik naar Azure repliceer, wat voor soort opslag account of Managed Disk heb ik nodig?

Het gebruik van opslag accounts als doel opslag wordt niet ondersteund door Azure Site Recovery. Het is raadzaam Managed disks te gebruiken als de doel opslag voor uw machines. Managed disks ondersteunen alleen LRS type voor gegevens tolerantie.

### <a name="how-often-can-i-replicate-data"></a>Hoe vaak kan ik gegevens repliceren?
* **Hyper-V:** Virtuele Hyper-V-machines kunnen elke 30 seconden worden gerepliceerd (met uitzonde ring van Premium-opslag), vijf minuten of 15 minuten.
* **Azure-vm's, virtuele VMware-machines, fysieke servers:** Een replicatie frequentie is hier niet relevant. Replicatie is doorlopend.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kan ik replicatie van de bestaande herstel site naar een andere tertiaire site uitbreiden?
Uitgebreide of gekoppelde replicatie wordt niet ondersteund. Deze functie aanvragen in het [Feedback forum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kan ik de eerste keer dat ik naar Azure repliceer, offline replicatie uitvoeren?
Nee, dit wordt niet ondersteund. Vraag deze functie aan in het [Feedback forum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kan ik bepaalde schijven uitsluiten van replicatie?
Dit wordt ondersteund bij het repliceren van virtuele VMware-machines en virtuele Hyper-V-machines naar Azure, met behulp van de Azure Portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Kan ik virtuele machines met dynamische schijven repliceren?
Dynamische schijven worden ondersteund bij het repliceren van virtuele Hyper-V-machines en bij het repliceren van VMware-Vm's en fysieke machines naar Azure. De schijf met het besturings systeem moet een standaard schijf zijn.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Kan ik de band breedte beperken die is toegewezen aan replicatie verkeer?
Ja. Meer informatie over het beperken van de band breedte vindt u in deze artikelen:

* [Capaciteits planning voor het repliceren van virtuele VMware-machines en fysieke servers](site-recovery-plan-capacity-vmware.md)
* [Capaciteits planning voor het repliceren van virtuele Hyper-V-machines naar Azure](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Kan ik replicatie inschakelen met app-consistentie in Linux-servers? 
Ja. Azure Site Recovery voor Linux-besturings systeem ondersteunt aangepaste scripts voor toepassingen voor app-consistentie. Het aangepaste script met de voor-en post opties wordt door de Azure Site Recovery Mobility agent gebruikt tijdens de app-consistentie. Hieronder vindt u de stappen om deze functie in te scha kelen.

1. Meld u aan als root in de computer.
2. Wijzig de map naar Azure Site Recovery installatie locatie voor de Mobility-agent. De standaard waarde is '/usr/local/ASR '<br>
    `# cd /usr/local/ASR`
3. Wijzig de map naar "" ""//scripts "onder de installatie locatie<br>
    `# cd VX/scripts`
4. Maak een bash-shell script met de naam ' customscript.sh ' met uitvoerings machtigingen voor de hoofd gebruiker.<br>
    a. Het script moet ondersteuning bieden voor '--pre ' en '--post ' (Let op de dubbele streepjes) opdracht regel opties<br>
    b. Wanneer het script wordt aangeroepen met de optie vooraf, wordt de invoer/uitvoer van de toepassing geblokkeerd en wanneer deze wordt aangeroepen met de post-optie, moet de toepassings invoer/-uitvoer ontdooien.<br>
    c. Een voorbeeld sjabloon-<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Voeg de opdrachten voor invoer/uitvoer in de voor-en na-stap toe voor de toepassingen die app-consistentie vereisen. U kunt ervoor kiezen om een ander script toe te voegen en deze aan te roepen van ' customscript.sh ' met vooraf en post opties.

>[!Note]
>De versie van de Site Recovery-agent moet 9,24 of hoger zijn om aangepaste scripts te kunnen ondersteunen.

## <a name="replication-policy"></a>Beleid voor replicatie

### <a name="what-is-a-replication-policy"></a>Wat is een replicatie beleid?

Een replicatie beleid definieert de instellingen voor de Bewaar geschiedenis van herstel punten. Het beleid definieert ook de frequentie van app-consistente moment opnamen. Azure Site Recovery maakt standaard een nieuw replicatie beleid met de standaard instellingen van:

- 24 uur voor de Bewaar geschiedenis van herstel punten.
- 4 uur voor de frequentie van app-consistente moment opnamen.

### <a name="what-is-a-crash-consistent-recovery-point"></a>Wat is een crash-consistent herstel punt?

Een crash consistent herstel punt heeft de gegevens op de schijf alsof u tijdens de moment opname het netsnoer van de server hebt opgehaald. Het crash-consistente herstel punt bevat geen items die zich in het geheugen bevonden toen de moment opname werd gemaakt.

Vandaag kunnen de meeste toepassingen goed worden hersteld met crash-consistente moment opnamen. Een crash-consistent herstel punt is doorgaans voldoende voor geen database besturingssystemen en-toepassingen, zoals bestands servers, DHCP-servers en afdruk servers.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Wat is de frequentie van crash-consistente herstel punten genereren?

Site Recovery maakt om de vijf minuten een crash-consistent herstel punt.

### <a name="what-is-an-application-consistent-recovery-point"></a>Wat is een toepassings consistent herstel punt?

Toepassings consistente herstel punten worden gemaakt op basis van toepassings consistente moment opnamen. Toepassings consistente herstel punten nemen dezelfde gegevens op als crash-consistente moment opnamen terwijl ook gegevens worden vastgelegd in het geheugen en alle trans acties die worden uitgevoerd.

Vanwege hun extra inhoud zijn toepassings consistente moment opnamen het meest betrokken en nemen ze het langst mee. We raden toepassings consistente herstel punten aan voor database besturingssystemen en-toepassingen, zoals SQL Server.

>[!Note]
>Het is niet mogelijk om toepassings consistente herstel punten te maken op Windows-computers als dit meer dan 64 volumes heeft.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Wat is de impact van toepassings consistente herstel punten op de prestaties van toepassingen?

Met toepassings consistente herstel punten worden alle gegevens in het geheugen en in verwerking vastgelegd. Omdat herstel punten die gegevens vastleggen, vereisen ze Framework als Volume Shadow Copy Service in Windows om de toepassing stil te leggen. Als het vastleggen veelvuldig is, kan dit van invloed zijn op de prestaties wanneer de werk belasting al bezet is. Het is niet raadzaam om lage frequentie te gebruiken voor app-consistente herstel punten voor niet-data base-workloads. Zelfs voor de werk belasting van de data base is 1 uur voldoende.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Wat is de minimale frequentie voor het genereren van toepassings consistente herstel punten?

Site Recovery kunt een toepassings consistent herstel punt maken met een minimale frequentie van 1 uur.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hoe worden herstel punten gegenereerd en opgeslagen?

Laten we een voor beeld van een replicatie beleid zien om te begrijpen hoe Site Recovery herstel punten genereert. Dit replicatie beleid heeft een herstel punt met een Bewaar periode van 24 uur en een app-consistente frequentie momentopname van 1 uur.

Site Recovery maakt om de vijf minuten een crash-consistent herstel punt. U kunt deze frequentie niet wijzigen. Voor het afgelopen uur kunt u kiezen uit 12 crash-consistente punten en één app-consistent punt. Na verloop van tijd worden Site Recovery alle herstel punten na het laatste uur verwijderd en wordt er slechts één herstel punt per uur opgeslagen.

De volgende scherm afbeelding illustreert het voor beeld. In de scherm afbeelding:

- In het afgelopen uur zijn er herstel punten met een frequentie van 5 minuten.
- Voorbij het afgelopen uur Site Recovery slechts één herstel punt behouden.

   ![Lijst met gegenereerde herstel punten](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Hoe ver terug kan ik herstellen?

Het oudste herstel punt dat u kunt gebruiken, is 72 uur.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Ik heb een replicatie beleid van 24 uur. Wat gebeurt er als een probleem verhindert dat Site Recovery meer dan 24 uur herstel punten genereert? Gaan mijn vorige herstel punten verloren?

Nee, Site Recovery blijven al uw vorige herstel punten bewaard. Afhankelijk van het retentie venster voor herstel punten wordt Site Recovery het oudste punt alleen vervangen als nieuwe punten worden gegenereerd. Vanwege het probleem kunnen Site Recovery geen nieuwe herstel punten genereren. Totdat er nieuwe herstel punten zijn, blijven alle oude punten aanwezig nadat u het venster van bewaren hebt bereikt.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hoe kan ik het replicatie beleid wijzigen nadat de replicatie is ingeschakeld op een VM?

Ga naar **site Recovery kluis**  >  **site Recovery infrastructuur**  >  **beleid voor replicatie**. Selecteer het beleid dat u wilt bewerken en sla de wijzigingen op. Elke wijziging is ook van toepassing op alle bestaande replicaties.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Zijn alle herstel punten een volledige kopie van de virtuele machine of een differentieel exemplaar?

Het eerste herstel punt dat wordt gegenereerd, heeft de volledige kopie. Eventuele opeenvolgende herstel punten bevatten Delta wijzigingen.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Verhoogt de Bewaar periode van herstel punten de opslag kosten?

Ja, als u de retentie periode van 24 uur tot 72 uur verhoogt, worden de herstel punten door Site Recovery voor een extra 48 uur opgeslagen. De toegevoegde tijd maakt kosten voor opslag. Eén herstel punt kan bijvoorbeeld Delta wijzigingen van 10 GB hebben, met een kosten per GB van $0,16 per maand. Meer kosten zijn $1,60 × 48 per maand.


## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Als ik een failover naar Azure krijg, krijg ik dan na een failover toegang tot de Azure-Vm's?

U kunt de virtuele Azure-machines benaderen via een beveiligde internetverbinding, via een VPN tussen sites of via Azure ExpressRoute. U moet een aantal dingen voorbereiden om verbinding te kunnen maken. [Meer informatie](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Als ik een failover naar Azure krijg, moet Azure ervoor zorgen dat mijn gegevens flexibel zijn?
Azure is ontworpen voor herstelbaarheid. Site Recovery is al ontworpen voor failover naar een secundair Azure-Data Center, conform de SLA van Azure. Als dit het geval is, zorgen we ervoor dat uw meta gegevens en kluizen binnen dezelfde geografische regio blijven die u voor uw kluis hebt gekozen.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Als ik een replicatie tussen twee data centers vind? wat gebeurt er als mijn primaire Data Center een onverwachte onderbreking ondervindt?
U kunt een ongeplande failover vanuit de secundaire site activeren. Site Recovery heeft geen connectiviteit vanuit de primaire site nodig om de failover uit te voeren.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?
Failover wordt niet automatisch uitgevoerd. U initieert failovers met één klik in de portal of u kunt [site Recovery Power shell](/powershell/module/az.recoveryservices) gebruiken om een failover te activeren. Failback is een eenvoudige actie in de Site Recovery Portal.

Als u wilt automatiseren, kunt u de on-premises Orchestrator of Operations Manager gebruiken om een fout in de virtuele machine op te sporen en vervolgens de failover activeren met behulp van de SDK.

* [Meer](site-recovery-create-recovery-plans.md) informatie over herstel plannen.
* [Lees meer](site-recovery-failover.md) over failover.
* [Meer](./vmware-azure-failback.md) informatie over het mislukken van back-ups van virtuele VMware-machines en fysieke servers

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Als mijn on-premises host niet reageert of vastloopt, kan ik dan een failback uitvoeren naar een andere host?
Ja, u kunt het herstel van de alternatieve locatie gebruiken om naar een andere host van Azure te failback.

* [Voor virtuele VMware-machines](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Voor virtuele Hyper-V-machines](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>Wat is het verschil tussen het volt ooien van de migratie, het door voeren en uitschakelen van replicatie?

Als er een failover is uitgevoerd naar de doel locatie van een machine vanaf de bron locatie, zijn er drie opties beschikbaar waaruit u kunt kiezen. Alle drie de verschillende doel einden gebruiken-

1.  Als u de **migratie voltooit** , gaat u niet meer terug naar de bron locatie. U hebt gemigreerd naar de doel regio en nu bent u klaar. Klik op volledige migratie triggers door voeren en schakel de replicatie vervolgens intern uit. 
2.  **Door voeren** houdt in dat dit niet het einde van uw replicatie proces is. Het replicatie-item in combi natie met de configuratie blijft behouden en u kunt op een later tijdstip **opnieuw beveiligen** om de replicatie van uw machines naar de bron regio mogelijk te maken. 
3.  Als u **replicatie uitschakelt** , wordt de replicatie uitgeschakeld en wordt alle gerelateerde configuratie verwijderd. Dit heeft geen invloed op de reeds bestaande machine in de doel regio.

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan ik Site Recovery scenario's automatiseren met een SDK?
Ja. U kunt Site Recovery-werkstromen automatiseren met de Rest API-, PowerShell- of Azure-SDK. Momenteel ondersteunde scenario's voor de implementatie van Site Recovery met behulp van Power shell:

* [Virtuele Hyper-V-machines in VMMs-Clouds repliceren naar Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Virtuele Hyper-V-machines zonder VMM repliceren naar Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [VMware naar Azure repliceren met Power shell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Upgrade van onderdeel/provider

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Waar vind ik de release opmerkingen/update pakketten van Site Recovery upgrades

[Meer](site-recovery-whats-new.md) informatie over nieuwe updates en het [ophalen van Rollup-informatie](service-updates-how-to.md).

## <a name="next-steps"></a>Volgende stappen
* Lees het [Site Recovery-overzicht](site-recovery-overview.md)