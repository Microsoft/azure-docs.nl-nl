---
title: Ondersteuning voor Hyper-V-migratie in Azure Migrate
description: Meer informatie over ondersteuning voor Hyper-V-migratie met Azure Migrate.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 90da16789344754c02d46022160db71ee261a056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754059"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Ondersteunings matrix voor Hyper-V-migratie

In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het migreren van virtuele Hyper-V-machines met [Azure migrate: Server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) . Als u op zoek bent naar informatie over het evalueren van Hyper-V-Vm's voor migratie naar Azure, raadpleegt u de [ondersteunings matrix voor evaluatie](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Migratiebeperkingen

U kunt Maxi maal 10 Vm's tegelijk selecteren voor replicatie. Als u meer computers wilt migreren, repliceert u in groepen van 10.


## <a name="hyper-v-host-requirements"></a>Vereisten voor de Hyper-V-host

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie**       | De Hyper-V-host kan zelfstandig of in een cluster worden geïmplementeerd. <br/>Azure Migrate replicatie software (Hyper-V-replicatie provider) is geïnstalleerd op de Hyper-V-hosts.|
| **Machtigingen**           | U hebt beheerders machtigingen nodig op de Hyper-V-host. |
| **Host het besturingssysteem** | Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2 met de nieuwste updates. Houd er rekening mee dat Server Core-installatie van deze besturings systemen ook wordt ondersteund. |
| **Andere software vereisten** | .NET Framework 4,7 of hoger |
| **Poort toegang** |  Uitgaande verbindingen op HTTPS-poort 443 voor het verzenden van VM-replicatie gegevens.
| **Vrije schijf ruimte (cache)** |  600 GB |
| **Vrije schijfruimte (bewaarschijf)** |  600 GB |


## <a name="hyper-v-vms"></a>Virtuele Hyper-V-machines

| **Ondersteuning**                  | **Details**               
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](../virtual-machines/linux/endorsed-distros.md) -besturings systemen die worden ondersteund door Azure. |
**Windows Server 2003** | Voor virtuele machines met Windows Server 2003 moet u [Hyper-V-integratie Services installeren](prepare-windows-server-2003-migration.md) voordat u de migratie uitvoert. | 
**Virtuele Linux-machines in azure** | Voor sommige Vm's zijn mogelijk wijzigingen vereist zodat ze kunnen worden uitgevoerd in Azure.<br/><br/> Voor Linux worden de wijzigingen automatisch door Azure Migrate voor deze besturings systemen aangebracht:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8 <br/> Oracle Linux 7.7, 7.7CI<br/> Voor andere besturings systemen voert u de [vereiste wijzigingen](prepare-for-migration.md#verify-required-changes-before-migrating) hand matig uit.
| **Vereiste wijzigingen voor Azure** | Voor sommige Vm's zijn mogelijk wijzigingen vereist zodat ze kunnen worden uitgevoerd in Azure. Pas aanpassingen hand matig aan voordat u de migratie uitvoert. De relevante artikelen bevatten instructies over hoe u dit doet. |
| **Linux-opstart**                 | Als/boot zich op een toegewezen partitie bevindt, moet deze zich op de besturingssysteem schijf bevinden en niet over meerdere schijven worden verspreid.<br/> Als/boot deel uitmaakt van de hoofd partitie (/), moet de partitie '/' zich op de besturingssysteem schijf bevindt en niet over andere schijven beschikken. |
| **UEFI-opstart**                  | Ondersteund. Vm's op basis van UEFI worden gemigreerd naar virtuele machines van Azure Generation 2.  |
| **UEFI-beveiligd opstarten**         | Niet ondersteund voor migratie.|
| **Schijf grootte**                  | 2 TB voor de besturingssysteem schijf (BIOS-opstart), 4 TB voor de besturingssysteem schijf (UEFI-opstart), 4 TB voor de gegevens schijven.|
| **Schijf nummer** | Maxi maal 16 schijven per VM.|
| **Versleutelde schijven/volumes**    | Niet ondersteund voor migratie.|
| **RDM/passthrough-schijven**      | Niet ondersteund voor migratie.|
| **Gedeelde schijf** | Vm's die gebruikmaken van gedeelde schijven, worden niet ondersteund voor migratie.|
| **NFS**                        | NFS-volumes die zijn gekoppeld als volumes op de Vm's, worden niet gerepliceerd.|
| **SCSI**                      | Vm's met iSCSI-doelen worden niet ondersteund voor migratie.
| **Doel schijf**                | U kunt alleen migreren naar Azure-Vm's met Managed disks. |
| **IPv6** | Wordt niet ondersteund.|
| **NIC-koppeling** | Wordt niet ondersteund.|
| **Azure Site Recovery** | U kunt niet repliceren met Azure Migrate server migratie als de virtuele machine is ingeschakeld voor replicatie met Azure Site Recovery.|
| **Poorten** | Uitgaande verbindingen op HTTPS-poort 443 voor het verzenden van VM-replicatie gegevens.|

### <a name="url-access-public-cloud"></a>URL-toegang (open bare Cloud)

De replicatie provider software op de Hyper-V-hosts heeft toegang tot deze Url's nodig.

**URL** | **Details**
--- | ---
login.microsoftonline.com | Toegangs beheer en identiteits beheer met behulp van Active Directory.
backup.windowsazure.com | Replicatie gegevens overdragen en coördinatie.
*.hypervrecoverymanager.windowsazure.com | Gebruikt voor replicatie beheer.
*.blob.core.windows.net | Gegevens uploaden naar opslag accounts. 
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
time.windows.com | Hiermee wordt de tijd synchronisatie tussen systeem en Global time gecontroleerd.

### <a name="url-access-azure-government"></a>URL-toegang (Azure Government)

De replicatie provider software op de Hyper-V-hosts heeft toegang tot deze Url's nodig.

**URL** | **Details**
--- | ---
login.microsoftonline.us | Toegangs beheer en identiteits beheer met behulp van Active Directory.
backup.windowsazure.us | Replicatie gegevens overdragen en coördinatie.
*. hypervrecoverymanager.windowsazure.us | Gebruikt voor replicatie beheer.
*. blob.core.usgovcloudapi.net | Gegevens uploaden naar opslag accounts.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
time.nist.gov | Hiermee wordt de tijd synchronisatie tussen systeem en Global time gecontroleerd.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

Alle on-premises Vm's die naar Azure worden gerepliceerd, moeten voldoen aan de vereisten voor de Azure-VM die in deze tabel worden samenvatten.

**Onderdeel** | **Vereisten** | **Details**
--- | --- | ---
Grootte van de besturingssysteemschijf | Maxi maal 2.048 GB. | De controle is mislukt als dit niet wordt ondersteund.
Aantal besturingssysteemschijven | 1 | De controle is mislukt als dit niet wordt ondersteund.
Aantal gegevensschijven | 16 of minder. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de gegevens schijf | Maxi maal 4.095 GB | De controle is mislukt als dit niet wordt ondersteund.
Netwerkadapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Wordt niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
FC-schijf | Wordt niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
BitLocker | Wordt niet ondersteund. | BitLocker moet worden uitgeschakeld voordat u replicatie voor een machine inschakelt.
VM-naam | Van 1 tot 63 tekens.<br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de computer moet beginnen en eindigen met een letter of cijfer. |  Werk de waarde in de computer eigenschappen in Site Recovery bij.
Verbinding maken na migratie-Windows | Verbinding maken met virtuele Azure-machines na de migratie:<br/><br/> -Schakel voor de migratie RDP in op de on-premises VM. Zorg dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.<br/><br/> -Voor site-naar-site-VPN-toegang schakelt u RDP in en staat u RDP toe in **Windows Firewall**  ->  **toegestane apps en functies** voor **domein-en particuliere** netwerken. Controleer bovendien of het SAN-beleid van het besturings systeem is ingesteld op **OnlineAll**. [Meer informatie](prepare-for-migration.md). |
Verbinding maken na migratie-Linux | Verbinding maken met virtuele Azure-machines na de migratie met SSH:<br/><br/> -Voordat de migratie op de on-premises computer, controleert u of de Secure shell-service is ingesteld op Start en of de firewall regels een SSH-verbinding toestaan.<br/><br/> -Na de migratie kunt u op de virtuele machine van Azure binnenkomende verbindingen met de SSH-poort toestaan voor de regels voor de netwerk beveiligings groep op de virtuele machine waarvoor een failover is uitgevoerd en voor het Azure-subnet waarmee deze is verbonden. Voeg bovendien een openbaar IP-adres voor de virtuele machine toe. |  

## <a name="next-steps"></a>Volgende stappen

[Virtuele Hyper-V-machines migreren](tutorial-migrate-hyper-v.md) voor migratie.
