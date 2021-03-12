---
title: Machines voorbereiden op migratie met Azure Migrate
description: Leer hoe u on-premises machines kunt voorbereiden voor migratie met Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 8083b9edd49f65f29fe9c9b2cfa30edfacf89507
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614884"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>On-premises machines voorbereiden voor migratie naar Azure

In dit artikel wordt beschreven hoe u on-premises machines kunt voorbereiden voordat u ze naar Azure migreert met het hulpprogramma [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool).

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Migratiebeperkingen controleren.
> * Een methode selecteren voor het migreren van VMware-VM's
> * Hypervisor- en besturingssysteemvereisten controleren voor machines die u wilt migreren.
> * URL- en poorttoegang controleren voor machines die u wilt migreren.
> * Wijzigingen bekijken die u mogelijk moet aanbrengen voordat u begint met de migratie.
> * Vereisten voor Azure-VM's voor gemigreerde machines controleren
> * Machines voorbereiden zodat u na migratie verbinding kunt maken met de Azure-VM's.



## <a name="verify-migration-limitations"></a>Migratiebeperkingen verifiëren

De tabel geeft een overzicht van de limieten voor detectie, beoordeling en migratie van Azure Migrate. Wij raden u aan machines te beoordelen voorafgaand aan de migratie, maar u hoeft dit niet te doen.

**Scenario** | **Project** | **Detectie/Evaluatie** | **Migratie**
--- | --- | --- | ---
**VMware-VM's** | Detecteer en evalueer tot 35.000 VM's in één Azure Migrate-project. | Detecteer tot 10.000 VMware-VM's met één [Azure Migrate-apparaat](common-questions-appliance.md) voor VMware. | **Migratie zonder agent**: u kunt vanaf elke vCenter Server gelijktijdig een maximum van 500 vm's repliceren. **Migratie met agents**: u kunt het [replicatieapparaat](migrate-replication-appliance.md) [uitschalen](./agent-based-migration-architecture.md#performance-and-scaling) om grote aantallen VM's te repliceren.<br/><br/> U kunt in de portal maximaal 10 machines tegelijk selecteren voor replicatie. Als u meer machines wilt repliceren, voegt u die toe in batches van 10.
**Virtuele Hyper-V-machines** | Detecteer en evalueer tot 35.000 VM's in één Azure Migrate-project. | Detecteer tot 5.000 Hyper-V-VM's met één Azure Migrate-apparaat | Voor Hyper-V-migratie wordt geen apparaat gebruikt. In plaats daarvan wordt de Hyper-V Replication Provider uitgevoerd op elke Hyper-V-host.<br/><br/> De replicatiecapaciteit wordt beïnvloed door prestatiefactoren zoals VM-verloop en uploadbandbreedte voor de replicatiegegevens.<br/><br/> U kunt in de portal maximaal 10 machines tegelijk selecteren voor replicatie. Als u meer machines wilt repliceren, voegt u die toe in batches van 10.
**Fysieke machines** | Detecteer en evalueer tot 35.000 virtuele machines in één Azure Migrate-project. | Detecteer tot 250 fysieke servers met één Azure Migrate-apparaat voor fysieke servers. | U kunt het [replicatieapparaat](migrate-replication-appliance.md) [uitschalen](./agent-based-migration-architecture.md#performance-and-scaling) om grote aantallen servers te repliceren.<br/><br/> U kunt in de portal maximaal 10 machines tegelijk selecteren voor replicatie. Als u meer machines wilt repliceren, voegt u die toe in batches van 10.

## <a name="select-a-vmware-migration-method"></a>Een VMware-migratiemethode selecteren

Als u VMware-VM's naar Azure migreert, kunt u de migratiemethode met en zonder agents [vergelijken](server-migrate-overview.md#compare-migration-methods) om te beslissen wat voor u het beste werkt.

## <a name="verify-hypervisor-requirements"></a>Hypervisor-vereisten verifiëren

- Verifieer vereisten voor [VMware-migratie zonder agents](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) of [VMware-migratie met agents](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based).
- Verifieer [Hyper-V-host](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)-vereisten.


## <a name="verify-operating-system-requirements"></a>Besturingssysteemvereisten verifiëren

Ondersteunde besturingssystemen voor migratie verifiëren:

- Als u VMware-VM's of Hyper-V-VM's migreert, verifieer dan VMware-VM-vereisten voor migratie [zonder agents](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) en [met agents](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based), en vereisten voor [Hyper-V-VM's](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms).
- Verifieer dat [Windows-besturingssystemen](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) worden ondersteund in Azure.
- Verifieer in Azure ondersteunde [Linux-distributies](../virtual-machines/linux/endorsed-distros.md).

## <a name="review-url-and-port-access"></a>URL- en poorttoegang controleren

Controleer welke URL's en poorten worden gebruikt tijdens de migratie.

**Scenario** | **Details** |  **URL's** | **Poorten**
--- | --- | --- | ---
**VMware-migratie zonder agents** | Gebruikt het [Azure Migrate-apparaat](migrate-appliance-architecture.md) voor migratie. Er wordt niets geïnstalleerd op VMware-VM's. | Controleer de openbare cloud- en overheids-[URL's](migrate-appliance.md#url-access) die nodig zijn voor de detectie, beoordeling en migratie met het apparaat. | [Controleer](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) de poortvereisten voor migratie zonder agents.
**VMware-migratie met agents** | Gebruikt het [replicatieapparaat](migrate-replication-appliance.md) voor migratie. De Mobility-serviceagent wordt geïnstalleerd op VM's. | Controleer de [openbare cloud](migrate-replication-appliance.md#url-access)- en [Azure Government](migrate-replication-appliance.md#azure-government-url-access)-URL's waartoe het replicatieapparaat toegang moet hebben. | [Controleer](migrate-replication-appliance.md#port-access) de poorten die worden gebruikt tijdens migratie met agents.
**Hyper-V-migratie** | Gebruikt een op Hyper-V-hosts geïnstalleerde provider voor migratie. Er wordt niets geïnstalleerd op Hyper-V-VM's. | Controleer de [openbare cloud](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud)- en [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government)-URL's waartoe de replicatieprovider die wordt uitgevoerd op de hosts toegang moet hebben. | De replicatieprovider op de Hyper-V-host gebruikt uitgaande verbindingen op HTTPS-poort 443 om VM-replicatiegegevens te verzenden.
**Fysieke machines** | Gebruikt het [replicatieapparaat](migrate-replication-appliance.md) voor migratie. De Mobility-serviceagent wordt geïnstalleerd op de fysieke machines. | Controleer de [openbare cloud](migrate-replication-appliance.md#url-access)- en [Azure Government](migrate-replication-appliance.md#azure-government-url-access)-URL's waartoe het replicatieapparaat toegang moet hebben. | [Controleer](migrate-replication-appliance.md#port-access) de poorten die worden gebruikt tijdens fysieke migratie.

## <a name="verify-required-changes-before-migrating"></a>Vereiste wijzigingen voor migratie verifiëren

U moet enkele wijzigingen doorvoeren aan virtuele machines voordat u ze naar Azure migreert.

- Bij sommige besturingssystemen brengt Azure Migrate automatisch wijzigingen aan tijdens het replicatie-/migratieproces.
- Voor andere besturingssystemen moet u handmatig instellingen configureren.
- Het is belangrijk om de instellingen handmatig te configureren voordat u begint met de migratie. Als u de VM migreert voordat u de wijzigingen doorvoert, start de VM mogelijk niet op in Azure.

Bekijk de tabellen om te zien welke wijzigingen u moet aanbrengen.

### <a name="windows-machines"></a>Windows-machines

Vereiste wijzigingen worden samengevat in de tabel.

**Actie** | **VMware (migratie zonder agents)** | **VMware (met agents)/fysieke machines** | **Windows op Hyper-V** 
--- | --- | --- | ---
**Het SAN-beleid configureren als Alles online**<br/><br/> Dit zorgt ervoor dat Windows-volumes in de Azure-VM dezelfde stationsletters krijgen toegewezen als de on-premises VM. | Wordt automatisch ingesteld voor machines met Windows Server 2008 R2 of later.<br/><br/> Moet handmatig worden geconfigureerd voor eerdere besturingssystemen. | Wordt in de meeste gevallen automatisch ingesteld. | Configureer handmatig.
**Hyper-V Guest Integration installeren** | [Installeer handmatig](prepare-windows-server-2003-migration.md#install-on-vmware-vms) op machines met Windows Server 2003. | [Installeer handmatig](prepare-windows-server-2003-migration.md#install-on-vmware-vms) op machines met Windows Server 2003. | [Installeer handmatig](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) op machines met Windows Server 2003.
**Schakel Azure Serial Console voor Linux in**.<br/><br/>[Schakel de console in](../virtual-machines/troubleshooting/serial-console-windows.md) op Azure VM's om te helpen bij het oplossen van problemen. U hoeft de VM niet opnieuw te starten. De Azure-VM wordt opgestart met behulp van de installatiekopie van de schijf. Het opstarten van de installatiekopie is equivalent aan het opnieuw opstarten van de nieuwe VM. | Handmatig inschakelen | Handmatig inschakelen | Handmatig inschakelen
**Verbinding maken na de migratie**<br/><br/> Om verbinding te maken na de migratie moet u vóór de migratie enkele stappen uitvoeren. | Handmatig [instellen](#prepare-to-connect-to-azure-windows-vms). | Handmatig [instellen](#prepare-to-connect-to-azure-windows-vms). | Handmatig [instellen](#prepare-to-connect-to-azure-windows-vms).


#### <a name="configure-san-policy"></a>SAN-beleid configureren

Standaard krijgen Azure-VM's station D toegewezen om te gebruiken als tijdelijke opslag.

- Door deze stationstoewijzing worden alle andere gekoppelde opslagstationstoewijzingen een letter opgeschoven.
- Als uw on-premises installatie bijvoorbeeld een gegevensschijf gebruikt die is toegewezen aan station D voor applicatie-installaties, wordt de toewijzing voor dit station E nadat u de VM naar Azure migreert. 
- Om deze automatische toewijzing te voorkomen en ervoor te zorgen dat Azure de volgende vrije stationsletter toewijst aan het tijdelijke volume, stelt u het SAN-beleid (Storage Area Network) in op **OnlineAll**:

Configureer deze instelling als volgt handmatig:

1. Open op de on-premises machine (niet op de hostserver) een opdrachtprompt met verhoogde bevoegdheid.
2. Voer **diskpart** in.
3. Voer **SAN** in. Als de stationsletter van het gastbesturingssysteem niet wordt onderhouden, wordt **Alles offline** of **Offline gedeeld** geretourneerd.
4. Voer achter de **DISKPART**-prompt **SAN Policy=OnlineAll** in. Deze instelling zorgt ervoor dat schijven online worden gezet, en dat u van en naar beide schijven kunt lezen en schrijven.
5. Tijdens de testmigratie kunt u controleren of de stationsletters behouden blijven.


### <a name="linux-machines"></a>Linux-machines

Azure Migrate voert deze acties automatisch uit voor deze versies

- Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x (de Azure Linux VM-agent wordt ook automatisch geïnstalleerd tijdens de migratie)
- Cent OS 7,7, 7,6, 7,5, 7,4, 6. x (Azure Linux VM-agent wordt ook automatisch geïnstalleerd tijdens de migratie)
- SUSE Linux Enterprise Server 12 SP1+
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19,04, 19,10, 18.04 LTS, 16.04 LTS, 14.04 LTS (de Azure Linux VM-agent wordt ook automatisch geïnstalleerd tijdens de migratie)
- Ubuntu 18.04 LTS, 16.04 LTS
- Debian 9, 8, 7
- Oracle Linux 7.7, 7.7CI

Bereid voor andere versies de machines voor zoals samengevat in de tabel.  


**Actie** | **Details** | **Linux-versie**
--- | --- | ---
**Hyper-V Linux Integration Services installeren** | Bouw de Linux-initialisatie-installatiekopie opnieuw op zodat deze de benodigde Hyper-V-stuurprogramma's bevat. Door de initialisatie-installatiekopie opnieuw op te bouwen, zorgt u ervoor dat de VM in Azure kan worden opgestart. | In meeste nieuwe versies van Linux-distributies is dit standaard opgenomen.<br/><br/> Als het niet is opgenomen, moet u het handmatig installeren voor alle versies behalve de hierboven genoemde.
**Azure Serial Console-logboekregistratie inschakelen** | Het inschakelen van consolelogboekregistratie helpt u bij het oplossen van problemen. U hoeft de VM niet opnieuw te starten. De Azure-VM wordt opgestart met behulp van de installatiekopie van de schijf. Het opstarten van de installatiekopie is equivalent aan het opnieuw opstarten van de nieuwe VM.<br/><br/> Volg [deze instructies](../virtual-machines/troubleshooting/serial-console-linux.md) om het in te schakelen.
**Apparaattoewijzingsbestand bijwerken** | Werk het apparaattoewijzingsbestand bij met de koppelingen van apparaatnaam en volume, zodat u persistente apparaat-id's gebruikt. | Installeer handmatig voor alle versies behalve de hierboven genoemde. (Alleen van toepassing in VMware-scenario's op basis van een agent)
**fstab-items bijwerken** |  Werk items bij om persistente volume-id's te gebruiken.    | Werk handmatig bij voor alle versies behalve de hierboven genoemde.
**udev-regel verwijderen** | Verwijder alle udev-regels die interfacenamen reserveren op basis van MAC-adres enzovoort. | Verwijder handmatig voor alle versies behalve de hierboven genoemde.
**Netwerkinterfaces bijwerken** | Werk netwerkinterfaces bij zodat ze IP-adressen ontvangen op basis van DHCP.nst | Werk handmatig bij voor alle versies behalve de hierboven genoemde.
**SSH inschakelen** | Zorg ervoor dat SSH is ingeschakeld en dat de SSHD-service is ingesteld om automatisch te worden gestart bij opnieuw opstarten.<br/><br/> Zorg ervoor dat inkomende SSH-verbindingsaanvragen niet worden geblokkeerd door de firewall van het besturingssysteem of door scriptregels.| Schakel handmatig in voor alle versies behalve de hierboven genoemde.

De volgende tabel bevat een samenvatting van de stappen die automatisch worden uitgevoerd voor de hierboven vermelde besturingssystemen.


| Bewerking                                      | VMware\-migratie met agents | VMware-migratie zonder agents | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Hyper\-V Linux Integration Services installeren | Ja                           | Ja                        | Niet nodig |
| Azure Serial Console-logboekregistratie inschakelen         | Ja                           | Ja                        | Nee         |
| Apparaattoewijzingsbestand bijwerken                      | Ja                           | Nee                         | Nee         |
| fstab-items bijwerken                        | Ja                           | Ja                        | Nee         |
| udev-regel verwijderen                            | Ja                           | Ja                        | Nee         |
| Netwerkinterfaces bijwerken                   | Ja                           | Ja                        | Nee         |
| SSH inschakelen                                  | Nee                            | Nee                         | Nee         |

Lees meer over stappen voor het [uitvoeren van een Linux-VM op Azure](../virtual-machines/linux/create-upload-generic.md) en instructies voor enkele populaire Linux-distributies.

Bekijk de lijst met [vereiste pakketten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#requirements) om de Linux VM-agent te installeren. Azure Migrate installeert de Linux VM-agent automatisch voor RHEL6, RHEL7, CentOS7 (6 moet worden ondersteund, vergelijkbaar met RHEL), Ubuntu 14,04, Ubuntu 16,04, Ubuntu 18.04 bij het gebruik van de agentloze methode van VMware-migratie.

## <a name="check-azure-vm-requirements"></a>Vereisten voor Azure-VM's controleren

On-premises machines die u naar Azure repliceert moeten voldoen aan de Azure-VM-vereisten voor het besturingssysteem en de architectuur, de schijven, netwerkinstellingen en VM-namen.

Controleer voordat u migreert de Azure-VM-vereisten voor migratie van [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) en [fysieke servers](migrate-support-matrix-physical-migration.md#azure-vm-requirements).



## <a name="prepare-to-connect-after-migration"></a>Verbinding maken na de migratie voorbereiden

Bij de migratie naar Azure worden Azure-VM's gemaakt. Na de migratie moet u verbinding kunnen maken met de nieuwe Azure-VM's. Er zijn meerdere stappen nodig om met succes verbinding te maken.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Voorbereiden op het verbinden met Azure Windows-VM's

Op on-premises Windows-machines:

1. Configureer Windows-instellingen. De instellingen omvatten het verwijderen van statische persistente routes of WinHTTP-proxy's.
2. Zorg ervoor dat [vereiste services](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) worden uitgevoerd.
3. Schakel extern bureaublad (RDP) in om externe verbindingen met de on-premises machine mogelijk te maken. Leer [PowerShell gebruiken om RDP in te schakelen](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Om na de migratie via het internet toegang te krijgen tot een Azure-VM, moet u in Windows Firewall op de on-premises machine TCP en UDP toestaan in het Openbare profiel en RDP instellen als toegestane app voor alle profielen.
5. Als u na de migratie toegang wilt krijgen tot een Azure-VM via een site-to-site VPN, staat u in Windows Firewall op de on-premises machine RDP toe voor de Domein- en Privé-profielen. Leer [RDP-verkeer toestaan](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Zorg ervoor dat er geen Windows-updates in behandeling zijn op de on-premises VM wanneer u migreert. Als die er zijn, kunnen er na de migratie updates worden geïnstalleerd op de Azure VM en kunt u zich pas op de VM aanmelden als de updates zijn voltooid.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Voorbereiden op het verbinden met Linux Azure-VM's

Op on-premises Linux-machines:

1. Controleer of de Secure Shell-service zo is ingesteld dat deze automatisch wordt gestart bij het opstarten van het systeem.
2. Controleer of SSH-verbindingen zijn toegestaan door de firewallregels.

### <a name="configure-azure-vms-after-migration"></a>Azure-VM's configureren na migratie

Voer na de migratie de volgende stappen uit op de gemaakte Azure-VM's:

1. Wijs een openbaar IP-adres toe aan de VM om verbinding te maken met de VM via internet. U moet een ander openbaar IP-adres voor de Azure-VM gebruiken dan het adres dat u gebruikte voor uw on-premises machine. [Meer informatie](../virtual-network/virtual-network-public-ip-address.md).
2. Controleer of de regels van de netwerkbeveiligingsgroep (NSG) op de VM inkomende verbindingen naar de RDP- of SSH-poort toestaan.
3. Controleer [diagnostische gegevens over opstarten](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) om de VM te bekijken.


## <a name="next-steps"></a>Volgende stappen

Bepaal welke methode u wilt gebruiken voor het [migreren van VMware-VM's](server-migrate-overview.md) naar Azure, of begin met het migreren van [Hyper-V-VM's](tutorial-migrate-hyper-v.md) of [fysieke servers of gevirtualiseerde of cloud-VM's](tutorial-migrate-physical-virtual-machines.md).

## <a name="see-whats-supported"></a>Wat er wordt ondersteund

Voor VMware-VM's ondersteunt Server Migration [migratie met of zonder agents](server-migrate-overview.md).

- **VMware-VM's**: verifieer [migratievereisten en ondersteuning](migrate-support-matrix-vmware-migration.md) voor VMware-VM's.
- **Hyper-V-VM's**: verifieer [migratievereisten en ondersteuning](migrate-support-matrix-hyper-v-migration.md) voor Hyper-V-VM's.
- **Fysieke machines**: verifieer [migratievereisten en ondersteuning](migrate-support-matrix-physical-migration.md) voor on-premises fysieke machines en andere gevirtualiseerde servers. 
