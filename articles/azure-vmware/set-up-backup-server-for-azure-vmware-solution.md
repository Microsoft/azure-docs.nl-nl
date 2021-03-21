---
title: Azure Backup Server voor de Azure VMware-oplossing instellen
description: Stel uw Azure VMware-oplossings omgeving in om een back-up te maken van virtuele machines met behulp van Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: e9204b9f86c7e9ef67d2e3d6b45ccf4248d00b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581479"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Azure Backup Server voor de Azure VMware-oplossing instellen

Azure Backup Server draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR). Met de Azure VMware-oplossing kunt u een back-up van een virtuele machine (VM) op basis van Azure Backup Server alleen configureren. 

Azure Backup Server kunnen back-upgegevens opslaan naar:

- **Schijf**: voor kortetermijnbeveiliging-opslag Azure backup server back-ups maken van gegevens naar schijf groepen.
- **Azure**: voor opslag op korte termijn en lange termijn kunnen Azure backup server gegevens die in schijf groepen zijn opgeslagen, met behulp van Azure backup worden gemaakt met de Microsoft Azure Cloud.

Gebruik Azure Backup Server om gegevens te herstellen naar de bron of een andere locatie. Op die manier kunt u, als de oorspronkelijke gegevens niet beschikbaar zijn vanwege geplande of onverwachte problemen, gegevens op een andere locatie herstellen.

Dit artikel helpt u om uw Azure VMware-oplossings omgeving voor te bereiden op back-ups van virtuele machines met behulp van Azure Backup Server. U wordt stapsgewijs begeleid bij de volgende stappen: 

> [!div class="checklist"]
> * Bepaal het aanbevolen type schijf en de grootte van de virtuele machine die u wilt gebruiken.
> * Maak een Recovery Services kluis waarin de herstel punten worden opgeslagen.
> * Stel de opslag replicatie voor een Recovery Services kluis in.
> * Voeg opslag toe aan Azure Backup Server.

## <a name="supported-vmware-features"></a>Ondersteunde VMware-functies

- **Back-up zonder agent:** Azure Backup Server geen agent moet worden geïnstalleerd op de vCenter-of ESXi-server om een back-up van de VM te maken. Geef in plaats daarvan alleen het IP-adres of de Fully Qualified Domain Name (FQDN) op en de aanmeldings referenties die worden gebruikt voor het verifiëren van de VMware-Server met Azure Backup Server.
- **Met de Cloud geïntegreerde back-up:** Azure Backup Server beveiligt workloads op schijf en in de Cloud. De werk stroom voor back-up en herstel van Azure Backup Server helpt u bij het beheren van lange termijn retentie en externe back-ups.
- **Vm's detecteren en beveiligen die worden beheerd door vCenter:** Azure Backup Server detecteert en beveiligt Vm's die op een vCenter-of ESXi-server zijn geïmplementeerd. Azure Backup Server detecteert ook Vm's die worden beheerd door vCenter, zodat u grote implementaties kunt beveiligen.
- **Automatische beveiliging op mapniveau:** met vCenter kunt u uw virtuele machines in VM-mappen ordenen. Azure Backup Server deze mappen worden gedetecteerd. U kunt deze gebruiken voor het beveiligen van Vm's op mapniveau, inclusief alle submappen. Bij het beveiligen van mappen beschermt Azure Backup Server de virtuele machines in die map en beveiligt u de virtuele machines die later worden toegevoegd. Azure Backup Server detecteert dagelijks nieuwe Vm's en beveiligt deze automatisch. Bij het indelen van uw Vm's in recursieve mappen, Azure Backup Server automatisch detecteert en beveiligt de nieuwe Vm's die in de recursieve mappen zijn geïmplementeerd.
- **Azure backup server blijft VMotioned vm's in het cluster beveiligen:** Als Vm's vMotioned zijn voor taak verdeling binnen het cluster, detecteert Azure Backup Server automatisch de VM-beveiliging.
- **Nood zakelijke bestanden sneller herstellen:** Azure Backup Server kunt bestanden of mappen van een Windows-VM herstellen zonder de volledige VM te herstellen.

## <a name="limitations"></a>Beperkingen

- Update pakket 1 voor Azure Backup Server v3 moet zijn geïnstalleerd.
- U kunt geen back-up maken van moment opnamen van de gebruiker voor de eerste Azure Backup Server back-up. Nadat Azure Backup Server de eerste back-up hebt voltooid, kunt u back-ups maken van gebruikers momentopnamen.
- Azure Backup Server kan geen virtuele VMware-machines beveiligen met doorgangs schijven en fysieke onbewerkte apparaattoewijzing (pRDMs).
- Azure Backup Server kan VMware-vApps niet detecteren of beveiligen.

Als u Azure Backup Server voor de Azure VMware-oplossing wilt instellen, moet u de volgende stappen volt ooien:

- Stel de vereisten en omgeving in.
- Maak een Recovery Services-kluis.
- Down load en installeer Azure Backup Server.
- Voeg opslag toe aan Azure Backup Server.

### <a name="deployment-architecture"></a>Implementatie architectuur

Azure Backup Server wordt geïmplementeerd als een Azure-infra structuur als een service (IaaS) voor de beveiliging van virtuele machines met Azure VMware-oplossingen.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Azure Backup Server wordt geïmplementeerd als een Azure-infra structuur als een service (IaaS) voor de beveiliging van virtuele machines met Azure VMware-oplossingen." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Vereisten voor de Azure Backup Server omgeving

Bekijk de aanbevelingen in deze sectie wanneer u Azure Backup Server installeert in uw Azure-omgeving.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Zorg ervoor dat u [netwerken configureert voor uw VMware privécloud in azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-vm"></a>De grootte van de VM bepalen

Volg de instructies in de zelf studie [uw eerste Windows-VM maken in de Azure Portal](../virtual-machines/windows/quick-create-portal.md) .  U maakt de VM in het virtuele netwerk dat u in de vorige stap hebt gemaakt. Begin met een galerie afbeelding van Windows Server 2019 Data Center om de Azure Backup Server uit te voeren. 

De tabel geeft een overzicht van het maximum aantal beveiligde werk belastingen voor elke Azure Backup Server VM-grootte. De informatie is gebaseerd op interne prestatie- en schaaltests met canonieke waarden voor de grootte van de werkbelasting en het verloop. De werkelijke werkbelasting grootte kan groter zijn, maar moet worden aangepast door de schijven die zijn gekoppeld aan de Azure Backup Server VM.

| Maximum aantal beveiligde werk belastingen | Gemiddelde werkbelastingsgrootte | Gemiddeld werkbelastingsverloop (dagelijks) | Minimale opslag-IOPS | Aanbevolen schijf type/-grootte      | Aanbevolen VM-grootte |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Net 5% verloop                   | 2.000             | Standard-HDD (8 TB of hogere grootte per schijf)  | A4V2       |
| 40                      | 150 GB                | Net 10% verloop                  | 4.500             | Premium-SSD * (1 TB of hogere grootte per schijf) | DS3_V2     |
| 60                      | 200 GB                | Net 10% verloop                  | 10.500            | Premium-SSD * (8 TB of hogere grootte per schijf) | DS3_V2     |

* Gebruik Mini maal aanbevolen schijven om de vereiste IOPs op te halen. Schijven met een kleinere grootte bieden lagere IOPs.

> [!NOTE]
> Azure Backup Server is ontworpen om te worden uitgevoerd op een toegewezen server met één doel. U kunt Azure Backup Server niet installeren op een computer die:
> * Wordt uitgevoerd als een domein controller.
> * Is de functie toepassings server geïnstalleerd.
> * Is een System Center Operations Manager-beheer server.
> * Exchange Server wordt uitgevoerd.
> * Is een knoop punt van een cluster.

### <a name="disks-and-storage"></a>Schijven en opslag

Azure Backup Server vereist schijven voor installatie. 

| Vereiste                      | Aanbevolen grootte  |
|----------------------------------|-------------------------|
| Installatie van Azure Backup Server                | Installatie locatie: 3 GB<br />Station voor databasebestanden: 900 MB<br />Systeem station: 1 GB voor SQL Server installatie<br /><br />U hebt ook ruimte nodig om Azure Backup Server de bestands catalogus te kopiëren naar een tijdelijke installatie locatie wanneer u archiveert.      |
| Schijf voor opslaggroep<br />(Maakt gebruik van basis volumes, niet op een dynamische schijf) | Twee tot drie keer de beveiligde gegevens grootte.<br />Zie [DPM capacity planner](https://www.microsoft.com/download/details.aspx?id=54301)voor gedetailleerde opslag berekening.   |

Zie [een beheerde gegevens schijf koppelen aan een virtuele Windows-machine met behulp van de Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md)voor meer informatie over het koppelen van een nieuwe beheerde gegevens schijf aan een bestaande virtuele machine van Azure.

> [!NOTE]
> Een enkele Azure Backup Server heeft een zachte limiet van 120 TB voor de opslag groep.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Back-upgegevens opslaan op een lokale schijf en in azure

Het opslaan van back-upgegevens in azure vermindert de back-upinfrastructuur op de Azure Backup Server VM. Azure Backup Server back-upgegevens op Azure-schijven die zijn gekoppeld aan de VM, worden opgeslagen voor operationele herstel (back-up). Nadat de schijven en opslag ruimte aan de virtuele machine zijn gekoppeld, beheert Azure Backup Server de opslag voor u. De hoeveelheid opslag is afhankelijk van het aantal en de grootte van de schijven die zijn gekoppeld aan elke virtuele machine van Azure. Elke grootte van de virtuele machine van Azure heeft een maximum aantal schijven dat kan worden bijgevoegd. A2 is bijvoorbeeld vier schijven, a3 is acht schijven en A4 is 16 schijven. De grootte en het aantal schijven bepalen de totale capaciteit van de back-upopslaggroep.

> [!IMPORTANT]
> U moet operationele herstel gegevens *niet* langer dan vijf dagen bewaren op schijven die zijn gekoppeld aan Azure backup server. Als gegevens meer dan vijf dagen oud zijn, slaat u deze op in een Recovery Services kluis.

Om back-upgegevens op te slaan in azure, maakt of gebruikt u een Recovery Services kluis. Wanneer u een back-up van de Azure Backup Server workload voorbereidt, [configureert u de Recovery Services kluis](#create-a-recovery-services-vault). Zodra een online back-uptaak is geconfigureerd, wordt een herstel punt in de kluis gemaakt. Elke Recovery Services kluis bevat Maxi maal 9.999 herstel punten. Afhankelijk van het aantal gemaakte herstel punten en hoe lang het duurt, kunt u back-upgegevens gedurende een aantal jaren bewaren. U kunt bijvoorbeeld maandelijkse herstel punten maken en deze vijf jaar blijven gebruiken.

> [!IMPORTANT]
> Of u back-upgegevens naar Azure verzendt of lokaal blijft, u moet Azure Backup Server registreren bij een Recovery Services kluis.

### <a name="scale-deployment"></a>Implementatie schalen

Als u de schaal van uw implementatie wilt aanpassen, hebt u de volgende opties:

- **Omhoog schalen**: Verhoog de grootte van de Azure Backup Server VM van een serie naar DS3-serie en verg root de lokale opslag.
- **Offload-gegevens**: Verstuur oudere gegevens naar Azure en behoud alleen de nieuwste gegevens op de opslag die is gekoppeld aan de Azure backup server machine.
- **Uitschalen**: voeg meer Azure backup server machines toe om de werk belastingen te beveiligen.

### <a name="net-framework"></a>.NET Framework

Op de VM moet .NET Framework 3,5 SP1 of hoger zijn geïnstalleerd.

### <a name="join-a-domain"></a>Lid worden van een domein

De Azure Backup Server virtuele machine moet lid zijn van een domein. Een domein gebruiker met beheerders bevoegdheden op de VM moet Azure Backup Server installeren.

Azure Backup Server geïmplementeerd in een Azure-VM kan een back-up maken van werk belastingen op de virtuele machines in de Azure VMware-oplossing. De werk belastingen moeten zich in hetzelfde domein bedoen om de back-upbewerking in te scha kelen.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services kluis is een opslag entiteit waarin de herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt. Het bevat ook een back-upbeleid dat is gekoppeld aan beveiligde items.

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

1. Selecteer in het menu links **Alle services**.

   ![Selecteer in het menu links alle services.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. Voer in het dialoog venster **alle services** het **Recovery Services** in en selecteer **Recovery Services kluizen** in de lijst.

   ![Voer Recovery Services kluizen in en kies deze.](../backup/media/backup-create-rs-vault/all-services.png)

   De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

1. Op het dashboard van de **Recovery Services**-kluizen selecteert u **Toevoegen**.

   ![Een Recovery Services kluis toevoegen.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Het dialoogvenster **Recovery Service-kluis** wordt geopend.

1. Voer waarden in voor de **naam**, het **abonnement**, de **resource groep** en de **locatie**.

   ![Configureer de Recovery Services kluis.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam**: Voer een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op met ten minste twee, maar niet meer dan 50 tekens. De naam moet beginnen met een letter en mag alleen uit letters, cijfers en afbreekstreepjes bestaan.
   - **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van maar één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere mogelijkheden als uw werk- of schoolaccount is gekoppeld aan meerdere Azure-abonnementen.
   - **Resourcegroep**: Gebruik een bestaande resourcegroep of maak een nieuwe. Als u de lijst met beschikbare resourcegroepen in uw abonnement wilt weergeven, selecteert u **Bestaande gebruiken** en vervolgens selecteert u een resource in de vervolgkeuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuwe maken** en voert u de naam in.
   - **Locatie**: Selecteer de geografische regio voor de kluis. Als u een kluis wilt maken om virtuele machines van Azure VMware-oplossingen te beveiligen, *moet* de kluis zich in dezelfde regio bevinden als de privécloud van de Azure VMware-oplossing.

1. Wanneer u klaar bent om de Recovery Services-kluis te maken, selecteert u **Maken**.

   ![Maak de Recovery Services kluis.](../backup/media/backup-create-rs-vault/click-create-button.png)

   Het kan even duren voordat de Recovery Services-kluis is gemaakt. Bewaak de status meldingen in het gebied **meldingen** in de rechter bovenhoek van de portal. Nadat u de kluis hebt gemaakt, wordt deze weer gegeven in de lijst met Recovery Services kluizen. Als uw kluis niet wordt weergegeven, selecteert u **Vernieuwen**.

   ![Vernieuw de lijst met back-upkluizen.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Opslag replicatie instellen

Met de optie voor opslag replicatie kunt u kiezen tussen geo-redundante opslag (de standaard instelling) en lokaal redundante opslag. Met geografisch redundante opslag worden de gegevens in uw opslag account gekopieerd naar een secundaire regio, waardoor uw gegevens duurzaam zijn. Lokaal redundante opslag is een goedkopere optie die niet zo duurzaam is. Zie [Azure Storage redundantie](../storage/common/storage-redundancy.md)voor meer informatie over de opties voor geografisch redundante en lokaal redundante opslag.

> [!IMPORTANT]
> Het wijzigen van de instelling van het **type opslag replicatie lokaal redundant/geo-redundant** voor een Recovery Services kluis moet worden gedaan voordat u back-ups in de kluis configureert. Nadat u back-ups hebt geconfigureerd, is de optie om deze te wijzigen uitgeschakeld en kunt u het type opslag replicatie niet meer wijzigen.

1. In **Recovery Services kluizen** selecteert u de nieuwe kluis. 

1. Selecteer onder **Alle instellingen** de optie **Eigenschappen**. Selecteer **Update** onder **back-upconfiguratie**.

1. Selecteer het type opslag replicatie en selecteer **Opslaan**.

## <a name="download-and-install-the-software-package"></a>Het software pakket downloaden en installeren

Volg de stappen in deze sectie om het software pakket te downloaden, uit te pakken en te installeren.

### <a name="download-the-software-package"></a>Het software pakket downloaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Als er al een Recovery Services kluis is geopend, gaat u verder met de volgende stap. Als u geen Recovery Services kluis hebt geopend en u zich in de Azure Portal bevindt, selecteert u in het hoofd menu **Bladeren**.

   1. Voer **Recovery Services** in de lijst met resources in.

   1. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Wanneer u **Recovery Services kluizen** ziet, selecteert u deze.

   ![Recovery Services kluis maken stap 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Selecteer in de lijst met Recovery Services-kluizen een kluis.

   Het geselecteerde kluisdashboard wordt geopend.

   ![Het geselecteerde kluisdashboard wordt geopend.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   De optie **instellingen** wordt standaard geopend. Als deze optie is gesloten, selecteert u **instellingen** om deze te openen.

   ![De optie instellingen wordt standaard geopend. Als deze optie is gesloten, selecteert u instellingen om deze te openen.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Selecteer **back-up** om de wizard **aan de slag** te openen.

   ![Selecteer back-up om de wizard aan de slag te openen.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. In het venster dat wordt geopend:

   1. Selecteer in het menu **waar wordt uw workload uitgevoerd? de** optie **on-premises**.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="Waar wordt uw werk belasting uitgevoerd?":::

   1. Selecteer in het menu **waarvan wilt u een back-up maken?** de werk belastingen die u wilt beveiligen met behulp van Azure backup server.

   1. Selecteer **infra structuur voorbereiden** om Azure backup server en de kluis referenties te downloaden en te installeren.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Infra structuur voorbereiden":::

1. In het venster **infra structuur voorbereiden** dat wordt geopend:

   1. Selecteer de **Download** koppeling om Azure backup server te installeren.

   1. Selecteer **al gedownload of gebruik de nieuwste Azure backup server-installatie** en **down load** de kluis referenties. U gebruikt deze referenties wanneer u de Azure Backup Server registreert bij de Recovery Services kluis. Met de koppelingen gaat u naar het Download centrum, waar u het software pakket downloadt.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Infra structuur voorbereiden-Azure Backup Server":::

1. Selecteer op de pagina downloaden de optie alle bestanden en selecteer **volgende**.

   > [!NOTE]
   > U moet alle bestanden naar dezelfde map downloaden. Omdat de download grootte van de bestanden groter is dan 3 GB, kan het tot 60 minuten duren voordat de down load is voltooid. 

   ![Selecteer op de pagina downloaden de optie alle bestanden en selecteer volgende.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Het software pakket uitpakken

Als u het software pakket naar een andere server hebt gedownload, kopieert u de bestanden naar de virtuele machine die u hebt gemaakt om Azure Backup Server te implementeren.

> [!WARNING]
> Er is ten minste 4 GB beschik bare ruimte nodig om de Setup-bestanden uit te pakken.

1. Nadat u alle bestanden hebt gedownload, dubbelklikt u op **MicrosoftAzureBackupInstaller.exe** om de wizard **Microsoft Azure backup** Setup te openen en selecteert u vervolgens **volgende**.

1. Selecteer de locatie waar u de bestanden wilt extra heren en selecteer **volgende**.

1. Selecteer **extra heren** om het extractie proces te starten.

   ![Selecteer extra heren om het extractie proces te starten.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Zodra het bestand is geëxtraheerd, selecteert u de optie om setup.exeuit te **voeren** en selecteert u vervolgens **volt ooien**.

> [!TIP]
> U kunt het setup.exe-bestand ook vinden in de map waar u het software pakket hebt uitgepakt.

### <a name="install-the-software-package"></a>Het software pakket installeren

1. Selecteer in het venster Setup onder **installeren** de optie **Microsoft Azure backup** om de installatie wizard te openen.

   ![Selecteer in het venster Setup onder installeren de optie Microsoft Azure Backup om de installatie wizard te openen.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Selecteer in het scherm **Welkom** de optie **volgende** om door te gaan naar de pagina **vereisten controles** .

1. Selecteer **opnieuw controleren** om te bepalen of de hardware en software voldoen aan de vereisten voor Azure backup server. Als u de bewerking hebt uitgevoerd, selecteert u **volgende**.

   ![ Selecteer opnieuw controleren om te bepalen of de hardware en software voldoen aan de vereisten voor Azure Backup Server. Als u de bewerking hebt uitgevoerd, selecteert u volgende.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Het Azure Backup Server-installatie pakket wordt geleverd met de juiste SQL Server binaire bestanden die nodig zijn. Wanneer u een nieuwe Azure Backup Server-installatie start, selecteert u de optie **nieuw exemplaar van SQL Server installeren met deze installatie** . Selecteer vervolgens **controleren en installeren**.

   ![Het Azure Backup Server-installatie pakket wordt geleverd met de juiste SQL Server binaire bestanden die nodig zijn.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Als u uw eigen SQL Server exemplaar wilt gebruiken, zijn de ondersteunde SQL Server versies SQL Server 2014 SP1 of hoger, 2016 en 2017. Alle SQL Server versies moeten Standard of ENTER prise 64-bit zijn. Het exemplaar dat door Azure Backup Server wordt gebruikt, moet alleen lokaal zijn. het kan niet extern zijn. Als u een bestaand SQL Server-exemplaar voor Azure Backup Server gebruikt, ondersteunt de installatie alleen het gebruik van *benoemde exemplaren* van SQL Server.

   Als er een fout optreedt met een aanbeveling om de computer opnieuw op te starten, doet u dit en selecteert u **opnieuw controleren**. Voor eventuele SQL Server configuratie problemen moet u SQL Server opnieuw configureren volgens de SQL Server richt lijnen. Probeer Azure Backup Server vervolgens opnieuw te installeren of bij te werken met behulp van het bestaande exemplaar van SQL Server.

   **Handmatige configuratie**

   Wanneer u uw eigen SQL Server-exemplaar gebruikt, moet u ervoor zorgen dat u builtin\Administrators toevoegt aan de sysadmin-rol aan de sysadmin-rol van de hoofd database.

   **Reporting Services configureren met SQL Server 2017**

   Als u uw exemplaar van SQL Server 2017 gebruikt, moet u SQL Server 2017 Reporting Services (SSRS) hand matig configureren. Nadat u SSRS hebt geconfigureerd, moet u de eigenschap **IsInitialized** van SSRS instellen op **True**. Als deze eigenschap is ingesteld op **True** Azure backup server, wordt ervan uitgegaan dat SSRS al is geconfigureerd en dat de SSRS-configuratie is overgeslagen.

   Als u de configuratie status van SSRS wilt controleren, voert u de volgende handelingen uit:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Gebruik de volgende waarden voor de SSRS-configuratie:

   * **Service account**: **ingebouwde account gebruiken** moet **netwerk service** zijn.
   * **URL van webservice**: de **virtuele map** moet **ReportServer_ \<SQLInstanceName>** zijn.
   * **Data Base**: **DATABASENAME** moet **\<SQLInstanceName> Report Server $** zijn.
   * **URL van webportal**: de **virtuele map** moet **Reports_ \<SQLInstanceName>** zijn.

   Meer [informatie](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) over de configuratie van SSRS.

   > [!NOTE]
   > [Micro soft Online Services-voor waarden](https://www.microsoft.com/licensing/product-licensing/products) (OST) bepaalt de licentie voor SQL Server gebruikt als de data base voor Azure backup server. Volgens OST, gebruiken alleen SQL Server gebundeld met Azure Backup Server als de Data Base voor Azure Backup Server.

1. Nadat de installatie is voltooid, selecteert u **volgende**.

1. Geef een locatie op voor de installatie van Microsoft Azure Backup Server-bestanden en selecteer **volgende**.

   > [!NOTE]
   > De Scratch locatie is vereist voor back-up naar Azure. Zorg ervoor dat de Scratch locatie ten minste 5% van de gegevens die zijn gepland voor het maken van een back-up naar de Cloud. Voor schijf beveiliging moeten afzonderlijke schijven worden geconfigureerd nadat de installatie is voltooid. Zie [opslag groepen en schijf opslag configureren](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12))voor meer informatie over opslag groepen.

   ![Geef een locatie op voor de installatie van Microsoft Azure Backup Server-bestanden en selecteer volgende.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Geef een sterk wacht woord op voor beperkte lokale gebruikers accounts en selecteer **volgende**.

   ![Geef een sterk wacht woord op voor beperkte lokale gebruikers accounts en selecteer volgende.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Selecteer of u Microsoft Update wilt gebruiken om te controleren op updates en selecteer **volgende**.

   > [!NOTE]
   > U kunt het beste Windows Update omleiden naar Microsoft Update, waarmee beveiligings-en belang rijke updates voor Windows en andere producten zoals Azure Backup Server worden geboden.

   ![Selecteer of u Microsoft Update wilt gebruiken om te controleren op updates en selecteer volgende.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Bekijk het **samen vatting van instellingen** en selecteer **installeren**.

   De installatie vindt plaats in fasen. 
   - In de eerste fase wordt de Microsoft Azure Recovery Services-agent geïnstalleerd.
   - In de tweede fase wordt gecontroleerd op Internet connectiviteit. Als deze beschikbaar is, kunt u door gaan met de installatie. Als deze niet beschikbaar is, moet u proxy gegevens opgeven om verbinding te maken met internet. 
   - In de laatste fase wordt de vereiste software gecontroleerd. Als dit niet is geïnstalleerd, wordt de ontbrekende software samen met de Microsoft Azure Recovery Services-agent geïnstalleerd.

1. Selecteer **Bladeren** om uw kluis referenties te zoeken om de computer te registreren bij de Recovery Services kluis, en selecteer vervolgens **volgende**.

1. Selecteer een wachtwoordzin voor het versleutelen of ontsleutelen van de gegevens die tussen Azure en uw locatie worden verzonden.

   > [!TIP]
   > U kunt automatisch een wachtwoordzin genereren of uw minimale wacht woord van 16 tekens opgeven.

1. Voer de locatie in om de wachtwoordzin op te slaan en selecteer vervolgens **volgende** om de server te registreren.

   > [!IMPORTANT]
   > Sla de wachtwoordzin op een veilige locatie op dan de lokale server. We raden u ten zeerste aan het gebruik van de Azure Key Vault om de wachtwoordzin op te slaan.

   Nadat de installatie van de Microsoft Azure Recovery Services-agent is voltooid, gaat de installatie stap over naar de installatie en configuratie van SQL Server en de Azure Backup Server-onderdelen.

   ![Nadat de installatie van de Microsoft Azure Recovery Services-agent is voltooid, gaat de installatie stap over naar de installatie en configuratie van SQL Server en de Azure Backup Server-onderdelen.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Nadat de installatie stap is voltooid, selecteert u **sluiten**.

### <a name="install-update-rollup-1"></a>Update pakket 1 installeren

Het installeren van update pakket 1 voor Azure Backup Server v3 is verplicht voordat u de werk belastingen kunt beveiligen.  In het [Knowledge Base-artikel](https://support.microsoft.com/en-us/help/4534062/)vindt u de oplossingen voor fouten en installatie-instructies.

## <a name="add-storage-to-azure-backup-server"></a>Opslag toevoegen aan Azure Backup Server

Azure Backup Server v3 ondersteunt Modern Backup Storage die het volgende biedt:

-  Besparing van de opslag van 50%.
-  Back-ups die drie keer sneller zijn.
-  Efficiëntere opslag.
-  Werkbelasting bewuste opslag.

### <a name="volumes-in-azure-backup-server"></a>Volumes in Azure Backup Server

Voeg de gegevens schijven toe met de vereiste opslag capaciteit van de Azure Backup Server VM als deze nog niet is toegevoegd.

Azure Backup Server v3 accepteert alleen opslag volumes. Wanneer u een volume toevoegt, wordt Azure Backup Server het volume geformatteerd naar het bestand met het flexibele bestands systeem (ReFS), dat Modern Backup Storage vereist.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Volumes toevoegen aan Azure Backup Server schijf opslag

1. Scan in het deel venster **beheer** de opslag opnieuw en selecteer vervolgens **toevoegen**. 

1. Selecteer een van de beschik bare volumes om toe te voegen aan de opslag groep. 

1. Nadat u de beschik bare volumes hebt toegevoegd, geeft u deze een beschrijvende naam waarmee u deze kunt beheren. 

1. Selecteer **OK** om deze volumes op ReFS op te maken zodat Azure backup server modern Backup Storage voor delen kunt gebruiken.


## <a name="next-steps"></a>Volgende stappen

Nu u hebt uitgelegd hoe u Azure Backup Server voor de Azure VMware-oplossing instelt, wilt u mogelijk meer informatie over:

- [Back-ups configureren voor uw Azure VMware-oplossing-vm's](backup-azure-vmware-solution-virtual-machines.md).
- [Bescherm uw Azure VMware-oplossing-vm's met Azure Security Center-integratie](azure-security-integration.md).
