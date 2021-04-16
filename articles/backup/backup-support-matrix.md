---
title: Ondersteuningsmatrix voor Azure Backup
description: Bevat een samenvatting van ondersteuningsinstellingen en -beperkingen voor de Azure Backup-service.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5c74a34efe8075ab7a34fab4570d9513900b3f81
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517416"
---
# <a name="support-matrix-for-azure-backup"></a>Ondersteuningsmatrix voor Azure Backup

U kunt deze [Azure Backup](backup-overview.md) om een back-up te maken van gegevens naar Microsoft Azure cloudplatform. In dit artikel worden de algemene ondersteuningsinstellingen en -beperkingen voor Azure Backup en implementaties samengevat.

Er zijn andere ondersteunings matrices beschikbaar:

- Ondersteuningsmatrix voor [back-ups van virtuele Azure-machines (VM's)](backup-support-matrix-iaas.md)
- Ondersteuningsmatrix voor back-up [met behulp System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Ondersteuningsmatrix voor back-up met behulp [van de MARS-agent (Microsoft Azure Recovery Services)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Ondersteuning voor kluizen

Azure Backup maakt gebruik van Recovery Services-kluizen om back-ups te beheren voor de volgende workloadtypen: Azure-VM's, SQL in Azure-VM's, SAP HANA in Azure-VM's, Azure-bestands shares en on-premises workloads met behulp van Azure Backup Agent, Azure Backup Server en System Center DPM. Er wordt ook gebruikgemaakt van Recovery Services-kluizen om back-upgegevens voor deze workloads op te slaan.

In de volgende tabel worden de functies van Recovery Services-kluizen beschreven:

**Functie** | **Details**
--- | ---
**Kluizen in abonnement** | Maximaal 500 Recovery Services-kluizen in één abonnement.
**Machines in een kluis** | Maximaal 2000 gegevensbrons voor alle workloads (zoals Azure-VM's, SQL Server-VM' en MABS-servers) kunnen worden beveiligd in één kluis.<br><br>Maximaal 1000 azure-VM's in één kluis.<br/><br/> Er kunnen maximaal 50 MABS-servers worden geregistreerd in één kluis.
**Gegevensbronnen** | De maximale grootte van een [afzonderlijke gegevensbron](./backup-azure-backup-faq.yml#how-is-the-data-source-size-determined-) is 54.400 GB. Deze limiet geldt niet voor back-ups van azure-VM's. Er zijn geen limieten van toepassing op de totale hoeveelheid gegevens waar u een back-up van kunt maken in de kluis.
**Back-ups naar kluis** | **Azure-VM's:** Eenmaal per dag.<br/><br/>**Machines die worden beveiligd door DPM/MABS:** Twee keer per dag.<br/><br/> **Er wordt rechtstreeks een back-up van machines gemaakt met behulp van de MARS-agent:** Drie keer per dag.
**Back-ups tussen kluizen** | Back-up is binnen een regio.<br/><br/> U hebt een kluis nodig in elke Azure-regio met VM's waar u een back-up van wilt maken. U kunt geen back-up maken naar een andere regio.
**Kluizen verplaatsen** | U kunt [kluizen verplaatsen tussen](./backup-azure-move-recovery-services-vault.md) abonnementen of tussen resourcegroepen in hetzelfde abonnement. Het verplaatsen van kluizen tussen regio's wordt echter niet ondersteund.
**Gegevens verplaatsen tussen kluizen** | Het verplaatsen van back-upgegevens tussen kluizen wordt niet ondersteund.
**Kluisopslagtype wijzigen** | U kunt het type opslagreplicatie (geografisch redundante opslag of lokaal redundante opslag) voor een kluis wijzigen voordat back-ups worden opgeslagen. Nadat een back-ups in de kluis is begonnen, kan het replicatietype niet meer worden gewijzigd.
**Zone-redundante opslag (ZRS)** | Beschikbaar in de regio'VK - zuid (UKS) en south Azië - oost (SEA).
**Privé-eindpunten** | Zie [deze sectie voor](./private-endpoints.md#before-you-start) vereisten voor het maken van privé-eindpunten voor een Recovery Service-kluis.  

## <a name="on-premises-backup-support"></a>On-premises ondersteuning voor back-ups

Dit wordt ondersteund als u een back-up wilt maken van on-premises machines:

**Machine** | **Back-up van de back-up** | **Locatie** | **Functies**
--- | --- | --- | ---
**Directe back-up van Windows-computer met MARS-agent** | Bestanden, mappen, systeemstatus | Back-up naar Recovery Services-kluis. | Drie keer per dag een back-up maken<br/><br/> Geen app-bewuste back-up<br/><br/> Bestand, map, volume herstellen
**Directe back-up van Linux-machine met MARS-agent** | Back-up wordt niet ondersteund
**Back-up naar DPM** | Bestanden, mappen, volumes, systeemtoestand, app-gegevens | Back-up naar lokale DPM-opslag. DPM voert vervolgens een back-up uit naar de kluis. | App-bewuste momentopnamen<br/><br/> Volledige granulariteit voor back-up en herstel<br/><br/> Linux ondersteund voor VM's (Hyper-V/VMware)<br/><br/> Oracle wordt niet ondersteund
**Back-up naar MABS** | Bestanden, mappen, volumes, systeemtoestand, app-gegevens | Back-up naar lokale MABS-opslag. MABS voert vervolgens een back-up uit naar de kluis. | App-bewuste momentopnamen<br/><br/> Volledige granulariteit voor back-up en herstel<br/><br/> Linux ondersteund voor VM's (Hyper-V/VMware)<br/><br/> Oracle wordt niet ondersteund

## <a name="azure-vm-backup-support"></a>Ondersteuning voor azure-VM-back-ups

### <a name="azure-vm-limits"></a>Limieten voor Azure VM's

**Limiet** | **Details**
--- | ---
**Azure VM-gegevensschijven** | Zie de [ondersteuningsmatrix voor Azure VM Backup.](./backup-support-matrix-iaas.md#vm-storage-support)
**Grootte Azure VM-gegevensschijven** | De grootte van een afzonderlijke schijf kan maximaal 32 TB zijn en een maximum van 256 TB voor alle schijven in een VM.

### <a name="azure-vm-backup-options"></a>Azure VM-back-upopties

Dit wordt ondersteund als u een back-up wilt maken van azure-VM's:

**Machine** | **Een back-up van de back-up maken** | **Locatie** | **Functies**
--- | --- | --- | ---
**Back-up van azure-VM's met behulp van VM-extensie** | Volledige VM | Back-up naar kluis. | Extensie geïnstalleerd wanneer u back-up voor een VM inschakelen.<br/><br/> Eenmaal per dag een back-up maken.<br/><br/> App-aware back-up voor Windows-VM's; bestands consistente back-up voor Linux-VM's. U kunt app-consistentie voor Linux-machines configureren met behulp van aangepaste scripts.<br/><br/> VM of schijf herstellen.<br/><br/>[Back-up en herstel van Active Directory-domeincontrollers](active-directory-backup-restore.md) wordt ondersteund.<br><br> Kan geen back-up maken van een Azure-VM naar een on-premises locatie.
**Back-up van azure-VM's met marsagent** | Bestanden, mappen, systeemstatus | Back-up naar kluis. | Drie keer per dag een back-up maken.<br/><br/> Als u een back-up wilt maken van specifieke bestanden of mappen in plaats van de hele VM, kan de MARS-agent naast de VM-extensie worden uitgevoerd.
**Azure VM met DPM** | Bestanden, mappen, volumes, systeemtoestand, app-gegevens | Back-up naar lokale opslag van azure-VM met DPM. DPM voert vervolgens een back-up uit naar de kluis. | App-aware momentopnamen.<br/><br/> Volledige granulariteit voor back-up en herstel.<br/><br/> Linux wordt ondersteund voor virtuele machines (Hyper-V-/VMware).<br/><br/> Oracle wordt niet ondersteund.
**Azure VM met MABS** | Bestanden, mappen, volumes, systeemtoestand, app-gegevens | Back-up naar lokale opslag van azure-VM met MABS. MABS voert vervolgens een back-up uit naar de kluis. | App-aware momentopnamen.<br/><br/> Volledige granulariteit voor back-up en herstel.<br/><br/> Linux wordt ondersteund voor virtuele machines (Hyper-V-/VMware).<br/><br/> Oracle wordt niet ondersteund.

## <a name="linux-backup-support"></a>Ondersteuning voor Linux-back-ups

Dit wordt ondersteund als u een back-up wilt maken van Linux-machines:

**Back-uptype** | **Linux (goedgekeurd door Azure)**
--- | ---
**Directe back-up van on-premises machine met Linux** | Wordt niet ondersteund. De MARS-agent kan alleen worden geïnstalleerd op Windows-computers.
**Agentextensie gebruiken om een back-up te maken van een Azure-VM met Linux** | App-consistente back-up met behulp [van aangepaste scripts](backup-azure-linux-app-consistent.md).<br/><br/> Herstel op bestandsniveau.<br/><br/> Herstellen door het maken van een VM vanaf een herstelpunt of schijf.
**DPM gebruiken om back-up te maken van on-premises machines met Linux** | Bestands consistente back-up van Linux-gast-VM's op Hyper-V en VMware.<br/><br/> VM-herstel van Hyper-V- en VMware Linux-gast-VM's.
**MABS gebruiken om back-up te maken van on-premises machines met Linux** | Bestands consistente back-up van Linux-gast-VM's op Hyper-V en VMware.<br/><br/> VM-herstel van Hyper-V- en VMware Linux-gast-VM's.
**MABS of DPM gebruiken om een back-up te maken van Virtuele Linux-VM's in Azure** | Wordt niet ondersteund.

## <a name="daylight-saving-time-support"></a>Ondersteuning voor zomer- en wintertijd

Azure Backup biedt geen ondersteuning voor automatische klokaanpassing voor zomer- en wintertijd voor back-ups van Azure-VM's. Het uur van de back-up wordt niet naar voren of naar achteren verplaatst. Om ervoor te zorgen dat de back-up op het gewenste moment wordt uitgevoerd, wijzigt u het back-upbeleid handmatig als dat nodig is.

## <a name="disk-deduplication-support"></a>Ondersteuning voor schijf ontdubbeling

Ondersteuning voor schijfontdubbeling is als volgt:

- Schijfverdubbeling wordt on-premises ondersteund wanneer u DPM of MABS gebruikt om back-up te maken van Hyper-V-VM's waarop Windows wordt uitgevoerd. Windows Server voert gegevensverdubbeling uit (op hostniveau) op virtuele harde schijven (VHD's) die als back-upopslag aan de virtuele machine zijn gekoppeld.
- Ontdubbeling wordt niet ondersteund in Azure voor Backup-onderdelen. Wanneer DPM en MABS worden geïmplementeerd in Azure, kunnen de opslagschijven die aan de VM zijn gekoppeld, niet worden ontdubbeld.

## <a name="security-and-encryption-support"></a>Ondersteuning voor beveiliging en versleuteling

Azure Backup ondersteunt versleuteling voor in transit en in-rest gegevens.

### <a name="network-traffic-to-azure"></a>Netwerkverkeer naar Azure

- Back-upverkeer van servers naar de Recovery Services-kluis wordt versleuteld met behulp Advanced Encryption Standard 256.
- Back-upgegevens worden verzonden via een beveiligde HTTPS-koppeling.

### <a name="data-security"></a>Gegevensbeveiliging

- Back-upgegevens worden versleuteld opgeslagen in de Recovery Services-kluis.
- Wanneer er een back-up wordt gemaakt van gegevens van on-premises servers met de MARS-agent, worden gegevens versleuteld met een wachtwoordzin voordat ze worden geüpload naar Azure Backup en pas ontsleuteld nadat ze zijn gedownload van Azure Backup.
- Wanneer u een back-up van virtuele Azure-machines wilt maken, moet u versleuteling instellen *binnen* de virtuele machine.
- Azure Backup biedt ondersteuning voor Azure Disk Encryption, dat gebruikmaakt van BitLocker op virtuele Windows-machines en **dm-crypt** op virtuele Linux-machines.
- In de back-end gebruikt Azure Backup [serviceversleuteling Azure Storage,](../storage/common/storage-service-encryption.md)waarmee data-at-rest worden beschermd.

**Machine** | **In-transit** | **Inactief**
--- | --- | ---
**On-premises Windows-machines zonder DPM/MABS** | ![Ja][green] | ![Ja][green]
**Azure-VM's** | ![Ja][green] | ![Ja][green]
**On-premises Windows-machines of Azure-VM's met DPM** | ![Ja][green] | ![Ja][green]
**On-premises Windows-machines of Azure-VM's met MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Ondersteuning voor compressie

Back-up ondersteunt de compressie van back-upverkeer, zoals samengevat in de volgende tabel.

- Voor azure-VM's leest de VM-extensie de gegevens rechtstreeks vanuit het Azure-opslagaccount via het opslagnetwerk, zodat het niet nodig is om dit verkeer te comprimeren.
- Als u DPM of MABS gebruikt, kunt u bandbreedte besparen door de gegevens te comprimeren voordat er een back-up van wordt gemaakt.

**Machine** | **Comprimeren naar MABS/DPM (TCP)** | **Comprimeren naar kluis (HTTPS)**
--- | --- | ---
**Directe back-up van on-premises Windows-machines** | N.v.t. | ![Ja][green]
**Back-up van Azure-VM's met behulp van VM-extensie** | NA | NA
**Back-up maken op on-premises/Azure-machines met behulp van MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Bewaarlimieten

**Instelling** | **Limieten**
--- | ---
**Maximum aantal herstelpunten per beveiligd exemplaar (machine of workload)** | 9,999
**Maximale verlooptijd voor een herstelpunt** | Geen limiet
**Maximale back-upfrequentie naar DPM/MABS** | Om de 15 minuten voor SQL Server<br/><br/> Eenmaal per uur voor andere workloads
**Maximale back-upfrequentie naar kluis** | **On-premises Windows-machines of Azure-VM's met MARS:** Drie per dag<br/><br/> **DPM/MABS:** Twee per dag<br/><br/> **Back-up van azure-VM:** Eén per dag
**Bewaarperiode van herstelpunt** | Dagelijks, wekelijks, maandelijks, jaarlijks
**Maximale bewaarperiode** | Afhankelijk van back-upfrequentie
**Herstelpunten op DPM-/MABS-schijf** | 64 voor bestandsservers; 448 voor app-servers <br/><br/>Onbeperkte tapeherstelpunten voor on-premises DPM

## <a name="cross-region-restore"></a>Herstellen tussen regio's

Azure Backup heeft de functie Herstellen tussen regio's toegevoegd om de beschikbaarheid en tolerantie van gegevens te verbeteren, zodat u volledige controle hebt over het herstellen van gegevens naar een secundaire regio. Ga naar het artikel Herstellen tussen regio's instellen om deze [functie te configureren.](backup-create-rs-vault.md#set-cross-region-restore). Deze functie wordt ondersteund voor de volgende beheertypen:

| Type back-upbeheer | Ondersteund                                                    | Ondersteunde regio's |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Ondersteund voor Azure-VM's (inclusief versleutelde Azure-VM's) met zowel beheerde als on beheerde schijven. Niet ondersteund voor klassieke VM's. | Beschikbaar in alle openbare Azure-regio's en soevereine regio's, met uitzondering van Frankrijk - centraal, Australië - centraal, Zuid-Afrika - noord, VAE - noord, Zwitserland - noord, Duitsland - west-centraal, Noorwegen - oost, UG IOWA en UG Virginia. <br>Neem contact op met voor informatie over het gebruik in deze regio's [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| SQL /SAP HANA | In preview                                                      | Beschikbaar in alle openbare Azure-regio's en soevereine regio's, met uitzondering van Frankrijk - centraal, Australië - centraal, Zuid-Afrika - noord, VAE - noord, Zwitserland - noord, Duitsland - west-centraal, Noorwegen - oost, UG IOWA en UG Virginia. <br>Neem contact op met voor informatie over het gebruik in deze regio's [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| MARS-agent/on-premises  | Nee                                                           | N.v.t.               |
| AFS (Azure-bestands shares)                 | Nee                                                           | N.v.t.               |

## <a name="next-steps"></a>Volgende stappen

- [Bekijk de ondersteuningsmatrix](backup-support-matrix-iaas.md) voor back-ups van azure-VM's.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png