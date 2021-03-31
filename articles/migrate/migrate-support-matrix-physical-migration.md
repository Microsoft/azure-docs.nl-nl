---
title: Ondersteuning voor de migratie van fysieke servers in Azure Migrate
description: Meer informatie over ondersteuning voor de migratie van fysieke servers in Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: 4ebc363f29ed8956d10e91f41be1d257cbcc492f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98703883"
---
# <a name="support-matrix-for-physical-server-migration"></a>Ondersteunings matrix voor de migratie van fysieke servers

In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het migreren van fysieke servers naar Azure met [Azure migrate: Server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) . Als u informatie zoekt over het beoordelen van fysieke servers voor migratie naar Azure, raadpleegt u de [ondersteunings matrix voor evaluatie](migrate-support-matrix-physical.md).

## <a name="migrating-machines-as-physical"></a>Computers als fysiek migreren

U kunt on-premises machines als fysieke servers migreren met behulp van replicatie op basis van een agent. Met dit hulp programma kunt u een breed scala aan machines migreren naar Azure:

- On-premises fysieke servers.
- Vm's gevirtualiseerd door platforms zoals xen, KVM.
- Virtuele Hyper-V-machines of virtuele VMware-machines als u de standaard [hyper-v-](tutorial-migrate-hyper-v.md) of [VMware](server-migrate-overview.md) -stromen niet wilt gebruiken.
- Vm's die in persoonlijke Clouds worden uitgevoerd.
- Vm's die worden uitgevoerd in open bare Clouds, waaronder Amazon Web Services (AWS) of Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Migratiebeperkingen

U kunt Maxi maal 10 computers tegelijk selecteren voor replicatie. Als u meer machines wilt migreren, repliceert u in groepen van 10.


## <a name="physical-server-requirements"></a>Vereisten voor fysieke servers

De tabel bevat een overzicht van de ondersteuning voor fysieke servers die u wilt migreren met behulp van migratie op basis van een agent.

**Ondersteuning** | **Details**
--- | ---
**Machine workload** | Azure Migrate ondersteunt de migratie van elke werk belasting (bijvoorbeeld Active Directory, SQL Server, enzovoort) die wordt uitgevoerd op een ondersteunde computer.
**Besturingssystemen** | Raadpleeg de ondersteuning van het [besturings systeem](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) voor site Recovery voor de meest recente informatie. Azure Migrate biedt dezelfde ondersteuning voor het besturings systeem.
**Linux-bestands systeem/gast opslag** | Raadpleeg de ondersteuning van het Linux- [Bestands systeem](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) voor site Recovery voor de meest recente informatie. Azure Migrate biedt identieke ondersteuning voor het Linux-bestands systeem.
**Netwerk/opslag** | Raadpleeg de vereisten voor het [netwerk](../site-recovery/vmware-physical-azure-support-matrix.md#network) en de [opslag](../site-recovery/vmware-physical-azure-support-matrix.md#storage) voor site Recovery voor de meest recente informatie. Azure Migrate biedt identieke netwerk-en opslag vereisten.
**Azure-vereisten** | Bekijk de vereisten voor het Azure- [netwerk](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), de [opslag](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)en de [reken kracht](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) voor site Recovery voor de meest recente informatie. Azure Migrate heeft identieke vereisten voor de migratie van fysieke servers.
**Mobility-service** | De Mobility Service-agent moet zijn geïnstalleerd op elke computer die u wilt migreren.
**UEFI-opstart** | Ondersteund. UEFI-machines worden gemigreerd naar virtuele machines van Azure Generation 2.  <br/><br/> De besturingssysteem schijf moet Maxi maal vier partities hebben en volumes moeten worden geformatteerd met NTFS.
**UEFI-beveiligd opstarten**         | Niet ondersteund voor migratie.
**Doel schijf** | Machines kunnen alleen worden gemigreerd naar Managed disks (standaard HDD, standaard SSD, Premium SSD) in Azure.
**Schijf grootte** | 2 TB besturingssysteem schijf; 32 TB voor gegevens schijven.
**Schijf limieten** |  Maxi maal 63 schijven per computer.
**Versleutelde schijven/volumes** |  Machines met versleutelde schijven/volumes worden niet ondersteund voor migratie.
**Gedeeld schijf cluster** | Wordt niet ondersteund.
**Onafhankelijke schijven** | Ondersteund.
**Passthrough-schijven** | Ondersteund.
**NFS** | NFS-volumes die zijn gekoppeld als volumes op de computers, worden niet gerepliceerd.
**iSCSI-doelen** | Computers met iSCSI-doelen worden niet ondersteund voor migratie zonder agent.
**Multipath IO** | Wordt niet ondersteund.
**Team kaarten** | Wordt niet ondersteund.
**IPv6** | Wordt niet ondersteund.



## <a name="replication-appliance-requirements"></a>Replicatie-apparaatvereisten

Als u het replicatie apparaat hand matig instelt op een fysieke server, moet u ervoor zorgen dat het voldoet aan de vereisten in de tabel. Wanneer u het Azure Migrate replicatie apparaat instelt als een VMware-VM met behulp van de eicellen-sjabloon die is opgegeven in de Azure Migrate hub, wordt het apparaat ingesteld met Windows Server 2016 en voldoet aan de ondersteunings vereisten. 

- Meer informatie over [vereisten voor replicatie apparaten](migrate-replication-appliance.md#appliance-requirements).
- MySQL moet op het apparaat zijn geïnstalleerd. Meer informatie over [installatie opties](migrate-replication-appliance.md#mysql-installation).
- Meer informatie over [url's](migrate-replication-appliance.md#url-access) waarvoor het replicatie apparaat toegang moet hebben.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

Alle on-premises Vm's die naar Azure worden gerepliceerd, moeten voldoen aan de vereisten voor de Azure-VM die in deze tabel worden samenvatten. Wanneer Site Recovery een controle op vereisten uitvoert voor replicatie, mislukt de controle als aan sommige vereisten niet wordt voldaan.

**Onderdeel** | **Vereisten** | **Details**
--- | --- | ---
Gastbesturingssysteem | Controleert ondersteunde besturings systemen.<br/> U kunt elke werk belasting migreren die wordt uitgevoerd op een ondersteund besturings systeem. | De controle is mislukt als dit niet wordt ondersteund.
Architectuur van gast besturingssysteem | 64-bits. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de besturingssysteemschijf | Maxi maal 2.048 GB. | De controle is mislukt als dit niet wordt ondersteund.
Aantal besturingssysteemschijven | 1 | De controle is mislukt als dit niet wordt ondersteund.
Aantal gegevensschijven | 64 of minder. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de gegevens schijf | Tot 32 TB | De controle is mislukt als dit niet wordt ondersteund.
Netwerkadapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Wordt niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
FC-schijf | Wordt niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
BitLocker | Wordt niet ondersteund. | BitLocker moet worden uitgeschakeld voordat u replicatie voor een machine inschakelt.
VM-naam | Van 1 tot 63 tekens.<br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de computer moet beginnen en eindigen met een letter of cijfer. |  Werk de waarde in de computer eigenschappen in Site Recovery bij.
Verbinding maken na migratie-Windows | Verbinding maken met virtuele Azure-machines na de migratie:<br/> -Voordat de migratie RDP op de on-premises VM maakt. Zorg dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.<br/> Schakel voor site-naar-site-VPN-toegang RDP in en sta RDP toe in **Windows Firewall**  ->  **toegestane apps en functies** voor **domein-en particuliere** netwerken. Controleer bovendien of het SAN-beleid van het besturings systeem is ingesteld op **OnlineAll**. [Meer informatie](prepare-for-migration.md). |
Verbinding maken na migratie-Linux | Verbinding maken met virtuele Azure-machines na de migratie met SSH:<br/> Controleer voordat u de migratie op de on-premises computer controleert of de Secure shell-service is ingesteld op Start en of de firewall regels een SSH-verbinding toestaan.<br/> Nadat de failover is uitgevoerd op de Azure-VM, moet u binnenkomende verbindingen met de SSH-poort toestaan voor de regels voor de netwerk beveiligings groep op de virtuele machine die is mislukt en voor het Azure-subnet waarmee deze is verbonden. Voeg bovendien een openbaar IP-adres voor de virtuele machine toe. |  


## <a name="next-steps"></a>Volgende stappen

Fysieke servers [migreren](tutorial-migrate-physical-virtual-machines.md) .
