---
title: Ondersteunings matrix voor het verplaatsen van virtuele Azure-machines naar een andere regio met Azure resource-overschakeling
description: Bekijk de ondersteuning voor het verplaatsen van virtuele Azure-machines tussen regio's met Azure resource-overschakeling.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 3022b2d4954ffaef71e17ed28dd9b6f141d4da70
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980487"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Ondersteuning voor het verplaatsen van virtuele Azure-machines tussen Azure-regio's

In dit artikel vindt u een overzicht van de ondersteuning en vereisten bij het verplaatsen van virtuele machines en gerelateerde netwerk bronnen in azure-regio's met behulp van resources.

> [!IMPORTANT]
> Azure Resource Mover is momenteel in preview.


## <a name="windows-vm-support"></a>Ondersteuning voor Windows-VM'S

Resource-overwerking biedt ondersteuning voor Azure-Vm's waarop deze Windows-besturings systemen worden uitgevoerd.

**Besturingssysteem** | **Details**
--- | ---
Windows Server 2019 | Ondersteund voor Server Core, server met bureaublad ervaring.
Windows Server 2016  | Ondersteunde Server Core, server met bureaublad ervaring.
Windows Server 2012 R2 | Ondersteund.
Windows Server 2012 | Ondersteund.
Windows Server 2008 R2 met SP1/SP2 | Ondersteund.<br/><br/> Voor computers met Windows Server 2008 R2 met SP1/SP2 moet u een Windows [Servicing Stack update (Ssu)](https://support.microsoft.com/help/4490628) en [SHA-2-update](https://support.microsoft.com/help/4474419)installeren.  SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt. Meer informatie over [SHA-2-upgrade en-vereisten](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Ondersteund.
Windows 8,1 (x64) | Ondersteund.
Windows 8 (x64) | Ondersteund.
Windows 7 (x64) met SP1 en hoger | Installeer een Windows [Servicing Stack update (Ssu)](https://support.microsoft.com/help/4490628) en [SHA-2-update](https://support.microsoft.com/help/4474419) op computers met Windows 7 met SP1.  SHA-1 wordt niet ondersteund vanaf september 2019 en als het ondertekenen van code voor SHA-2 niet is ingeschakeld, mislukt de stap voorbereiden. Meer informatie over [SHA-2-upgrade en-vereisten](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Ondersteuning voor Linux-VM

Resource verplaatsing biedt ondersteuning voor Azure-Vm's met deze Linux-besturings systemen.

**Besturingssysteem** | **Details**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0, 8,1
Ubuntu 14,04 LTS-server | [Ondersteunde kernel-versies](#supported-ubuntu-kernel-versions)
Ubuntu 16,04 LTS-server | [Ondersteunde kernel-versie](#supported-ubuntu-kernel-versions)<br/><br/> Ubuntu-servers die gebruikmaken van verificatie op basis van wacht woorden en het maken van een Cloud-init-pakket voor het configureren van Cloud-Vm's, kunnen op een wacht woord gebaseerde aanmelding hebben uitgeschakeld bij failover (afhankelijk van de Cloud-init-configuratie). Aanmelding op basis van wacht woorden kan opnieuw worden ingeschakeld op de virtuele machine door het wacht woord opnieuw in te stellen in het menu > ondersteuning voor het oplossen van problemen met de >-instellingen (van de failover-VM in de Azure Portal.
Ubuntu 18,04 LTS-server | [Ondersteunde kernel-versie](#supported-ubuntu-kernel-versions).
Debian 7 | [Ondersteunde kernel-versies](#supported-debian-kernel-versions).
Debian 8 | [Ondersteunde kernel-versies](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [Ondersteunde kernel-versies](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 en 15 SP1. [(Ondersteunde kernel-versies)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | Pack
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Met de Red Hat compatibele kernel of een onherstelbare versie van de Enter prise kernel van 3, 4 & 5 (UEK3, UEK4, UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Ondersteunde versies van Ubuntu-kernel

**Release** | **Kernelversie** 
--- | --- 
14,04 LTS |  3.13.0-24-generic naar 3.13.0-170-generic,<br/>3.16.0-25-generic naar 3.16.0-77-generic,<br/>3.19.0-18-generic naar 3.19.0-80-generic,<br/>4.2.0-18-generic naar 4.2.0-42-generic,<br/>4.4.0-21-generic naar 4.4.0-148-generic,<br/>4.15.0-1023-Azure naar 4.15.0-1045-Azure 
16,04 LTS |  4.4.0-21-algemeen naar 4.4.0-171-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-74-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1.066-Azure
18,04 LTS | 4.15.0-20-algemeen naar 4.15.0-74-algemeen </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-algemeen naar 5.0.0-37-generic </br> 5.3.0-19-generic tot 5.3.0-24-algemeen </br> 4.15.0-1009-Azure naar 4.15.0-1037-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1028-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1009-Azure


### <a name="supported-debian-kernel-versions"></a>Ondersteunde versies van Debian-kernel 

**Release** |  **Kernelversie** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 tot 3.2.0-6-amd64, 3.16.0 -0. bpo. 4-amd64 
Debian 8 |  3.16.0-4-amd64 tot 3.16.0-10-amd64, 4.9.0 -0. bpo. 4-amd64 tot 4.9.0 -0. bpo. 11-amd64 
Debian 8 |  3.16.0-4-amd64 tot 3.16.0-10-amd64, 4.9.0 -0. bpo. 4-amd64 tot 4.9.0 -0. bpo. 9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>Ondersteunde SUSE Linux Enterprise Server 12 kernel-versies 

**Release** | **Kernelversie** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Alle [Stock-SuSE 12 SP1-, SP2-, SP3-en SP4-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.34-Azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>Ondersteund SUSE Linux Enterprise Server 15-kernel-versies

**Release** | **Kernelversie** |
--- |  --- |
SUSE Linux Enterprise Server 15 en 15 SP1 |  Alle Stock-SUSE 15-en 15-kernels worden ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-8.22-Azure |

## <a name="supported-linux-file-systemguest-storage"></a>Ondersteund Linux-bestands systeem/gast opslag

* Bestands systemen: ext3, ext4, XFS, BTRFS
* Volume manager: LVM2
* Multipath-software: toewijzing van apparaten


## <a name="supported-vm-compute-settings"></a>Ondersteunde VM-computer instellingen

**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
Grootte | Een Azure VM-grootte met ten minste twee CPU-kernen en 1 GB RAM | Controleer de [grootte van virtuele Azure-machines](../virtual-machines/sizes-general.md).
Beschikbaarheidssets | Ondersteund | Ondersteund.
Beschikbaarheidszones | Ondersteund | Ondersteund, afhankelijk van de ondersteuning van het doel gebied.
Installatie kopieën van Azure Gallery (gepubliceerd door micro soft) | Ondersteund | Wordt ondersteund als de virtuele machine wordt uitgevoerd op een ondersteund besturings systeem.
Installatie kopieën van Azure Gallery (gepubliceerd door derden)  | Ondersteund | Wordt ondersteund als de virtuele machine wordt uitgevoerd op een ondersteund besturings systeem.
Aangepaste installatie kopieën (gepubliceerd door derden)| Ondersteund | Wordt ondersteund als de virtuele machine wordt uitgevoerd op een ondersteund besturings systeem.
Vm's met Site Recovery | Niet ondersteund | Resources verplaatsen tussen regio's voor virtuele machines, met behulp van Site Recovery op de back-end. Als u Site Recovery al gebruikt, schakelt u replicatie uit en start u vervolgens het voorbereidings proces.
Azure RBAC-beleid | Niet ondersteund | Beleids regels op basis van op rollen gebaseerd toegangs beheer (Azure RBAC) op Vm's worden niet naar de virtuele machine in de doel regio gekopieerd.
Uitbreidingen | Niet ondersteund | Extensies worden niet naar de virtuele machine in de doel regio gekopieerd. Installeer ze hand matig nadat de verplaatsing is voltooid.


## <a name="supported-vm-storage-settings"></a>Ondersteunde VM-opslag instellingen

Deze tabel bevat een overzicht van de ondersteuning voor de Azure VM-besturingssysteem schijf, de gegevens schijf en de tijdelijke schijf. Het is belang rijk om te kijken naar de schijfruimte limieten en doelen van de virtuele machine voor [beheerde schijven](../virtual-machines/disks-scalability-targets.md) om prestatie problemen te voor komen.

> [!NOTE]
> De grootte van de doel-VM moet gelijk zijn aan of groter zijn dan de bron-VM. De para meters die worden gebruikt voor validatie zijn: aantal gegevens schijven, Nic's aantal, beschik bare Cpu's, geheugen in GB. Als er een fout optreedt, wordt deze sn't.


**Onderdeel** | **Ondersteuning** | **Details**
--- | --- | ---
Maximale grootte van de besturingssysteem schijf | 2048 GB | Meer [informatie](../virtual-machines/managed-disks-overview.md) over VM-schijven.
Tijdelijke schijf | Niet ondersteund | De tijdelijke schijf wordt altijd uitgesloten van het voorbereidings proces.<br/><br/> Sla geen permanente gegevens op de tijdelijke schijf op. [Meer informatie](../virtual-machines/managed-disks-overview.md#temporary-disk).
Maximale grootte van gegevens schijf | 8192 GB voor beheerde schijven
Minimale grootte van gegevens schijf |  2 GB voor beheerde schijven |
Maximum aantal gegevens schijven | Maxi maal 64, in overeenstemming met de ondersteuning voor een specifieke Azure VM-grootte | Meer [informatie](../virtual-machines/sizes.md) over VM-grootten.
Wijzigings frequentie van gegevens schijven | Maxi maal 10 MBps per schijf voor Premium-opslag. Maxi maal 2 MBps per schijf voor standaard opslag. | Als de gemiddelde waarde voor het wijzigen van de gegevens op de schijf continu hoger is dan het maximum, wordt de voor bereiding niet opvangen.<br/><br/>  Als het maximum echter sporadisch wordt overschreden, kan de voor bereiding worden opgevangen, maar ziet u mogelijk enigszins vertraagde herstel punten.
Gegevens schijf (standaard opslag account) | Wordt niet ondersteund. | Wijzig het opslag type in beheerde schijf en probeer vervolgens de virtuele machine te verplaatsen.
Gegevens schijf (Premium Storage-account) | Niet ondersteund | Wijzig het opslag type in beheerde schijf en probeer vervolgens de virtuele machine te verplaatsen.
Beheerde schijf (standaard) | Ondersteund  |
Beheerde schijf (Premium) | Ondersteund |
Standard SSD | Ondersteund |
Generatie 2 (UEFI-opstart) | Ondersteund
Opslag account voor diagnostische gegevens over opstarten | Niet ondersteund | Schakel deze opnieuw in nadat u de virtuele machine naar de doel regio hebt verplaatst.
Vm's met Azure Disk Encryption ingeschakeld | Ondersteund | [Meer informatie](tutorial-move-region-encrypted-virtual-machines.md)
Vm's die versleuteling aan de server zijde gebruiken met door de klant beheerde sleutel | Ondersteund | [Meer informatie](tutorial-move-region-encrypted-virtual-machines.md)

### <a name="limits-and-data-change-rates"></a>Limieten en snelheid van gegevens wijzigingen

De volgende tabel geeft een overzicht van de limieten die zijn gebaseerd op onze tests. Deze omvatten niet alle mogelijke toepassings-I/O-combi Naties. De werkelijke resultaten variëren op basis van uw toepassings-I/O-mix. Er zijn twee limieten om te overwegen: gegevens verloop per schijf en per VM-gegevens verloop.

**Opslag doel** | **Gemiddelde bron schijf-I/O** |**Gemiddeld gegevens verloop van bron schijf** | **Totale gegevens verloop van bron schijf per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf

## <a name="supported-vm-networking-settings"></a>Ondersteunde VM-netwerk instellingen

**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
NIC | Ondersteund | Geef een bestaande resource in de doel regio op of maak een nieuwe resource tijdens het voorbereidings proces. 
Interne load balancer | Ondersteund | Geef een bestaande resource in de doel regio op of maak een nieuwe resource tijdens het voorbereidings proces.  
Openbare load balancer | Ondersteund | Geef een bestaande resource in de doel regio op of maak een nieuwe resource tijdens het voorbereidings proces.  
Openbaar IP-adres | Ondersteund | Geef een bestaande resource in de doel regio op of maak een nieuwe resource tijdens het voorbereidings proces.<br/><br/> Het open bare IP-adres is specifiek voor een regio en wordt niet na de verplaatsing in de doel regio bewaard. Houd er rekening mee dat u de netwerk instellingen (met inbegrip van de taakverdelings regels) op de doel locatie wijzigt.
Netwerkbeveiligingsgroep | Ondersteund | Geef een bestaande resource in de doel regio op of maak een nieuwe resource tijdens het voorbereidings proces.  
Gereserveerd (statisch) IP-adres | Ondersteund | U kunt dit momenteel niet configureren. De waarde wordt standaard ingesteld op de bron waarde. <br/><br/> Als de NIC op de bron-VM een statisch IP-adres heeft en het doel-subnet hetzelfde IP-adres beschikbaar heeft, wordt het toegewezen aan de doel-VM.<br/><br/> Als het doel-subnet niet hetzelfde IP-adres beschikbaar heeft, mislukt het verplaatsen van de virtuele machine.
Dynamisch IP-adres | Ondersteund | U kunt dit momenteel niet configureren. De waarde wordt standaard ingesteld op de bron waarde.<br/><br/> Als de NIC op de bron dynamische IP-adres Sering heeft, is de NIC op de doel-VM standaard ook dynamisch.
IP-configuraties | Ondersteund | U kunt dit momenteel niet configureren. De waarde wordt standaard ingesteld op de bron waarde.

## <a name="outbound-access-requirements"></a>Vereisten voor uitgaande toegang

Virtuele Azure-machines die u wilt verplaatsen, hebben uitgaande toegang nodig.


### <a name="url-access"></a>URL-toegang

 Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze URL’s toe:

**Naam** | **Openbare Azure-cloud** | **Details** 
--- | --- | --- 
Storage | `*.blob.core.windows.net`  | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount in de bronregio worden geschreven. 
Azure Active Directory | `login.microsoftonline.com`  | Verzorgt autorisatie en authenticatie voor de URL’s van Site Recovery. 
Replicatie | `*.hypervrecoverymanager.windowsazure.com` | Maakt het de VM mogelijk te communiceren met de Site Recovery-service. 
Service Bus | `*.servicebus.windows.net` | Maakt het de VM mogelijk bewakings- en diagnosegegevens van Site Recovery te schrijven. 

## <a name="nsg-rules"></a>NSG-regels
Als u een NSG-regels (netwerk beveiligings groep) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze regels voor de [service Tags](../virtual-network/service-tags-overview.md) . Elke regel moet uitgaande toegang toestaan op HTTPS (443).
- Maak een opslag label regel voor de bron regio.
- Maak een *AzureSiteRecovery* -label regel om toegang tot de site Recovery-service in een wille keurige regio toe te staan. Deze tag heeft afhankelijkheden voor deze andere tags, dus u moet hiervoor regels maken voor het volgende:
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- We raden u aan regels te testen in een niet-productie omgeving. [Bekijk enkele voor beelden](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Volgende stappen

Verplaats [een Azure VM](tutorial-move-region-virtual-machines.md) naar een andere regio met resource-overschakeling.
