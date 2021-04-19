---
title: Over Azure VM Backup
description: In dit artikel leert u hoe de service Azure Backup back-up van virtuele Azure-machines en hoe u de best practices volgt.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 5ce76f64093bab362d62afcc3f94d07f7ee7883d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718445"
---
# <a name="an-overview-of-azure-vm-backup"></a>Een overzicht van back-ups van azure-VM's

In dit artikel wordt beschreven hoe [de Azure Backup back-up van](./backup-overview.md) virtuele Azure-machines (VM's) kan maken.

Azure Backup biedt onafhankelijke en geïsoleerde back-ups om bescherming te bieden tegen onbedoelde vernietiging van de gegevens op uw VM's. Back-ups worden in een Recovery Services-kluis opgeslagen waarin het beheer van herstelpunten is ingebouwd. Het is eenvoudig om te configureren en de schaal aan te passen, back-ups worden geoptimaliseerd en u kunt, indien nodig, eenvoudig herstelbewerkingen uitvoeren.

Als onderdeel van het back-upproces wordt een momentopname gemaakt [en](#snapshot-creation)worden de gegevens overgebracht naar de Recovery Services-kluis zonder dat dit van invloed is op productieworkloads. De momentopname biedt verschillende consistentieniveaus, zoals hier wordt [beschreven.](#snapshot-consistency)

Azure Backup heeft ook speciale aanbiedingen voor databaseworkloads zoals [SQL Server](backup-azure-sql-database.md) en [SAP HANA](sap-hana-db-about.md) die werkbelastingbewust zijn, een RPO van 15 minuten bieden (recovery point objective) en back-up en herstel van afzonderlijke databases toestaan.

## <a name="backup-process"></a>Back-upproces

Hier volgt een beschrijving van de manier waarop in Azure Backup een back-up voor virtuele Azure-machines wordt uitgevoerd:

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="encryption-of-azure-vm-backups"></a>Versleuteling van back-ups van azure-VM's

Wanneer u een back-up van Virtuele Azure-Azure Backup, worden VM's in rust versleuteld met Storage Service Encryption (SSE). Azure Backup kunt ook back-up maken van Virtuele Azure-VM's die zijn versleuteld met behulp van Azure Disk Encryption.

**Versleuteling** | **Details** | **Ondersteuning**
--- | --- | ---
**Sse** | Met SSE biedt Azure Storage versleuteling in rust door gegevens automatisch te versleutelen voordat deze worden opgeslagen. Azure Storage ontsleutelt ook gegevens voordat deze worden opgehaald. Azure Backup ondersteunt back-ups van VM's met twee typen Storage Service Encryption:<li> **SSE met door het platform beheerde sleutels:** deze versleuteling is standaard voor alle schijven in uw VM's. Kijk hier [voor meer informatie.](../virtual-machines/disk-encryption.md#platform-managed-keys)<li> **SSE met door de klant beheerde sleutels**. Met CMK beheert u de sleutels die worden gebruikt om de schijven te versleutelen. Kijk hier [voor meer informatie.](../virtual-machines/disk-encryption.md#customer-managed-keys) | Azure Backup maakt gebruik van SSE voor versleuteling van azure-VM's in rust.
**Azure Disk Encryption** | Azure Disk Encryption versleutelt zowel besturingssysteem- als gegevensschijven voor Azure-VM's.<br/><br/> Azure Disk Encryption kan worden geïntegreerd met BitLocker-versleutelingssleutels (BEK's), die als geheimen in een sleutelkluis worden beschermd. Azure Disk Encryption kan ook worden geïntegreerd met Azure Key Vault key encryption keys (KKE's). | Azure Backup ondersteunt back-ups van beheerde en niet-beheerde Azure-VM's die alleen zijn versleuteld met BEK's, of met BEK's samen met KK's.<br/><br/> Er wordt een back-up van zowel BEK's als KK's opgeslagen en versleuteld.<br/><br/> Omdat er een back-up wordt van KLUIZEN en BEK's, kunnen gebruikers met de benodigde machtigingen sleutels en geheimen zo nodig terugzetten naar de sleutelkluis. Deze gebruikers kunnen ook de versleutelde VM herstellen.<br/><br/> Versleutelde sleutels en geheimen kunnen niet worden gelezen door onbevoegde gebruikers of door Azure.

Voor beheerde en niet-beheerde Azure-VM's ondersteunt Backup beide VM's die alleen zijn versleuteld met BEK's of VM's die zijn versleuteld met BEK's samen met KK's.

De bek's (geheimen) en KKE's (sleutels) met een back-up worden versleuteld. Ze kunnen alleen worden gelezen en gebruikt wanneer ze door gemachtigde gebruikers worden hersteld naar de sleutelkluis. Niet-geautoriseerde gebruikers, of Azure, kunnen geen back-upsleutels of geheimen lezen of gebruiken.

Er wordt ook een back-up van BEK's gebruikt. Dus als de BEK's verloren gaan, kunnen gemachtigde gebruikers de BEK's herstellen naar de sleutelkluis en de versleutelde VM's herstellen. Alleen gebruikers met het benodigde machtigingsniveau kunnen een back-up maken van versleutelde VM's of sleutels en geheimen en deze herstellen.

## <a name="snapshot-creation"></a>Maken van momentopnamen

Azure Backup maakt momentopnamen volgens het back-upschema.

- **Windows-VM's:** Voor Windows-VM's coördineert de Backup-service vss om een app-consistente momentopname van de VM-schijven te maken.  Standaard maakt Azure Backup een volledige VSS-back-up (de logboeken van de toepassing, zoals SQL Server op het moment van back-up, worden afgekapt om een consistente back-up op toepassingsniveau te krijgen).  Als u een back-updatabase SQL Server azure-VM gebruikt, kunt u de instelling wijzigen om een back-up van VSS Copy te maken (om logboeken te behouden). Raadpleeg [dit artikel](./backup-azure-vms-troubleshoot.md#troubleshoot-vm-snapshot-issues) voor meer informatie.

- **Linux-VM's:** Als u app-consistente momentopnamen van virtuele Linux-VM's wilt maken, gebruikt u het Framework voor linux-prescripts en postscripts om uw eigen aangepaste scripts te schrijven om consistentie te garanderen.

  - Azure Backup roept alleen de scripts aan die vooraf/achteraf door u zijn geschreven.
  - Als de scripts vóór en na de scripts goed worden uitgevoerd, Azure Backup herstelpunt als toepassings-consistent. Wanneer u echter aangepaste scripts gebruikt, bent u uiteindelijk verantwoordelijk voor de consistentie van de toepassing.
  - [Meer informatie over](backup-azure-linux-app-consistent.md) het configureren van scripts.

## <a name="snapshot-consistency"></a>Consistentie van momentopnamen

In de volgende tabel worden de verschillende typen consistentie van momentopnamen uitgelegd:

**Momentopname** | **Details** | **Herstel** | **Overweging**
--- | --- | --- | ---
**Toepassings-consistent** | App-consistente back-ups leggen geheugeninhoud en I/O-bewerkingen vast die in behandeling zijn. App-consistente momentopnamen maken gebruik van een VSS-schrijver (of pre-/post-scripts voor Linux) om de consistentie van de app-gegevens te garanderen voordat er een back-up wordt gemaakt. | Wanneer u een VM herstelt met een app-consistente momentopname, wordt de VM opgebootsd. Er is geen beschadiging of verlies van gegevens. De apps starten in een consistente status. | Windows: Alle VSS-schrijvers zijn geslaagd<br/><br/> Linux: scripts vooraf/achteraf zijn geconfigureerd en geslaagd
**Bestandssysteem consistent** | Bestandssysteemconsistente back-ups bieden consistentie door tegelijkertijd een momentopname van alle bestanden te maken.<br/><br/> | Wanneer u een VM herstelt met een bestandssysteem-consistente momentopname, wordt de VM opnieuw opgebootsd. Er is geen beschadiging of verlies van gegevens. Apps moeten hun eigen mechanisme voor het oplossen van problemen implementeren om ervoor te zorgen dat herstelde gegevens consistent zijn. | Windows: Sommige VSS Writers zijn mislukt <br/><br/> Linux: standaard (als scripts vooraf/achteraf niet zijn geconfigureerd of mislukt)
**Crash-consistent** | Crash-consistente momentopnamen treden doorgaans op als een azure-VM wordt afgesloten op het moment van back-up. Alleen de gegevens die op het moment van de back-up al op de schijf bestaan, worden vastgelegd en er wordt een back-up van gemaakt. | Begint met het opstartproces van de VM, gevolgd door een schijfcontrole om beschadigingsfouten op te lossen. Alle in-memory gegevens of schrijfbewerkingen die niet zijn overgedragen naar de schijf voordat de crash verloren gaat. Apps implementeren hun eigen gegevensverificatie. Een database-app kan bijvoorbeeld het transactielogboek gebruiken voor verificatie. Als het transactielogboek vermeldingen bevat die zich niet in de database voorkomen, worden transacties door de databasesoftware terug worpen totdat de gegevens consistent zijn. | De VM heeft de status Afsluiten (gestopt/toewijzing is beëindigd).

>[!NOTE]
> Als de inrichtingstoestand is **geslaagd,** maakt Azure Backup back-ups die consistent zijn met het bestandssysteem. Als de inrichtingstoestand niet **beschikbaar** is of **mislukt,** worden crash-consistente back-ups gemaakt. Als de inrichtingstoestand wordt **aan-** of **verwijderd,** betekent Azure Backup dat de bewerkingen opnieuw worden uitgevoerd.

## <a name="backup-and-restore-considerations"></a>Overwegingen voor back-up en herstel

**Overweging** | **Details**
--- | ---
**Schijf** | Back-up van VM-schijven is parallel. Als een VM bijvoorbeeld vier schijven heeft, probeert de Backup-service gelijktijdig een back-up te maken van alle vier de schijven. Back-up is incrementeel (alleen gewijzigde gegevens).
**Planning** |  Als u het back-upverkeer wilt verminderen, maakt u op verschillende tijdstippen van de dag een back-up van verschillende VM's en zorgt u ervoor dat de tijden niet overlappen. Als op hetzelfde moment back-ups worden gemaakt van VM's, treden er netwerkproblemen op.
**Back-ups voorbereiden** | Houd rekening met de tijd die nodig is om de back-up voor te bereiden. De voorbereidingstijd omvat het installeren of bijwerken van de back-upextensie en het activeren van een schaduwkopie volgens het back-upschema.
**Gegevensoverdracht** | Houd rekening met de tijd die nodig Azure Backup om de incrementele wijzigingen ten opzichte van de vorige back-up te identificeren.<br/><br/> In een incrementele back-up worden de wijzigingen door Azure Backup bepaald door de controlesom van het blok te berekenen. Als een blok wordt gewijzigd, wordt het gemarkeerd voor overdracht naar de kluis. De service analyseert de geïdentificeerde blokken om de hoeveelheid gegevens die moet worden overgedragen, verder te minimaliseren. Nadat alle gewijzigde blokken zijn geëvalueerd, worden de wijzigingen in de kluis door Azure Backup overgedragen.<br/><br/> Er kan een vertraging optreden tussen het maken van de schaduwkopie en het kopiëren ervan naar de kluis. Tijdens piekmomenten kan het tot acht uur duren voordat de momentopnamen naar de kluis zijn overgebracht. De back-uptijd voor een virtuele machine is minder dan 24 uur voor de dagelijkse back-up.
**Eerste back-up** | Hoewel de totale back-uptijd voor incrementele back-ups minder dan 24 uur is, is dit mogelijk niet het geval voor de eerste back-up. De tijd die nodig is om de initiële back-up te maken, is afhankelijk van de grootte van de gegevens en wanneer de back-up wordt verwerkt.
**Wachtrij herstellen** | Azure Backup hersteltaken van meerdere opslagaccounts tegelijk verwerkt en herstelaanvragen in een wachtrij geplaatst.
**Kopie herstellen** | Tijdens het herstelproces worden gegevens gekopieerd van de kluis naar het opslagaccount.<br/><br/> De totale hersteltijd is afhankelijk van de I/O-bewerkingen per seconde (IOPS) en de doorvoer van het opslagaccount.<br/><br/> Als u de kopieertijd wilt verminderen, selecteert u een opslagaccount dat niet is geladen met andere schrijf- en lees- en schrijfgegevens van de toepassing.

### <a name="backup-performance"></a>Back-upprestaties

Deze veelvoorkomende scenario's kunnen van invloed zijn op de totale back-uptijd:

- **Een nieuwe schijf toevoegen aan een beveiligde azure-VM:** Als een VM een incrementele back-up ondergaat en er een nieuwe schijf wordt toegevoegd, neemt de back-uptijd toe. De totale back-uptijd kan langer dan 24 uur duren vanwege de initiële replicatie van de nieuwe schijf, samen met de deltareplicatie van bestaande schijven.
- **Gefragmenteerde schijven:** Back-upbewerkingen zijn sneller wanneer schijfwijzigingen aaneengesloten zijn. Als wijzigingen worden verspreid en gefragmenteerd over een schijf, is de back-up langzamer.
- **Schijfverloop:** Als beveiligde schijven die een incrementele back-up ondergaan een dagelijks verloop van meer dan 200 GB hebben, kan het maken van een back-up lang duren (meer dan acht uur).
- **Back-upversies:** De meest recente versie van Backup (ook wel de versie voor direct herstellen genoemd) maakt gebruik van een geoptimaliseerder proces dan de controlesumvergelijking voor het identificeren van wijzigingen. Maar als u direct herstellen gebruikt en een back-upmomentopname hebt verwijderd, schakelt de back-up over naar de vergelijking van de controlesum. In dit geval duurt de back-upbewerking langer dan 24 uur (of mislukt deze).

### <a name="restore-performance"></a>Prestaties herstellen

Deze veelvoorkomende scenario's kunnen van invloed zijn op de totale hersteltijd:

- De totale hersteltijd is afhankelijk van de invoer-/uitvoerbewerkingen per seconde (IOPS) en de doorvoer van het opslagaccount.
- De totale hersteltijd kan worden beïnvloed als het doelopslagaccount wordt geladen met andere lees- en schrijfbewerkingen van de toepassing. Als u de herstelbewerking wilt verbeteren, selecteert u een opslagaccount dat niet is geladen met andere toepassingsgegevens.

## <a name="best-practices"></a>Aanbevolen procedures

Wanneer u back-ups van VM's configureert, wordt u aangeraden deze procedures te volgen:

- Wijzig de standaardplanningstijden die in een beleid zijn ingesteld. Als de standaardtijd in het beleid bijvoorbeeld 12:00 uur is, verhoogt u de timing met enkele minuten zodat de resources optimaal worden benut.
- Als u VM's vanuit één kluis herstelt, raden we u ten zeerste aan verschillende [v2-opslagaccounts](../storage/common/storage-account-upgrade.md) voor algemeen gebruik te gebruiken om ervoor te zorgen dat het doelopslagaccount niet wordt beperkt. Elke VM moet bijvoorbeeld een ander opslagaccount hebben. Als er bijvoorbeeld 10 VM's worden hersteld, gebruikt u 10 verschillende opslagaccounts.
- Voor back-ups van VM's die gebruikmaken van Premium Storage met Direct terugzetten, raden we  u aan *50%* vrije ruimte toe te wijzen van de totale toegewezen opslagruimte, wat alleen nodig is voor de eerste back-up. De 50% vrije ruimte is geen vereiste voor back-ups nadat de eerste back-up is voltooid
- De limiet voor het aantal schijven per opslagaccount is relatief ten opzichte van de mate van toegang tot de schijven door toepassingen die worden uitgevoerd op een IaaS-VM (infrastructure as a service). Als er op één opslagaccount vijf tot tien schijven of meer aanwezig zijn, geldt als vuistregel dat de belasting kan worden verdeeld door sommige schijven naar afzonderlijke opslagaccounts te verplaatsen.
- Als u VM's met beheerde schijven wilt herstellen met behulp van PowerShell, geeft u de aanvullende parameter ***TargetResourceGroupName*** op om de resourcegroep op te geven waarop beheerde schijven worden hersteld. Meer informatie vindt u [hier.](./backup-azure-vms-automation.md#restore-managed-disks)

## <a name="backup-costs"></a>Back-upkosten

Voor azure-VM's die een back-up Azure Backup, gelden [Azure Backup prijzen.](https://azure.microsoft.com/pricing/details/backup/)

De facturering begint pas als de eerste geslaagde back-up is gemaakt. Op dit punt begint de facturering voor zowel opslag- als beveiligde VM's. De facturering wordt voortgezet zolang back-upgegevens voor de VM worden opgeslagen in een kluis. Als u de beveiliging voor een VM stopt, maar er back-upgegevens voor de VM aanwezig zijn in een kluis, wordt de facturering voortgezet.

Facturering voor een opgegeven VM stopt alleen als de beveiliging is gestopt en alle back-upgegevens worden verwijderd. Wanneer de beveiliging stopt en er geen actieve back-uptaken zijn, wordt de grootte van de laatste geslaagde VM-back-up de grootte van het beveiligde exemplaar dat wordt gebruikt voor de maandelijkse factuur.

De berekening van de grootte van het beveiligde exemplaar is gebaseerd op de *werkelijke* grootte van de VM. De grootte van de VM is de som van alle gegevens in de VM, met uitzondering van de tijdelijke opslag. Prijzen zijn gebaseerd op de werkelijke gegevens die zijn opgeslagen op de gegevensschijven, niet op de maximaal ondersteunde grootte voor elke gegevensschijf die is gekoppeld aan de VM.

Op dezelfde manier is de factuur voor back-upopslag gebaseerd op de hoeveelheid gegevens die is opgeslagen in Azure Backup. Dit is de som van de werkelijke gegevens in elk herstelpunt.

Neem bijvoorbeeld een VM van A2 Standard met twee extra gegevensschijven met een maximale grootte van elk 32 TB. In de volgende tabel ziet u de werkelijke gegevens die op elk van deze schijven zijn opgeslagen:

**Schijf** | **Maximale grootte** | **Werkelijke gegevens aanwezig**
--- | --- | ---
Besturingssysteemschijf | 32 TB | 17 GB
Lokale/tijdelijke schijf | 135 GB | 5 GB (niet inbegrepen voor back-up)
Gegevensschijf 1 | 32 TB| 30 GB
Gegevensschijf 2 | 32 TB | 0 GB

De werkelijke grootte van de VM is in dit geval 17 GB + 30 GB + 0 GB = 47 GB. Deze grootte van het beveiligde exemplaar (47 GB) wordt de basis voor de maandelijkse factuur. Naarmate de hoeveelheid gegevens in de VM groeit, komt de grootte van het beveiligde exemplaar dat wordt gebruikt voor factureringswijzigingen overeen.

## <a name="next-steps"></a>Volgende stappen

- [Bereid u voor op Back-up van Azure-VM.](backup-azure-arm-vms-prepare.md)