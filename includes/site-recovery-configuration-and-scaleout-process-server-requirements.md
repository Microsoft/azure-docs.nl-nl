---
title: bestand opnemen
description: bestand opnemen
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 61aa5ffcbab493109371067b1eb9d199a29cb852
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551561"
---
**Vereisten voor configuratie en processerver**


## <a name="hardware-requirements"></a>Hardwarevereisten

**Onderdeel** | **Vereiste** 
--- | ---
CPU-kernen | 8 
RAM | 16 GB
Aantal schijven | 3, waaronder de besturingssysteemschijf, de cacheschijf van de processerver en de bewaarschijf voor failback 
Vrije schijfruimte (cache van de processerver) | 600 GB
Vrije schijfruimte (bewaarschijf) | 600 GB
 | 

## <a name="software-requirements"></a>Softwarevereisten

**Onderdeel** | **Vereiste** 
--- | ---
Besturingssysteem | Windows Server 2012 R2 <br> Windows Server 2016
Landinstelling van het besturingssysteem | Engels (en-*)
Windows Server-functies | Deze rollen niet inschakelen: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Groepsbeleidsregels | Deze groepsbeleidsregels niet inschakelen: <br> - Toegang tot de opdrachtprompt voorkomen. <br> - Toegang tot registerbewerkingsprogramma's voorkomen. <br> - Op logica vertrouwen voor bestandsbijlagen. <br> - Uitvoering van script inschakelen. <br> [Meer informatie](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Geen vooraf bestaande standaardwebsite <br> - Geen vooraf bestaande website/toepassing die luistert op poort 443 <br>- [Anonieme verificatie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) inschakelen <br> - Instelling [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) inschakelen 
\- FIPS (Federal Information Processing Standard) | FIPS-modus niet inschakelen
|

## <a name="network-requirements"></a>Netwerkvereisten

**Onderdeel** | **Vereiste** 
--- | --- 
Type IP-adres | Statisch 
Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport) 
Type NIC | VMXNET3 (als de configuratieserver een VMware-VM is)
 |
**Internettoegang** (de server moet rechtstreeks of via proxy toegang hebben tot de volgende URL's):|
\*.backup.windowsazure.com | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.blob.core.windows.net | Wordt gebruikt voor toegang tot het opslagaccount waarin de gerepliceerde gegevens worden opgeslagen. U kunt de specifieke URL van uw cache-opslagaccount opgeven.
\*.hypervrecoverymanager.windowsazure.com | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
https:\//login.microsoftonline.com | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer 
time.nist.gov | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren
time.windows.com | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> *.services.visualstudio.com (optioneel) </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF-installatie vereist toegang tot deze aanvullende URL's. Ze worden door Azure Active Directory gebruikt voor toegangsbeheer en identiteitsbeheer.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Voor het voltooien van het downloaden van MySQL. </br> In enkele regio's wordt het downloadproces mogelijk omgeleid naar de CDN-URL. Zorg er zo nodig voor dat de CDN-URL ook is goedgekeurd.
|

> [!NOTE]
> Als er [connectiviteit van particuliere koppelingen](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) naar de Site Recovery-kluis is, hebt u geen extra internettoegang nodig voor de configuratieserver. Een uitzondering hierop bestaat tijdens het instellen van de CS-computer met behulp van de OVA-sjabloon. In dat geval moet u naast de toegang tot de persoonlijke koppeling, toegang hebben tot de volgende URL's: https://management.azure.com, https://www.live.com en https://www.microsoft.com. Als u geen toegang tot deze URL's wilt toestaan, moet u de CS instellen met behulp van Unified Installer.

## <a name="required-software"></a>Vereiste software

**Onderdeel** | **Vereiste** 
--- | ---
VMware vSphere PowerCLI | Niet vereist voor versie 9,14 en hoger
MYSQL | MySQL moet worden geïnstalleerd. U kunt handmatig installeren of dit door Site Recovery laten doen. (Zie [instellingen configureren](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) voor meer informatie)
|

## <a name="sizing-and-capacity-requirements"></a>Vereisten voor grootte en capaciteit

De volgende tabel bevat een overzicht van de capaciteitsvereisten voor de configuratieserver. Als u meerdere virtuele VMware-machines repliceert, raadpleegt u de [overwegingen ten aanzien van de capaciteitsplanning](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) en voert u het [hulpprogramma Azure Site Recovery Deployment Planner](../articles/site-recovery/site-recovery-deployment-planner.md) uit.


**CPU** | **Geheugen** | **Cacheschijf** | **Frequentie van gegevenswijzigingen** | **Gerepliceerde machines**
--- | --- | --- | --- | ---
8 vCPU's<br/><br/> 2 sockets * 4 kernen \@ 2,5 GHz | 16 GB | 300 GB | 500 GB of minder | < 100 computers
12 vCPU's<br/><br/> 2 sockets * 6 kernen \@ 2,5 GHz | 18 GB | 600 GB | 500 GB - 1 TB | 100 - 150 computers
16 vCPU's<br/><br/> 2 sockets * 8 kernen \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 - 200 computers
|