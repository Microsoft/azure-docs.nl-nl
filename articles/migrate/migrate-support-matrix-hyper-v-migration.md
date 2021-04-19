---
title: Ondersteuning voor Hyper-V-migratie in Azure Migrate
description: Meer informatie over ondersteuning voor Hyper-V-migratie met Azure Migrate.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 33e34e777a78e1c609d2eacdcb501c0bce1f5c9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714916"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Ondersteuningsmatrix voor Hyper-V-migratie

In dit artikel worden de ondersteuningsinstellingen en beperkingen voor het migreren van Hyper-V-VM's met [Azure Migrate: Server Migration samengevat.](migrate-services-overview.md#azure-migrate-server-migration-tool) Als u op zoek bent naar informatie over het evalueren van Hyper-V-VM's voor migratie naar Azure, bekijkt u de [matrix voor ondersteuning van evaluaties.](migrate-support-matrix-hyper-v.md)

## <a name="migration-limitations"></a>Migratiebeperkingen

U kunt maximaal 10 VM's tegelijk selecteren voor replicatie. Als u meer machines wilt migreren, repliceert u in groepen van 10.


## <a name="hyper-v-host-requirements"></a>Vereisten voor Hyper-V-host

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie**       | De Hyper-V-host kan zelfstandig zijn of worden geïmplementeerd in een cluster. <br/>Azure Migrate replicatiesoftware (Hyper-V-replicatieprovider) is geïnstalleerd op de Hyper-V-hosts.|
| **Machtigingen**           | U hebt beheerdersmachtigingen nodig op de Hyper-V-host. |
| **Host het besturingssysteem** | Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2 met de meest recente updates. Houd er rekening mee dat serverkerninstallatie van deze besturingssystemen ook wordt ondersteund. |
| **Overige softwarevereisten** | .NET Framework 4.7 of hoger |
| **Poorttoegang** |  Uitgaande verbindingen op HTTPS-poort 443 voor het verzenden van VM-replicatiegegevens.
| **Vrije schijfruimte (cache)** |  600 GB |
| **Vrije schijfruimte (bewaarschijf)** |  600 GB |


## <a name="hyper-v-vms"></a>Virtuele Hyper-V-machines

| **Ondersteuning**                  | **Details**               
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) [en Linux-besturingssystemen](../virtual-machines/linux/endorsed-distros.md) die worden ondersteund door Azure. |
**Windows Server 2003** | Voor VM's met Windows Server 2003 moet u [Hyper-V Integration Services installeren](prepare-windows-server-2003-migration.md) vóór de migratie. | 
**Linux-VM's in Azure** | Voor sommige VM's zijn mogelijk wijzigingen vereist, zodat ze kunnen worden uitgevoerd in Azure.<br/><br/> Voor Linux worden Azure Migrate wijzigingen automatisch aangebracht voor deze besturingssystemen:<br/> - Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x<br/> - Cent OS 7.7, 7.6, 7.5, 7.4, 6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8 <br/> Oracle Linux 7.7, 7.7CI<br/> Voor andere besturingssystemen moet u de [vereiste wijzigingen](prepare-for-migration.md#verify-required-changes-before-migrating) handmatig aanbrengen.
| **Vereiste wijzigingen voor Azure** | Voor sommige VM's zijn mogelijk wijzigingen vereist, zodat ze kunnen worden uitgevoerd in Azure. Maak handmatig wijzigingen aan vóór de migratie. De relevante artikelen bevatten instructies over hoe u dit doet. |
| **Linux opstarten**                 | Als /boot zich op een toegewezen partitie bevindt, moet deze zich op de besturingssysteemschijf bevinden en niet worden verdeeld over meerdere schijven.<br/> Als /boot deel uitmaakt van de hoofdpartitie (/), moet de partitie '/' zich op de besturingssysteemschijf en geen andere schijven omspannen. |
| **UEFI opstarten**                  | Ondersteund. UEFI-VM's worden gemigreerd naar azure-VM's van de tweede generatie.  |
| **UEFI - Beveiligd opstarten**         | Niet ondersteund voor migratie.|
| **Schijfgrootte**                  | 2 TB voor de besturingssysteemschijf (BIOS-opstart), 4 TB voor de besturingssysteemschijf (UEFI opstarten), 4 TB voor de gegevensschijven.|
| **Schijfnummer** | Maximaal 16 schijven per VM.|
| **Versleutelde schijven/volumes**    | Niet ondersteund voor migratie.|
| **RDM-/passthrough-schijven**      | Niet ondersteund voor migratie.|
| **Gedeelde schijf** | VM's die gedeelde schijven gebruiken, worden niet ondersteund voor migratie.|
| **NFS**                        | NFS-volumes die als volumes op de VM's zijn bevestigd, worden niet gerepliceerd.|
| **Iscsi**                      | VM's met iSCSI-doelen worden niet ondersteund voor migratie.
| **Doelschijf**                | U kunt alleen met beheerde schijven migreren naar Azure-VM's. |
| **IPv6** | Wordt niet ondersteund.|
| **NIC-teams** | Wordt niet ondersteund.|
| **Azure Site Recovery** | U kunt niet repliceren met Azure Migrate Server Migration als de VM is ingeschakeld voor replicatie met Azure Site Recovery.|
| **Poorten** | Uitgaande verbindingen op HTTPS-poort 443 voor het verzenden van VM-replicatiegegevens.|

### <a name="url-access-public-cloud"></a>URL-toegang (openbare cloud)

De software van de replicatieprovider op de Hyper-V-hosts heeft toegang tot deze URL's nodig.

**URL** | **Details**
--- | ---
login.microsoftonline.com | Toegangsbeheer en identiteitsbeheer met Active Directory.
backup.windowsazure.com | Overdracht en coördinatie van replicatiegegevens.
*.hypervrecoverymanager.windowsazure.com | Wordt gebruikt voor replicatiebeheer.
*.blob.core.windows.net | Gegevens uploaden naar opslagaccounts. 
dc.services.visualstudio.com | Upload app-logboeken die worden gebruikt voor interne bewaking.
time.windows.com | Controleert de tijdsynchronisatie tussen systeemtijd en globale tijd.

### <a name="url-access-azure-government"></a>URL-toegang (Azure Government)

De software van de replicatieprovider op de Hyper-V-hosts heeft toegang tot deze URL's nodig.

**URL** | **Details**
--- | ---
login.microsoftonline.us | Toegangsbeheer en identiteitsbeheer met Active Directory.
backup.windowsazure.us | Overdracht en coördinatie van replicatiegegevens.
*.hypervrecoverymanager.windowsazure.us | Wordt gebruikt voor replicatiebeheer.
*.blob.core.usgovcloudapi.net | Gegevens uploaden naar opslagaccounts.
dc.services.visualstudio.com | Upload app-logboeken die worden gebruikt voor interne bewaking.
time.nist.gov | Controleert de tijdsynchronisatie tussen systeemtijd en globale tijd.   

>[!Note]
>
> Als u een project migreert met een privé-eindpuntverbinding, heeft de replicatieprovidersoftware op de Hyper-V-hosts toegang nodig tot deze URL's voor ondersteuning van private link. 
> - *.blob.core.windows.com: voor toegang tot het opslagaccount waar gerepliceerde gegevens worden opgeslagen. Dit is optioneel en is niet vereist als aan het opslagaccount een privé-eindpunt is gekoppeld. 
> - login.windows.net voor toegangsbeheer en identiteitsbeheer met Active Directory.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

Alle on-premises VM's die naar Azure worden gerepliceerd, moeten voldoen aan de Azure VM-vereisten die in deze tabel worden samengevat.

**Onderdeel** | **Vereisten** | **Details**
--- | --- | ---
Grootte van de besturingssysteemschijf | Maximaal 2048 GB. | Controleren mislukt als dit niet wordt ondersteund.
Aantal besturingssysteemschijven | 1 | Controleren mislukt als dit niet wordt ondersteund.
Aantal gegevensschijven | 16 of minder. | Controleren mislukt als dit niet wordt ondersteund.
Grootte van gegevensschijf | Maximaal 4095 GB | Controleren mislukt als dit niet wordt ondersteund.
Netwerkadapters | Er worden meerdere adapters ondersteund. |
Gedeelde VHD | Wordt niet ondersteund. | Controleer mislukt als dit niet wordt ondersteund.
FC-schijf | Wordt niet ondersteund. | Controleer mislukt als dit niet wordt ondersteund.
BitLocker | Wordt niet ondersteund. | BitLocker moet worden uitgeschakeld voordat u replicatie voor een machine inschakelen.
VM-naam | Van 1 tot 63 tekens.<br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De computernaam moet beginnen en eindigen met een letter of cijfer. |  Werk de waarde in de computereigenschappen in Site Recovery.
Verbinding maken na migratie-Windows | Verbinding maken met Azure-VM's met Windows na de migratie:<br/><br/> - Schakel Vóór de migratie RDP in op de on-premises VM. Zorg dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.<br/><br/> - Voor site-naar-site-VPN-toegang moet u RDP inschakelen en RDP toestaan in **toegestane Windows Firewall-apps** en -functies  ->   voor **domein- en privénetwerken.** Controleer bovendien of het SAN-beleid van het besturingssysteem is ingesteld **op OnlineAll.** [Meer informatie](prepare-for-migration.md). |
Verbinding maken na migratie -Linux | Verbinding maken met azure-VM's na de migratie met behulp van SSH:<br/><br/> - Controleer vóór de migratie op de on-premises computer of de Secure Shell-service is ingesteld op Start en of firewallregels een SSH-verbinding toestaan.<br/><br/> - Sta na de migratie op de Azure-VM binnenkomende verbindingen toe naar de SSH-poort voor de netwerkbeveiligingsgroepsregels op de virtuele machine waarvoor een failed over is uitgevoerd en voor het Azure-subnet waarmee deze is verbonden. Voeg daarnaast een openbaar IP-adres toe voor de VM. |  

## <a name="next-steps"></a>Volgende stappen

[Hyper-V-VM's migreren](tutorial-migrate-hyper-v.md) voor migratie.
