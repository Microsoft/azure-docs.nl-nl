---
title: Ondersteuning voor nood herstel van virtuele Hyper-V-machines naar Azure met Azure Site Recovery
description: Een overzicht van de ondersteunde onderdelen en vereisten voor herstel na nood gevallen voor Hyper-V-VM'S naar Azure met Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 8d748f93337a770e0d565bab79fdfb3625bda70d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735519"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Ondersteunings matrix voor herstel na nood gevallen van on-premises virtuele Hyper-V-machines naar Azure

In dit artikel vindt u een overzicht van de ondersteunde onderdelen en instellingen voor herstel na nood gevallen van on-premises virtuele Hyper-V-machines naar Azure met behulp van [Azure site Recovery](site-recovery-overview.md).

>[!NOTE]
> Met Site Recovery worden klant gegevens niet uit de doel regio verplaatst of opgeslagen, waarin nood herstel voor de bron machines is ingesteld. Klanten kunnen een Recovery Services kluis uit een andere regio selecteren als ze dat doen. De Recovery Services kluis bevat meta gegevens, maar geen werkelijke klant gegevens.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

**Scenario** | **Details**
--- | ---
Hyper-V met Virtual Machine Manager <br> <br>| U kunt herstel na nood geval uitvoeren op Azure voor virtuele machines die worden uitgevoerd op Hyper-V-hosts die worden beheerd in de System Center Virtual Machine Manager infrastructuur resources.<br/><br/> U kunt dit scenario implementeren in de Azure Portal of met behulp van Power shell.<br/><br/> Wanneer Hyper-V-hosts door Virtual Machine Manager worden beheerd, kunt u herstel na nood gevallen ook uitvoeren op een secundaire on-premises site. Lees [deze zelf studie](hyper-v-vmm-disaster-recovery.md)voor meer informatie over dit scenario.
Hyper-V zonder Virtual Machine Manager | U kunt herstel na nood geval uitvoeren op Azure voor virtuele machines die worden uitgevoerd op Hyper-V-hosts die niet worden beheerd door Virtual Machine Manager.<br/><br/> U kunt dit scenario implementeren in de Azure Portal of met behulp van Power shell.

## <a name="on-premises-servers"></a>On-premises servers

**Server** | **Vereisten** | **Details**
--- | --- | ---
Hyper-V (wordt uitgevoerd zonder Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 met de nieuwste updates <br/><br/> **Opmerking:** De Server Core-installatie van deze besturings systemen wordt ook ondersteund. | Als u Windows Server 2012 R2 al hebt geconfigureerd met/of SCVMM 2012 R2 met Azure Site Recovery en een upgrade van het besturings systeem wilt uitvoeren, volgt u de [documentatie bij](upgrade-2012R2-to-2016.md) de richt lijnen.
Hyper-V (uitgevoerd met Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 <br/><br/> **Opmerking:** De Server Core-installatie van deze besturings systemen wordt ook ondersteund.  | Als Virtual Machine Manager wordt gebruikt, moeten hosts met Windows Server 2019 worden beheerd in Virtual Machine Manager 2019. Op dezelfde manier moeten hosts met Windows Server 2016 worden beheerd in Virtual Machine Manager 2016.

> [!NOTE]
> Zorg ervoor dat .NET Framework 4.6.2 of hoger aanwezig is op de on-premises server.

## <a name="replicated-vms"></a>Gerepliceerde Vm's


De volgende tabel bevat een overzicht van de VM-ondersteuning. Site Recovery ondersteunt alle workloads die worden uitgevoerd op een ondersteund besturings systeem.

 **Onderdeel** | **Details**
--- | ---
VM-configuratie | Vm's die naar Azure repliceren, moeten voldoen aan de [vereisten van Azure](#azure-vm-requirements).
Gastbesturingssysteem | Elk gast besturingssysteem dat wordt [ondersteund voor Azure](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases)..<br/><br/> Windows Server 2016 nano server wordt niet ondersteund.


## <a name="vmdisk-management"></a>VM/schijf beheer

**Actie** | **Details**
--- | ---
Grootte van schijf op gerepliceerde Hyper-V-VM wijzigen | Wordt niet ondersteund. Schakel replicatie uit, breng de wijziging aan en schakel de replicatie voor de virtuele machine opnieuw in.
Een schijf toevoegen aan een gerepliceerde Hyper-V-VM | Wordt niet ondersteund. Schakel replicatie uit, breng de wijziging aan en schakel de replicatie voor de virtuele machine opnieuw in.

## <a name="hyper-v-network-configuration"></a>Configuratie van Hyper-V-netwerk

**Onderdeel** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Hostnetwerkadapter: NIC-koppeling | Ja | Ja
Hostnetwerkadapter: VLAN | Ja | Ja
Hostcluster: IPv4 | Ja | Ja
Hostnetwerkadapter: IPv6 | Nee | Nee
VM-netwerk van de gast: NIC-koppeling | Nee | Nee
VM-netwerk van gast: IPv4 | Ja | Ja
VM-netwerk van de gast: IPv6 | Nee | Ja
VM-netwerk van de gast: statisch IP-adres (Windows) | Ja | Ja
VM-netwerk van de gast: statisch IP-adres (Linux) | Nee | Nee
VM-netwerk van de gast: meerdere NIC'S | Ja | Ja
HTTPS-proxy | Nee | Nee
Persoonlijke koppelings toegang tot Site Recovery service | Ja. [Meer informatie](hybrid-how-to-enable-replication-private-endpoints.md). | Ja. [Meer informatie](hybrid-how-to-enable-replication-private-endpoints.md).




## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM-netwerk configuratie (na failover)

**Onderdeel** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ja | Ja
ILB | Ja | Ja
ELB | Ja | Ja
Azure Traffic Manager | Ja | Ja
Multi-NIC | Ja | Ja
Gereserveerd IP | Ja | Ja
IPv4 | Ja | Ja
Bron-IP-adres behouden | Ja | Ja
Azure Virtual Network Service-eind punten<br/> (zonder Azure Storage firewalls) | Ja | Ja
Versneld netwerken | Nee | Nee


## <a name="hyper-v-host-storage"></a>Opslag voor Hyper-V-host

**Storage** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Meerdere paden (MPIO). Getest met:<br></br> Micro soft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM voor CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM-gast opslag

**Storage** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | Ja | Ja
Generatie 2 VM | Ja | Ja
EFI/UEFI<br></br>De gemigreerde VM in azure wordt automatisch geconverteerd naar een BIOS-opstart-VM. Op de VM moet Windows Server 2012 en hoger worden uitgevoerd. De besturingssysteem schijf moet Maxi maal vijf partities of minder hebben en de grootte van de besturingssysteem schijf moet kleiner zijn dan 300 GB.| Ja | Ja
Gedeelde cluster schijf | Nee | Nee
Versleutelde schijf | Nee | Nee
NFS | NA | NA
SMB 3.0 | Nee | Nee
RDM | NA | NA
Schijf >1 TB | Ja, Maxi maal 4.095 GB | Ja, Maxi maal 4.095 GB
Schijf: logische en fysieke sector van 4 KB | Niet ondersteund: gen 1/gen 2 | Niet ondersteund: gen 1/gen 2
Schijf: 4 KB logische en 512-bytes fysieke sector | Ja |  Ja
Beheer van logische volumes (LVM). LVM wordt alleen ondersteund op gegevens schijven. Azure biedt slechts één besturingssysteem schijf. | Ja | Ja
Volume met gestripte schijf >1 TB | Ja | Ja
Opslagruimten | Nee | Nee
Hot toevoegen/verwijderen schijf | Nee | Nee
Schijf uitsluiten | Ja | Ja
Meerdere paden (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Onderdeel** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Lokaal redundante opslag | Ja | Ja
Geografisch redundante opslag | Ja | Ja
Geografisch redundante opslag met leestoegang | Ja | Ja
Zone-redundante opslag | Nee | Nee
Cool Storage | Nee | Nee
Hot Storage| Nee | Nee
Blok-blobs | Nee | Nee
Versleuteling op rest (SSE)| Ja | Ja
Versleuteling in rust (CMK) <br></br> (Alleen voor failover naar beheerde schijven)| Ja (via Power shell AZ 3.3.0 module) | Ja (via Power shell AZ 3.3.0 module)
Dubbele versleuteling bij rest <br></br> (Alleen voor failover naar beheerde schijven) <br></br> Meer informatie over ondersteunde regio's voor [Windows](../virtual-machines/disk-encryption.md) en [Linux](../virtual-machines/disk-encryption.md) | Ja (via Power shell AZ 3.3.0 module) | Ja (via Power shell AZ 3.3.0 module)
Premium Storage | Ja | Ja
Standard Storage | Ja | Ja
Import/export-service | Nee | Nee
Azure Storage accounts waarvoor Firewall is ingeschakeld | Ja. Voor doel opslag en cache. | Ja. Voor doel opslag en cache.
Opslag account wijzigen | Nee. Het doel Azure Storage account kan niet worden gewijzigd nadat de replicatie is ingeschakeld. Als u herstel na nood gevallen wilt wijzigen, uitschakelen en opnieuw inschakelen. | Nee
Optie voor beveiligde overdracht | Ja | Ja


## <a name="azure-compute-features"></a>Azure Compute-functies

**Functie** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Beschikbaarheidssets | Ja | Ja
HUB | Ja | Ja  
Managed Disks | Ja, voor failover.<br/><br/> Failback van beheerde schijven wordt niet ondersteund. | Ja, voor failover.<br/><br/> Failback van beheerde schijven wordt niet ondersteund.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

On-premises Vm's die u naar Azure repliceert, moeten voldoen aan de vereisten van de Azure-VM die in deze tabel worden samenvatten.

**Onderdeel** | **Vereisten** | **Details**
--- | --- | ---
Gastbesturingssysteem | Site Recovery ondersteunt alle besturings systemen die worden [ondersteund door Azure](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).  | De controle van vereisten mislukt als dit niet wordt ondersteund.
Architectuur van gast besturingssysteem | 32-bits (Windows Server 2008)/64-bit | De controle van vereisten mislukt als dit niet wordt ondersteund.
Grootte van de besturingssysteemschijf | Maxi maal 2.048 GB voor virtuele machines van de eerste generatie.<br/><br/> Maxi maal 300 GB voor virtuele machines van de tweede generatie.  | De controle van vereisten mislukt als dit niet wordt ondersteund.
Aantal besturingssysteemschijven | 1 | De controle van vereisten mislukt als dit niet wordt ondersteund.
Aantal gegevensschijven | 16 of minder  | De controle van vereisten mislukt als dit niet wordt ondersteund.
Grootte van VHD-gegevensschijf | Maxi maal 4.095 GB | De controle van vereisten mislukt als dit niet wordt ondersteund.
Netwerkadapters | Meerdere netwerkadapters worden ondersteund |
Gedeelde VHD | Niet ondersteund | De controle van vereisten mislukt als dit niet wordt ondersteund.
FC-schijf | Niet ondersteund | De controle van vereisten mislukt als dit niet wordt ondersteund.
Harde-schijfindeling | VHD <br/><br/> INDELING | Site Recovery converteert VHDX automatisch naar VHD wanneer u een failover naar Azure voert. Wanneer u een failover naar on-premises doorvoert, blijven de virtuele machines de VHDX-indeling gebruiken.
BitLocker | Niet ondersteund | BitLocker moet worden uitgeschakeld voordat u replicatie voor een virtuele machine inschakelt.
VM-naam | 1 tot 63 tekens. Alleen letters, cijfers en afbreekstreepjes. De VM-naam moet beginnen en eindigen met een letter of cijfer. | Werk de waarde in de VM-eigenschappen in Site Recovery bij.
VM-type | Eerste generatie<br/><br/> Generatie 2: Windows | Virtuele machines van de tweede generatie met het type basis van het besturings systeem (met een of twee gegevens volumes, geformatteerd als VHDX) en minder dan 300 GB schijf ruimte worden ondersteund.<br></br>Vm's van de Linux-generatie 2 worden niet ondersteund. [Meer informatie](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services kluis acties

**Actie** |  **Hyper-V met VMM** | **Hyper-V zonder VMM**
--- | --- | ---
De kluis verplaatsen tussen resource groepen<br/><br/> Binnen en tussen abonnementen | Nee | Nee
Opslag, netwerk, Azure-Vm's verplaatsen tussen resource groepen<br/><br/> Binnen en tussen abonnementen | Nee | Nee

> [!NOTE]
> Bij het repliceren van Hyper-VMs van on-premises naar Azure, kunt u naar slechts één AD-Tenant repliceren vanaf één specifieke omgeving-Hyper-V-site of Hyper-V met VMM, indien van toepassing.


## <a name="provider-and-agent"></a>Provider en agent

Om ervoor te zorgen dat uw implementatie compatibel is met de instellingen in dit artikel, moet u ervoor zorgen dat u de nieuwste versie van de provider en de agent uitvoert.

**Naam** | **Beschrijving** | **Details**
--- | --- | --- 
Azure Site Recovery provider | Coördineert de communicatie tussen on-premises servers en Azure <br/><br/> Hyper-V met Virtual Machine Manager: geïnstalleerd op Virtual Machine Manager-servers<br/><br/> Hyper-V zonder Virtual Machine Manager: geïnstalleerd op Hyper-V-hosts| Nieuwste versie: 5.1.2700.1 (beschikbaar via de Azure Portal)<br/><br/> [Nieuwste functies en oplossingen](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-agent | Coördinaten van replicatie tussen Hyper-V-Vm's en Azure<br/><br/> Geïnstalleerd op on-premises Hyper-V-servers (met of zonder Virtual Machine Manager) | Nieuwste agent die beschikbaar is via de portal






## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [voorbereiden van Azure](tutorial-prepare-azure.md) voor herstel na nood gevallen van on-premises virtuele Hyper-V-machines.