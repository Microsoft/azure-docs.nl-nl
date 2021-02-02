---
title: Ondersteunings matrix voor nood herstel voor VMware/fysiek in Azure Site Recovery
description: Hierin wordt een overzicht gegeven van de ondersteuning voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure met behulp van Azure Site Recovery.
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 48eba42548287b54d99d1a2188786437f4b05a2b
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475468"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Ondersteunings matrix voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure

In dit artikel vindt u een overzicht van de ondersteunde onderdelen en instellingen voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure met behulp van [Azure site Recovery](site-recovery-overview.md).

- Meer [informatie](vmware-azure-architecture.md) over de architectuur voor herstel na nood gevallen voor de virtuele VMWare-machine of fysieke server.
- Volg de [zelf studies](tutorial-prepare-azure.md) om herstel na nood gevallen uit te proberen.

> [!NOTE]
> Met Site Recovery worden klant gegevens niet uit de doel regio verplaatst of opgeslagen, waarin nood herstel voor de bron machines is ingesteld. Klanten kunnen een Recovery Services kluis uit een andere regio selecteren als ze dat doen. De Recovery Services kluis bevat meta gegevens, maar geen werkelijke klant gegevens.

## <a name="deployment-scenarios"></a>Implementatiescenario's

**Scenario** | **Details**
--- | ---
Herstel na nood geval voor virtuele VMware-machines | Replicatie van on-premises virtuele VMware-machines naar Azure. U kunt dit scenario implementeren in de Azure Portal of met behulp van [Power shell](vmware-azure-disaster-recovery-powershell.md).
Herstel na nood geval van fysieke servers | Replicatie van on-premises fysieke Windows/Linux-servers naar Azure. U kunt dit scenario implementeren in de Azure Portal.

## <a name="on-premises-virtualization-servers"></a>On-premises virtualisatieservers

**Server** | **Vereisten** | **Details**
--- | --- | ---
vCenter Server | Versie 7,0 & volgende updates in deze versie, 6,7, 6,5, 6,0 of 5,5 | U wordt aangeraden een vCenter-Server te gebruiken in uw implementatie voor herstel na nood gevallen.
vSphere-hosts | Versie 7,0 & volgende updates in deze versie, 6,7, 6,5, 6,0 of 5,5 | We raden aan dat vSphere-hosts en vCenter-servers zich in hetzelfde netwerk bevinden als de proces server. Standaard wordt de proces server uitgevoerd op de configuratie server. [Meer informatie](vmware-physical-azure-config-process-server-overview.md).

## <a name="site-recovery-configuration-server"></a>Configuratie Server Site Recovery

De configuratie server is een on-premises machine waarop Site Recovery-onderdelen worden uitgevoerd, met inbegrip van de configuratie server, de proces server en de hoofddoel server.

- Voor virtuele VMware-machines stelt u de configuratie server in door een OVF-sjabloon te downloaden om een virtuele VMware-machine te maken.
- Voor fysieke servers stelt u de computer van de configuratie server hand matig in.

**Onderdeel** | **Vereisten**
--- |---
CPU-kernen | 8
RAM | 16 GB
Aantal schijven | 3 schijven<br/><br/> Schijven bevatten de besturingssysteem schijf, de cache schijf van de proces server en het Bewaar station voor failback.
Beschik bare schijf ruimte | 600 GB aan ruimte voor de cache van de proces server.
Beschik bare schijf ruimte | 600 GB aan ruimte voor het Bewaar station.
Besturingssysteem  | Windows Server 2012 R2 of Windows Server 2016 met bureaublad ervaring <br/><br> Als u van plan bent het ingebouwde hoofd doel van dit apparaat te gebruiken voor failback, moet u ervoor zorgen dat de versie van het besturings systeem gelijk is aan of hoger is dan de gerepliceerde items.|
Landinstelling van het besturingssysteem | Engels (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Niet nodig voor de configuratie Server versie [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) of hoger.
Windows Server-functies | Schakel Active Directory Domain Services niet in. Internet Information Services (IIS) of Hyper-V.
Groepsbeleidsregels| - Toegang tot de opdrachtprompt voorkomen. <br/> - Toegang tot registerbewerkingsprogramma's voorkomen. <br/> - Op logica vertrouwen voor bestandsbijlagen. <br/> - Uitvoering van script inschakelen. <br/> - [Meer informatie](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | Zorg ervoor dat:<br/><br/> -Geen vooraf bestaande standaard website hebben <br/> - [Anonieme verificatie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) inschakelen <br/> - Instelling [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) inschakelen  <br/> -Geen vooraf bestaande website/app Luis teren op poort 443<br/>
Type NIC | VMXNET3 (wanneer geïmplementeerd als een VMware-VM)
Type IP-adres | Statisch
Poorten | 443 gebruikt voor het beheren van de kanaal indeling<br/>9443 voor gegevens transport

> [!NOTE]
Het besturings systeem moet worden geïnstalleerd met de Engelse land instelling. Conversie van de installatie van de land instelling kan leiden tot potentiële problemen.

## <a name="replicated-machines"></a>Gerepliceerde machines

Site Recovery ondersteunt replicatie van elke werk belasting die wordt uitgevoerd op een ondersteunde computer.

**Onderdeel** | **Details**
--- | ---
Computer instellingen | Machines die naar Azure repliceren, moeten voldoen aan de [vereisten van Azure](#azure-vm-requirements).
Machine workload | Site Recovery ondersteunt replicatie van elke werk belasting die wordt uitgevoerd op een ondersteunde computer. [Meer informatie](./site-recovery-workload.md).
Computer naam | Zorg ervoor dat de weergave naam van de computer niet in door [Azure gereserveerde resource namen](../azure-resource-manager/templates/error-reserved-resource-name.md) valt<br/><br/> Namen van logische volumes zijn niet hoofdletter gevoelig. Zorg ervoor dat er geen twee volumes op een apparaat dezelfde naam hebben. Bijvoorbeeld: volumes met de naam ' voLUME1 ', ' voLUME1 ' kunnen niet worden beveiligd via Azure Site Recovery.

### <a name="for-windows"></a>Voor Windows

**Besturingssysteem** | **Details**
--- | ---
Windows Server 2019 | Ondersteund door [Update pakket 34](https://support.microsoft.com/help/4490016) (versie 9,22 van de Mobility-service).
Windows Server 2016 64-bits | Ondersteund voor Server Core, server met bureaublad ervaring.
Windows Server 2012 R2/Windows Server 2012 | Ondersteund.
Windows Server 2008 R2 met SP1 en hoger. | Ondersteund.<br/><br/> Van versie [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility Service-agent hebt u de [onderhouds stack update (Ssu)](https://support.microsoft.com/help/4490628) en de [SHA-2-update](https://support.microsoft.com/help/4474419) geïnstalleerd op computers met Windows 2008 R2 met SP1 of hoger. SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt. Meer informatie over [SHA-2-upgrade en-vereisten](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339).
Windows Server 2008 met SP2 of hoger (64-bits/32-bits) |  Alleen ondersteund voor migratie. [Meer informatie](migrate-tutorial-windows-server-2008.md).<br/><br/> Van versie [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility Service-agent hebt u de [onderhouds stack update (Ssu)](https://support.microsoft.com/help/4493730) en de [SHA-2-update](https://support.microsoft.com/help/4474419) geïnstalleerd op computers met Windows 2008 SP2. SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt. Meer informatie over [SHA-2-upgrade en-vereisten](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339).
Windows 10, Windows 8.1, Windows 8 | Alleen 64-bits systeem wordt ondersteund. 32-bits systeem wordt niet ondersteund.
Windows 7 met SP1 64-bits | Ondersteund door [Update pakket 36](https://support.microsoft.com/help/4503156) (versie 9,22 van de Mobility-service). </br></br> Van [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) van de Mobility Service-agent hebt u de [onderhouds stack update (Ssu)](https://support.microsoft.com/help/4490628) en de [SHA-2-update](https://support.microsoft.com/help/4474419) geïnstalleerd op Windows 7 SP1-computers.  SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt. Meer informatie over [SHA-2-upgrade en-vereisten](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339).

### <a name="for-linux"></a>Voor Linux

**Besturingssysteem** | **Details**
--- | ---
Linux | Alleen 64-bits systeem wordt ondersteund. 32-bits systeem wordt niet ondersteund.<br/><br/>Op elke Linux-server moeten [Lis-onderdelen (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) zijn geïnstalleerd. Het is vereist om de server op te starten in azure na een testfailover of failover. Als ingebouwde LIS-onderdelen ontbreken, moet u ervoor zorgen dat u de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) installeert voordat u replicatie inschakelt om de computers op te starten in Azure. <br/><br/> Site Recovery organiseert de failover voor het uitvoeren van Linux-servers in Azure. Linux-leveranciers kunnen echter wel de ondersteuning beperken tot distributie versies die geen einde van de levens duur hebben bereikt.<br/><br/> In Linux-distributies worden alleen de voorraad kernels ondersteund die deel uitmaken van de distributie secundaire versie/update.<br/><br/> Het bijwerken van beveiligde machines in grote Linux-distributie versies wordt niet ondersteund. Als u een upgrade wilt uitvoeren, schakelt u replicatie uit, voert u een upgrade van het besturings systeem uit en schakelt u de replicatie opnieuw in<br/><br/> Meer [informatie](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) over ondersteuning voor Linux en open-source technologie in Azure.
Linux Red Hat Enter prise | 5,2 tot 5,11</b><br/> 6,1 tot 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9 bèta versie](https://support.microsoft.com/help/4578241/), [7,9](https://support.microsoft.com/help/4590304/) </br> [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609), [8,3](https://support.microsoft.com/help/4597409/) <br/> Enkele oudere kernels op servers met Red Hat Enterprise Linux 5.2-5,11 & 6.1-6.10 hebben geen [Lis-onderdelen (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) vooraf geïnstalleerd. Als ingebouwde LIS-onderdelen ontbreken, moet u ervoor zorgen dat u de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) installeert voordat u replicatie inschakelt om de computers op te starten in Azure.
Linux: CentOS | 5,2 tot 5,11</b><br/> 6,1 tot 6,10</b><br/> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/) </br> [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609), [8,3](https://support.microsoft.com/help/4597409/) <br/><br/> Enkele oudere kernels op servers met CentOS 5.2-5,11 & 6.1-6.10 hebben geen  [Lis-onderdelen (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) vooraf geïnstalleerd. Als ingebouwde LIS-onderdelen ontbreken, moet u ervoor zorgen dat u de [onderdelen](https://www.microsoft.com/download/details.aspx?id=55106) installeert voordat u replicatie inschakelt om de computers op te starten in Azure.
Ubuntu | Ubuntu 14,04 * LTS-server [(ondersteunde kernel-versies controleren)](#ubuntu-kernel-versions)<br/>Ubuntu 16,04 * LTS-server [(ondersteunde kernel-versies controleren)](#ubuntu-kernel-versions) </br> Ubuntu 18,04 * LTS-server [(ondersteunde kernel-versies controleren)](#ubuntu-kernel-versions) </br> Ubuntu 20,04 * LTS-server [(ondersteunde kernel-versies controleren)](#ubuntu-kernel-versions) </br> (*inclusief ondersteuning voor alle 14,04.* x *, 16,04.* x *, 18,04.* x *, 20,04.* x * versies)
Debian | Debian 7/Debian 8 (inclusief ondersteuning voor alle 7. *x*, 8. *x* -versies); Debian 9 (biedt ondersteuning voor 9,1 tot 9,13. Debian 9,0 wordt niet ondersteund.), Debian 10 [(ondersteunde kernel-versies controleren)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4, [SP5](https://support.microsoft.com/help/4570609) [(ondersteunde kernel-versies controleren)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(ondersteunde kernel-versies controleren)](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 SP3. [Zorg ervoor dat u het meest recente installatie programma van de Mobility-agent op de configuratie server downloadt](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server). </br> SUSE Linux Enterprise Server 11 SP4 </br> **Opmerking**: het upgraden van gerepliceerde machines van SuSE Linux Enterprise Server 11 SP3 naar SP4 wordt niet ondersteund. Als u een upgrade wilt uitvoeren, schakelt u replicatie uit en schakelt u na de upgrade opnieuw in. <br/>|
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [7,9](https://support.microsoft.com/help/4597409/), [8,0](https://support.microsoft.com/help/4573888/), [8,1](https://support.microsoft.com/help/4573888/), [8,2](https://support.microsoft.com/help/4573888/), [8,3](https://support.microsoft.com/help/4597409/)  <br/> Met de Red Hat compatibele kernel of een onherstelbare versie van de Enter prise kernel van 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8.1<br/>Uitvoeren op alle UEK-kernels en RedHat-kernel <= 3.10.0-1062. * worden ondersteund in [9,35](https://support.microsoft.com/help/4573888/) -ondersteuning voor de rest van de RedHat-kernels is beschikbaar in [9,36](https://support.microsoft.com/help/4578241/)

> [!Note]
>- Voor elk van de Windows-versies biedt Azure Site Recovery alleen ondersteuning voor [LTSC-builds (Long-term Servicing Channel)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) .  [Semi-Annual-kanaal](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) releases worden op dit moment niet ondersteund.
>- Zorg ervoor dat voor Linux-versies Azure Site Recovery aangepaste installatie kopieën van besturings systemen niet worden ondersteund. Alleen de aandelen-kernels die deel uitmaken van de distributie secundaire versie/update worden ondersteund.

### <a name="ubuntu-kernel-versions"></a>Ubuntu-kernel-versies

**Ondersteunde versie** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
14,04 LTS | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.13.0-24-generic naar 3.13.0-170-generic,<br/>3.16.0-25-generic naar 3.16.0-77-generic,<br/>3.19.0-18-generic naar 3.19.0-80-generic,<br/>4.2.0-18-generic naar 4.2.0-42-generic,<br/>4.4.0-21-generic naar 4.4.0-148-generic,<br/>4.15.0-1023-Azure naar 4.15.0-1045-Azure |
|||
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic naar 4.4.0-197-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-128-algemeen<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1102-Azure |
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-algemeen naar 4.4.0-194-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-123-algemeen<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1098-azure|
16,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-algemeen naar 4.4.0-190-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-118-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1096-Azure|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-algemeen naar 4.4.0-189-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-115-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1093-Azure |
16,04 LTS | [9,36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-algemeen naar 4.4.0-186-generic,<br/>4.8.0-34-generic naar 4.8.0-58-generic,<br/>4.10.0-14-generic naar 4.10.0-42-generic,<br/>4.11.0-13-algemeen naar 4.11.0-14-generic,<br/>4.13.0-16-generic naar 4.13.0-45-generic,<br/>4.15.0-13-algemeen naar 4.15.0-112-generic<br/>4.11.0-1009-Azure naar 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure naar 4.13.0-1018-Azure <br/>4.15.0-1012-Azure naar 4.15.0-1092-Azure |
|||
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-algemeen naar 4.15.0-129-generic </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-generic naar 5.0.0-63-generic </br> 5.3.0-19-Gene riek tot 5.3.0-69-algemeen </br> 5.4.0-37-generic naar 5.4.0-59-generic</br> 4.15.0-1009-Azure naar 4.15.0-1103-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1035-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1035-Azure|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-algemeen naar 4.15.0-123-algemeen </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-generic naar 5.0.0-63-generic </br> 5.3.0-19-Gene riek tot 5.3.0-69-algemeen </br> 5.4.0-37-generic tot 5.4.0-53-generic</br> 4.15.0-1009-Azure naar 4.15.0-1099-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1035-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1031-Azure|
18,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-algemeen naar 4.15.0-118-generic </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-algemeen naar 5.0.0-61-algemeen </br> 5.3.0-19-generic naar 5.3.0-67-generic </br> 5.4.0-37-generic tot 5.4.0-48-algemeen</br> 4.15.0-1009-Azure naar 4.15.0-1096-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1035-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1026-Azure|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-algemeen naar 4.15.0-115-generic </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-generic naar 5.0.0-60-generic </br> 5.3.0-19-Gene riek tot 5.3.0-66-algemeen </br> 5.4.0-37-generic tot 5.4.0-45-algemeen</br> 4.15.0-1009-Azure naar 4.15.0-1093-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1035-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1023-Azure|
18,04 LTS | [9,36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-algemeen naar 4.15.0-112-generic </br> 4.18.0-13-algemeen naar 4.18.0-25-algemeen </br> 5.0.0-15-generic naar 5.0.0-58-generic </br> 5.3.0-19-Gene riek tot 5.3.0-64-algemeen </br> 5.4.0-37-generic naar 5.4.0-42-generic</br> 4.15.0-1009-Azure naar 4.15.0-1092-Azure </br> 4.18.0-1006-Azure naar 4.18.0-1025-Azure </br> 5.0.0-1012-Azure naar 5.0.0-1036-Azure </br> 5.3.0-1007-Azure naar 5.3.0-1032-Azure </br> 5.4.0-1020-Azure naar 5.4.0-1022-Azure|
|||
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic naar 5.4.0-59 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1035-Azure </br> 5.8.0-29-generic naar 5.8.0-34-generic|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic naar 5.4.0-53 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1031-Azure
20,04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic tot 5.4.0-48 </br> -generic 5.4.0-1010-Azure tot 5.4.0-1026-Azure
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic naar 5.4.0-45 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1023-Azure
20,04 LTS |[9,36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generic naar 5.4.0-42 </br> -generic 5.4.0-1010-Azure naar 5.4.0-1022-Azure

### <a name="debian-kernel-versions"></a>Debian-kernel-versies


**Ondersteunde versie** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
Debian 7 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.2.0-4-amd64 tot 3.2.0-6-amd64, 3.16.0 -0. bpo. 4-amd64 |
|||
Debian 8 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 3.16.0-4-amd64 tot 3.16.0-11-amd64, 4.9.0 -0. bpo. 4-amd64 tot 4.9.0 -0. bpo. 11-amd64 |
|||
Debian 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 tot 4.9.0-14-amd64 </br> 4.19.0 -0. bpo. 1-amd64 to 4.19.0 -0. bpo. 13-amd64 </br> 4.19.0 -0. bpo. 1-Cloud-amd64 to 4.19.0 -0. bpo. 13-Cloud-amd64 </br>
Debian 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 tot 4.9.0-14-amd64 </br> 4.19.0 -0. bpo. 1-amd64 to 4.19.0 -0. bpo. 12-amd64 </br> 4.19.0 -0. bpo. 1-Cloud-amd64 to 4.19.0 -0. bpo. 12-Cloud-amd64 </br> 
Debian 9,1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 tot 4.9.0-13-amd64 </br> 4.19.0 -0. bpo. 1-amd64 to 4.19.0 -0. bpo. 11-amd64 </br> 4.19.0 -0. bpo. 1-Cloud-amd64 to 4.19.0 -0. bpo. 11-Cloud-amd64 </br> 
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 tot 4.9.0-13-amd64, 4.19.0 -0. bpo. 6-amd64 tot 4.19.0 -0. bpo. 10-amd64, 4.19.0 -0. bpo. 6-Cloud-amd64 tot 4.19.0 -0. bpo. 10-Cloud-amd64

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 ondersteunde kernel-versies

**Release** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Alle [Stock-SuSE 12 SP1-, SP2-, SP3-en SP4-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure naar 4.12.14-16.38-Azure|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | Alle [Stock-SuSE 12 SP1-, SP2-, SP3-en SP4-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure naar 4.12.14-16.34-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.38](https://support.microsoft.com/help/4590304/) | Alle [Stock-SuSE 12 SP1-, SP2-, SP3-en SP4-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure naar 4.12.14-16.28-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/),  | Alle [Stock-SuSE 12 SP1-, SP2-, SP3-en SP4-kernels](https://www.suse.com/support/kb/doc/?id=000019587) worden ondersteund.</br></br> 4.4.138-4.7-Azure naar 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure naar 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure naar 4.12.14-16.22-Azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 ondersteunde kernel-versies

**Release** | **Mobility Service-versie** | **Kernelversie** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Standaard worden alle [Stock-SuSE 15-en 15-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure naar 4.12.14-8.55-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure naar 5.3.18-18.29-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Standaard worden alle [Stock-SuSE 15-en 15-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure naar 4.12.14-8.47-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure naar 5.3.18-18.21-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | Standaard worden alle [Stock-SuSE 15-en 15-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure naar 4.12.14-8.44-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure naar 5.3.18-18.18-Azure
SUSE Linux Enterprise Server 15 en 15 SP1 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/)  | Standaard worden alle [Stock-SuSE 15-en 15-kernels](https://www.suse.com/support/kb/doc/?id=000019587) ondersteund.</br></br> 4.12.14-5,5-Azure naar 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure naar 4.12.14-8.38-Azure

## <a name="linux-file-systemsguest-storage"></a>Linux-bestands systemen/gast opslag

**Onderdeel** | **Ondersteund**
--- | ---
Bestandssystemen | ext3, ext4, XFS, BTRFS (voor waarden die van toepassing zijn volgens deze tabel)
Inrichting van Logical Volume Management (LVM)| Brede inrichting-Ja <br></br> Thin Provisioning-no
Volume manager | -LVM wordt ondersteund.<br/> -/boot op LVM wordt ondersteund door [Update pakket 31](https://support.microsoft.com/help/4478871/) (versie 9,20 van de Mobility-service). Het wordt niet ondersteund in eerdere versies van de Mobility-service.<br/> -Meerdere besturingssysteem schijven worden niet ondersteund.
Geparavirtualiseerde-opslag apparaten | Apparaten die zijn geëxporteerd door geparavirtualiseerde stuurprogramma's worden niet ondersteund.
Meerdere wacht rijen voor blok-i/o-apparaten | Wordt niet ondersteund.
Fysieke servers met de HP CCISS-opslag controller | Wordt niet ondersteund.
Naamgevings regels voor apparaten en koppel punten | De naam van het apparaat of het koppel punt moet uniek zijn.<br/> Zorg ervoor dat er geen twee apparaten/koppel punten bestaan uit hoofdletter gevoelige namen. Het benoemen van apparaten voor dezelfde VM als *device1* en *device1* wordt niet ondersteund.
Mappen | Als u een versie van de Mobility-service hebt die ouder is dan versie 9,20 (uitgebracht in [Update pakket 31](https://support.microsoft.com/help/4478871/)), gelden de volgende beperkingen:<br/><br/> -Deze directory's (indien ingesteld als afzonderlijke partities/bestands systemen) moeten zich op dezelfde besturingssysteem schijf op de bron server bevindt:/(root),/boot,/usr,/usr/local,/var,/etc.</br> -De/boot-map moet zich op een schijf partitie benemen en geen LVM-volume zijn.<br/><br/> Vanaf versie 9,20 worden deze beperkingen niet toegepast. 
Opstartmap | -Opstart schijven mag zich in GPT-partitie-indeling. Dit is een beperking van Azure-architectuur. GPT-schijven worden ondersteund als gegevens schijven.<br/><br/> Meerdere opstart schijven op een virtuele machine worden niet ondersteund<br/><br/> -/boot op een LVM-volume op meer dan één schijf wordt niet ondersteund.<br/> -Een machine zonder een opstart schijf kan niet worden gerepliceerd.
Vereisten voor beschik bare ruimte| 2 GB op de/root-partitie <br/><br/> 250 MB in de installatiemap
XFSv5 | XFSv5-functies op XFS-bestands systemen, zoals controlesom van de meta gegevens, worden ondersteund (de Mobility Service-versie 9,10 en hoger).<br/> Gebruik het hulp programma xfs_info om de XFS-superblok kering voor de partitie te controleren. Als `ftype` is ingesteld op 1, worden XFSv5-functies in gebruik.
BTRFS | BTRFS wordt ondersteund door [Update pakket 34](https://support.microsoft.com/help/4490016) (versie 9,22 van de Mobility-service). BTRFS wordt niet ondersteund als:<br/><br/> -Het subvolume van het BTRFS-bestands systeem wordt gewijzigd nadat de beveiliging is ingeschakeld.</br> -Het BTRFS-bestands systeem is verdeeld over meerdere schijven.</br> -Het BTRFS-bestands systeem ondersteunt RAID.

## <a name="vmdisk-management"></a>VM/schijf beheer

**Actie** | **Details**
--- | ---
Grootte van schijf op een gerepliceerde VM wijzigen | Wordt op de bron-VM vóór de failover direct in de VM-eigenschappen ondersteund. U hoeft replicatie niet uit te scha kelen of opnieuw in te scha kelen.<br/><br/> Als u de bron-VM na een failover wijzigt, worden de wijzigingen niet vastgelegd.<br/><br/> Als u na een failover de schijf grootte op de Azure-VM wijzigt, maakt Site Recovery een nieuwe virtuele machine met de updates.
Schijf toevoegen op een gerepliceerde VM | Wordt niet ondersteund.<br/> Schakel de replicatie voor de virtuele machine uit, voeg de schijf toe en schakel de replicatie opnieuw in.

> [!NOTE]
> Wijzigingen in de schijf identiteit worden niet ondersteund. Als het partitioneren van de schijf bijvoorbeeld is gewijzigd van GPT naar MBR of andersom, wordt de schijf identiteit gewijzigd. In een dergelijk scenario wordt de replicatie onderbroken en wordt een nieuwe installatie vereist. 

## <a name="network"></a>Netwerk

**Onderdeel** | **Ondersteund**
--- | ---
NIC-koppeling host netwerk | Ondersteund voor VMware-Vm's. <br/><br/>Niet ondersteund voor replicatie van de fysieke machine.
VLAN host netwerk | Ja.
Host-netwerk IPv4 | Ja.
Host netwerk IPv6 | Nee.
NIC-koppeling voor gast/Server netwerk | Nee.
Gast/Server netwerk IPv4 | Ja.
Gast/Server netwerk IPv6 | Nee.
Statisch IP-adres van gast/Server netwerk (Windows) | Ja.
Statisch IP-adres van gast/Server netwerk (Linux) | Ja. <br/><br/>Vm's zijn geconfigureerd voor het gebruik van DHCP bij failback.
Gast/Server netwerk meerdere Nic's | Ja.
Persoonlijke koppelings toegang tot Site Recovery service | Ja. [Meer informatie](hybrid-how-to-enable-replication-private-endpoints.md).


## <a name="azure-vm-network-after-failover"></a>Azure VM-netwerk (na failover)

**Onderdeel** | **Ondersteund**
--- | ---
Azure ExpressRoute | Ja
ILB | Ja
ELB | Ja
Azure Traffic Manager | Ja
Multi-NIC | Ja
Gereserveerd IP adres | Ja
IPv4 | Ja
Bron-IP-adres behouden | Ja
Service-eindpunten voor een virtueel Azure-netwerk<br/> | Ja
Versneld netwerken | Nee

## <a name="storage"></a>Storage
**Onderdeel** | **Ondersteund**
--- | ---
Dynamische schijf | De besturingssysteem schijf moet een standaard schijf zijn. <br/><br/>Gegevens schijven kunnen dynamische schijven zijn
Configuratie van docker-schijf | Nee
Host-NFS | Ja voor VMware<br/><br/> Nee voor fysieke servers
SAN van host (iSCSI/FC) | Ja
VSAN hosten | Ja voor VMware<br/><br/> N.v.t. voor fysieke servers
Multipath (MPIO) hosten | Ja, getest met micro soft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM voor CLARiiON
Virtuele volumes hosten (VVols) | Ja voor VMware<br/><br/> N.v.t. voor fysieke servers
VMDK van gast/server | Ja
Gedeelde gast/server-cluster schijf | Nee
Door gast/server versleutelde schijf | Nee
Gast/server-NFS | Nee
ISCSI voor gast/server | Voor migratie-Ja<br/>Voor herstel na nood gevallen-Nee, iSCSI zal failback als een gekoppelde schijf aan de VM
Het SMB 3,0 van de gast/server | Nee
RDM/server | Ja<br/><br/> N.v.t. voor fysieke servers
Gast/server schijf > 1 TB | Ja, schijf moet groter zijn dan 1024 MB<br/><br/>Maxi maal 32 TB bij het repliceren naar Managed disks (9,40-versie en hoger)<br></br> Maxi maal 4.095 GB bij het repliceren naar opslag accounts
Gast/server-schijf met 4 KB logische en 4.000 fysieke sector grootte | Nee
Gast/server schijf met 4 KB logische en 512-bytes fysieke sector grootte | Nee
Volume van gast/server met gestripte schijf >4 TB | Ja
Beheer van logische volumes (LVM)| Dik inrichten-Ja <br></br> Thin Provisioning-Nee
Gast/Server-opslag ruimten | Nee
Gast/server-NVMe-interface | Nee
Hot-of-Remove-schijf voor gast/server | Nee
Gast/server-schijf uitsluiten | Ja
Meerdere paden gast/server (MPIO) | Nee
GPT/server-GUID-partities | Er worden vijf partities ondersteund van [Update pakket 37](https://support.microsoft.com/help/4508614/) (versie 9,25 van de Mobility-service). Eerder vier werden ondersteund.
ReFS | Flexibel bestands systeem wordt ondersteund met Mobility Service versie 9,23 of hoger
EFI/UEFI-opstart procedure voor gast/server | -Wordt ondersteund voor alle [UEFI-besturings systemen van Azure Marketplace](../virtual-machines/generation-2.md#generation-2-vm-images-in-azure-marketplace) met site Recovery Mobility agent versie 9,30 en hoger. <br/> -Secure UEFI-opstart type wordt niet ondersteund. [Meer informatie.](../virtual-machines/generation-2.md#on-premises-vs-azure-generation-2-vms)

## <a name="replication-channels"></a>Replicatie kanalen

|**Type replicatie**   |**Ondersteund**  |
|---------|---------|
|Offloaded data transfers (ODX)    |       Nee  |
|Offline seeding        |   Nee      |
| Azure Data Box | Nee

## <a name="azure-storage"></a>Azure Storage

**Onderdeel** | **Ondersteund**
--- | ---
Lokaal redundante opslag | Ja
Geografisch redundante opslag | Ja
Geografisch redundante opslag met leestoegang | Ja
Cool Storage | Nee
Hot Storage| Nee
Blok-blobs | Nee
Versleuteling-at-rest (SSE)| Ja
Versleuteling-at-rest (CMK)| Ja (via Power shell AZ 3.3.0 module)
Dubbele versleuteling bij rest | Ja (via Power shell AZ 3.3.0 module). Meer informatie over ondersteunde regio's voor [Windows](../virtual-machines/disk-encryption.md) en [Linux](../virtual-machines/disk-encryption.md).
Premium Storage | Ja
Optie voor beveiligde overdracht | Ja
Import/export-service | Nee
Firewalls voor VNets Azure Storage | Ja.<br/> Geconfigureerd op het doel opslag/cache-opslag account (wordt gebruikt voor het opslaan van replicatie gegevens).
V2-opslag accounts voor algemeen gebruik (warme en coole lagen) | Ja (de transactie kosten zijn aanzienlijk hoger voor v2 vergeleken met v1)

## <a name="azure-compute"></a>Azure Compute

**Functie** | **Ondersteund**
--- | ---
Beschikbaarheidssets | Ja
Beschikbaarheidszones | Nee
HUB | Ja
Managed Disks | Ja

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

On-premises Vm's die naar Azure worden gerepliceerd, moeten voldoen aan de vereisten van de Azure-VM die in deze tabel worden samenvatten. Wanneer Site Recovery een controle op vereisten uitvoert voor replicatie, mislukt de controle als aan sommige vereisten niet wordt voldaan.

**Onderdeel** | **Vereisten** | **Details**
--- | --- | ---
Gastbesturingssysteem | Controleer de [ondersteunde besturings systemen](#replicated-machines) voor gerepliceerde machines. | De controle is mislukt als dit niet wordt ondersteund.
Architectuur van gast besturingssysteem | 64-bits. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de besturingssysteemschijf | Maxi maal 2.048 GB. | De controle is mislukt als dit niet wordt ondersteund.
Aantal besturingssysteemschijven | 1 </br> de opstart-en systeem partitie op verschillende schijven wordt niet ondersteund | De controle is mislukt als dit niet wordt ondersteund.
Aantal gegevensschijven | 64 of minder. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de gegevens schijf | Tot 32 TB bij het repliceren naar een beheerde schijf (9,40-versie en hoger)<br></br>Maxi maal 4.095 GB bij het repliceren naar het opslag account| De controle is mislukt als dit niet wordt ondersteund.
Netwerkadapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Wordt niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
FC-schijf | Wordt niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
BitLocker | Wordt niet ondersteund. | BitLocker moet worden uitgeschakeld voordat u replicatie voor een machine inschakelt. |
VM-naam | Van 1 tot 63 tekens.<br/><br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de computer moet beginnen en eindigen met een letter of cijfer. |  Werk de waarde in de computer eigenschappen in Site Recovery bij.

## <a name="resource-group-limits"></a>Limieten voor resource groep

Raadpleeg het artikel over de [limieten en quota voor abonnementen](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)voor meer informatie over het aantal virtuele machines dat kan worden beveiligd onder één resource groep.

## <a name="churn-limits"></a>Verloop limieten

De volgende tabel bevat de Azure Site Recovery-limieten.
- Deze limieten zijn gebaseerd op onze tests, maar dekken niet alle mogelijke app-I/O-combi Naties.
- De werkelijke resultaten kunnen variëren op basis van uw toepassings-I/O-combinatie.
- Voor de beste resultaten wordt u ten zeerste aangeraden het [Deployment planner-hulp programma](site-recovery-deployment-planner.md)uit te voeren en uitgebreide toepassings tests uit te voeren met testfailover om de ware prestatie afbeelding voor uw app op te halen.

**Replicatie doel** | **Gemiddelde I/O-grootte van bronschijf** |**Gemiddeld gegevens verloop van bron schijf** | **Totale gegevens verloop van bron schijf per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 8 kB    | 2 MB/s | 168 GB per schijf
Premium P10 of P15 schijf | 16 kB | 4 MB/s |    336 GB per schijf
Premium P10 of P15 schijf | 32 kB of meer | 8 MB/s | 672 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 8 kB    | 5 MB/s | 421 GB per schijf
Premium P20 of P30 of P40 of P50 schijf | 16 kB of meer |20 MB/s | 1684 GB per schijf


**brongegevensverloop** | **Maximum limiet**
---|---
Piekgegevensverloop over alle schijven op een VM | 54 MB/s
Maximumgegevensverloop per dag dat wordt ondersteund door een processerver | 2 TB

- Dit zijn gemiddelden uitgaande van een I/O-overlapping van 30%.
- Site Recovery kan een hogere doorvoer verwerken op basis van overlappingsverhouding, grotere schrijfgrootten en daadwerkelijk workload-I/O-gedrag.
- Deze nummers nemen een typische achterstand van ongeveer vijf minuten. Dat wil zeggen dat de gegevens na het uploaden binnen vijf minuten worden verwerkt en er een herstelpunt is gemaakt.

## <a name="vault-tasks"></a>Kluis taken

**Actie** | **Ondersteund**
--- | ---
De kluis verplaatsen tussen resource groepen | Nee
De kluis verplaatsen binnen en tussen abonnementen | Nee
Opslag, netwerk, Azure-Vm's verplaatsen tussen resource groepen | Nee
Verplaats opslag-, netwerk-, Azure-Vm's binnen en tussen abonnementen. | Nee


## <a name="obtain-latest-components"></a>Nieuwste onderdelen ophalen

**Naam** | **Beschrijving** | **Details**
--- | --- | ---
Configuratieserver | On-premises geïnstalleerd.<br/> Coördineert de communicatie tussen on-premises VMware-servers of fysieke machines en Azure. | - [Meer informatie over](vmware-physical-azure-config-process-server-overview.md) de configuratie server.<br/> - [Meer informatie over](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) het upgraden naar de nieuwste versie.<br/> - [Meer informatie over](vmware-azure-deploy-configuration-server.md) het instellen van de configuratie server.
Proces server | standaard geïnstalleerd op de configuratieserver.<br/> Hiermee ontvangt u replicatie gegevens, optimaliseert u deze met caching, compressie en versleuteling, en verzendt u deze naar Azure.<br/> Naarmate uw implementatie groeit, kunt u extra proces servers toevoegen om grotere volumes van replicatie verkeer af te handelen. | - [Meer informatie over](vmware-physical-azure-config-process-server-overview.md) de proces server.<br/> - [Meer informatie over](vmware-azure-manage-process-server.md#upgrade-a-process-server) het upgraden naar de nieuwste versie.<br/> - [Meer informatie over](vmware-physical-large-deployment.md#set-up-a-process-server) het instellen van scale-out proces servers.
Mobility-service | Geïnstalleerd op virtuele VMware-machines of fysieke servers die u wilt repliceren.<br/> Coördineert de replicatie tussen on-premises VMware-servers/fysieke servers en Azure.| - [Meer informatie over](vmware-physical-mobility-service-overview.md) de Mobility-service.<br/> - [Meer informatie over](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) het upgraden naar de nieuwste versie.<br/>



## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het](tutorial-prepare-azure.md) voorbereiden van Azure voor herstel na nood gevallen van virtuele VMware-machines.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
