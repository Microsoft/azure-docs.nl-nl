---
title: Ondersteuningsmatrix voor herstel na noodherstel van Azure-VM'Azure Site Recovery
description: Een overzicht van de ondersteuning voor herstel na noodherstel van Azure-VM's naar een secundaire regio met Azure Site Recovery.
ms.topic: article
ms.date: 11/29/2020
ms.openlocfilehash: 02268471d58cbd473493b6001aa9f1df271077bb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376151"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Ondersteuningsmatrix voor herstel na noodgeval van Azure-VM's tussen Azure-regio's

Dit artikel bevat een overzicht van de ondersteuning en vereisten voor herstel na noodherstel van Azure-VM's van de ene Azure-regio naar de andere, met behulp [van de Azure Site Recovery service.](site-recovery-overview.md)


## <a name="deployment-method-support"></a>Ondersteuning voor implementatiemethode

**Implementatie** |  **Ondersteuning**
--- | ---
**Azure-portal** | Ondersteund.
**PowerShell** | Ondersteund. [Meer informatie](azure-to-azure-powershell.md)
**REST API** | Ondersteund.
**CLI** | Momenteel niet ondersteund


## <a name="resource-support"></a>Resource-ondersteuning

**Resourceactie** | **Details**
--- | ---
**Kluizen verplaatsen tussen resourcegroepen** | Niet ondersteund
**Reken-/opslag-/netwerkresources verplaatsen tussen resourcegroepen** | Wordt niet ondersteund.<br/><br/> Als u een VM of gekoppelde onderdelen, zoals opslag/netwerk, verplaatst nadat de VM is repliceerd, moet u replicatie voor de VM uitschakelen en vervolgens opnieuw inschakelen.
**Azure-VM's repliceren van het ene naar het andere abonnement voor herstel na noodherstel** | Ondersteund binnen dezelfde Azure Active Directory tenant.
**VM's migreren tussen regio's binnen ondersteunde geografische clusters (binnen en tussen abonnementen)** | Ondersteund binnen dezelfde Azure Active Directory tenant.
**VM's binnen dezelfde regio migreren** | Wordt niet ondersteund.
**Toegewezen Azure-hosts** | Wordt niet ondersteund.

## <a name="region-support"></a>Ondersteuning voor regio

U kunt VM's repliceren en herstellen tussen twee regio's binnen hetzelfde geografische cluster. Geografische clusters worden gedefinieerd met het oog op gegevenslatentie en -soevereiniteit.


**Geografisch cluster** | **Azure-regio's**
-- | --
Amerika | Canada - oost, Canada - centraal, VS - zuid-centraal, VS - west-centraal, VS - oost, VS - oost 2, VS - west, VS - west 2, VS - centraal, VS - noord-centraal
Europa | VK - west, VK - zuid, Europa - noord, Europa - west, Zuid-Afrika - west, Zuid-Afrika - noord, Noorwegen - oost, Frankrijk - centraal, Zwitserland - noord, Duitsland - west-centraal
Azië | India - zuid, India - centraal, India - west, Azië - zuidoost, Azië - oost, Japan - oost, Japan - west, Korea - centraal, Korea - zuid
Australië    | Australië - oost, Australië - zuidoost, Australië - centraal, Australië - centraal 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Duitsland    | Duitsland - centraal, Duitsland - noordoost
China | China - oost, China - noord, China - noord 2, China - oost 2
Beperkte regio's gereserveerd voor herstel na noodherstel in een land |Zwitserland - west gereserveerd voor Zwitserland - noord, Frankrijk - zuid gereserveerd voor Frankrijk - centraal, VAE - centraal beperkt voor VAE - noord-klanten en Noorwegen - west Noorwegen - oost klanten

>[!NOTE]
>
> - Voor **Brazilië - zuid** kunt u repliceren en failovers naar deze regio's: VS - zuid-centraal, VS - west-centraal, VS - oost, VS - oost 2, VS - west, VS - west 2 en VS - noord-centraal.
> - Brazilië - zuid kunnen alleen worden gebruikt als een bronregio van waaruit VM's kunnen repliceren met behulp van Site Recovery. Het kan niet fungeren als een doelregio. Dit komt door latentieproblemen vanwege geografische afstanden. Als u een failback van Brazilië - zuid als bronregio naar een doel, wordt failback naar Brazilië - zuid van de doelregio ondersteund.
> - U kunt werken binnen regio's waarvoor u de juiste toegang hebt.
> - Als de regio waarin u een kluis wilt maken niet wordt weer geven, moet u ervoor zorgen dat uw abonnement toegang heeft tot het maken van resources in die regio.
> - Als u een regio in een geografisch cluster niet kunt zien wanneer u replicatie inschakelen, moet u ervoor zorgen dat uw abonnement machtigingen heeft voor het maken van VM's in die regio.



## <a name="cache-storage"></a>Cacheopslag

Deze tabel bevat een overzicht van de ondersteuning voor het cacheopslagaccount dat tijdens Site Recovery replicatie wordt gebruikt.

**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
V2-opslagaccounts voor algemeen gebruik (hot- en cool-laag) | Ondersteund | Het gebruik van GPv2 wordt niet aanbevolen omdat de transactiekosten voor V2 aanzienlijk hoger zijn dan V1-opslagaccounts.
Premium Storage | Niet ondersteund | Standard-opslagaccounts worden gebruikt voor cacheopslag om de kosten te optimaliseren.
Azure Storage voor virtuele netwerken maken  | Ondersteund | Als u een cacheopslagaccount of doelopslagaccount met een firewall gebruikt, controleert u of u vertrouwde toegang [Microsoft-services.](../storage/common/storage-network-security.md#exceptions)<br></br>Zorg er ook voor dat u toegang toestaat tot ten minste één subnet van het bron-Vnet.

De onderstaande tabel bevat de limieten voor het aantal schijven dat naar één opslagaccount kan worden gerepliceerd.

**Type opslagaccount**    |    **Verloop = 4 MBps per schijf**    |    **Verloop = 8 MBps per schijf**
---    |    ---    |    ---
V1-opslagaccount    |    300 schijven    |    150 schijven
V2-opslagaccount    |    750 schijven    |    375 schijven

Naarmate het gemiddelde verloop van de schijven toeneemt, neemt het aantal schijven dat een opslagaccount kan ondersteunen af. De bovenstaande tabel kan worden gebruikt als richtlijn voor het nemen van beslissingen over het aantal opslagaccounts dat moet worden ingericht.

Houd er rekening mee dat de bovenstaande limieten specifiek zijn voor Scenario's voor Azure- en zone-naar-zone-DR. 

## <a name="replicated-machine-operating-systems"></a>Besturingssystemen van gerepliceerde machines

Site Recovery ondersteunt replicatie van Azure-VM's met de besturingssystemen die in deze sectie worden vermeld. Houd er rekening mee dat als een reeds replicerende machine vervolgens wordt bijgewerkt (of gedowngraded) naar een andere belangrijke kernel, u replicatie moet uitschakelen en replicatie na de upgrade opnieuw moet inschakelen.

### <a name="windows"></a>Windows


**Besturingssysteem** | **Details**
--- | ---
Windows Server 2019 | Ondersteund voor Server Core, Server met Bureaubladervaring.
Windows Server 2016  | Ondersteunde Server Core, Server met Bureaubladervaring.
Windows Server 2012 R2 | Ondersteund.
Windows Server 2012 | Ondersteund.
Windows Server 2008 R2 met SP1/SP2 | Ondersteund.<br/><br/> Vanaf versie [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility-service-extensie voor Azure-VM's moet u een Windows-update voor de onderhoudsstack [(SSU)](https://support.microsoft.com/help/4490628) en [SHA-2-update installeren](https://support.microsoft.com/help/4474419) op computers met Windows Server 2008 R2 SP1/SP2.  SHA-1 wordt niet ondersteund vanaf september 2019 en als SHA-2-ondertekening van code niet is ingeschakeld, wordt de agentextensie niet geïnstalleerd/geupgraded zoals verwacht. Meer informatie over [de SHA-2-upgrade en -vereisten.](https://aka.ms/SHA-2KB)
Windows 10 (x64) | Ondersteund.
Windows 8.1 (x64) | Ondersteund.
Windows 8 (x64) | Ondersteund.
Windows 7 (x64) met SP1 en meer | Vanaf versie [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility-service-extensie voor Azure-VM's moet u een Windows [Servicing Stack Update (SSU)](https://support.microsoft.com/help/4490628) en [SHA-2-update](https://support.microsoft.com/help/4474419) installeren op computers met Windows 7 met SP1.  SHA-1 wordt niet ondersteund vanaf september 2019 en als SHA-2-ondertekening van code niet is ingeschakeld, wordt de agentextensie niet geïnstalleerd/geupgraded zoals verwacht. Meer informatie over [de SHA-2-upgrade en -vereisten.](https://aka.ms/SHA-2KB)



#### <a name="linux"></a>Linux

**Besturingssysteem** | **Details**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [7.8,](https://support.microsoft.com/help/4564347/) [7.9,](https://support.microsoft.com/help/4578241/) [8.0,](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)8.1, [8.2](https://support.microsoft.com/help/4570609/), [8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10 </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, [7.8,](https://support.microsoft.com/help/4564347/) [7.9 pre-GA-versie,](https://support.microsoft.com/help/4578241/)7.9 GA-versie wordt ondersteund vanaf de patch voor hotfixing van 9.37** </br> 8.0, 8.1, [8.2,](https://support.microsoft.com/en-us/help/4570609) [8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS-server | Bevat ondersteuning voor alle 14.04. *x* versies; [Ondersteunde kernelversies](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Ubuntu 16.04 LTS-server | Bevat ondersteuning voor alle 16.04. *x* versies; [Ondersteunde kernelversie](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servers die gebruikmaken van verificatie en aanmelding op basis van wachtwoorden, en het cloud-init-pakket voor het configureren van cloud-VM's, hebben mogelijk aanmelding op basis van een wachtwoord uitgeschakeld bij failover (afhankelijk van de cloudconfiguratie). Aanmelden op basis van een wachtwoord kan opnieuw worden ingeschakeld op de virtuele machine door het wachtwoord opnieuw in te stellen in het menu >-instellingen voor ondersteuning > (van de virtuele machine met een mislukte Azure Portal.
Ubuntu 18.04 LTS-server | Biedt ondersteuning voor alle 18.04. *x* versies; [Ondersteunde kernelversie](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servers die gebruikmaken van verificatie en aanmelding op basis van wachtwoorden, en het cloud-init-pakket voor het configureren van cloud-VM's, hebben mogelijk aanmelding op basis van wachtwoorden uitgeschakeld bij failover (afhankelijk van de cloudinit-configuratie). Aanmelden op basis van wachtwoorden kan opnieuw worden ingeschakeld op de virtuele machine door het wachtwoord opnieuw in te stellen in het menu >-instellingen voor probleemoplossing > (van de virtuele machine met een mislukte Azure Portal.
Ubuntu 20.04 LTS-server | Biedt ondersteuning voor alle 20.04. *x* versies; [Ondersteunde kernelversie](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Bevat ondersteuning voor alle 7. *x* versies [Ondersteunde kernelversies](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Bevat ondersteuning voor alle 8. *x* versies [Ondersteunde kernelversies](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | Biedt ondersteuning voor 9.1 tot 9.13. Debian 9.0 wordt niet ondersteund. [Ondersteunde kernelversies](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Ondersteunde kernelversies](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(ondersteunde kernelversies)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(ondersteunde kernelversies)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Het upgraden van replicerende machines van SP3 naar SP4 wordt niet ondersteund. Als een gerepliceerde machine is bijgewerkt, moet u replicatie uitschakelen en replicatie na de upgrade opnieuw inschakelen.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7,](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) [7.8,](https://support.microsoft.com/help/4573888/) [7.9,](https://support.microsoft.com/help/4597409) [8.0,](https://support.microsoft.com/help/4573888/) [8.1](https://support.microsoft.com/help/4573888/) (met de red hat compatibele kernel of Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8.1 (uitgevoerd op alle UEK-kernels en RedHat kernel <= 3.10.0-1062.* worden ondersteund in [9.35](https://support.microsoft.com/help/4573888/). Ondersteuning voor de rest van de RedHat-kernels is beschikbaar in [9.36](https://support.microsoft.com/help/4578241/))

> [!NOTE]
> Voor Linux-versies biedt Azure Site Recovery geen ondersteuning voor aangepaste besturingssysteemafbeeldingen. Alleen de voorraadkernel die deel uitmaken van de distributie secundaire versie release/update worden ondersteund.

**Opmerking: ter ondersteuning van de meest recente Linux-kernels binnen 15 dagen na de release, Azure Site Recovery de hotfix-patch uitgebracht boven op de nieuwste versie van de Mobility-agent. Deze oplossing wordt tussen twee belangrijke versiereleases uitgerold. Volg de stappen in dit artikel om bij te werken naar de nieuwste versie van de Mobility-agent (inclusief [hotfix-patch).](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Deze patch wordt momenteel uitgerold voor mobility-agents die worden gebruikt in het scenario van Azure naar Azure DR.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde Ubuntu-kernelversies voor virtuele Azure-machines

**Release** | **Mobility-service versie** | **Kernelversie** |
--- | --- | --- |
14,04 LTS | [9,37](https://support.microsoft.com/help/4582666/), [9,38,](https://support.microsoft.com/help/4590304/) [9,39,](https://support.microsoft.com/help/4597409/) [9,40,](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24-generic tot 3.13.0-170-generic,<br/>3.16.0-25-generic tot 3.16.0-77-generic,<br/>3.19.0-18-generic tot 3.19.0-80-generic,<br/>4.2.0-18-generic tot 4.2.0-42-generic,<br/>4.4.0-21-generic tot 4.4.0-148-generic,<br/>4.15.0-1023-azure naar 4.15.0-1045-azure |
|||
16.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-generic tot 4.4.0-201-generic,<br/>4.8.0-34-generic tot 4.8.0-58-generic,<br/>4.10.0-14-generic tot 4.10.0-42-generic,<br/>4.11.0-13-generic tot 4.11.0-14-generic,<br/>4.13.0-16-generic tot 4.13.0-45-generic,<br/>4.15.0-13-generic tot 4.15.0-133-generic<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1106-azure <br/> 4.4.0-203-generic, 4.4.0-204-generic, 4.4.0-206-generic, 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 4.15.0-1108-azure, 4.15.0-1109-azure, 4.15.0-1110-azure, 4.15.0-1111-azure through 9.41 hot fix patch**|
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic tot 4.4.0-197-generic,<br/>4.8.0-34-generic tot 4.8.0-58-generic,<br/>4.10.0-14-generic tot 4.10.0-42-generic,<br/>4.11.0-13-generic tot 4.11.0-14-generic,<br/>4.13.0-16-generic tot 4.13.0-45-generic,<br/>4.15.0-13-generic tot 4.15.0-128-generic<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1102-azure </br> 4.15.0-132-generic, 4.4.0-200-generic, 4.15.0-1106-azure, 4.15.0-133-generic, 4.4.0-201-generic through 9.40 hot fix patch**|
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic tot 4.4.0-194-generic,<br/>4.8.0-34-generic tot 4.8.0-58-generic,<br/>4.10.0-14-algemeen tot 4.10.0-42-algemeen,<br/>4.11.0-13-generic tot 4.11.0-14-generic,<br/>4.13.0-16-generic tot 4.13.0-45-generic,<br/>4.15.0-13-generic tot 4.15.0-123-generic<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1098-azure </br> 4.4.0-197-generic, 4.15.0-126-generic, 4.15.0-128-generic, 4.15.0-1100-azure, 4.15.0-1102-azure through 9.39 hot fix patch**|
16.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic tot 4.4.0-190-generic,<br/>4.8.0-34-generic tot 4.8.0-58-generic,<br/>4.10.0-14-algemeen tot 4.10.0-42-algemeen,<br/>4.11.0-13-generic tot 4.11.0-14-generic,<br/>4.13.0-16-generic tot 4.13.0-45-generic,<br/>4.15.0-13-generic tot 4.15.0-118-generic<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1096-azure </br> 4.4.0-193-generic, 4.15.0-120-generic, 4.15.0-122-generic, 4.15.0-1098-azure through 9.38 hot fix patch**|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic tot 4.4.0-189-generic,<br/>4.8.0-34-generic tot 4.8.0-58-generic,<br/>4.10.0-14-algemeen tot 4.10.0-42-algemeen,<br/>4.11.0-13-generic tot 4.11.0-14-generic,<br/>4.13.0-16-generic tot 4.13.0-45-generic,<br/>4.15.0-13-generic tot 4.15.0-115-generic<br/>4.11.0-1009-azure naar 4.11.0-1016-azure,<br/>4.13.0-1005-azure naar 4.13.0-1018-azure <br/>4.15.0-1012-azure naar 4.15.0-1093-azure </br> 4.4.0-190-generic, 4.15.0-117-generic, 4.15.0-118-generic, 4.15.0-1095-azure, 4.15.0-1096-azure through 9.37 hot fix patch**|
|||
18.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-generic tot 4.15.0-135-generic </br> 4.18.0-13-generic tot 4.18.0-25-generic </br> 5.0.0-15-generic tot 5.0.0-65-generic </br> 5.3.0-19-generic tot 5.3.0-70-generic </br> 5.4.0-37-generic tot 5.4.0-59-generic</br> 5.4.0-60-generic tot 5.4.0-65-generic </br> 4.15.0-1009-azure naar 4.15.0-1106-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1036-azure </br> 5.3.0-1007-azure naar 5.3.0-1035-azure </br> 5.4.0-1020-azure naar 5.4.0-1039-azure </br> 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 5.3.0-72-generic, 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 4.15.0-1108-azure, 4.15.0-1111-azure, 5.4.0-1040-azure, 5.4.0-1041-azure, 5.4.0-1043-azure, 4.15.0-1109-azure, 4.15.0-1110-azure tot en met 9.41 hot fix patch**|
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-generic tot 4.15.0-129-generic </br> 4.18.0-13-generic tot 4.18.0-25-generic </br> 5.0.0-15-generic tot 5.0.0-63-generic </br> 5.3.0-19-generic tot 5.3.0-69-generic </br> 5.4.0-37-generic tot 5.4.0-59-generic</br> 4.15.0-1009-azure naar 4.15.0-1103-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1036-azure </br> 5.3.0-1007-azure naar 5.3.0-1035-azure </br> 5.4.0-1020-azure naar 5.4.0-1035-azure </br> 4.15.0-1104-azure, 4.15.0-130-generic, 4.15.0-132-generic, 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 4.15.0-1106-azure, 4.15.0-134-generic, 4.15.0-135-generic, 5.5 4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic through 9.40 hot fix patch**|
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-generic tot 4.15.0-123-generic </br> 4.18.0-13-generic tot 4.18.0-25-generic </br> 5.0.0-15-generic tot 5.0.0-63-generic </br> 5.3.0-19-generic tot 5.3.0-69-generic </br> 5.4.0-37-generic tot 5.4.0-53-generic</br> 4.15.0-1009-azure naar 4.15.0-1099-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1036-azure </br> 5.3.0-1007-azure naar 5.3.0-1035-azure </br> 5.4.0-1020-azure naar 5.4.0-1031-azure </br> 4.15.0-124-generic, 5.4.0-54-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 4.15.0-1100-azure, 4.15.0-126-generic, 4.15.0-128-generic, 5.4.0-58-generic, 4.15.0-1102-azure, 5.4.0-1034-azure through 9.39 hot fix patch**|
18.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic tot 4.15.0-118-generic </br> 4.18.0-13-generic tot 4.18.0-25-generic </br> 5.0.0-15-generic tot 5.0.0-61-generic </br> 5.3.0-19-generic tot 5.3.0-67-generic </br> 5.4.0-37-generic tot 5.4.0-48-generic</br> 4.15.0-1009-azure naar 4.15.0-1096-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1036-azure </br> 5.3.0-1007-azure naar 5.3.0-1035-azure </br> 5.4.0-1020-azure naar 5.4.0-1026-azure </br> 4.15.0-121-generic, 4.15.0-122-generic, 5.0.0-62-generic, 5.3.0-68-generic, 5.4.0-51-generic, 5.4.0-52-generic, 4.15.0-1099-azure, 5.4.0-1031-azure through 9.38 hot fix patch**|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic tot 4.15.0-115-generic </br> 4.18.0-13-generic tot 4.18.0-25-generic </br> 5.0.0-15-generic tot 5.0.0-60-generic </br> 5.3.0-19-generic tot 5.3.0-66-generic </br> 5.4.0-37-generic tot 5.4.0-45-generic</br> 4.15.0-1009-azure naar 4.15.0-1093-azure </br> 4.18.0-1006-azure naar 4.18.0-1025-azure </br> 5.0.0-1012-azure naar 5.0.0-1036-azure </br> 5.3.0-1007-azure naar 5.3.0-1035-azure </br> 5.4.0-1020-azure naar 5.4.0-1023-azure</br> 4.15.0-117-generic, 4.15.0-118-generic, 5.0.0-61-generic, 5.3.0-67-generic, 5.4.0-47-generic, 5.4.0-48-generic, 4.15.0-1095-azure, 4.15.0-1096-azure, 5.4.0-1025-azure, 5.4.0-1026-azure through 9.37 hot fix patch**|
|||
20.04 LTS |[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-generic tot 5.4.0-65 </br> -generic 5.4.0-1010-azure to 5.4.0-1039-azure </br> 5.8.0-29-generic tot 5.8.0-43-generic </br> 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 5.8.0-44-generic, 5.8.0-45-generic, 5.8.0-48-generic, 5.4.0-1040-azure, 5.4.0-1041-azure, 5.4.0-1043-azure through 9.41 hot fix patch**|
20.04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic tot 5.4.0-59 </br> -generic 5.4.0-1010-azure to 5.4.0-1035-azure </br> 5.8.0-29-generic tot 5.8.0-34-generic </br> 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 5.8.0-36-generic, 5.8.0-38-generic, 5.4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic, 5.8.0-40-generic, 5.8.0-41-generic through 9.40 hot fix patch**|
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic tot 5.4.0-53 </br> -generic 5.4.0-1010-azure to 5.4.0-1031-azure </br> 5.4.0-54-generic, 5.8.0-29-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 5.8.0-31-generic, 5.8.0-33-generic, 5.4.0-58-generic, 5.4.0-1034-azure through 9.39 hot fix patch**
20,04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic tot 5.4.0-53 </br> -generic 5.4.0-1010-azure to 5.4.0-1031-azure </br> 5.4.0-54-generic, 5.8.0-29-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 5.8.0-31-generic, 5.8.0-33-generic, 5.4.0-58-generic, 5.4.0-1034-azure through 9.39 hot fix patch**
20,04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic tot 5.4.0-48 </br> -generic 5.4.0-1010-azure to 5.4.0-1026-azure </br> 5.4.0-51-generic, 5.4.0-52-generic, 5.8.0-23-generic, 5.8.0-25-generic, 5.4.0-1031-azure through 9.38 hot fix patch**
20,04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic tot 5.4.0-45 </br> -generic 5.4.0-1010-azure to 5.4.0-1023-azure </br> 5.4.0-47-generic, 5.4.0-48-generic, 5.4.0-1025-azure, 5.4.0-1026-azure through 9.37 hot fix patch**

**Opmerking: ter ondersteuning van de meest recente Linux-kernels binnen 15 dagen na de release, Azure Site Recovery de hotfix-patch uitgebracht boven op de nieuwste versie van de Mobility-agent. Deze oplossing wordt tussen twee belangrijke versiereleases uitgerold. Volg de stappen in dit artikel om bij te werken naar de nieuwste versie van de Mobility-agent (inclusief [hotfix-patch).](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Deze patch is momenteel uitgerold voor mobility-agents die worden gebruikt in het scenario van Azure naar Azure DR.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde Debian-kernelversies voor virtuele Azure-machines

**Release** | **Mobility-service versie** | **Kernelversie** |
--- | --- | --- |
Debian 7 | [9,37](https://support.microsoft.com/help/4582666/), [9,38,](https://support.microsoft.com/help/4590304/) [9,39,](https://support.microsoft.com/help/4597409/) [9,40,](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.2.0-4-amd64 tot 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9,37](https://support.microsoft.com/help/4582666/), [9,38,](https://support.microsoft.com/help/4590304/) [9,39,](https://support.microsoft.com/help/4597409/) [9,40,](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-amd64 tot 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64 tot 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 tot 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 tot 4.19.0-0.bpo.14-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 tot 4.19.0-0.bpo.14-cloud-amd64 </br> 4.9.0-15-amd64, 4.19.0-0.bpo.16-amd64, 4.19.0-0.bpo.16-cloud-amd64 tot en met 9.41 hot fix patch**
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 tot 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 tot 4.19.0-0.bpo.13-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 tot 4.19.0-0.bpo.13-cloud-amd64 
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 tot 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 tot 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 tot 4.19.0-0.bpo.12-cloud-amd64 </br> 4.19.0-0.bpo.13-amd64, 4.19.0-0.bpo.13-cloud-amd64 tot en met 9.39 hot fix patch**</br> 
Debian 9.1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 tot 4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 tot 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 tot 4.19.0-0.bpo.11-cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0-0.bpo.12-amd64, 4.19.0-0.bpo.12-cloud-amd64 tot en met 9.38 hot fix patch**
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 tot 4.9.0-13-amd64, 4.19.0-0.bpo.6-amd64 tot 4.19.0-0.bpo.10-amd64, 4.19.0-0.bpo.6-cloud-amd64 tot 4.19.0-0.bpo.10-cloud-amd64
|||
Debian 10 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64 tot 4.19.0-14-amd64 </br> 4.19.0-6-cloud-amd64 tot 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64 </br> 4.19.0-10-cloud-amd64, 4.19.0-16-amd64, 4.19.0-16-cloud-amd64 tot en met 9.41 hot fix patch**
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 tot 4.19.0-13-amd64 </br> 4.19.0-6-cloud-amd64 tot 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

**Opmerking: ter ondersteuning van de meest recente Linux-kernels binnen 15 dagen na de release, Azure Site Recovery de hotfix-patch boven op de nieuwste versie van de Mobility-agent. Deze oplossing wordt tussen twee belangrijke versiereleases uitgerold. Volg de stappen in dit artikel om bij te werken naar de nieuwste versie van de Mobility-agent (inclusief [hotfix-patch).](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Deze patch is momenteel uitgerold voor mobility-agents die worden gebruikt in het scenario van Azure naar Azure DR.

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde SUSE Linux Enterprise Server 12 kernelversies voor virtuele Azure-machines

**Release** | **Mobility-service versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Alle [SUSE 12 SP1,SP2,SP3,SP4,SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-azure naar 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.43-azure </br> 4.12.14-16.7-azure naar 4.12.14-16.44-azure </br> 4.12.14-16.47-azure tot en met 9.41 hot fix patch**|
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Alle [SUSE 12 SP1-, SP2-, SP3-, SP4-, SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-azure naar 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.43-azure </br> 4.12.14-16.7-azure naar 4.12.14-16.38-azure </br> 4.12.14-16.41-azure tot en met 9.40 hot fix patch**|
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.39](https://support.microsoft.com/help/4597409/) | Alle [SUSE 12 SP1-, SP2-, SP3-, SP4-, SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-azure tot 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.43-azure </br> 4.12.14-16.7-azure naar 4.12.14-16.34-azure </br> 4.12.14-16.38-azure tot en met 9.39 hot fix patch**|
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.38](https://support.microsoft.com/help/4590304/) | Alle [SUSE 12 SP1-, SP2-, SP3-, SP4-, SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-azure tot 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.43-azure </br> 4.12.14-16.7-azure naar 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.37](https://support.microsoft.com/help/4582666/) | Alle [SUSE 12 SP1,SP2,SP3,SP4,SP5-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-azure naar 4.4.180-4.31-azure,</br>4.12.14-6.3-azure naar 4.12.14-6.43-azure </br> 4.12.14-16.7-azure naar 4.12.14-16.22-azure </br> 4.12.14-16.25-azure, 4.12.14-16.28-azure through 9.37 hot fix patch**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Ondersteunde SUSE Linux Enterprise Server 15 kernelversies voor virtuele Azure-machines

**Release** | **Mobility-service versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Standaard worden alle [SUSE 15-, SP1- en SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) op voorraad ondersteund.</br></br> 4.12.14-5.5-azure naar 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure naar 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure naar 5.3.18-18.35-azure </br> 5.3.18-18.38-azure through 9.41 hot fix patch**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Standaard worden alle [SUSE 15-, SP1- en SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) op voorraad ondersteund.</br></br> 4.12.14-5.5-azure naar 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure naar 4.12.14-8.58-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure naar 5.3.18-18.29-azure </br> 5.3.18-18.32-azure, 4.12.14-8.58-azure through 9.40 hot fix patch**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | Standaard worden alle [SUSE 15-, SP1-, SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) op voorraad ondersteund.</br></br> 4.12.14-5.5-azure naar 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure naar 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure naar 5.3.18-18.21-azure </br> 4.12.14-8.52-azure, 5.3.18-18.24-azure, 4.12.14-8.55-azure, 5.3.18-18.29-azure through 9.39 hot fix patch**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | Standaard worden alle [SUSE 15-, SP1-, SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) op voorraad ondersteund.</br></br> 4.12.14-5.5-azure naar 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure naar 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure naar 5.3.18-18.18-azure </br> 4.12.14-8.47-azure, 5.3.18-18.21-azure tot en met 9.38 hot fix patch**
SUSE Linux Enterprise Server 15 en 15 SP1 | [9.37](https://support.microsoft.com/help/4582666/)  | Standaard worden alle [SUSE 15-, SP1- en SP2-kernels](https://www.suse.com/support/kb/doc/?id=000019587) op voorraad ondersteund.</br></br> 4.12.14-5.5-azure naar 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure naar 4.12.14-8.38-azure </br> 4.12.14-8.41-azure, 4.12.14-8.44-azure through 9.37 hot fix patch**

**Opmerking: ter ondersteuning van de meest recente Linux-kernels binnen 15 dagen na de release, Azure Site Recovery de hotfix-patch boven op de nieuwste versie van de Mobility-agent. Deze oplossing wordt tussen twee belangrijke versiereleases uitgerold. Volg de stappen in dit artikel om bij te werken naar de nieuwste versie van de Mobility-agent (inclusief [hotfix-patch).](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Deze patch is momenteel uitgerold voor mobility-agents die worden gebruikt in het scenario van Azure naar Azure DR.

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Gerepliceerde machines - Linux-bestandssysteem/gastopslag

* Bestandssystemen: ext3, ext4, XFS, BTRFS
* Volumebeheer: LVM2

> [!NOTE]
> Multipath-software wordt niet ondersteund.


## <a name="replicated-machines---compute-settings"></a>Gerepliceerde machines - rekeninstellingen

**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
Grootte | Elke Azure-VM-grootte met ten minste 2 CPU-kernen en 1 GB RAM | Controleer [de grootte van virtuele Azure-machines.](../virtual-machines/sizes.md)
Beschikbaarheidssets | Ondersteund | Als u replicatie inschakelen voor een Azure-VM met de standaardopties, wordt er automatisch een beschikbaarheidsset gemaakt op basis van de instellingen voor de bronregio. U kunt deze instellingen wijzigen.
Beschikbaarheidszones | Ondersteund |
Hybrid Use Benefit (HUB) | Ondersteund | Als voor de bron-VM een HUB-licentie is ingeschakeld, gebruikt een test-failover of failover-VM ook de HUB-licentie.
Virtuele-machineschaalsets | Niet ondersteund |
Azure-galerie-afbeeldingen - Microsoft gepubliceerd | Ondersteund | Ondersteund als de VM wordt uitgevoerd op een ondersteund besturingssysteem.
Azure Gallery-afbeeldingen - Gepubliceerd door derden | Ondersteund | Ondersteund als de VM wordt uitgevoerd op een ondersteund besturingssysteem.
Aangepaste afbeeldingen - Gepubliceerd door derden | Ondersteund | Ondersteund als de VM wordt uitgevoerd op een ondersteund besturingssysteem.
VM's gemigreerd met Site Recovery | Ondersteund | Als een VMware-VM of fysieke machine naar Azure is gemigreerd met behulp van Site Recovery, moet u de oudere versie van Mobility-service die op de machine wordt uitgevoerd, verwijderen en de machine opnieuw opstarten voordat u deze repliceert naar een andere Azure-regio.
Azure RBAC-beleid | Niet ondersteund | Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) op VM's wordt niet gerepliceerd naar de failover-VM in de doelregio.
Uitbreidingen | Niet ondersteund | Extensies worden niet gerepliceerd naar de failover-VM in de doelregio. Deze moet handmatig worden geïnstalleerd na een failover.
Nabijheidsplaatsingsgroepen | Ondersteund | Virtuele machines die zich in een nabijheidsplaatsingsgroep bevinden, kunnen worden beveiligd met behulp Site Recovery.
Tags  | Ondersteund | Door de gebruiker gegenereerde tags die worden toegepast op virtuele bronmachines, worden na een test-failover of failover overgedragen naar de doel-virtuele machines. Tags op de VM('s) worden eenmaal per 24 uur gerepliceerd zolang de VM('s) aanwezig zijn/zijn in de doelregio.


## <a name="replicated-machines---disk-actions"></a>Gerepliceerde machines - schijfacties

**Actie** | **Details**
-- | ---
Hetize van schijf op gerepliceerde VM's | Ondersteund op de bron-VM vóór failover. U hoeft replicatie niet uit te schakelen of opnieuw in te schakelen.<br/><br/> Als u de bron-VM na een failover wijzigt, worden de wijzigingen niet vastgelegd.<br/><br/> Als u de schijfgrootte op de Azure-VM na een failover wijzigt, worden wijzigingen niet vastgelegd door Site Recovery en wordt de failback naar de oorspronkelijke VM-grootte.
Een schijf toevoegen aan een gerepliceerde VM | Ondersteund
Offlinewijzigingen in beveiligde schijven | Als u schijven loskoppelt en offline wijzigingen aan deze schijven aanbreed, moet u een volledige hersynsyn sync activeren.

## <a name="replicated-machines---storage"></a>Gerepliceerde machines - opslag

Deze tabel bevat een overzicht van de ondersteuning voor de azure VM-besturingssysteemschijf, gegevensschijf en tijdelijke schijf.

- Het is belangrijk om de VM-schijflimieten en -doelen voor beheerde schijven te observeren om [prestatieproblemen](../virtual-machines/disks-scalability-targets.md) te voorkomen.
- Als u implementeert met de standaardinstellingen, Site Recovery automatisch schijven en opslagaccounts op basis van de broninstellingen.
- Zorg ervoor dat u de richtlijnen volgt als u deze aan past.

**Onderdeel** | **Ondersteuning** | **Details**
--- | --- | ---
Maximale grootte van besturingssysteemschijf | 2048 GB | [Meer informatie](../virtual-machines/managed-disks-overview.md) over VM-schijven.
Tijdelijke schijf | Niet ondersteund | De tijdelijke schijf wordt altijd uitgesloten van replicatie.<br/><br/> Sla geen permanente gegevens op de tijdelijke schijf op. [Meer informatie](../virtual-machines/managed-disks-overview.md).
Maximale grootte van gegevensschijf | 32 TB voor beheerde schijven<br></br>4 TB voor niet-mande schijven|
Minimale grootte van gegevensschijf | Geen beperking voor niet-mande schijven. 2 GB voor beheerde schijven |
Maximumaantal gegevensschijven | Maximaal 64, in overeenstemming met ondersteuning voor een specifieke Azure-VM-grootte | [Meer informatie](../virtual-machines/sizes.md) over VM-grootten.
Wijzigingssnelheid van gegevensschijf | Maximaal 20 MBps per schijf voor Premium Storage. Maximaal 2 MBps per schijf voor Standard-opslag. | Als de gemiddelde wijzigingssnelheid van gegevens op de schijf continu hoger is dan het maximum, zal replicatie geen achterstand inhalen.<br/><br/>  Als het maximum echter sporadisch wordt overschreden, kan de replicatie een achterstand oplopen, maar ziet u mogelijk iets vertraagde herstelpunten.
Gegevensschijf - standaardopslagaccount | Ondersteund |
Gegevensschijf - Premium Storage-account | Ondersteund | Als een VM schijven heeft die zijn verdeeld over Premium- en Standard-opslagaccounts, kunt u voor elke schijf een ander doelopslagaccount selecteren om ervoor te zorgen dat u dezelfde opslagconfiguratie hebt in de doelregio.
Beheerde schijf - standaard | Ondersteund in Azure-regio's waarin Azure Site Recovery wordt ondersteund. |
Beheerde schijf - Premium | Ondersteund in Azure-regio's waarin Azure Site Recovery wordt ondersteund. |
Standard SSD | Ondersteund |
Redundantie | LRS en GRS worden ondersteund.<br/><br/> ZRS wordt niet ondersteund.
Cool storage en hot storage | Niet ondersteund | VM-schijven worden niet ondersteund op 'cool' en 'hot' opslag
Opslagruimten | Ondersteund |
NVMe-opslaginterface | Niet ondersteund
Versleuteling-at-rest (SSE) | Ondersteund | SSE is de standaardinstelling voor opslagaccounts.
Versleuteling-at-rest (CMK) | Ondersteund | Zowel software- als HSM-sleutels worden ondersteund voor beheerde schijven
Dubbele versleuteling in rust | Ondersteund | Meer informatie over ondersteunde regio's [voor Windows](../virtual-machines/disk-encryption.md) en [Linux](../virtual-machines/disk-encryption.md)
Azure Disk Encryption (ADE) voor Windows-besturingssysteem | Ondersteund voor VM's met beheerde schijven. | VM's die niet-managede schijven gebruiken, worden niet ondersteund. <br/><br/> Met HSM beveiligde sleutels worden niet ondersteund. <br/><br/> Versleuteling van afzonderlijke volumes op één schijf wordt niet ondersteund. |
Azure Disk Encryption (ADE) voor Linux-besturingssysteem | Ondersteund voor VM's met beheerde schijven. | VM's die niet-managede schijven gebruiken, worden niet ondersteund. <br/><br/> Met HSM beveiligde sleutels worden niet ondersteund. <br/><br/> Versleuteling van afzonderlijke volumes op één schijf wordt niet ondersteund. <br><br> Bekend probleem met het inschakelen van replicatie. [Meer informatie.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
ROULATIE VAN SAS-sleutel | Niet ondersteund | Als de SAS-sleutel voor opslagaccounts wordt geroteerd, moet de klant replicatie uitschakelen en opnieuw inschakelen. |
Host-caching | Ondersteund
Hot toevoegen    | Ondersteund | Het inschakelen van replicatie voor een gegevensschijf die u toevoegt aan een gerepliceerde Azure-VM wordt ondersteund voor VM's die gebruikmaken van beheerde schijven. <br/><br/> Er kan slechts één schijf tegelijk aan een Azure-VM worden toegevoegd. Parallelle toevoeging van meerdere schijven wordt niet ondersteund. |
Hot remove disk    | Niet ondersteund | Als u de gegevensschijf op de VM verwijdert, moet u replicatie uitschakelen en replicatie opnieuw inschakelen voor de VM.
Schijf uitsluiten | Ondersteuning. U moet [PowerShell gebruiken om](azure-to-azure-exclude-disks.md) te configureren. |    Tijdelijke schijven worden standaard uitgesloten.
Opslagruimten direct  | Ondersteund voor crash-consistente herstelpunten. Toepassings consistente herstelpunten worden niet ondersteund. |
Scale-out bestandsserver  | Ondersteund voor crash-consistente herstelpunten. Toepassings consistente herstelpunten worden niet ondersteund. |
Drbd | Schijven die deel uitmaken van een DRBD-installatie worden niet ondersteund. |
LRS | Ondersteund |
GRS | Ondersteund |
RA-GRS | Ondersteund |
ZRS | Niet ondersteund |
Cool storage en Hot Storage | Niet ondersteund | Schijven van virtuele machines worden niet ondersteund in 'cool' en 'hot' opslag
Azure Storage voor virtuele netwerken maken  | Ondersteund | Als u de toegang van het virtuele netwerk tot opslagaccounts beperkt, schakel [dan Vertrouwde toegang Microsoft-services.](../storage/common/storage-network-security.md#exceptions)
V2-opslagaccounts voor algemeen gebruik (zowel hot- als cool-laag) | Ondersteund | De transactiekosten nemen aanzienlijk toe in vergelijking met V1-opslagaccounts voor algemeen gebruik
Generatie 2 (UEFI opstarten) | Ondersteund
NVMe-schijven | Niet ondersteund
Gedeelde Azure-schijven | Niet ondersteund
Optie voor veilige overdracht | Ondersteund
Schijven die zijn ingeschakeld voor Write Accelerator | Niet ondersteund
Tags  | Ondersteund | Door de gebruiker gegenereerde tags worden elke 24 uur gerepliceerd.

>[!IMPORTANT]
> Om prestatieproblemen te voorkomen, moet u ervoor zorgen dat u de schaalbaarheids- en prestatiedoelen van de VM-schijf voor [beheerde schijven volgt.](../virtual-machines/disks-scalability-targets.md) Als u de standaardinstellingen gebruikt, Site Recovery de vereiste schijven en opslagaccounts gemaakt op basis van de bronconfiguratie. Als u uw eigen instellingen wilt aanpassen en selecteren, volgt u de schaalbaarheids- en prestatiedoelen voor uw bron-VM's.

## <a name="limits-and-data-change-rates"></a>Limieten en gegevenswijzigingssnelheden

De volgende tabel bevat een Site Recovery limieten.

- Deze limieten zijn gebaseerd op onze tests, maar omvatten natuurlijk niet alle mogelijke toepassings-I/O-combinaties.
- De werkelijke resultaten kunnen variëren op basis van uw app-I/O-combinatie.
- Er zijn twee limieten om rekening mee te houden, per gegevensverloop van schijf en gegevensverloop per virtuele machine.
- De huidige limiet voor gegevensverloop per virtuele machine is 54 MB/s, ongeacht de grootte.

**Opslagdoel** | **Gemiddelde bronschijf-I/O** |**Gemiddeld gegevensverloop van bronschijf** | **Totaal gegevensverloop van bronschijf per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf

## <a name="replicated-machines---networking"></a>Gerepliceerde machines - netwerken
**Instelling** | **Ondersteuning** | **Details**
--- | --- | ---
NIC | Maximum aantal ondersteund voor een specifieke Azure-VM-grootte | NIC's worden gemaakt wanneer de VM wordt gemaakt tijdens de failover.<br/><br/> Het aantal NIC's op de failover-VM is afhankelijk van het aantal NIC's op de bron-VM toen replicatie werd ingeschakeld. Als u een NIC toevoegt of verwijdert na het inschakelen van replicatie, heeft dit geen invloed op het aantal NIC's op de gerepliceerde VM na een failover. <br/><br/> De volgorde van NIC's na een failover is niet gegarandeerd hetzelfde als de oorspronkelijke volgorde. <br/><br/> U kunt de naam van NIC's in de doelregio wijzigen op basis van de naamgevingsconventieën van uw organisatie. Naamswijziging van NIC wordt ondersteund met behulp van PowerShell.
Internet Load Balancer | Niet ondersteund | U kunt openbare/internet load balancers instellen in de primaire regio. Openbare/internet load balancers worden echter niet ondersteund door Azure Site Recovery in de DR-regio.
Interne load balancer | Ondersteund | Koppel de vooraf geconfigureerde load balancer met behulp van Azure Automation script in een herstelplan.
Openbaar IP-adres | Ondersteund | Koppel een bestaand openbaar IP-adres aan de NIC. Of maak een openbaar IP-adres en koppel dit aan de NIC met behulp van een Azure Automation script in een herstelplan.
NSG op NIC | Ondersteund | Koppel de NSG aan de NIC met behulp van een Azure Automation script in een herstelplan.
NSG in subnet | Ondersteund | Koppel de NSG aan het subnet met behulp van Azure Automation script in een herstelplan.
Gereserveerd (statisch) IP-adres | Ondersteund | Als de NIC op de bron-VM een statisch IP-adres heeft en het doelsubnet hetzelfde IP-adres beschikbaar heeft, wordt deze toegewezen aan de VM met een mislukte poging.<br/><br/> Als het doelsubnet niet hetzelfde IP-adres beschikbaar heeft, wordt een van de beschikbare IP-adressen in het subnet gereserveerd voor de virtuele machine.<br/><br/> U kunt ook een vast IP-adres en subnet opgeven in **Gerepliceerde items**  >  **Instellingen**  >  **Compute- en netwerknetwerkinterfaces.**  >  
Dynamisch IP-adres | Ondersteund | Als de NIC op de bron dynamische IP-adressering heeft, is de NIC op de VM met een mislukte over nic ook standaard dynamisch.<br/><br/> U kunt dit zo nodig wijzigen in een vast IP-adres.
Meerdere IP-adressen | Niet ondersteund | Wanneer u een fail over een VM met een NIC met meerdere IP-adressen, wordt alleen het primaire IP-adres van de NIC in de bronregio bewaard. Als u meerdere IP-adressen wilt toewijzen, [](recovery-plan-overview.md) kunt u VM's toevoegen aan een herstelplan en een script toevoegen om extra IP-adressen aan het plan toe te wijzen. U kunt de wijziging ook handmatig of met een script na een failover uitvoeren.
Traffic Manager     | Ondersteund | U kunt de Traffic Manager zo configureren dat verkeer regelmatig wordt gerouteerd naar het eindpunt in de bronregio en naar het eindpunt in de doelregio in het geval van een failover.
Azure DNS | Ondersteund |
Aangepaste DNS    | Ondersteund |
Niet-gemachtigde proxy | Ondersteund | [Meer informatie](./azure-to-azure-about-networking.md)
Geverifieerde proxy | Niet ondersteund | Als de VM een geverifieerde proxy gebruikt voor uitgaande connectiviteit, kan deze niet worden gerepliceerd met behulp van Azure Site Recovery.
Vpn-site-naar-site-verbinding met on-premises<br/><br/>(met of zonder ExpressRoute)| Ondersteund | Zorg ervoor dat de UDR's en NSG's zodanig zijn geconfigureerd dat het Site Recovery niet wordt gerouteerd naar on-premises. [Meer informatie](./azure-to-azure-about-networking.md)
VNET-naar-VNET-verbinding    | Ondersteund | [Meer informatie](./azure-to-azure-about-networking.md)
Service-eindpunten voor virtueel netwerk | Ondersteund | Als u de toegang tot het virtuele netwerk tot opslagaccounts beperkt, moet u ervoor zorgen dat de vertrouwde Microsoft-services toegang tot het opslagaccount hebben.
Versneld netwerken | Ondersteund | Versneld netwerken moet zijn ingeschakeld op de bron-VM. [Meer informatie](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo Alto-netwerkapparaat | Niet ondersteund | Bij apparaten van derden gelden er vaak beperkingen die door de provider in de virtuele machine worden opgelegd. Azure Site Recovery moeten agent, extensies en uitgaande connectiviteit beschikbaar zijn. Maar het apparaat laat geen uitgaande activiteit binnen de virtuele machine configureren.
IPv6  | Niet ondersteund | Gemengde configuraties met zowel IPv4 als IPv6 worden ook niet ondersteund. Maak het subnet van het IPv6-bereik vrij vóór een Site Recovery bewerking.
Private Link-toegang tot Site Recovery service | Ondersteund | [Meer informatie](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Tags  | Ondersteund | Door de gebruiker gegenereerde tags op NIC's worden elke 24 uur gerepliceerd.



## <a name="next-steps"></a>Volgende stappen

- Lees [de richtlijnen voor netwerken](./azure-to-azure-about-networking.md)  voor het repliceren van Azure-VM's.
- Implementeer herstel na noodherstel [door Azure-VM's te repliceren.](./azure-to-azure-quickstart.md)
