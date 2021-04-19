---
title: Overzicht van de architectuur
description: Biedt een overzicht van de architectuur, onderdelen en processen die worden gebruikt door de Azure Backup service.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 8fca05f8718fc5e44da33b19447895f5daafc905
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716740"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup architectuur en onderdelen

U kunt de service [Azure Backup gebruiken om een](backup-overview.md) back-up te maken van gegevens naar Microsoft Azure cloudplatform. Dit artikel bevat een Azure Backup architectuur, onderdelen en processen.

## <a name="what-does-azure-backup-do"></a>Wat doet Azure Backup doen?

Azure Backup back-up van de gegevens, machinetoestand en workloads die worden uitgevoerd op on-premises machines en exemplaren van virtuele Azure-machines (VM's). Er zijn een aantal Azure Backup scenario's.

## <a name="how-does-azure-backup-work"></a>Hoe werkt Azure Backup?

U kunt een back-up maken van machines en gegevens met behulp van een aantal methoden:

- **Back-up maken van on-premises machines:**
  - U kunt rechtstreeks een back-up maken van on-premises Windows-machines naar Azure met behulp van de MARS-agent (Azure Backup Microsoft Azure Recovery Services). Linux-machines worden niet ondersteund.
  - U kunt een back-up maken van on-premises machines naar een back-upserver: System Center Data Protection Manager (DPM) of Microsoft Azure Backup Server (MABS). U kunt vervolgens een back-up maken van de back-upserver naar een Recovery Services-kluis in Azure.

- **Back-up maken van Azure-VM's:**
  - U kunt rechtstreeks een back-up maken van azure-VM's. Azure Backup installeert een back-upextensie op de Azure VM-agent die wordt uitgevoerd op de VM. Deze extensie back-up van de hele VM.
  - U kunt een back-up maken van specifieke bestanden en mappen op de azure-VM door de MARS-agent uit te uitvoeren.
  - U kunt een back-up maken van azure-VM's naar de MABS die wordt uitgevoerd in Azure en u kunt vervolgens een back-up maken van de MABS naar een Recovery Services-kluis.

Meer informatie over waar [u een back-up van kunt maken](backup-overview.md) en over [ondersteunde back-upscenario's.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Waar wordt een back-up van gegevens gemaakt?

Azure Backup back-upgegevens op in kluizen: Recovery Services-kluizen en Backup-kluizen. Een kluis is een onlineopslagentiteit in Azure die wordt gebruikt voor het opslaan van gegevens, zoals back-ups, herstelpunten en back-upbeleid.

Kluizen hebben de volgende functies:

- Met kluizen kunt u uw back-upgegevens eenvoudig ordenen en tegelijkertijd de beheeroverhead minimaliseren.
- U kunt back-upitems in een kluis bewaken, inclusief Azure-VM's en on-premises machines.
- U kunt toegang tot de kluis beheren met [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).](../role-based-access-control/role-assignments-portal.md)
- U geeft op hoe gegevens in de kluis worden gerepliceerd voor redundantie:
  - **Lokaal redundante opslag (LRS)**: U kunt LRS gebruiken om te beveiligen tegen fouten in een datacenter. LRS repliceert gegevens naar een opslagschaaleenheid. [Meer informatie](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Geografisch redundante opslag (GRS)**: als bescherming tegen uitval in de hele regio, kunt u GRS gebruiken. GrS repliceert uw gegevens naar een secundaire regio. [Meer informatie](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Zone-redundante opslag (ZRS)**: repliceert uw gegevens in [beschikbaarheidszones,](../availability-zones/az-overview.md#availability-zones)wat gegevensopslag en tolerantie in dezelfde regio garandeert. [Meer informatie](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Recovery Services-kluizen maken standaard gebruik van GRS.

Recovery Services-kluizen hebben de volgende aanvullende functies:

- In elk Azure-abonnement kunt u maximaal 500 kluizen maken.

## <a name="backup-agents"></a>Back-upagents

Azure Backup biedt verschillende back-upagents, afhankelijk van het type machine waarvoor een back-up wordt gemaakt:

**Agent** | **Details**
--- | ---
**MARS-agent** | <ul><li>Wordt uitgevoerd op afzonderlijke on-premises Windows Server-machines om back-up te maken van bestanden, mappen en de systeemtoestand.</li> <li>Wordt uitgevoerd op azure-VM's om back-up te maken van bestanden, mappen en de systeemtoestand.</li> <li>Wordt uitgevoerd op DPM/MABS-servers om een back-up te maken van de lokale DPM/MABS-opslagschijf naar Azure.</li></ul>
**Azure VM-extensie** | Wordt uitgevoerd op azure-VM's om er een back-up van te maken in een kluis.

## <a name="backup-types"></a>Back-uptypen

In de volgende tabel worden de verschillende typen back-ups uitgelegd en wanneer deze worden gebruikt:

**Back-uptype** | **Details** | **Gebruik**
--- | --- | ---
**Volledig** | Een volledige back-up bevat de volledige gegevensbron. Neemt meer netwerkbandbreedte dan differentiële of incrementele back-ups. | Wordt gebruikt voor de eerste back-up.
**Differentiële** |  Een differentiële back-up slaat de blokken op die zijn gewijzigd sinds de eerste volledige back-up. Maakt gebruik van een kleinere hoeveelheid netwerk en opslag en houdt geen redundante kopieën van ongewijzigde gegevens bij.<br/><br/> Inefficiënt omdat gegevensblokken die ongewijzigd zijn tussen latere back-ups worden overgedragen en opgeslagen. | Niet gebruikt door Azure Backup.
**Incrementeel** | Een incrementele back-up slaat alleen de gegevensblokken op die sinds de vorige back-up zijn gewijzigd. Hoge opslag- en netwerkefficiëntie. <br/><br/> Met incrementele back-ups hoeft u deze niet aan te vullen met volledige back-ups. | Wordt gebruikt door DPM/MABS voor schijfback-ups en gebruikt in alle back-ups naar Azure. Niet gebruikt voor SQL Server back-up.

## <a name="sql-server-backup-types"></a>SQL Server back-uptypen

In de volgende tabel worden de verschillende typen back-ups uitgelegd die worden gebruikt SQL Server databases en hoe vaak ze worden gebruikt:

**Back-uptype** | **Details** | **Gebruik**
--- | --- | ---
**Volledige back-up** | Bij een volledige back-up wordt er een back-up van de hele database gemaakt. Het bevat alle gegevens in een specifieke database of in een set bestandsgroepen of bestanden. Een volledige back-up bevat ook voldoende logboeken om die gegevens te herstellen. | U kunt maximaal één volledige back-up per dag activeren.<br/><br/> U kunt ervoor kiezen om dagelijks of wekelijks een volledige back-up te maken.
**Differentiële back-up** | Een differentiële back-up is gebaseerd op de meest recente, vorige volledige gegevensback-up.<br/><br/> Het legt alleen de gegevens vast die zijn gewijzigd sinds de volledige back-up. |  U kunt maximaal één differentiële back-up per dag activeren.<br/><br/> U kunt niet zowel een volledige back-up als een differentiële back-up configureren op dezelfde dag.
**Back-up van transactielogboek** | Met een logboekback-up kunt u herstel naar een bepaald tijdstip uitvoeren, tot op een specifieke seconde. | U kunt maximaal elke 15 minuten een back-up van het transactielogboek configureren.

### <a name="comparison-of-backup-types"></a>Vergelijking van back-uptypen

Opslagverbruik, RTO (Recovery Time Objective) en netwerkverbruik variëren voor elk type back-up. In de volgende afbeelding ziet u een vergelijking van de back-uptypen:

- Gegevensbron A bestaat uit tien opslagblokken, A1-A10, waarvan maandelijks een back-up wordt gemaakt.
- Blokken A2, A3, A4 en A9 zijn in de eerste maand gewijzigd, en blok A5 is de maand erna gewijzigd.
- Voor differentiële back-ups wordt in de tweede maand een back-up gemaakt van blokken A2, A3, A4 en A9. In de derde maand wordt er opnieuw een back-up gemaakt van dezelfde blokken en van het gewijzigde blok A5. Van de gewijzigde blokken worden back-ups gemaakt totdat de volgende volledige back-up wordt uitgevoerd.
- Voor incrementele back-ups worden in de tweede maand blokken A2, A3, A4 en A9 gemarkeerd als gewijzigd en overgedragen. In het derde maand wordt alleen gewijzigd blok A5 gemarkeerd als gemarkeerd en overgedragen.

![Afbeelding met vergelijkingen van back-upmethoden](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Back-upfuncties

De volgende tabel bevat een overzicht van de ondersteunde functies voor de verschillende typen back-ups:

**Functie** | **Directe back-up van bestanden en mappen (met marsagent)** | **Azure VM Backup** | **Machines of apps met DPM/MABS**
--- | --- | --- | ---
Back-up naar kluis | ![Ja][green] | ![Ja][green] | ![Ja][green]
Back-up naar DPM/MABS-schijf en vervolgens naar Azure | | | ![Yes][green]
Gegevens comprimeren die worden verzonden voor back-up | ![Yes][green] | Er wordt geen compressie gebruikt bij het overdragen van gegevens. De opslag wordt iets groter, maar herstel gaat sneller.  | ![Yes][green]
Incrementele back-up uitvoeren |![Ja][green] |![Ja][green] |![Ja][green]
Back-up maken van ontdubbelde schijven | | | ![Gedeeltelijk][yellow]<br/><br/> Alleen voor DPM/MABS-servers die on-premises zijn geïmplementeerd.

![Tabelsleutel](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Essentiële onderdelen van back-upbeleid

- Er wordt per kluis een back-upbeleid gemaakt.
- Er kan een back-upbeleid worden gemaakt voor de back-up van de volgende workloads: Azure-VM's, SQL in Azure-VM's, SAP HANA in Azure-VM's en Azure-bestands shares. Het beleid voor back-ups van bestanden en mappen met behulp van de MARS-agent wordt opgegeven in de MARS-console.
  - Azure-bestandsshare
- Een beleid kan worden toegewezen aan veel resources. Een back-upbeleid voor azure-VM's kan worden gebruikt om veel Azure-VM's te beveiligen.
- Een beleid bestaat uit twee onderdelen
  - Planning: Wanneer de back-up moet worden gemaakt
  - Retentie: hoe lang elke back-up moet worden bewaard.
- Planning kan worden gedefinieerd als 'dagelijks' of 'wekelijks' met een bepaald tijdstip.
- Retentie kan worden gedefinieerd voor 'dagelijks', 'wekelijks', 'maandelijks', 'jaarlijks' back-uppunten.
  - 'wekelijks' verwijst naar een back-up op een bepaalde dag van de week
  - 'maandelijks' verwijst naar een back-up op een bepaalde dag van de maand
  - 'jaarlijks' verwijst naar een back-up op een bepaalde dag van het jaar
- Retentie voor 'maandelijks', 'jaarlijkse' back-uppunten wordt langetermijnretentie (LTR) genoemd
- Wanneer een kluis wordt gemaakt, wordt er ook een 'DefaultPolicy' gemaakt en kan deze worden gebruikt om een back-up van resources te maken.
- Wijzigingen in de retentieperiode van een back-upbeleid worden met terugwerkende kracht toegepast op alle oudere herstelpunten, afgezien van de nieuwe herstelpunten.

### <a name="impact-of-policy-change-on-recovery-points"></a>Gevolgen van beleidswijziging op herstelpunten

- **Retentieduur wordt verhoogd/verlaagd:** Wanneer de retentieduur wordt gewijzigd, wordt de nieuwe bewaarperiode ook toegepast op de bestaande herstelpunten. Als gevolg hiervan worden sommige herstelpunten opgeschoond. Als de retentieperiode wordt verhoogd, hebben de bestaande herstelpunten ook een verhoogde retentie.
- **Gewijzigd van dagelijks in wekelijks:** Wanneer de geplande back-ups worden gewijzigd van dagelijks in wekelijks, worden de bestaande dagelijkse herstelpunten opgeschoond.
- **Gewijzigd van wekelijks in dagelijks:** De bestaande wekelijkse back-ups worden bewaard op basis van het aantal resterende dagen volgens het huidige bewaarbeleid.

### <a name="additional-reference"></a>Aanvullende naslaginformatie

- Azure VM-machine: beleid [maken](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) [en](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) wijzigen.
- SQL Server database maken op azure-VM-machine: [beleid](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) [maken en](./manage-monitor-sql-database-backup.md#modify-policy) wijzigen.
- Azure-bestands share: beleid [maken](./backup-afs.md) [en](./manage-afs-backup.md#modify-policy) wijzigen.
- SAP HANA: Beleid [maken](./backup-azure-sap-hana-database.md#create-a-backup-policy) [en](./sap-hana-db-manage.md#change-policy) wijzigen.
- MARS: Beleid [maken](./backup-windows-with-mars-agent.md#create-a-backup-policy) [en](./backup-azure-manage-mars.md#modify-a-backup-policy) wijzigen.
- [Zijn er beperkingen voor het plannen van back-ups op basis van het type workload?](./backup-azure-backup-faq.yml#are-there-limits-on-backup-scheduling-)
- [Wat gebeurt er met de bestaande herstelpunten als ik het bewaarbeleid wijzig?](./backup-azure-backup-faq.yml#what-happens-when-i-change-my-backup-policy-)

## <a name="architecture-built-in-azure-vm-backup"></a>Architectuur: Ingebouwde Azure VM Backup

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architectuur: directe back-up van on-premises Windows Server-machines of Azure VM-bestanden of -mappen

1. Als u het scenario wilt instellen, downloadt en installeert u de MARS-agent op de computer. Vervolgens selecteert u waar u een back-up van wilt maken, wanneer back-ups worden uitgevoerd en hoe lang ze worden bewaard in Azure.
1. De eerste back-up wordt uitgevoerd volgens uw back-upinstellingen.
1. De MARS-agent gebruikt VSS om een momentopname te maken van de volumes die zijn geselecteerd voor back-up.
    - De MARS-agent gebruikt alleen de schrijfbewerking van het Windows-systeem om de momentopname vast te leggen.
    - Omdat de agent geen VSS Writers voor toepassingen gebruikt, worden er geen app-consistente momentopnamen gemaakt.
1. Nadat de momentopname met VSS is gemaakt, maakt de MARS-agent een virtuele harde schijf (VHD) in de cachemap die u hebt opgegeven bij het configureren van de back-up. De agent slaat ook controlesums op voor elk gegevensblok. Deze worden later gebruikt voor het detecteren van gewijzigde blokken voor latere incrementele back-ups.
1. Incrementele back-ups worden uitgevoerd volgens de planning die u opgeeft, tenzij u een back-up op aanvraag hebt uitgevoerd.
1. In incrementele back-ups worden gewijzigde bestanden geïdentificeerd en wordt er een nieuwe VHD gemaakt. De VHD wordt gecomprimeerd en versleuteld en vervolgens naar de kluis verzonden.
1. Nadat de incrementele back-up is gemaakt, wordt de nieuwe VHD samengevoegd met de VHD die is gemaakt na de initiële replicatie. Deze samengevoegde VHD biedt de meest recente status die moet worden gebruikt voor vergelijking voor continue back-up.

![Back-up van on-premises Windows Server-machines met MARS-agent](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architectuur: Back-up naar DPM/MABS

1. U installeert de DPM- of MABS-beveiligingsagent op de machines die u wilt beveiligen. Vervolgens voegt u de machines toe aan een DPM-beveiligingsgroep.
    - Als u on-premises machines wilt beveiligen, moet de DPM- of MABS-server zich on-premises bevinden.
    - Om Azure-VM's te beveiligen, moet de MABS-server zich in Azure bevinden en worden uitgevoerd als een Azure-VM.
    - Met DPM/MABS kunt u back-upvolumes, shares, bestanden en mappen beveiligen. U kunt ook de systeemtoestand van een machine beveiligen (bare metal) en u kunt specifieke apps beveiligen met app-gerichte back-upinstellingen.
1. Wanneer u beveiliging voor een computer of app in DPM/MABS in stelt, selecteert u om een back-up te maken van de lokale MABS/DPM-schijf voor kortetermijnopslag en naar Azure voor onlinebeveiliging. U geeft ook op wanneer de back-up naar de lokale DPM/MABS-opslag moet worden uitgevoerd en wanneer de online back-up naar Azure moet worden uitgevoerd.
1. Er wordt een back-up van de schijf van de beveiligde werkbelasting op de lokale MABS-/DPM-schijven opgeslagen volgens het schema dat u hebt opgegeven.
1. Van de DPM/MABS-schijven wordt een back-up gemaakt naar de kluis door de MARS-agent die wordt uitgevoerd op de DPM/MABS-server.

![Back-up van machines en workloads die worden beveiligd door DPM of MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM-opslag

Azure-VM's gebruiken schijven om hun besturingssysteem, apps en gegevens op te slaan. Elke Azure-VM heeft ten minste twee schijven: een schijf voor het besturingssysteem en een tijdelijke schijf. Azure-VM's kunnen ook gegevensschijven voor app-gegevens hebben. Schijven worden opgeslagen als VHD's.

- VHD's worden opgeslagen als pagina-blobs in Standard- of Premium Storage-accounts in Azure:
  - **Standaardopslag:** Betrouwbare, goedkope schijfondersteuning voor VM's met workloads die niet gevoelig zijn voor latentie. Standard-opslag kan standaard SSD-schijven (Solid-State Drive) of HDD-schijven (Standard Hard Disk Drive) gebruiken.
  - **Premium-opslag:** Schijfondersteuning met hoge prestaties. Maakt gebruik van Premium SSD-schijven.
- Er zijn verschillende prestatielagen voor schijven:
  - **Standard - HDD schijf:** Wordt mogelijk gemaakt door HDD's en gebruikt voor rendabele opslag.
  - **Standard - SSD schijf:** Combineert elementen van Premium SSD-schijven en Standard HDD-schijven. Biedt consistentere prestaties en betrouwbaarheid dan HDD, maar toch rendabel.
  - **Premium - SSD schijf:** Wordt mogelijk gemaakt door SSD's en biedt hoge prestaties en lage latentie voor VM's met I/O-intensieve workloads.
- Schijven kunnen worden beheerd of niet-beheerd:
  - **Niet-mande schijven:** Traditionele typen schijven die worden gebruikt door VM's. Voor deze schijven maakt u uw eigen opslagaccount en geeft u dit op wanneer u de schijf maakt. Vervolgens moet u na gaan hoe u de opslagbronnen voor uw VM's kunt maximaliseren.
  - **Beheerde schijven:** Azure maakt en beheert de opslagaccounts voor u. U geeft de schijfgrootte en prestatielaag op en Azure maakt beheerde schijven voor u. Wanneer u schijven toevoegt en VM's schaalt, verwerkt Azure de opslagaccounts.

Zie de volgende artikelen voor meer informatie over schijfopslag en de beschikbare schijftypen voor VM's:

- [Azure Managed Disks voor Linux-VM's](../virtual-machines/managed-disks-overview.md)
- [Beschikbare schijftypen voor VM's](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Back-up maken van virtuele Azure-VM's en deze herstellen met Premium Storage

U kunt een back-up maken van azure-VM's met behulp van Premium Storage met Azure Backup:

- Tijdens het maken van back-ups van VM's met Premium Storage maakt de Backup-service een tijdelijke faseringslocatie met de naam *AzureBackup-* in het opslagaccount. De grootte van de faseringslocatie is gelijk aan de grootte van de momentopname van het herstelpunt.
- Zorg ervoor dat het Premium Storage-account voldoende vrije ruimte heeft voor de tijdelijke faseringslocatie. Zie Schaalbaarheidsdoelen voor [Premium-pagina-blobopslagaccounts voor meer informatie.](../storage/blobs/scalability-targets-premium-page-blobs.md) Wijzig de faseringslocatie niet.
- Nadat de back-up is gemaakt, wordt de faseringslocatie verwijderd.
- De opslagprijs die wordt gebruikt voor de faseringslocatie is consistent met de [prijzen voor Premium Storage.](../virtual-machines/disks-types.md#billing)

Wanneer u azure-VM's herstelt met behulp van Premium-opslag, kunt u ze herstellen naar Premium- of Standard-opslag. Normaal gesproken herstelt u ze naar Premium Storage. Maar als u slechts een subset van bestanden van de virtuele machine nodig hebt, kan het rendabel zijn om ze te herstellen naar de standaardopslag.

### <a name="back-up-and-restore-managed-disks"></a>Back-up en herstel van beheerde schijven

U kunt een back-up maken van virtuele Azure-VM's met beheerde schijven:

- U kunt op dezelfde manier een back-up maken van VM's met beheerde schijven als elke andere Azure-VM. U kunt rechtstreeks vanuit de instellingen van de virtuele machine een back-up maken van de virtuele machine of u kunt back-ups inschakelen voor VM's in de Recovery Services-kluis.
- U kunt back-ups maken van virtuele machines op beheerde schijven via RestorePoint-verzamelingen die zijn gebouwd boven op beheerde schijven.
- Azure Backup biedt ook ondersteuning voor het maken van back-up van VM's met beheerde schijven die zijn versleuteld met behulp van Azure Disk Encryption.

Wanneer u VM's met beheerde schijven herstelt, kunt u herstellen naar een volledige VM met beheerde schijven of naar een opslagaccount:

- Tijdens het herstelproces verwerkt Azure de beheerde schijven. Als u de optie voor het opslagaccount gebruikt, beheert u het opslagaccount dat tijdens het herstelproces is gemaakt.
- Als u een beheerde VM herstelt die is versleuteld, moet u ervoor zorgen dat de sleutels en geheimen van de VM aanwezig zijn in de sleutelkluis voordat u het herstelproces start.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de ondersteuningsmatrix voor [meer informatie over ondersteunde functies en beperkingen voor back-upscenario's.](backup-support-matrix.md)
- Back-up instellen voor een van deze scenario's:
  - [Back-up maken van Azure-VM's.](backup-azure-arm-vms-prepare.md)
  - [Rechtstreeks een back-up maken van Windows-machines](tutorial-backup-windows-server-to-azure.md), zonder een back-upserver.
  - [MABS instellen](backup-azure-microsoft-azure-backup.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar MABS maken.
  - [DPM instellen](backup-azure-dpm-introduction.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar DPM maken.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
