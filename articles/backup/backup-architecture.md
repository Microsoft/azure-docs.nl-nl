---
title: Overzicht van de architectuur
description: Hierin wordt een overzicht gegeven van de architectuur, onderdelen en processen die door de Azure Backup-service worden gebruikt.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 1e5a61bd4e3287c1100ff1f54fda797c1add438b
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466408"
---
# <a name="azure-backup-architecture-and-components"></a>Architectuur en onderdelen van Azure Backup

U kunt de [Azure backup-service](backup-overview.md) gebruiken om een back-up te maken van gegevens naar het Microsoft Azure Cloud platform. Dit artikel bevat een overzicht van Azure Backup architectuur, onderdelen en processen.

## <a name="what-does-azure-backup-do"></a>Wat doet Azure Backup?

Azure Backup maakt een back-up van de gegevens, machine status en workloads die worden uitgevoerd op on-premises machines en Azure virtual machine-exemplaren (VM). Er zijn een aantal Azure Backup scenario's.

## <a name="how-does-azure-backup-work"></a>Hoe werkt Azure Backup?

U kunt met behulp van een aantal methoden back-ups maken van computers en gegevens:

- **Back-ups van on-premises machines**:
  - U kunt rechtstreeks een back-up maken van on-premises Windows-machines naar Azure met behulp van de MARS-agent (Azure Backup Microsoft Azure Recovery Services). Linux-machines worden niet ondersteund.
  - U kunt back-ups van on-premises machines maken op een back-upserver: System Center Data Protection Manager (DPM) of Microsoft Azure Backup Server (MABS). U kunt vervolgens een back-up maken van de back-upserver naar een Recovery Services kluis in Azure.

- **Back-ups maken van virtuele Azure-machines**:
  - U kunt rechtstreeks back-ups maken van Azure-Vm's. Azure Backup installeert een back-upextensie in de Azure VM-agent die wordt uitgevoerd op de VM. Deze uitbrei ding maakt een back-up van de volledige VM.
  - U kunt een back-up maken van specifieke bestanden en mappen op de virtuele Azure-machine door de MARS-agent uit te voeren.
  - U kunt back-ups maken van virtuele Azure-machines naar de MABS die worden uitgevoerd in azure, en u kunt vervolgens een back-up maken van de MABS naar een Recovery Services kluis.

Meer informatie over [waarvan u een back-up kunt maken](backup-overview.md) en over [ondersteunde back-upscenario's](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Waar wordt een back-up van gegevens gemaakt?

Azure Backup opgeslagen back-upgegevens in kluizen-Recovery Services kluizen en back-upkluizen. Een kluis is een online opslag entiteit in azure die wordt gebruikt om gegevens op te slaan, zoals back-ups, herstel punten en back-upbeleid.

Kluizen hebben de volgende kenmerken:

- Kluizen maken het eenvoudig om uw back-upgegevens te organiseren en zo de beheer overhead te minimaliseren.
- U kunt back-upitems bewaken in een kluis, waaronder Azure Vm's en on-premises machines.
- U kunt de toegang tot de kluis beheren met [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/role-assignments-portal.md).
- U geeft op hoe gegevens in de kluis worden gerepliceerd voor redundantie:
  - **Lokaal redundante opslag (LRS)**: als u wilt beveiligen tegen fouten in een Data Center, kunt u LRS gebruiken. LRS repliceert gegevens naar een opslag schaal eenheid. [Meer informatie](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Geografisch redundante opslag (GRS)**: als u wilt beveiligen tegen regionale storingen, kunt u GRS gebruiken. GRS repliceert uw gegevens naar een secundaire regio. [Meer informatie](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Zone-redundante opslag (ZRS)**: repliceert uw gegevens in [beschikbaarheids zones](../availability-zones/az-overview.md#availability-zones), waarbij de gegevens locatie en tolerantie in dezelfde regio worden gegarandeerd. [Meer informatie](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Recovery Services kluizen gebruiken standaard GRS.

Recovery Services kluizen hebben de volgende extra functies:

- In elk Azure-abonnement kunt u Maxi maal 500 kluizen maken.

## <a name="backup-agents"></a>Back-upagents

Azure Backup biedt verschillende back-upagenten, afhankelijk van het type computer waarvan een back-up wordt gemaakt:

**Tussen** | **Details**
--- | ---
**MARS-agent** | <ul><li>Wordt uitgevoerd op afzonderlijke on-premises Windows Server-machines om een back-up te maken van bestanden, mappen en de systeem status.</li> <li>Wordt uitgevoerd op virtuele machines van Azure om een back-up te maken van bestanden, mappen en de systeem status.</li> <li>Wordt uitgevoerd op DPM-MABS-servers om een back-up te maken van de lokale opslag schijf DPM/MABS naar Azure.</li></ul>
**VM-extensie van Azure** | Wordt uitgevoerd op virtuele Azure-machines om een back-up te maken naar een kluis.

## <a name="backup-types"></a>Back-uptypen

In de volgende tabel worden de verschillende typen back-ups beschreven en wanneer deze worden gebruikt:

**Back-uptype** | **Details** | **Gebruik**
--- | --- | ---
**Volledig** | Een volledige back-up bevat de volledige gegevens bron. Vergt meer netwerk bandbreedte dan differentiële of incrementele back-ups. | Wordt gebruikt voor de eerste back-up.
**Differentiële** |  Een differentiële back-up slaat de blokken op die zijn gewijzigd sinds de eerste volledige back-up. Maakt gebruik van een kleinere hoeveelheid netwerk en opslag, en houdt geen redundante kopieën van ongewijzigde gegevens in beslag.<br/><br/> Inefficiënt omdat gegevens blokken die niet worden gewijzigd tussen latere back-ups worden overgebracht en opgeslagen. | Niet gebruikt door Azure Backup.
**Incrementeel** | Een incrementele back-up slaat alleen de gegevens blokken op die zijn gewijzigd sinds de vorige back-up. Hoge opslag en netwerk efficiency. <br/><br/> Met incrementele back-up hoeft u niet aan te vullen met volledige back-ups. | Gebruikt door DPM-MABS voor schijf back-ups en wordt gebruikt in alle back-ups naar Azure. Wordt niet gebruikt voor SQL Server back-up.

## <a name="sql-server-backup-types"></a>SQL Server back-uptypen

In de volgende tabel worden de verschillende typen back-ups beschreven die worden gebruikt voor SQL Server data bases en hoe vaak ze worden gebruikt:

**Back-uptype** | **Details** | **Gebruik**
--- | --- | ---
**Volledige back-up** | Bij een volledige back-up wordt er een back-up van de hele database gemaakt. Het bevat alle gegevens in een specifieke data base of in een set bestands groepen of bestanden. Een volledige back-up bevat ook voldoende Logboeken om die gegevens te herstellen. | U kunt maximaal één volledige back-up per dag activeren.<br/><br/> U kunt ervoor kiezen om een volledige back-up op een dagelijks of wekelijks interval te maken.
**Differentiële back-up** | Een differentiële back-up is gebaseerd op de meest recente back-up van de vorige volledige gegevens.<br/><br/> Het legt alleen de gegevens vast die zijn gewijzigd sinds de volledige back-up. |  U kunt maximaal één differentiële back-up per dag activeren.<br/><br/> U kunt niet zowel een volledige back-up als een differentiële back-up configureren op dezelfde dag.
**Back-up van transactie logboek** | Met een logboekback-up kunt u herstel naar een bepaald tijdstip uitvoeren, tot op een specifieke seconde. | U kunt maximaal elke 15 minuten een back-up van het transactielogboek configureren.

### <a name="comparison-of-backup-types"></a>Vergelijking van back-uptypen

Het opslag verbruik, de beoogde herstel tijd (RTO) en het netwerk verbruik variëren voor elk type back-up. In de volgende afbeelding ziet u een vergelijking van de back-uptypen:

- Gegevens bron A bestaat uit tien opslag blokken, a1-A10, die maandelijks een back-up maken.
- Blokken A2, A3, A4 en A9 zijn in de eerste maand gewijzigd, en blok A5 is de maand erna gewijzigd.
- Voor differentiële back-ups, in de tweede maand gewijzigde blokken a2, a3, A4 en A9, wordt een back-up gemaakt. In de derde maand wordt er opnieuw een back-up gemaakt van dezelfde blokken en van het gewijzigde blok A5. Van de gewijzigde blokken worden back-ups gemaakt totdat de volgende volledige back-up wordt uitgevoerd.
- Voor incrementele back-ups worden in de tweede maand de blokken a2, a3, A4 en A9 gemarkeerd als gewijzigd en overgezet. In het derde maand wordt alleen gewijzigd blok A5 gemarkeerd als gemarkeerd en overgedragen.

![Afbeelding van de vergelijking van back-upmethoden](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Back-upfuncties

De volgende tabel bevat een overzicht van de ondersteunde functies voor de verschillende typen back-ups:

**Functie** | **Directe back-ups van bestanden en mappen (met behulp van de MARS-agent)** | **Back-up van Azure VM** | **Computers of apps met DPM/MABS**
--- | --- | --- | ---
Back-up naar kluis maken | ![Ja][green] | ![Ja][green] | ![Ja][green]
Back-up naar DPM/MABS-schijf en vervolgens naar Azure | | | ![Ja][green]
Gegevens comprimeren die voor back-up zijn verzonden | ![Ja][green] | Er wordt geen compressie gebruikt bij de overdracht van gegevens. De opslag is enigszins geflateerd, maar het herstellen gaat sneller.  | ![Ja][green]
Incrementele back-up uitvoeren |![Ja][green] |![Ja][green] |![Ja][green]
Back-ups maken van ontdubbelde schijven | | | ![Gedeeltelijk][yellow]<br/><br/> Voor DPM-MABS-servers die alleen on-premises worden geïmplementeerd.

![Tabel sleutel](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Basis beginselen van back-upbeleid

- Er wordt een back-upbeleid per kluis gemaakt.
- Er kan een back-upbeleid worden gemaakt voor het maken van een back-up van de volgende workloads: Azure-Vm's, SQL in azure-vm's, SAP HANA in azure-Vm's en Azure-bestands shares. Het beleid voor het maken van back-ups van bestanden en mappen met behulp van de MARS-agent is opgegeven in de MARS-console.
  - Azure-bestandsshare
- Een beleid kan aan veel resources worden toegewezen. Een Azure VM-back-upbeleid kan worden gebruikt om veel virtuele Azure-machines te beveiligen.
- Een beleid bestaat uit twee onderdelen
  - Planning: wanneer de back-up moet worden gemaakt
  - Bewaren: voor hoe lang elke back-up moet worden bewaard.
- De planning kan worden gedefinieerd als dagelijks of wekelijks met een bepaald tijd punt.
- U kunt Bewaar perioden definiëren voor ' dagelijks ', ' Wekelijks ', ' maandelijks ', ' jaarlijks ' back-uppunten.
  - ' Wekelijks ' verwijst naar een back-up op een bepaalde dag van de week
  - ' maandelijks ' verwijst naar een back-up op een bepaalde dag van de maand
  - ' jaarlijks ' verwijst naar een back-up op een bepaalde dag van het jaar
- Het bewaren van ' maandelijks ', ' jaarlijks ' back-uppunten wordt aangeduid als een lange termijn retentie (LTR)
- Wanneer een kluis wordt gemaakt, wordt er ook een ' Defaultpolicy bij ' gemaakt en kan deze worden gebruikt voor het maken van een back-up van bronnen.
- Alle wijzigingen die zijn aangebracht in de Bewaar periode van een back-upbeleid, worden met terugwerkende kracht toegepast op alle oudere herstel punten, van de nieuwe.

### <a name="impact-of-policy-change-on-recovery-points"></a>Gevolgen van beleids wijzigingen op herstel punten

- De **Bewaar periode wordt verg root of verkleind:** Wanneer de Bewaar periode wordt gewijzigd, wordt de nieuwe Bewaar periode ook toegepast op de bestaande herstel punten. Als gevolg hiervan worden sommige herstel punten opgeruimd. Als de retentie periode is toegenomen, zullen de bestaande herstel punten ook een grotere Bewaar termijn hebben.
- **Gewijzigd van dagelijks in wekelijks:** Wanneer de geplande back-ups worden gewijzigd van dagelijks in wekelijks, worden de bestaande dagelijkse herstel punten opgeschoond.
- **Gewijzigd van wekelijks in dagelijks:** De bestaande wekelijkse back-ups worden bewaard op basis van het aantal resterende dagen volgens het huidige Bewaar beleid.

### <a name="additional-reference"></a>Aanvullende naslaginformatie

- Azure VM-machine: beleid [maken](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) en [wijzigen](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) .
- SQL Server data base in azure VM-machine: beleid [maken](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) en [wijzigen](./manage-monitor-sql-database-backup.md#modify-policy) .
- Azure-bestands share: beleid [maken](./backup-afs.md) en [wijzigen](./manage-afs-backup.md#modify-policy) .
- SAP HANA: beleid [maken](./backup-azure-sap-hana-database.md#create-a-backup-policy) en [wijzigen](./sap-hana-db-manage.md#change-policy) .
- MARS: beleid [maken](./backup-windows-with-mars-agent.md#create-a-backup-policy) en [wijzigen](./backup-azure-manage-mars.md#modify-a-backup-policy) .
- [Zijn er beperkingen voor het plannen van back-ups op basis van het type werk belasting?](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [Wat gebeurt er met de bestaande herstelpunten als ik het bewaarbeleid wijzig?](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>Architectuur: ingebouwde Azure VM-back-up

1. Wanneer u back-up voor een virtuele machine van Azure inschakelt, wordt een back-up uitgevoerd volgens het schema dat u opgeeft.
1. Tijdens de eerste back-up wordt een back-upextensie op de VM geïnstalleerd als de virtuele machine wordt uitgevoerd.
    - Op virtuele Windows-machines wordt de VMSnapshot-extensie geïnstalleerd.
    - Voor virtuele Linux-machines wordt de VMSnapshot Linux-extensie geïnstalleerd.
1. De uitbrei ding neemt een moment opname op het opslag niveau.
    - Voor Windows-Vm's waarop wordt uitgevoerd, worden er back-upcoördinaten met de Windows-Volume Shadow Copy Service (VSS) gebruikt voor het maken van een app-consistente moment opname van de virtuele machine. Standaard maakt back-ups volledige VSS-back-ups. Als back-up geen app-consistente moment opname kan maken, wordt een bestands consistente moment opname gebruikt.
    - Voor Linux-Vm's neemt Backup een bestands consistente moment opname. Voor app-consistente moment opnamen moet u hand matig vooraf/post-scripts aanpassen.
    - De back-up wordt geoptimaliseerd door een back-up te maken van elke VM-schijf parallel. Voor elke schijf waarvan een back-up wordt gemaakt, leest Azure Backup de blokken op de schijf en worden alleen de gewijzigde gegevens opgeslagen.
1. Nadat de moment opname is gemaakt, worden de gegevens overgedragen naar de kluis.
    - Alleen gegevens blokken die zijn gewijzigd sinds de laatste back-up zijn gekopieerd.
    - De gegevens zijn niet versleuteld. Azure Backup kunt een back-up maken van virtuele Azure-machines die zijn versleuteld met behulp van Azure Disk Encryption.
    - Momentopnamegegevens worden mogelijk niet direct naar de kluis gekopieerd. Het maken van een back-up kan enige tijd duren. De totale back-uptijd voor een virtuele machine is minder dan 24 uur bij beleid voor dagelijkse back-ups.
1. Nadat de gegevens naar de kluis zijn verzonden, wordt een herstel punt gemaakt. Standaard worden moment opnamen twee dagen bewaard voordat ze worden verwijderd. Met deze functie kunt u de herstel bewerking van deze moment opnamen herstellen. Het vermindert de tijd die nodig is om gegevens terug te zetten en te kopiëren van de kluis. Zie [Azure backup mogelijkheid om direct te herstellen](./backup-instant-restore-capability.md).

U hoeft geen expliciete Internet connectiviteit toe te staan om een back-up te maken van uw Azure-Vm's.

![Back-ups van virtuele Azure-machines](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architectuur: directe back-ups van on-premises Windows Server-machines of Azure-VM-bestanden of-mappen

1. Als u het scenario wilt instellen, downloadt en installeert u de MARS-agent op de computer. Selecteer vervolgens waarvan u een back-up wilt maken, wanneer back-ups worden uitgevoerd en hoe lang ze in Azure worden bewaard.
1. De eerste back-up wordt uitgevoerd volgens uw back-upinstellingen.
1. De MARS-agent gebruikt VSS om een tijdgebonden moment opname te maken van de volumes die zijn geselecteerd voor back-up.
    - De MARS-agent gebruikt alleen de Windows-systeem schrijf bewerking voor het vastleggen van de moment opname.
    - Omdat de agent geen VSS-schrijvers van toepassingen gebruikt, worden er geen app-consistente moment opnamen vastgelegd.
1. Na het maken van de moment opname met VSS, maakt de MARS-agent een virtuele harde schijf (VHD) in de cachemap die u hebt opgegeven tijdens het configureren van de back-up. De agent slaat ook de controle sommen voor elk gegevens blok op. Deze worden later gebruikt voor het detecteren van gewijzigde blokken voor volgende incrementele back-ups.
1. Incrementele back-ups worden uitgevoerd volgens het schema dat u opgeeft, tenzij u een back-up op aanvraag uitvoert.
1. In incrementele back-ups worden gewijzigde bestanden geïdentificeerd en wordt een nieuwe VHD gemaakt. De VHD wordt gecomprimeerd en versleuteld en vervolgens naar de kluis verzonden.
1. Nadat de incrementele back-up is voltooid, wordt de nieuwe VHD samengevoegd met de VHD die is gemaakt na de initiële replicatie. Deze samengevoegde VHD bevat de meest recente status om te vergelijken voor continue back-ups.

![Back-ups van on-premises Windows Server-machines met MARS agent](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architectuur: back-up naar DPM-MABS

1. U installeert de DPM-of MABS-beveiligings agent op computers die u wilt beveiligen. Vervolgens voegt u de machines toe aan een DPM-beveiligings groep.
    - Als u on-premises machines wilt beveiligen, moet de DPM-of MABS-server zich lokaal bevinden.
    - Om virtuele Azure-machines te beveiligen, moet de MABS-server zich in azure bevinden, die wordt uitgevoerd als een virtuele machine van Azure.
    - Met DPM-MABS kunt u back-upvolumes, shares, bestanden en mappen beveiligen. U kunt ook de systeem status van een computer beveiligen (bare metal) en u kunt specifieke apps beveiligen met app-Aware back-upinstellingen.
1. Wanneer u de beveiliging voor een machine of app in DPM-MABS instelt, selecteert u een back-up naar de lokale MABS/DPM-schijf voor kortetermijnbeveiliging en naar Azure voor online beveiliging. U geeft ook op wanneer de back-up naar de lokale DPM-MABS-opslag moet worden uitgevoerd en wanneer de online back-up naar Azure moet worden uitgevoerd.
1. Op de schijf van de beveiligde werk belasting wordt een back-up gemaakt op de lokale MABS/DPM-schijven volgens het schema dat u hebt opgegeven.
1. Er wordt een back-up van de DPM-MABS-schijven gemaakt op de kluis door de MARS-agent die wordt uitgevoerd op de DPM/MABS-server.

![Back-ups van machines en workloads die worden beveiligd door DPM of MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM-opslag

Azure-Vm's gebruiken schijven voor het opslaan van hun besturings systeem, apps en gegevens. Elke virtuele machine van Azure heeft ten minste twee schijven: een schijf voor het besturings systeem en een tijdelijke schijf. Virtuele Azure-machines kunnen ook gegevens schijven hebben voor app-gegevens. Schijven worden opgeslagen als Vhd's.

- Vhd's worden opgeslagen als pagina-blobs in standaard-of Premium Storage-accounts in Azure:
  - **Standaard opslag:** Betrouw bare, voordelige schijf ondersteuning voor Vm's met werk belastingen die niet gevoelig zijn voor latentie. Standaard opslag kan gebruikmaken van standaard SSD-schijven (Solid-State Drive) of harde schijven van harde schijven.
  - **Premium-opslag:** Ondersteuning voor schijf met hoge prestaties. Maakt gebruik van Premium SSD-schijven.
- Er zijn verschillende prestatie lagen voor schijven:
  - **Standard-HDD schijf:** Ondersteund door Hdd's en gebruikt voor rendabele opslag.
  - **Standard-SSD schijf:** Hiermee worden elementen van Premium SSD-schijven en standaard HDD-schijven gecombineerd. Biedt consistente prestaties en betrouw baarheid dan HDD, maar nog steeds rendabel.
  - **Premium-SSD schijf:** Ondersteund door Ssd's en biedt hoge prestaties en lage latentie voor Vm's met I/O-intensieve workloads.
- Schijven kunnen worden beheerd of onbeheerd:
  - Niet- **beheerde schijven:** Traditioneel type schijven dat wordt gebruikt door Vm's. Voor deze schijven maakt u uw eigen opslag account en geeft u deze op wanneer u de schijf maakt. Vervolgens moet u nagaan hoe u de opslag resources voor uw virtuele machines kunt maximaliseren.
  - **Beheerde schijven:** Azure maakt en beheert de opslag accounts voor u. U geeft de schijf grootte en de prestatie-laag op en Azure maakt beheerde schijven voor u. Wanneer u schijven toevoegt en virtuele machines schaalt, worden de opslag accounts door Azure afgehandeld.

Zie de volgende artikelen voor meer informatie over schijf ruimte en de beschik bare schijf typen voor Vm's:

- [Azure Managed disks voor Linux Vm's](../virtual-machines/managed-disks-overview.md)
- [Beschik bare schijf typen voor Vm's](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Back-ups van virtuele Azure-machines maken en herstellen met Premium Storage

U kunt back-ups van virtuele Azure-machines maken met behulp van Premium Storage met Azure Backup:

- Tijdens het proces van het maken van back-ups van virtuele machines met Premium Storage maakt de back-upservice een tijdelijke faserings locatie met de naam *AzureBackup-* in het opslag account. De grootte van de faserings locatie is gelijk aan de grootte van de moment opname van het herstel punt.
- Zorg ervoor dat het Premium-opslag account voldoende vrije ruimte heeft voor de tijdelijke faserings locatie. Zie [schaalbaarheids doelen voor Premium-pagina-Blob Storage-accounts](../storage/blobs/scalability-targets-premium-page-blobs.md)voor meer informatie. Wijzig de faserings locatie niet.
- Nadat de back-uptaak is voltooid, wordt de faserings locatie verwijderd.
- De prijs van de opslag die wordt gebruikt voor de faserings locatie is consistent met de [prijzen voor Premium Storage](../virtual-machines/disks-types.md#billing).

Wanneer u Azure-Vm's herstelt met behulp van Premium-opslag, kunt u ze herstellen naar de Premium-of Standard-opslag. Normaal gesp roken herstelt u ze naar Premium Storage. Maar als u slechts een subset van bestanden van de virtuele machine nodig hebt, is het mogelijk rendabel om ze te herstellen naar de standaard opslag.

### <a name="back-up-and-restore-managed-disks"></a>Back-up en herstel van beheerde schijven

U kunt back-ups maken van virtuele Azure-machines met beheerde schijven:

- U maakt een back-up van virtuele machines met beheerde schijven op dezelfde manier als andere virtuele Azure-machines. U kunt rechtstreeks vanuit de instellingen van de virtuele machine een back-up van de VM maken of u kunt back-ups voor virtuele machines inschakelen in de Recovery Services kluis.
- U kunt back-ups maken van virtuele machines op beheerde schijven via RestorePoint-verzamelingen die zijn gebouwd boven op beheerde schijven.
- Azure Backup biedt ook ondersteuning voor het maken van back-ups van virtuele machines met beheerde schijven die zijn versleuteld met behulp van Azure Disk Encryption.

Wanneer u Vm's herstelt met Managed disks, kunt u een volledige VM met beheerde schijven of een opslag account herstellen:

- Tijdens het herstel proces verwerkt Azure de beheerde schijven. Als u de optie voor het opslag account gebruikt, beheert u het opslag account dat tijdens het herstel proces is gemaakt.
- Als u een beheerde virtuele machine herstelt die is versleuteld, moet u ervoor zorgen dat de sleutels en geheimen van de virtuele machine in de sleutel kluis voor komen voordat u het herstel proces start.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de ondersteunings matrix voor [meer informatie over ondersteunde functies en beperkingen voor back-upscenario's](backup-support-matrix.md).
- Stel de back-up in voor een van deze scenario's:
  - [Maak een back-up van virtuele Azure-machines](backup-azure-arm-vms-prepare.md).
  - [Rechtstreeks een back-up maken van Windows-machines](tutorial-backup-windows-server-to-azure.md), zonder een back-upserver.
  - [MABS instellen](backup-azure-microsoft-azure-backup.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar MABS maken.
  - [DPM instellen](backup-azure-dpm-introduction.md) voor het maken van een back-up naar Azure, en vervolgens een back-up van workloads naar DPM maken.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
