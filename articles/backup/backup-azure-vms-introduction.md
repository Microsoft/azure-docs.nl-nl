---
title: Over Azure VM Backup
description: In dit artikel leest u hoe de Azure Backup-service een back-up maakt van virtuele Azure-machines en hoe u de aanbevolen procedures volgt.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 7fa47b83eb8fa06c028079cf47ea0cb46df31860
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325227"
---
# <a name="an-overview-of-azure-vm-backup"></a>Een overzicht van Azure VM backup

In dit artikel wordt beschreven hoe de [Azure backup-service](./backup-overview.md) een back-up maakt van Azure virtual machines (vm's).

Azure Backup biedt onafhankelijke en geïsoleerde back-ups om bescherming te bieden tegen onbedoelde vernietiging van de gegevens op uw VM's. Back-ups worden in een Recovery Services-kluis opgeslagen waarin het beheer van herstelpunten is ingebouwd. Het is eenvoudig om te configureren en de schaal aan te passen, back-ups worden geoptimaliseerd en u kunt, indien nodig, eenvoudig herstelbewerkingen uitvoeren.

Als onderdeel van het back-upproces [wordt er een moment opname gemaakt](#snapshot-creation)en worden de gegevens overgebracht naar de Recovery Services kluis zonder dat dit van invloed is op de werk belasting van de productie. De moment opname biedt verschillende consistentie niveaus, zoals [hier](#snapshot-consistency)wordt beschreven.

Azure Backup heeft ook gespecialiseerde aanbiedingen voor data base-workloads, zoals [SQL Server](backup-azure-sql-database.md) en [SAP Hana](sap-hana-db-about.md) die workload bewust zijn, 15 minuten RPO (Recovery Point Objective) bieden en back-up en herstel van afzonderlijke data bases toestaan.

## <a name="backup-process"></a>Back-upproces

Hier volgt een beschrijving van de manier waarop in Azure Backup een back-up voor virtuele Azure-machines wordt uitgevoerd:

1. Azure Backup start een back-uptaak op basis van het back-upschema dat u opgeeft voor Azure-Vm's die zijn geselecteerd voor back-up.
1. Tijdens de eerste back-up wordt een back-upextensie op de VM geïnstalleerd als de virtuele machine wordt uitgevoerd.
    - Voor virtuele Windows-machines wordt de [VMSnapshot-extensie](../virtual-machines/extensions/vmsnapshot-windows.md) geïnstalleerd.
    - Voor virtuele Linux-machines wordt de [VMSnapshotLinux-extensie](../virtual-machines/extensions/vmsnapshot-linux.md) geïnstalleerd.
1. Voor Windows-Vm's waarop wordt uitgevoerd, worden er back-upcoördinaten met Windows Volume Shadow Copy Service (VSS) gebruikt om een app-consistente moment opname van de virtuele machine te maken.
    - Standaard maakt back-ups volledige VSS-back-ups.
    - Als back-up geen app-consistente moment opname kan maken, wordt een bestands consistente moment opname van de onderliggende opslag gebruikt (omdat er geen schrijf bewerkingen voor de toepassing plaatsvinden terwijl de virtuele machine wordt gestopt).
1. Voor Linux-Vm's neemt Backup een bestands consistente back-up. Voor app-consistente moment opnamen moet u hand matig vooraf/post-scripts aanpassen.
1. Nadat de back-up de moment opname heeft gemaakt, worden de gegevens overgedragen naar de kluis.
    - De back-up wordt geoptimaliseerd door parallel een back-up te maken van elke VM-schijf.
    - Azure Backup leest de blokken op elke schijf waarvan een back-up wordt gemaakt, en identificeert en verplaatst alleen de gegevensblokken die sinds de vorige back-up zijn gewijzigd (de delta).
    - Momentopnamegegevens worden mogelijk niet direct naar de kluis gekopieerd. Het kan enkele uren duren om piek tijden. De totale back-uptijd voor een virtuele machine is minder dan 24 uur bij beleid voor dagelijkse back-ups.
1. Wijzigingen die zijn aangebracht in een Windows-VM nadat Azure Backup is ingeschakeld, zijn:
    - Te distribueren pakket micro soft Visual C++ 2013 (x64)-12.0.40660 is geïnstalleerd in de VM
    - Het opstart type van de Volume Shadow Copy service (VSS) is gewijzigd in automatisch vanuit hand matig
    - IaaSVmProvider Windows-service is toegevoegd

1. Wanneer de gegevens overdracht is voltooid, wordt de moment opname verwijderd en wordt er een herstel punt gemaakt.

![Back-uparchitectuur van Azure virtual machine](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Versleuteling van back-ups van Azure-VM'S

Wanneer u back-ups maakt van virtuele Azure-machines met Azure Backup, worden virtuele machines op rest versleuteld met Storage Service Encryption (SSE). Azure Backup kunt ook een back-up maken van virtuele Azure-machines die zijn versleuteld met behulp van Azure Disk Encryption.

**Versleuteling** | **Details** | **Ondersteuning**
--- | --- | ---
**SSE** | Met SSE biedt Azure Storage versleuteling op rest door gegevens automatisch te versleutelen voordat ze worden opgeslagen. Azure Storage worden ook gegevens ontsleuteld voordat ze worden opgehaald. Azure Backup ondersteunt back-ups van Vm's met twee typen Storage Service Encryption:<li> **SSE met door het platform beheerde sleutels**: deze versleuteling is standaard voor alle schijven in uw vm's. Meer informatie vindt u [hier](../virtual-machines/disk-encryption.md#platform-managed-keys).<li> **SSE met door de klant beheerde sleutels**. Met CMK beheert u de sleutels die worden gebruikt om de schijven te versleutelen. Meer informatie vindt u [hier](../virtual-machines/disk-encryption.md#customer-managed-keys). | Azure Backup maakt gebruik van SSE voor op reste versleuteling van virtuele Azure-machines.
**Azure Disk Encryption** | Azure Disk Encryption versleutelt zowel besturings systeem-als gegevens schijven voor virtuele Azure-machines.<br/><br/> Azure Disk Encryption kan worden geïntegreerd met BitLocker-versleutelings sleutels (BEKs), die worden beveiligd in een sleutel kluis als geheimen. Azure Disk Encryption is ook geïntegreerd met Azure Key Vault Key Encryption Keys (KEKs). | Azure Backup ondersteunt back-ups van beheerde en onbeheerde Azure-Vm's die alleen met BEKs zijn versleuteld, of met BEKs samen met KEKs.<br/><br/> Zowel BEKs als KEKs worden van een back-up gemaakt en versleuteld.<br/><br/> Als er een back-up van KEKs en BEKs wordt gemaakt, kunnen gebruikers met de vereiste machtigingen sleutels en geheimen terug naar de sleutel kluis herstellen als dat nodig is. Deze gebruikers kunnen ook de versleutelde VM herstellen.<br/><br/> Versleutelde sleutels en geheimen kunnen niet worden gelezen door onbevoegde gebruikers of Azure.

Voor beheerde en onbeheerde Azure-Vm's ondersteunt back-ups beide Vm's die zijn versleuteld met alleen BEKs of Vm's die zijn versleuteld met BEKs, samen met KEKs.

De back-up-BEKs (geheimen) en KEKs (sleutels) zijn versleuteld. Ze kunnen alleen worden gelezen en gebruikt wanneer ze terug worden teruggezet naar de sleutel kluis door gemachtigde gebruikers. Niet-geautoriseerde gebruikers of Azure kunnen geen back-upsleutels of geheimen lezen of gebruiken.

Er wordt ook een back-up gemaakt van BEKs. Als de BEKs verloren zijn gegaan, kunnen geautoriseerde gebruikers dus de BEKs herstellen naar de sleutel kluis en de versleutelde Vm's herstellen. Alleen gebruikers met het benodigde machtigings niveau kunnen back-ups maken van versleutelde Vm's, sleutels en geheimen en deze herstellen.

## <a name="snapshot-creation"></a>Maken van momentopnamen

Azure Backup maakt moment opnamen volgens het back-upschema.

- **Windows-vm's:** Voor Windows-Vm's coördineert de back-upservice met VSS voor het maken van een app-consistente moment opname van de VM-schijven.  Azure Backup maakt standaard gebruik van een volledige VSS-back-up (de logboeken van de toepassing worden afgekapt, zoals SQL Server op het moment van de back-up om consistente back-ups op toepassings niveau op te halen).  Als u een SQL Server-Data Base gebruikt in een back-up van Azure VM, kunt u de instelling wijzigen voor het maken van een VSS Copy-back-up (om logboeken te behouden). Zie [dit artikel](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues)voor meer informatie.

- **Virtuele Linux-machines:** Als u app-consistente moment opnamen van virtuele Linux-machines wilt maken, gebruikt u het Linux pre-script en post-script-Framework om uw eigen aangepaste scripts te schrijven om consistentie te garanderen.

  - Azure Backup roept alleen de vooraf/post-scripts aan die door u zijn geschreven.
  - Als de pre-scripts en post-scripts met succes worden uitgevoerd, Azure Backup het herstel punt als toepassings consistent gemarkeerd. Wanneer u echter aangepaste scripts gebruikt, bent u uiteindelijk verantwoordelijk voor de consistentie van de toepassing.
  - Meer [informatie](backup-azure-linux-app-consistent.md) over het configureren van scripts.

## <a name="snapshot-consistency"></a>Consistentie van moment opnamen

In de volgende tabel ziet u de verschillende soorten consistentie van moment opnamen:

**Snapshot** | **Details** | **Herstel** | **Overweging**
--- | --- | --- | ---
**Toepassings consistent** | Met app-consistente back-ups worden geheugen inhoud en I/O-bewerkingen in behandeling vastgelegd. App-consistente moment opnamen maken gebruik van een VSS Writer (of pre/post scripts voor Linux) om de consistentie van de app-gegevens te garanderen voordat een back-up wordt uitgevoerd. | Wanneer u een virtuele machine herstelt met een app-consistente moment opname, wordt de VM opgestart. Er zijn geen gegevens beschadiging of-verlies. De apps beginnen met een consistente status. | Windows: alle VSS-schrijvers zijn geslaagd<br/><br/> Linux: vóór/post-scripts zijn geconfigureerd en geslaagd
**Bestands systeem consistent** | Bestandssysteem consistente back-ups bieden consistentie door een moment opname van alle bestanden tegelijk te maken.<br/><br/> | Wanneer u een virtuele machine herstelt met een bestandssysteem consistente moment opname, wordt de VM opgestart. Er zijn geen gegevens beschadiging of-verlies. Apps moeten hun eigen mechanisme ' herstellen ' implementeren om ervoor te zorgen dat de herstelde gegevens consistent zijn. | Windows: sommige VSS-schrijvers zijn mislukt <br/><br/> Linux: standaard (als er vooraf/post-scripts niet zijn geconfigureerd of mislukt)
**Crash-consistent** | Crash-consistente moment opnamen doen zich meestal voor als een Azure-VM wordt afgesloten op het moment van de back-up. Alleen de gegevens die al op de schijf aanwezig zijn op het moment van de back-up worden vastgelegd en er een back-up van wordt gemaakt. | Begint met het opstart proces van de virtuele machine, gevolgd door een schijf controle om beschadigings fouten op te lossen. Gegevens in het geheugen of schrijf bewerkingen die niet naar de schijf zijn overgedragen voordat de crash is verbroken. Apps implementeren hun eigen gegevens verificatie. Een Data Base-app kan bijvoorbeeld het transactie logboek voor verificatie gebruiken. Als het transactie logboek vermeldingen bevat die zich niet in de data base bevinden, worden trans acties teruggedraaid door de data base software totdat de gegevens consistent zijn. | De virtuele machine is afgesloten (status gestopt/toegewezen).

>[!NOTE]
> Als de inrichtings status is **geslaagd**, neemt Azure backup bestandssysteem consistente back-ups. Als de inrichtings status niet **beschikbaar** of **mislukt** is, worden er crash-consistente back-ups gemaakt. Als de inrichtings status **maakt** of **verwijdert**, betekent dit dat Azure backup de bewerkingen opnieuw probeert uit te voeren.

## <a name="backup-and-restore-considerations"></a>Overwegingen voor back-up en herstel

**Overweging** | **Details**
--- | ---
**Schijf** | Back-ups van VM-schijven zijn parallel. Als een virtuele machine bijvoorbeeld vier schijven heeft, probeert de back-upservice tegelijkertijd een back-up te maken van alle vier de schijven. De back-up is incrementeel (alleen gewijzigde gegevens).
**Planning** |  Als u het back-upverkeer wilt verminderen, maakt u op verschillende tijdstippen van de dag een back-up van verschillende Vm's en zorgt u ervoor dat de tijden niet overlappen. Als op hetzelfde moment back-ups worden gemaakt van VM's, treden er netwerkproblemen op.
**Back-ups voorbereiden** | Houd de tijd die nodig is om de back-up voor te bereiden. De voorbereidingstijd omvat het installeren of bijwerken van de back-upextensie en het activeren van een schaduwkopie volgens het back-upschema.
**Gegevensoverdracht** | Bedenk de tijd die nodig is om Azure Backup de incrementele wijzigingen van de vorige back-up te identificeren.<br/><br/> In een incrementele back-up worden de wijzigingen door Azure Backup bepaald door de controlesom van het blok te berekenen. Als een blok wordt gewijzigd, wordt het gemarkeerd voor overdracht naar de kluis. De service analyseert de geïdentificeerde blokken om de hoeveelheid gegevens die moet worden overgedragen, verder te minimaliseren. Nadat alle gewijzigde blokken zijn geëvalueerd, worden de wijzigingen in de kluis door Azure Backup overgedragen.<br/><br/> Er kan een vertraging optreden tussen het maken van de schaduwkopie en het kopiëren ervan naar de kluis. Het kan Maxi maal acht uur duren voordat de moment opnamen worden overgebracht naar de kluis. De back-uptijd voor een virtuele machine is minder dan 24 uur voor de dagelijkse back-up.
**Eerste back-up** | Hoewel de totale back-uptijd voor incrementele back-ups minder dan 24 uur is, is dit mogelijk niet het geval voor de eerste back-up. De tijd die nodig is om de initiële back-up te maken, is afhankelijk van de grootte van de gegevens en wanneer de back-up wordt verwerkt.
**Wachtrij herstellen** | Met Azure Backup worden herstel taken van meerdere opslag accounts tegelijk verwerkt en worden herstel aanvragen in een wachtrij geplaatst.
**Kopie terugzetten** | Tijdens het herstel proces worden gegevens van de kluis naar het opslag account gekopieerd.<br/><br/> De totale herstel tijd is afhankelijk van de I/O-bewerkingen per seconde (IOPS) en de door Voer van het opslag account.<br/><br/> Als u de Kopieer tijd wilt beperken, selecteert u een opslag account dat niet is geladen met andere schrijf bewerkingen en lees bewerkingen van de toepassing.

### <a name="backup-performance"></a>Back-upprestaties

Deze algemene scenario's kunnen van invloed zijn op de totale back-uptijd:

- **Een nieuwe schijf toevoegen aan een beveiligde Azure-VM:** Als een virtuele machine incrementele back-up ondergaat en er een nieuwe schijf wordt toegevoegd, neemt de back-uptijd toe. De totale back-uptijd kan langer dan 24 uur duren vanwege de initiële replicatie van de nieuwe schijf, samen met de Delta replicatie van bestaande schijven.
- **Gefragmenteerde schijven:** Back-upbewerkingen zijn sneller wanneer de schijf wijzigingen aaneengesloten zijn. Als de wijzigingen zijn verdeeld over en gefragmenteerd op een schijf, wordt de back-up langzamer.
- **Schijf verloop:** Als beveiligde schijven die een incrementele back-up uitvoeren, een dagelijks verloop hebben van meer dan 200 GB, kan het maken van een back-up lang duren (meer dan acht uur).
- **Back-upversies:** De meest recente versie van de back-up (ook wel bekend als de versie voor direct terugzetten) maakt gebruik van een meer geoptimaliseerd proces dan de controlesom vergelijking voor het identificeren van wijzigingen. Maar als u direct terugzetten gebruikt en een back-upmomentopname hebt verwijderd, worden de back-upswitches naar controlesom vergelijking. In dit geval wordt de back-upbewerking langer dan 24 uur (of mislukken).

### <a name="restore-performance"></a>Prestaties herstellen

Deze algemene scenario's kunnen van invloed zijn op de totale herstel tijd:

- De totale herstel tijd is afhankelijk van de invoer/uitvoer-bewerkingen per seconde (IOPS) en de door Voer van het opslag account.
- De totale herstel tijd kan worden beïnvloed als het doel-opslag account is geladen met andere Lees-en schrijf bewerkingen van toepassingen. Als u de herstel bewerking wilt verbeteren, selecteert u een opslag account dat niet is geladen met andere toepassings gegevens.

## <a name="best-practices"></a>Aanbevolen procedures

Wanneer u back-ups van VM's configureert, wordt u aangeraden deze procedures te volgen:

- Wijzig de standaardplanningstijden die in een beleid zijn ingesteld. Als de standaardtijd in het beleid bijvoorbeeld 12:00 uur is, verhoogt u de timing met enkele minuten zodat de resources optimaal worden benut.
- Als u virtuele machines herstelt vanuit één kluis, raden we u ten zeerste aan om andere [v2-opslag accounts voor algemeen](../storage/common/storage-account-upgrade.md) gebruik te gebruiken om ervoor te zorgen dat het doel-opslag account niet wordt beperkt. Elke virtuele machine moet bijvoorbeeld een ander opslag account hebben. Als er bijvoorbeeld tien Vm's zijn teruggezet, gebruikt u 10 verschillende opslag accounts.
- Voor het maken van back-ups van virtuele machines die gebruikmaken van Premium Storage met direct terugzetten, raden we aan om *50%* beschik bare ruimte toe te wijzen aan de totale toegewezen opslag ruimte. Dit is **alleen** vereist voor de eerste back-up. De 50% beschik bare ruimte is geen vereiste voor back-ups nadat de eerste back-up is voltooid.
- De limiet voor het aantal schijven per opslagaccount is relatief ten opzichte van de mate van toegang tot de schijven door toepassingen die worden uitgevoerd op een IaaS-VM (infrastructure as a service). Als er op één opslagaccount vijf tot tien schijven of meer aanwezig zijn, geldt als vuistregel dat de belasting kan worden verdeeld door sommige schijven naar afzonderlijke opslagaccounts te verplaatsen.

## <a name="backup-costs"></a>Back-upkosten

Voor Azure-Vm's waarvoor een back-up is gemaakt met Azure Backup gelden [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/).

De facturering begint pas nadat de eerste geslaagde back-up is voltooid. Op dit moment begint de facturering voor zowel opslag-als beveiligde Vm's. De facturering blijft zo lang dat back-upgegevens voor de virtuele machine worden opgeslagen in een kluis. Als u de beveiliging voor een virtuele machine stopt, maar er in een kluis back-upgegevens voor de VM bestaan, wordt de facturering voortgezet.

Facturering voor een opgegeven virtuele machine stopt alleen als de beveiliging is gestopt en alle back-upgegevens worden verwijderd. Wanneer de beveiliging wordt gestopt en er geen actieve back-uptaken zijn, wordt de grootte van de laatste geslaagde back-up van de virtuele machine de beveiligde instantie grootte die wordt gebruikt voor de maandelijkse factuur.

De berekening van de grootte van beveiligde instanties is gebaseerd op de *werkelijke* grootte van de virtuele machine. De grootte van de virtuele machine is de som van alle gegevens in de virtuele machine, met uitzonde ring van de tijdelijke opslag. Prijzen zijn gebaseerd op de werkelijke gegevens die op de gegevens schijven zijn opgeslagen, niet op de Maxi maal ondersteunde grootte voor elke gegevens schijf die is gekoppeld aan de virtuele machine.

Op dezelfde manier is de factuur voor back-upopslag gebaseerd op de hoeveelheid gegevens die is opgeslagen in Azure Backup, wat de som is van de werkelijke gegevens in elk herstel punt.

Neem bijvoorbeeld een VM met a2-standaard met twee extra gegevens schijven met een maximale grootte van 32 TB. In de volgende tabel ziet u de werkelijke gegevens die op elk van deze schijven zijn opgeslagen:

**Schijf** | **Maximale grootte** | **Werkelijke gegevens aanwezig**
--- | --- | ---
Besturingssysteemschijf | 32 TB | 17 GB
Lokale/tijdelijke schijf | 135 GB | 5 GB (niet inbegrepen voor back-up)
Gegevens schijf 1 | 32 TB| 30 GB
Gegevens schijf 2 | 32 TB | 0 GB

De werkelijke grootte van de virtuele machine in dit geval 17 GB + 30 GB + 0 GB = 47 GB. Deze beveiligde-instantie grootte (47 GB) wordt de basis voor de maandelijkse factuur. Naarmate de hoeveelheid gegevens in de virtuele machine groeit, wordt de grootte van de beveiligde instantie die wordt gebruikt voor het aanpassen van de facturering, gewijzigd.

## <a name="next-steps"></a>Volgende stappen

- [Bereid u voor op Azure VM-back-up](backup-azure-arm-vms-prepare.md).