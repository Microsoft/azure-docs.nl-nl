---
title: Ondersteunings matrix voor herstel na nood gevallen voor Azure VM met Azure Site Recovery
description: Hiermee wordt een overzicht gegeven van de ondersteuning voor herstel na nood gevallen voor Azure Vm's naar een secundaire regio met Azure Site Recovery.
ms.topic: article
ms.date: 11/29/2020
ms.author: raynew
ms.openlocfilehash: 78c27292a92152946ba33258d27940e3c1aea47d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391572"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Ondersteuningsmatrix voor herstel na noodgeval van Azure-VM's tussen Azure-regio's

In dit artikel vindt u een overzicht van de ondersteuning en vereisten voor herstel na nood gevallen van Azure-Vm's van de ene Azure-regio naar de andere, met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.


## <a name="deployment-method-support"></a>Ondersteuning voor implementatie methoden

**Implementatie** |  **Ondersteuning**
--- | ---
**Azure-portal** | Ondersteund.
**PowerShell** | Ondersteund. [Meer informatie](azure-to-azure-powershell.md)
**REST API** | Ondersteund.
**CLI** | Momenteel niet ondersteund


## <a name="resource-support"></a>Resource-ondersteuning

**Resource actie** | **Details**
--- | ---
**Kluizen verplaatsen tussen resource groepen** | Niet ondersteund
**Berekenings-en opslag/netwerk bronnen verplaatsen over resource groepen** | Wordt niet ondersteund.<br/><br/> Als u een virtuele machine of gekoppelde onderdelen, zoals opslag/netwerk, verplaatst nadat de VM is gerepliceerd, moet u de replicatie voor de virtuele machine uitschakelen en opnieuw inschakelen.
**Virtuele Azure-machines van het ene naar het andere abonnement repliceren voor herstel na nood gevallen** | Ondersteund binnen dezelfde Azure Active Directory Tenant.
**Vm's migreren tussen regio's binnen ondersteunde geografische clusters (binnen en tussen abonnementen)** | Ondersteund binnen dezelfde Azure Active Directory Tenant.
**Vm's binnen dezelfde regio migreren** | Wordt niet ondersteund.
**Met Azure toegewezen hosts** | Wordt niet ondersteund.

## <a name="region-support"></a>Ondersteuning voor regio

U kunt virtuele machines repliceren en herstellen tussen twee regio's binnen hetzelfde geografische cluster. Geografische clusters worden gedefinieerd om gegevens latentie en soevereiniteit in gedachte te houden.


**Geografisch cluster** | **Azure-regio's**
-- | --
Lopende | Canada-oost, Canada-centraal, Zuid-Centraal VS, West-Centraal VS, VS-Oost, VS-Oost 2, VS-West, VS-West 2, VS-midden, Noord-Centraal VS
Europa | UK-west, UK-zuid, Europa-noord, Europa-west, Zuid-Afrika-west, Zuid-Afrika-noord, Noor wegen Oost, Frankrijk-centraal, Zwitserland-noord, Duitsland-west-centraal
Azië | India-zuid, Centraal-India, West-India, Zuidoost-Azië, Azië-oost, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid
Australië    | Australië-oost, Australië-zuidoost, Australië-centraal, Australië-centraal 2
Azure Government    | Amerikaanse GOVe Virginia, VS GOV Iowa, US GOV Arizona, VS GOV Texas, US DOD Oost, US DOD-centraal
Duitsland    | Duitsland-centraal, Duitsland-noordoost
China | China-oost, China-noord, China North2, China Oost2
Beperkte regio's die zijn gereserveerd voor in-the-Country nood herstel |Zwitserland-west gereserveerd voor Zwitserland-noord, Frankrijk-zuid gereserveerd voor Frankrijk-centraal, UAE-centraal beperkt voor UAE-noord klanten, Noor wegen West voor Noor wegen Oost-klanten

>[!NOTE]
>
> - U kunt voor **Brazilië-Zuid** repliceren en een failover uitvoeren naar deze regio's: Zuid-Centraal VS, West-Centraal VS, VS-Oost, VS-Oosten 2, VS-West, VS-West 2 en Noord-Centraal vs.
> - Brazilië-zuid kan alleen worden gebruikt als bron regio op basis waarvan Vm's kunnen worden gerepliceerd met behulp van Site Recovery. Het kan niet als doel regio fungeren. Dit komt door latentie problemen als gevolg van geografische afstanden. Houd er rekening mee dat als u een failover van Brazilië-zuid als bron regio naar een doel, de failback-Brazilië-zuid vanuit de doel regio wordt ondersteund.
> - U kunt binnen regio's werken waarvoor u de juiste toegang hebt.
> - Als de regio waarin u een kluis wilt maken, niet wordt weer gegeven, controleert u of uw abonnement toegang heeft tot het maken van resources in die regio.
> - Als u geen regio binnen een geografisch cluster ziet wanneer u replicatie inschakelt, moet u ervoor zorgen dat uw abonnement beschikt over machtigingen voor het maken van Vm's in die regio.



## <a name="cache-storage"></a>Cacheopslag

Deze tabel geeft een overzicht van de ondersteuning van het cache-opslag account dat wordt gebruikt door Site Recovery tijdens de replicatie.

**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
V2-opslag accounts voor algemeen gebruik (warme en koele laag) | Ondersteund | Het gebruik van GPv2 wordt niet aanbevolen omdat de transactie kosten voor v2 beduidend hoger zijn dan v1 opslag accounts.
Premium Storage | Niet ondersteund | Standaard opslag accounts worden gebruikt voor cache opslag, om de kosten te optimaliseren.
Firewalls voor virtuele netwerken Azure Storage  | Ondersteund | Als u gebruikmaakt van het cache-opslag account of het doel opslag account van de firewall, moet u [vertrouwde micro soft-Services toestaan](../storage/common/storage-network-security.md#exceptions).<br></br>Zorg er ook voor dat u toegang tot ten minste één subnet van het bron-Vnet toestaat.


## <a name="replicated-machine-operating-systems"></a>Besturings systemen van de gerepliceerde machine

Site Recovery ondersteunt replicatie van virtuele Azure-machines met de besturings systemen die worden vermeld in deze sectie. Houd er rekening mee dat als er een al replicerende machine wordt geüpgraded (of downgradet) naar een andere belang rijke kernel, u de replicatie wilt uitschakelen en de replicatie opnieuw wilt inschakelen na de upgrade.

### <a name="windows"></a>Windows


**Besturingssysteem** | **Details**
--- | ---
Windows Server 2019 | Ondersteund voor Server Core, server met bureaublad ervaring.
Windows Server 2016  | Ondersteunde Server Core, server met bureaublad ervaring.
Windows Server 2012 R2 | Ondersteund.
Windows Server 2012 | Ondersteund.
Windows Server 2008 R2 met SP1/SP2 | Ondersteund.<br/><br/> Van versie [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility service-extensie voor virtuele Azure-machines moet u een Windows [Servicing Stack update (Ssu)](https://support.microsoft.com/help/4490628) en [SHA-2-update](https://support.microsoft.com/help/4474419) installeren op computers met Windows Server 2008 R2 SP1/SP2.  SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt. Meer informatie over [SHA-2-upgrade en-vereisten](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Ondersteund.
Windows 8,1 (x64) | Ondersteund.
Windows 8 (x64) | Ondersteund.
Windows 7 (x64) met SP1 en hoger | Van versie [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility service-extensie voor virtuele Azure-machines moet u een Windows [Servicing Stack update (Ssu)](https://support.microsoft.com/help/4490628) en [SHA-2-update](https://support.microsoft.com/help/4474419) installeren op computers met Windows 7 met SP1.  SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/geüpgraded. Meer informatie over [SHA-2-upgrade en-vereisten](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Besturingssysteem** | **Details**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609/), [8,3](https://support.microsoft.com/help/4597409/)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10 </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, [7,8](https://support.microsoft.com/help/4564347/), [7,9 pre-GA versie](https://support.microsoft.com/help/4578241/), 7,9 ga-versie wordt ondersteund vanuit 9,37 Hot Fix patch * * </br> 8,0, 8,1, [8,2](https://support.microsoft.com/en-us/help/4570609), [8,3](https://support.microsoft.com/help/4597409/)
Ubuntu 14,04 LTS-server | Biedt ondersteuning voor alle 14,04. *x* -versies; [Ondersteunde kernel-versies](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Ubuntu 16,04 LTS-server | Biedt ondersteuning voor alle 16,04. *x* -versies; [Ondersteunde kernel-versie](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servers die gebruikmaken van verificatie op basis van wacht woorden en aanmelden en het pakket Cloud-init om Cloud-Vm's te configureren, hebben mogelijk een op wacht woord gebaseerde aanmelding uitgeschakeld bij failover (afhankelijk van de cloudinit-configuratie). Aanmelden op basis van wacht woorden kan opnieuw worden ingeschakeld op de virtuele machine door het wacht woord opnieuw in te stellen in het menu > ondersteuning voor het oplossen van problemen met de >-instellingen (van de VM waarvoor een failover is uitgevoerd in de Azure Portal.
Ubuntu 18,04 LTS-server | Biedt ondersteuning voor alle 18,04. *x* -versies; [Ondersteunde kernel-versie](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servers die gebruikmaken van verificatie op basis van wacht woorden en aanmelden en het pakket Cloud-init om Cloud-Vm's te configureren, hebben mogelijk een op wacht woord gebaseerde aanmelding uitgeschakeld bij failover (afhankelijk van de cloudinit-configuratie). Aanmelden op basis van wacht woorden kan opnieuw worden ingeschakeld op de virtuele machine door het wacht woord opnieuw in te stellen in het menu > ondersteuning voor het oplossen van problemen met de >-instellingen (van de VM waarvoor een failover is uitgevoerd in de Azure Portal.
Ubuntu 20,04 LTS-server | Biedt ondersteuning voor alle 20,04. *x* -versies; [Ondersteunde kernel-versie](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Biedt ondersteuning voor alle 7. [ondersteunde versies](#supported-debian-kernel-versions-for-azure-virtual-machines) van de kernel voor *x* -versies
Debian 8 | Biedt ondersteuning voor alle 8. [ondersteunde versies](#supported-debian-kernel-versions-for-azure-virtual-machines) van de kernel voor *x* -versies
Debian 9 | Biedt ondersteuning voor 9,1 tot 9,13. Debian 9,0 wordt niet ondersteund. [Ondersteunde kernel-versies](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Ondersteunde kernel-versies](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(ondersteunde kernel-versies)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(ondersteunde kernel-versies)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | Pack<br/><br/> De upgrade van replicerende machines van SP3 naar SP4 wordt niet ondersteund. Als een gerepliceerde machine is bijgewerkt, moet u de replicatie uitschakelen en de replicatie opnieuw inschakelen na de upgrade.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [7,9](https://support.microsoft.com/help/4597409), [8,0](https://support.microsoft.com/help/4573888/), [8,1](https://support.microsoft.com/help/4573888/) (met de Red Hat compatibele kernel of een onherstelbare versie van de Enter PRISE kernel 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8,1 (uitgevoerd op alle UEK-kernels en RedHat-kernel <= 3.10.0-1062. * worden ondersteund in [9,35](https://support.microsoft.com/help/4573888/). Ondersteuning voor rest van de RedHat-kernels is beschikbaar in [9,36](https://support.microsoft.com/help/4578241/))

> [!NOTE]
> Voor Linux-versies ondersteunt Azure Site Recovery geen aangepaste installatie kopieën van besturings systemen. Alleen de aandelen-kernels die deel uitmaken van de distributie secundaire versie/update worden ondersteund.

* * Opmerking: ter ondersteuning van de meest recente Linux-kernels binnen vijf tien dagen na de release, Azure Site Recovery roll-to-pluggable patch van de nieuwste versie van de Mobility-agent. Deze oplossing wordt uitgegerold tussen twee versies van de primaire versie. Volg de stappen die in [dit artikel](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)worden beschreven om bij te werken naar de nieuwste versie van Mobility agent (inclusief Hot Fix patch). Deze patch is momenteel geïmplementeerd voor Mobility-agents die worden gebruikt in azure naar Azure DR scenario.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde Ubuntu-kernel-versies voor virtuele Azure-machines

**Release** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
14,04 LTS | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 3.13.0-24-generic naar 3.13.0-170-generic,<br/>3.16.0-25-generic naar 3.16.0-77-generic,<br/>3.19.0-18-generic naar 3.19.0-80-generic,<br/>4.2.0-18-generic naar 4.2.0-42-generic,<br/>4.4.0-21-generic naar 4.4.0-148-generic,<br/>4.15.0-1023-Azure naar 4.15.0-1045-Azure |
|||
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic naar 4.4.0-197-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-128-algemeen<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1102-Azure </br> 4.15.0-132-algemeen, 4.4.0-200-generic tot en met 9,40 Hot Fix patch * *|
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-algemeen naar 4.4.0-194-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-123-algemeen<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1098-azure </br> 4.4.0-197-algemeen, 4.15.0-126-generic, 4.15.0-128-algemeen, 4.15.0-1100-Azure, 4.15.0-1102-Azure tot en met 9,39 Hot Fix patch * *|
16,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-algemeen naar 4.4.0-190-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-118-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1096-Azure </br> 4.4.0-193-generic, 4.15.0-120-algemeen, 4.15.0-122-generic, 4.15.0-1098-azure tot en met 9,38 Hot Fix patch * *|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-algemeen naar 4.4.0-189-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-115-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1093-Azure </br> 4.4.0-190-generic, 4.15.0-117-algemeen, 4.15.0-118-generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure tot en met 9,37 Hot Fix patch * *|
16,04 LTS | [9,36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-algemeen naar 4.4.0-187-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-112-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1092-Azure |
|||
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-algemeen naar 4.15.0-129-generic </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-generic naar 5.0.0-63-generic </br> 5.3.0-19-Gene riek tot 5.3.0-69-algemeen </br> 5.4.0-37-generic naar 5.4.0-59-generic</br> 4.15.0-1009-Azure naar 4.15.0-1103-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1035-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1035-Azure </br> 4.15.0-1104-Azure, 4.15.0-130-generic, 4.15.0-132-generic, 5.4.0-1036-Azure, 5.4.0-60-algemeen, 5.4.0-62-generic tot en met 9,40 Hot Fix patch * *|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-algemeen naar 4.15.0-123-algemeen </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-generic naar 5.0.0-63-generic </br> 5.3.0-19-Gene riek tot 5.3.0-69-algemeen </br> 5.4.0-37-generic tot 5.4.0-53-generic</br> 4.15.0-1009-Azure naar 4.15.0-1099-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1035-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1031-Azure </br> 4.15.0-124-generic, 5.4.0-54-generic, 5.4.0-1032-Azure, 5.4.0-56-Gene riek, 4.15.0-1100-Azure, 4.15.0-126-generic, 4.15.0-128-algemeen, 5.4.0-58-generic, 4.15.0-1102-Azure, 5.4.0-1034-Azure tot en met 9,39 Hot Fix patch * *|
18,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-algemeen naar 4.15.0-118-generic </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-algemeen naar 5.0.0-61-algemeen </br> 5.3.0-19-generic naar 5.3.0-67-generic </br> 5.4.0-37-generic tot 5.4.0-48-algemeen</br> 4.15.0-1009-Azure naar 4.15.0-1096-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1035-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1026-Azure </br> 4.15.0-121-generic, 4.15.0-122-generic, 5.0.0-62-algemeen, 5.3.0-68-algemeen, 5.4.0-51-generic, 5.4.0-52-generic, 4.15.0-1099-Azure, 5.4.0-1031-Azure tot en met 9,38 Hot Fix patch * *|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-algemeen naar 4.15.0-115-generic </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-generic naar 5.0.0-60-generic </br> 5.3.0-19-Gene riek tot 5.3.0-66-algemeen </br> 5.4.0-37-generic tot 5.4.0-45-algemeen</br> 4.15.0-1009-Azure naar 4.15.0-1093-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1035-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1023-Azure</br> 4.15.0-117-algemeen, 4.15.0-118-generic, 5.0.0-61-algemeen, 5.3.0-67-generic, 5.4.0-47-algemeen, 5.4.0-48-generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure, 5.4.0-1025-Azure, 5.4.0-1026-Azure tot en met 9,37 Hot Fix patch * *|
18,04 LTS | [9,36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-algemeen naar 4.15.0-112-generic </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-generic naar 5.0.0-58-generic </br> 5.3.0-19-generic naar 5.3.0-65-generic </br> 5.4.0-37-generic naar 5.4.0-42-generic</br> 4.15.0-1009-Azure naar 4.15.0-1092-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1032-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1022-Azure </br> 5.0.0-60-generic & 5.3.0-1035-Azure tot en met 9,36 Hot Fix patch * *|
|||
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic naar 5.4.0-59 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1035-Azure </br> 5.8.0-29-generic naar 5.8.0-34-generic </br> 5.4.0-1036-Azure, 5.4.0-60-generic, 5.4.0-62-algemeen, 5.8.0-36-algemeen, 5.8.0-38-generic tot en met 9,40 Hot Fix patch * *|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic naar 5.4.0-53 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1031-Azure </br> 5.4.0-54-generic, 5.8.0-29-Gene riek, 5.4.0-1032-Azure, 5.4.0-56-generic, 5.8.0-31-Gene riek, 5.8.0-33-generic, 5.4.0-58-algemeen, 5.4.0-1034-Azure tot en met 9,39 Hot Fix patch * *
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic naar 5.4.0-53 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1031-Azure </br> 5.4.0-54-generic, 5.8.0-29-Gene riek, 5.4.0-1032-Azure, 5.4.0-56-generic, 5.8.0-31-Gene riek, 5.8.0-33-generic, 5.4.0-58-algemeen, 5.4.0-1034-Azure tot en met 9,39 Hot Fix patch * *
20,04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic tot 5.4.0-48 </br> -generic 5.4.0-1010-Azure tot 5.4.0-1026-Azure </br> 5.4.0-51-generic, 5.4.0-52-generic, 5.8.0-23-algemeen, 5.8.0-25-generic, 5.4.0-1031-Azure tot en met 9,38 Hot Fix patch * *
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic naar 5.4.0-45 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1023-Azure </br> 5.4.0-47-generic, 5.4.0-48-generic, 5.4.0-1025-Azure, 5.4.0-1026-Azure tot en met 9,37 Hot Fix patch * *
20,04 LTS |[9,36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generic naar 5.4.0-42 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1022-Azure

* * Opmerking: ter ondersteuning van de meest recente Linux-kernels binnen vijf tien dagen na de release, Azure Site Recovery roll-to-pluggable patch van de nieuwste versie van de Mobility-agent. Deze oplossing wordt uitgegerold tussen twee versies van de primaire versie. Volg de stappen die in [dit artikel](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)worden beschreven om bij te werken naar de nieuwste versie van Mobility agent (inclusief Hot Fix patch). Deze patch is momenteel geïmplementeerd voor Mobility-agents die worden gebruikt in azure naar Azure DR scenario.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde Debian-kernel-versies voor virtuele Azure-machines

**Release** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
Debian 7 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.2.0-4-amd64 tot 3.2.0-6-amd64, 3.16.0 -0. bpo. 4-amd64 |
|||
Debian 8 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.16.0-4-amd64 tot 3.16.0-11-amd64, 4.9.0 -0. bpo. 4-amd64 tot 4.9.0 -0. bpo. 11-amd64 |
|||
Debian 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 tot 4.9.0-14-amd64 </br> 4.19.0 -0. bpo. 1-amd64 to 4.19.0 -0. bpo. 13-amd64 </br> 4.19.0 -0. bpo. 1-Cloud-amd64 to 4.19.0 -0. bpo. 13-Cloud-amd64 </br> 
Debian 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 tot 4.9.0-14-amd64 </br> 4.19.0 -0. bpo. 1-amd64 to 4.19.0 -0. bpo. 12-amd64 </br> 4.19.0 -0. bpo. 1-Cloud-amd64 to 4.19.0 -0. bpo. 12-Cloud-amd64 </br> 4.19.0 -0. bpo. 13-amd64, 4.19.0 -0. bpo. 13-Cloud-amd64 tot en met 9,39 Hot Fix patch * *</br> 
Debian 9,1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 tot 4.9.0-13-amd64 </br> 4.19.0 -0. bpo. 1-amd64 to 4.19.0 -0. bpo. 11-amd64 </br> 4.19.0 -0. bpo. 1-Cloud-amd64 to 4.19.0 -0. bpo. 11-Cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0 -0. bpo. 12-amd64, 4.19.0 -0. bpo. 12-Cloud-amd64 tot en met 9,38 Hot Fix patch * *
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 tot 4.9.0-13-amd64, 4.19.0 -0. bpo. 6-amd64 tot 4.19.0 -0. bpo. 10-amd64, 4.19.0 -0. bpo. 6-Cloud-amd64 tot 4.19.0 -0. bpo. 10-Cloud-amd64
|||
Debian 10 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 tot 4.19.0-13-amd64 </br> 4.19.0-6-Cloud-amd64 tot 4.19.0-13-Cloud-amd64 </br> 5.8.0 -0. bpo. 2-amd64 </br> 5.8.0 -0. bpo. 2-Cloud-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde SUSE Linux Enterprise Server 12 kernel-versies voor virtuele Azure-machines

**Release** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Alle [Stock-SuSE 12 SP1, SP2, SP3, SP4, SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure naar 4.12.14-16.38-Azure </br> 4.12.14-16.41-Azure via 9,40 Hot Fix patch * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | Alle [Stock-SuSE 12 SP1, SP2, SP3, SP4, SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure naar 4.12.14-16.34-Azure </br> 4.12.14-16.38-Azure via 9,39 Hot Fix patch * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.38](https://support.microsoft.com/help/4590304/) | Alle [Stock-SuSE 12 SP1, SP2, SP3, SP4, SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure naar 4.12.14-16.28-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/) | Alle [Stock-SuSE 12 SP1, SP2, SP3, SP4, SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure naar 4.12.14-16.22-Azure </br> 4.12.14-16.25-Azure, 4.12.14-16.28-Azure tot en met 9,37 Hot Fix patch * *|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Ondersteund SUSE Linux Enterprise Server 15-kernel-versies voor virtuele Azure-machines

**Release** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Standaard worden alle [Stock-SuSE 15, SP1-en SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure naar 4.12.14-8.55-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure naar 5.3.18-18.29-Azure </br> 5.3.18-18.32-Azure, 4.12.14-8.58-Azure tot en met 9,40 Hot Fix patch * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Standaard worden alle [Stock-SuSE 15, SP1-en SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure naar 4.12.14-8.47-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure naar 5.3.18-18.21-Azure </br> 4.12.14-8.52-Azure, 5.3.18-18.24-Azure, 4.12.14-8.55-Azure, 5.3.18-18.29-Azure tot en met 9,39 Hot Fix patch * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | Standaard worden alle [Stock-SuSE 15, SP1-en SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure naar 4.12.14-8.44-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure naar 5.3.18-18.18-Azure </br> 4.12.14-8.47-Azure, 5.3.18-18.21-Azure tot en met 9,38 Hot Fix patch * *
SUSE Linux Enterprise Server 15 en 15 SP1 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/)  | Standaard worden alle [Stock-SuSE 15, SP1-en SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure naar 4.12.14-8.38-Azure </br> 4.12.14-8.41-Azure, 4.12.14-8.44-Azure tot en met 9,37 Hot Fix patch * *


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Gerepliceerde machines-Linux-bestands systeem/gast opslag

* Bestands systemen: ext3, ext4, XFS, BTRFS
* Volume manager: LVM2

> [!NOTE]
> Multipath-software wordt niet ondersteund.


## <a name="replicated-machines---compute-settings"></a>Gerepliceerde machines-Compute Settings

**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
Grootte | Een Azure VM-grootte met ten minste twee CPU-kernen en 1 GB RAM | Controleer de [grootte van virtuele Azure-machines](../virtual-machines/sizes.md).
Beschikbaarheidssets | Ondersteund | Als u replicatie inschakelt voor een virtuele machine van Azure met de standaard opties, wordt er automatisch een beschikbaarheidsset gemaakt, op basis van de instellingen van de bron regio. U kunt deze instellingen wijzigen.
Beschikbaarheidszones | Ondersteund |
Voor deel voor hybride gebruik (HUB) | Ondersteund | Als op de bron-VM een HUB-licentie is ingeschakeld, gebruikt een testfailover of een failover van de VM ook de HUB-licentie.
Virtuele-machineschaalsets | Niet ondersteund |
Installatie kopieën van Azure galerie-micro soft gepubliceerd | Ondersteund | Wordt ondersteund als de virtuele machine wordt uitgevoerd op een ondersteund besturings systeem.
Azure Gallery-installatie kopieën-derde partij gepubliceerd | Ondersteund | Wordt ondersteund als de virtuele machine wordt uitgevoerd op een ondersteund besturings systeem.
Aangepaste installatie kopieën-externe partij gepubliceerd | Ondersteund | Wordt ondersteund als de virtuele machine wordt uitgevoerd op een ondersteund besturings systeem.
Vm's die zijn gemigreerd met behulp van Site Recovery | Ondersteund | Als een VMware-VM of fysieke machine met Site Recovery is gemigreerd naar Azure, moet u de oudere versie van Mobility service op de computer verwijderen en de computer opnieuw opstarten voordat u deze naar een andere Azure-regio repliceert.
Azure RBAC-beleid | Niet ondersteund | Beleid voor toegangs beheer op basis van rollen (Azure RBAC) op Vm's wordt niet gerepliceerd naar de failover-VM in de doel regio.
Uitbreidingen | Niet ondersteund | Uitbrei dingen worden niet gerepliceerd naar de failover-VM in de doel regio. Deze moet hand matig worden geïnstalleerd na een failover.
Proximity-plaatsings groepen | Ondersteund | Virtuele machines die zich in een proximity-plaatsings groep bevinden, kunnen worden beveiligd met Site Recovery.
Tags  | Ondersteund | Door de gebruiker gegenereerde tags die worden toegepast op virtuele bron machines, worden overgebracht naar doel-virtual machines na testfailover of failover. Tags op de virtuele machine (s) worden één keer per 24 uur gerepliceerd, zolang de VM ('s) is/aanwezig is in de doel regio.


## <a name="replicated-machines---disk-actions"></a>Gerepliceerde machines-schijf acties

**Actie** | **Details**
-- | ---
Grootte van schijf op een gerepliceerde VM wijzigen | Ondersteund op de bron-VM vóór de failover. U hoeft replicatie niet uit te scha kelen of opnieuw in te scha kelen.<br/><br/> Als u de bron-VM na een failover wijzigt, worden de wijzigingen niet vastgelegd.<br/><br/> Als u na de failover de schijf grootte op de Azure-VM wijzigt, worden de wijzigingen niet door Site Recovery vastgelegd en wordt de oorspronkelijke VM-grootte door failback gewijzigd.
Een schijf toevoegen aan een gerepliceerde VM | Ondersteund
Offline wijzigingen in beveiligde schijven | Als u schijven wilt ontkoppelen en offline wijzigingen wilt aanbrengen, moet u een volledige hersynchronisatie activeren.

## <a name="replicated-machines---storage"></a>Gerepliceerde machines-opslag

Deze tabel bevat een overzicht van de ondersteuning voor de Azure VM-besturingssysteem schijf, de gegevens schijf en de tijdelijke schijf.

- Het is belang rijk om te kijken naar de schijfruimte limieten en doelen van de virtuele machine voor [beheerde schijven](../virtual-machines/disks-scalability-targets.md) om prestatie problemen te voor komen.
- Als u implementeert met de standaard instellingen, maakt Site Recovery automatisch schijven en opslag accounts op basis van de bron instellingen.
- Als u aanpassingen aanpast, moet u de richt lijnen volgen.

**Onderdeel** | **Ondersteuning** | **Details**
--- | --- | ---
Maximale grootte van de besturingssysteem schijf | 2048 GB | Meer [informatie](../virtual-machines/managed-disks-overview.md) over VM-schijven.
Tijdelijke schijf | Niet ondersteund | De tijdelijke schijf wordt altijd uitgesloten van replicatie.<br/><br/> Sla geen permanente gegevens op de tijdelijke schijf op. [Meer informatie](../virtual-machines/managed-disks-overview.md).
Maximale grootte van gegevens schijf | 32 TB voor beheerde schijven<br></br>4 TB voor niet-beheerde schijven|
Minimale grootte van gegevens schijf | Geen beperking voor niet-beheerde schijven. 2 GB voor beheerde schijven |
Maximum aantal gegevens schijven | Maxi maal 64, in overeenstemming met de ondersteuning voor een specifieke Azure VM-grootte | Meer [informatie](../virtual-machines/sizes.md) over VM-grootten.
Wijzigings frequentie van gegevens schijven | Maxi maal 20 MBps per schijf voor Premium-opslag. Maxi maal 2 MBps per schijf voor standaard opslag. | Als de gemiddelde waarde voor het wijzigen van de gegevens op de schijf continu hoger is dan het maximum, wordt de replicatie niet opvangen.<br/><br/>  Als het maximum echter sporadisch wordt overschreden, kan het zijn dat de replicatie kan worden vertraagd.
Gegevens schijf-Standard-opslag account | Ondersteund |
Gegevens schijf-Premium-opslag account | Ondersteund | Als een virtuele machine schijven heeft verspreid over Premium-en Standard-opslag accounts, kunt u een ander doel opslag account voor elke schijf selecteren om ervoor te zorgen dat u dezelfde opslag configuratie in de doel regio hebt.
Beheerde schijf-standaard | Ondersteund in azure-regio's waarin Azure Site Recovery wordt ondersteund. |
Beheerde schijf-Premium | Ondersteund in azure-regio's waarin Azure Site Recovery wordt ondersteund. |
Standard SSD | Ondersteund |
Redundantie | LRS en GRS worden ondersteund.<br/><br/> ZRS wordt niet ondersteund.
Coole en warme opslag | Niet ondersteund | VM-schijven worden niet ondersteund in coole en warme opslag
Opslagruimten | Ondersteund |
NVMe-opslag interface | Niet ondersteund
Versleuteling op rest (SSE) | Ondersteund | SSE is de standaard instelling voor opslag accounts.
Versleuteling in rust (CMK) | Ondersteund | Zowel software-als HSM-sleutels worden ondersteund voor beheerde schijven
Dubbele versleuteling bij rest | Ondersteund | Meer informatie over ondersteunde regio's voor [Windows](../virtual-machines/disk-encryption.md) en [Linux](../virtual-machines/disk-encryption.md)
Azure Disk Encryption (ADE) voor Windows-besturings systeem | Ondersteund voor virtuele machines met beheerde schijven. | Vm's met niet-beheerde schijven worden niet ondersteund. <br/><br/> Met HSM beveiligde sleutels worden niet ondersteund. <br/><br/> Het versleutelen van afzonderlijke volumes op één schijf wordt niet ondersteund. |
Azure Disk Encryption (ADE) voor Linux-besturings systeem | Ondersteund voor virtuele machines met beheerde schijven. | Vm's met niet-beheerde schijven worden niet ondersteund. <br/><br/> Met HSM beveiligde sleutels worden niet ondersteund. <br/><br/> Het versleutelen van afzonderlijke volumes op één schijf wordt niet ondersteund. <br><br> Bekend probleem bij het inschakelen van replicatie. [Meer informatie.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
Rotatie van SAS-sleutel | Niet ondersteund | Als de SAS-sleutel voor opslag accounts is geroteerd, moet de klant de replicatie uitschakelen en opnieuw inschakelen. |
Hot add    | Ondersteund | Het inschakelen van replicatie voor een gegevens schijf die u toevoegt aan een gerepliceerde Azure-VM wordt ondersteund voor virtuele machines die gebruikmaken van beheerde schijven. <br/><br/> Er kan slechts één schijf dynamisch worden toegevoegd aan een virtuele machine van Azure. Het gelijktijdig toevoegen van meerdere schijven wordt niet ondersteund. |
Hot Remove-schijf    | Niet ondersteund | Als u de gegevens schijf op de virtuele machine verwijdert, moet u de replicatie uitschakelen en de replicatie opnieuw inschakelen voor de virtuele machine.
Schijf uitsluiten | Voor. U moet [Power shell](azure-to-azure-exclude-disks.md) gebruiken om te configureren. |    Tijdelijke schijven worden standaard uitgesloten.
Opslagruimten direct  | Ondersteund voor crash consistente herstel punten. Toepassings consistente herstel punten worden niet ondersteund. |
Scale-out Bestands server  | Ondersteund voor crash consistente herstel punten. Toepassings consistente herstel punten worden niet ondersteund. |
DRBD | Schijven die deel uitmaken van een DRBD-installatie, worden niet ondersteund. |
LRS | Ondersteund |
GRS | Ondersteund |
RA-GRS | Ondersteund |
ZRS | Niet ondersteund |
Coole en warme opslag | Niet ondersteund | Schijven van virtuele machines worden niet ondersteund in coole en warme opslag
Firewalls voor virtuele netwerken Azure Storage  | Ondersteund | Schakel [vertrouwde micro soft-Services toestaan](../storage/common/storage-network-security.md#exceptions)in als u de toegang tot het virtuele netwerk beperken tot opslag accounts.
V2-opslag accounts voor algemeen gebruik (zowel warme als koud-tier) | Ondersteund | De transactie kosten stijgen aanzienlijk vergeleken met de V1-opslag accounts voor algemeen gebruik
Generatie 2 (UEFI-opstart) | Ondersteund
NVMe-schijven | Niet ondersteund
Gedeelde Azure-schijven | Niet ondersteund
Optie voor beveiligde overdracht | Ondersteund
Schijven met ingeschakelde Accelerators schrijven | Niet ondersteund
Tags  | Ondersteund | Door de gebruiker gegenereerde tags worden elke 24 uur gerepliceerd.

>[!IMPORTANT]
> Om prestatie problemen te voor komen, moet u de schaal baarheid en prestaties van de VM-schijf voor [beheerde schijven](../virtual-machines/disks-scalability-targets.md)volgen. Als u de standaard instellingen gebruikt, Site Recovery de vereiste schijven en opslag accounts maken op basis van de configuratie van de bron. Als u uw eigen instellingen aanpast en selecteert, volgt u de schaal baarheid en prestaties van de schijf voor uw bron-Vm's.

## <a name="limits-and-data-change-rates"></a>Limieten en snelheid van gegevens wijzigingen

De volgende tabel geeft een overzicht van Site Recovery limieten.

- Deze limieten zijn gebaseerd op onze tests, maar uiteraard dekken niet alle mogelijke toepassings-I/O-combi Naties.
- De werkelijke resultaten kunnen variëren op basis van de I/O-mix van de app.
- Er zijn twee limieten die u moet overwegen: gegevens verloop per schijf en per gegevens verloop van de virtuele machine.
- De huidige limiet voor gegevens verloop per virtuele machine is 54 MB/s, ongeacht de grootte.

**Opslag doel** | **Gemiddelde bron schijf-I/O** |**Gemiddeld gegevens verloop van bron schijf** | **Totale gegevens verloop van bron schijf per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf

## <a name="replicated-machines---networking"></a>Gerepliceerde computers-netwerken
**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
NIC | Maximum aantal dat wordt ondersteund voor een specifieke Azure VM-grootte | Nic's worden gemaakt wanneer de virtuele machine wordt gemaakt tijdens de failover.<br/><br/> Het aantal Nic's op de failover-VM is afhankelijk van het aantal Nic's op de bron-VM wanneer replicatie is ingeschakeld. Als u een NIC toevoegt of verwijdert nadat de replicatie is ingeschakeld, heeft dit geen invloed op het aantal Nic's op de gerepliceerde VM na een failover. <br/><br/> De volg orde van de Nic's na een failover is niet gegarandeerd hetzelfde als de oorspronkelijke volg orde. <br/><br/> U kunt de naam van de netwerk interface kaarten in de doel regio wijzigen op basis van de naamgevings regels van uw organisatie. Het wijzigen van de naam van de NIC wordt ondersteund met Power shell.
Internet Load Balancer | Niet ondersteund | U kunt open bare/Internet load balancers instellen in de primaire regio. Open bare/Internet load balancers worden echter niet ondersteund door Azure Site Recovery in de DR-regio.
Interne Load Balancer | Ondersteund | De vooraf geconfigureerde load balancer koppelen met behulp van een Azure Automation script in een herstel plan.
Openbaar IP-adres | Ondersteund | Een bestaand openbaar IP-adres koppelen aan de NIC. U kunt ook een openbaar IP-adres maken en dit koppelen aan de NIC met behulp van een Azure Automation script in een herstel plan.
NSG op NIC | Ondersteund | Koppel de NSG aan de NIC met behulp van een Azure Automation script in een herstel plan.
NSG op subnet | Ondersteund | Koppel de NSG aan het subnet met behulp van een Azure Automation script in een herstel plan.
Gereserveerd (statisch) IP-adres | Ondersteund | Als de NIC op de bron-VM een statisch IP-adres heeft en het doel-subnet hetzelfde IP-adres beschikbaar heeft, wordt het toegewezen aan de virtuele machine waarvoor een failover is uitgevoerd.<br/><br/> Als het doel-subnet niet hetzelfde IP-adres beschikbaar heeft, is een van de beschik bare IP-adressen in het subnet gereserveerd voor de virtuele machine.<br/><br/> U kunt ook een vast IP-adres en een subnet opgeven in de instellingen voor de **gerepliceerde items**  >    >  **Compute-en netwerk**  >  **netwerk interfaces**.
Dynamisch IP-adres | Ondersteund | Als de NIC op de bron dynamische IP-adres Sering heeft, is de NIC op de virtuele machine waarvoor een failover is uitgevoerd, standaard ook dynamisch.<br/><br/> U kunt dit zo nodig wijzigen in een vast IP-adres.
Meerdere IP-adressen | Niet ondersteund | Wanneer u een failover van een virtuele machine met meerdere IP-adressen hebt uitgevoerd, wordt alleen het primaire IP-adres van de NIC in de bron regio bewaard. Als u meerdere IP-adressen wilt toewijzen, kunt u virtuele machines toevoegen aan een [herstel plan](recovery-plan-overview.md) en een script koppelen om extra IP-adressen toe te wijzen aan het plan, of u kunt de wijziging hand matig of met een script uitvoeren na een failover.
Traffic Manager     | Ondersteund | U kunt Traffic Manager vooraf configureren zodat verkeer regel matig wordt gerouteerd naar het eind punt in de bron regio, en naar het eind punt in de doel regio in het geval van een failover.
Azure DNS | Ondersteund |
Aangepaste DNS    | Ondersteund |
Niet-geverifieerde proxy | Ondersteund | [Meer informatie](./azure-to-azure-about-networking.md)
Geverifieerde proxy | Niet ondersteund | Als de virtuele machine gebruikmaakt van een geverifieerde proxy voor uitgaande verbindingen, kan deze niet worden gerepliceerd met behulp van Azure Site Recovery.
VPN site-naar-site-verbinding met on-premises<br/><br/>(met of zonder ExpressRoute)| Ondersteund | Zorg ervoor dat de Udr's en Nsg's zodanig zijn geconfigureerd dat de Site Recovery verkeer niet naar on-premises wordt gerouteerd. [Meer informatie](./azure-to-azure-about-networking.md)
VNET-naar-VNET-verbinding    | Ondersteund | [Meer informatie](./azure-to-azure-about-networking.md)
Service-eindpunten voor virtueel netwerk | Ondersteund | Als u de toegang tot het virtuele netwerk beperkt tot opslag accounts, moet u ervoor zorgen dat de vertrouwde micro soft-Services toegang krijgen tot het opslag account.
Versneld netwerken | Ondersteund | Versnelde netwerken moeten zijn ingeschakeld op de bron-VM. [Meer informatie](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo Alto-netwerk apparaat | Niet ondersteund | Bij apparaten van derden worden er vaak beperkingen opgelegd door de provider in de virtuele machine. Azure Site Recovery behoefte aan agents, uitbrei dingen en uitgaande connectiviteit beschikbaar. Het apparaat staat echter niet toe dat er uitgaande activiteiten worden geconfigureerd in de virtuele machine.
IPv6  | Niet ondersteund | Gemengde configuraties met zowel IPv4 als IPv6 worden ook niet ondersteund. Maak het subnet van het IPv6-bereik vóór een Site Recovery bewerking.
Persoonlijke koppelings toegang tot Site Recovery service | Ondersteund | [Meer informatie](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Tags  | Ondersteund | Door de gebruiker gegenereerde tags op Nic's worden elke 24 uur gerepliceerd.



## <a name="next-steps"></a>Volgende stappen

- Lees de [netwerk richtlijnen](./azure-to-azure-about-networking.md)  voor het repliceren van virtuele Azure-machines.
- Implementeer herstel na nood gevallen door [Azure vm's te repliceren](./azure-to-azure-quickstart.md).
