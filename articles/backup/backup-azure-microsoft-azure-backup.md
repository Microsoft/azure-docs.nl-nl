---
title: Azure Backup Server gebruiken om een back-up te maken van workloads
description: In dit artikel leert u hoe u uw omgeving voorbereidt op het beveiligen en maken van een back-up van workloads met behulp van Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: d476c228a619f03f798c1a2cd6854a8d603c3637
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987019"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Azure Backup Server installeren en upgraden

> [!div class="op_single_selector"]
>
> * [Azure Backup-server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Van toepassing op: MABS v3. (MABS v2 wordt niet meer ondersteund. Als u een eerdere versie dan MABS V3 gebruikt, moet u een upgrade uitvoeren naar de nieuwste versie.)

In dit artikel wordt uitgelegd hoe u uw omgeving voorbereidt op back-ups van werk belastingen met behulp van Microsoft Azure Backup Server (MABS). Met Azure Backup Server kunt u werk belastingen van toepassingen, zoals virtuele machines van Hyper-V, Microsoft SQL Server, share Point server, micro soft Exchange en Windows-clients beveiligen vanaf één console.

> [!NOTE]
> Azure Backup Server kan nu VMware-Vm's beveiligen en biedt verbeterde beveiligings mogelijkheden. Installeer het product zoals beschreven in de onderstaande secties en de nieuwste Azure Backup-Agent. Voor meer informatie over het maken van een back-up van VMware-servers met Azure Backup Server raadpleegt u het artikel [Azure backup server gebruiken om een back-up te maken van een VMware-Server](backup-azure-backup-server-vmware.md). Raadpleeg de [documentatie over Azure backup beveiligings functies](backup-azure-security-feature.md)voor meer informatie over de beveiligings mogelijkheden.
>
>

MABS geïmplementeerd in een Azure-VM kan een back-up maken van virtuele machines in azure, maar ze moeten zich in hetzelfde domein bevinden om back-upbewerking mogelijk te maken. Het proces voor een back-up van een virtuele Azure-machine blijft hetzelfde als het maken van back-ups van virtuele machines op locatie, maar het implementeren van MABS in azure heeft enkele beperkingen. Zie [DPM als een virtuele machine van Azure](/system-center/dpm/install-dpm#setup-prerequisites) voor meer informatie over beperking.

> [!NOTE]
> Azure heeft twee implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../azure-resource-manager/management/deployment-models.md). In dit artikel vindt u informatie en procedures voor het herstellen van Vm's die zijn geïmplementeerd met het Resource Manager-model.
>
>

Azure Backup Server neemt een groot deel van de back-up van de werk belasting over van Data Protection Manager (DPM). Dit artikel is gekoppeld aan de DPM-documentatie om een deel van de gedeelde functionaliteit uit te leggen. Hoewel Azure Backup Server veel dezelfde functionaliteit deelt als DPM, Azure Backup Server geen back-up op tape en ook wel geïntegreerd met System Center.

## <a name="choose-an-installation-platform"></a>Een installatie platform kiezen

De eerste stap voor het voorbereiden van de Azure Backup Server is het instellen van een Windows-Server. Uw server kan zich in azure of on-premises bevindt.

* Als u on-premises workloads wilt beveiligen, moet de MABS-server zich op locatie bevinden.
* Voor het beveiligen van werk belastingen die worden uitgevoerd op virtuele Azure-machines, moet de MABS-server zich in azure bevinden, die wordt uitgevoerd als een virtuele machine van Azure.

### <a name="using-a-server-in-azure"></a>Een server gebruiken in azure

Wanneer u een server kiest voor het uitvoeren van Azure Backup Server, is het raadzaam om te beginnen met een galerie-afbeelding van Windows Server 2016 Data Center of Windows Server 2019 Data Center. In het artikel [maakt u uw eerste virtuele Windows-machine in de Azure Portal](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json), een zelf studie om aan de slag te gaan met de aanbevolen virtuele machine in azure, zelfs als u Azure nog nooit eerder hebt gebruikt. De aanbevolen minimum vereisten voor de virtuele machine van de server (VM) zijn: Standard_A4_v2 met vier kernen en 8 GB RAM-geheugen.

Het beveiligen van werk belastingen met Azure Backup Server heeft veel nuances. De [beveiligings matrix voor MABS](./backup-mabs-protection-matrix.md) helpt u bij het uitleggen van deze nuances. Lees dit artikel volledig voordat u de computer implementeert.

### <a name="using-an-on-premises-server"></a>Een on-premises server gebruiken

Als u de basis server niet in azure wilt uitvoeren, kunt u de-server uitvoeren op een Hyper-V-VM, een virtuele VMware-machine of een fysieke host. De aanbevolen minimale vereisten voor de serverhardware zijn twee kernen en 8 GB RAM-geheugen. De ondersteunde besturings systemen worden weer gegeven in de volgende tabel:

| Besturingssysteem | Platform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2016 en de nieuwste SPs |64 bits |Standard, Datacenter, Essentials  |

U kunt de DPM-opslag ontdubbelen met behulp van Windows Server ontdubbeling. Meer informatie over hoe [DPM en ontdubbeling](/system-center/dpm/deduplicate-dpm-storage) samen werken wanneer ze worden geïmplementeerd in virtuele Hyper-V-machines.

> [!NOTE]
> Azure Backup Server is ontworpen om te worden uitgevoerd op een toegewezen server met één doel. U kunt Azure Backup Server niet installeren op:
>
> * Een computer die wordt uitgevoerd als een domeincontroller
> * Een computer waarop de toepassingsserverfunctie is geïnstalleerd
> * Een computer die een System Center Operations Manager-beheer server is
> * Een computer waarop Exchange Server wordt uitgevoerd
> * Een computer die een knoop punt van een cluster is
>
> Het installeren van Azure Backup Server wordt niet ondersteund op Windows Server Core-of Microsoft Hyper-V-Server.

Voeg Azure Backup Server altijd toe aan een domein. Als u van plan bent om de server naar een ander domein te verplaatsen, installeert u Azure Backup Server eerst en voegt u vervolgens de-server toe aan het nieuwe domein. Het verplaatsen van een bestaande Azure Backup Server machine naar een nieuw domein na implementatie wordt *niet ondersteund*.

Of u back-upgegevens naar Azure verzendt of lokaal blijft, Azure Backup Server moeten zijn geregistreerd bij een Recovery Services kluis.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Opslag replicatie instellen

U kunt met de optie voor opslagreplicatie kiezen tussen geografisch redundante opslag en lokaal redundante opslag. Recovery Services kluizen gebruiken standaard geografisch redundante opslag. Als deze kluis uw primaire kluis is, houdt u de opslag optie ingesteld op geografisch redundante opslag. Kies lokaal redundante opslag als u een goedkopere optie wilt die niet zo duurzaam is. Meer informatie over [geo-redundante](../storage/common/storage-redundancy.md#geo-redundant-storage), [lokaal redundante](../storage/common/storage-redundancy.md#locally-redundant-storage)en [zone-redundante](../storage/common/storage-redundancy.md#zone-redundant-storage) opslag opties vindt u in het [overzicht van Azure storage-replicatie](../storage/common/storage-redundancy.md).

De instelling voor opslagreplicatie bewerken:

1. Selecteer in het deel venster **Recovery Services kluizen** de nieuwe kluis. Selecteer in de sectie **instellingen** de optie  **Eigenschappen**.
2. In **Eigenschappen**, onder **back-upconfiguratie**, selecteert u **bijwerken**.

3. Selecteer het type opslag replicatie en selecteer **Opslaan**.

     ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Software pakket

### <a name="downloading-the-software-package"></a>Het software pakket downloaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Als er al een Recovery Services kluis is geopend, gaat u verder met stap 3. Als u nog geen Recovery Services kluis hebt geopend, maar wel in de Azure Portal, klikt u op **Bladeren** in het hoofd menu.

   * Typ in de lijst met resources **Recovery Services**.
   * Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Wanneer u **Recovery Services kluizen** ziet, selecteert u deze.

     ![Recovery Services kluis maken stap 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     De lijst met Recovery Services-kluizen wordt weergegeven.
   * Selecteer in de lijst met Recovery Services-kluizen een kluis.

     Het geselecteerde kluisdashboard wordt geopend.

     ![Kluis dashboard](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Het deel venster **instellingen** wordt standaard geopend. Als deze is gesloten, selecteert u **instellingen** om het deel venster instellingen te openen.

    ![Deel venster instellingen](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Selecteer **back-up** om de wizard aan de slag te openen.

    ![Back-up aan de slag](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    In het deel venster **aan de slag met back-up** dat wordt geopend, worden de **Back-updoelen** automatisch geselecteerd.

    ![Back-up-doel stellingen-standaard-geopend](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Selecteer **on-premises** in het deel venster doel van de **back-up** van het menu **waar is uw workload actief** .

    ![on-premises en workloads als doel stellingen](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Selecteer in de vervolg keuzelijst **waarvan wilt u een back-up maken?** de werk belastingen die u wilt beveiligen met Azure backup server en selecteer vervolgens **OK**.

    Met de wizard aan de slag **met back-up** kunt u de optie **infra structuur voorbereiden** gebruiken om een back-up te maken van werk belastingen naar Azure.

   > [!NOTE]
   > Als u alleen back-ups wilt maken van bestanden en mappen, raden we u aan de Azure Backup-Agent te gebruiken en de instructies in het artikel [eerst te bekijken: back-ups maken van bestanden en mappen](./backup-windows-with-mars-agent.md). Als u meer dan bestanden en mappen wilt beveiligen, of als u de beveiligings behoeften in de toekomst wilt uitbreiden, selecteert u die werk belastingen.
   >
   >

    ![Wizard aan de slag](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. In het deel venster **infra structuur voorbereiden** dat wordt geopend, selecteert u de **Download** koppelingen voor installatie Azure backup server en kluis referenties downloaden. U gebruikt de kluis referenties tijdens de registratie van Azure Backup Server aan de Recovery Services kluis. Met de koppelingen gaat u naar het Download centrum waar het software pakket kan worden gedownload.

    ![Infra structuur voorbereiden voor Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selecteer alle bestanden en selecteer **volgende**. Down load alle bestanden die afkomstig zijn van op de pagina Microsoft Azure Backup downloaden en plaats alle bestanden in dezelfde map.

    ![Down load Center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Omdat de download grootte van alle bestanden samen > 3 GB is, kan het tot 60 minuten duren voordat de down load is voltooid.

### <a name="extracting-the-software-package"></a>Het software pakket extra heren

Nadat u alle bestanden hebt gedownload, selecteert u **MicrosoftAzureBackupInstaller.exe**. Hiermee start u de **installatie wizard van Microsoft Azure backup** om de installatie bestanden uit te pakken op een locatie die door u is opgegeven. Ga door met de wizard en selecteer de **extra heren** knop om het extractie proces te starten.

> [!WARNING]
> Er is ten minste 4 GB beschik bare ruimte nodig om de Setup-bestanden uit te pakken.
>
>

![Setup uitpakken van bestanden voor installatie](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Zodra het uitpakken is voltooid, schakelt u het selectie vakje in om de volledig geëxtraheerde *setup.exe* te starten om te beginnen met de installatie van Microsoft Azure backup server en selecteert u de knop **volt ooien** .

### <a name="installing-the-software-package"></a>Het software pakket installeren

1. Selecteer **Microsoft Azure backup** om de installatie wizard te starten.

    ![Wizard Microsoft Azure Backup-installatie](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Selecteer de knop **volgende** in het welkomst scherm. Hiermee gaat u naar de sectie *vereisten controles* . Selecteer op dit scherm **controleren** om te bepalen of aan de hardware-en software vereisten voor Azure backup server is voldaan. Als aan alle vereisten wordt voldaan, wordt er een bericht weer gegeven waarin staat dat de computer aan de vereisten voldoet. Selecteer de knop **Volgende**.

    ![Azure Backup Server-welkom en vereisten controleren](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Het Azure Backup Server-installatie pakket wordt geleverd met de juiste SQL Server binaire bestanden die nodig zijn. Wanneer u een nieuwe Azure Backup Server-installatie start, kiest u de optie **nieuw exemplaar van SQL Server installeren met deze installatie** en selecteert u de knop **controleren en installeren** . Zodra de vereiste onderdelen zijn geïnstalleerd, selecteert u **volgende**.

    >[!NOTE]
    >Als u uw eigen SQL Server wilt gebruiken, zijn de ondersteunde SQL Server versies SQL Server 2014 SP1 of hoger, 2016 en 2017.  Alle SQL Server versies moeten Standard of ENTER prise 64-bit zijn.
    >Azure Backup Server werkt niet met een extern SQL Server exemplaar. Het exemplaar dat door Azure Backup Server wordt gebruikt, moet lokaal zijn. Als u een bestaande SQL Server gebruikt voor MABS, ondersteunt de installatie van MABS alleen het gebruik van *benoemde exemplaren* van SQL Server.

    ![Azure Backup Server-SQL-controle](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Als er een fout optreedt met een aanbeveling om de computer opnieuw op te starten, doet u dit en selecteert u **opnieuw controleren**. Als er SQL-configuratie problemen zijn, moet u SQL opnieuw configureren volgens de SQL-richt lijnen en opnieuw proberen om MABS te installeren of bij te werken met behulp van het bestaande exemplaar van SQL.

   **Handmatige configuratie**

   Wanneer u uw eigen exemplaar van SQL gebruikt, moet u builtin\Administrators toevoegen aan de rol sysadmin voor de hoofd database.

    **SSRS-configuratie met SQL 2017**

    Wanneer u uw eigen exemplaar van SQL 2017 gebruikt, moet u SSRS hand matig configureren. Controleer na de configuratie van SSRS of de eigenschap *IsInitialized* van SSRS is ingesteld op *True*. Als deze waarde is ingesteld op True, wordt ervan uitgegaan dat SSRS al is geconfigureerd en wordt de SSRS-configuratie overgeslagen.

    Gebruik de volgende waarden voor de SSRS-configuratie:
    * Service account: ' ingebouwd account gebruiken ' moet netwerk service zijn
    * URL van webservice: ' virtuele map ' moet worden ReportServer_\<SQLInstanceName>
    * Data Base: DATABASENAME moet Report Server $\<SQLInstanceName>
    * URL van webportal: ' virtuele map ' moet worden Reports_\<SQLInstanceName>

    Meer [informatie](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) over de configuratie van SSRS.

    > [!NOTE]
    > Licentie verlening voor SQL Server die als de Data Base voor MABS wordt gebruikt, is onderhevig aan [micro soft Online Services-voor waarden](https://www.microsoft.com/licensing/product-licensing/products) (OST). Volgens OST kunnen SQL Server gebundeld met MABS alleen worden gebruikt als de Data Base voor MABS.

4. Geef een locatie op voor de installatie van Microsoft Azure Backup Server-bestanden en selecteer **volgende**.

    ![Locatie opgeven voor installatie van bestanden](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    De Scratch locatie is een vereiste voor het maken van een back-up naar Azure. Zorg ervoor dat de Scratch locatie ten minste 5% van de gegevens bevindt waarvan een back-up naar de Cloud wordt gepland. Voor schijf beveiliging moeten afzonderlijke schijven worden geconfigureerd zodra de installatie is voltooid. Zie voor meer informatie over opslag groepen voorbereiden van [gegevens opslag](/system-center/dpm/plan-long-and-short-term-data-storage).

    De capaciteits vereisten voor schijf opslag zijn voornamelijk afhankelijk van de grootte van de beveiligde gegevens, de dagelijkse herstel punt grootte, het verwachte groei tempo van de volume gegevens en de Bewaar termijn van het bereik. U wordt aangeraden de schijf opslag twee maal zo groot te maken als de beveiligde gegevens. Hierbij wordt uitgegaan van een dagelijkse herstelpuntgrootte van 10% van de beveiligde gegevensgrootte en een bewaartermijn van tien dagen. Controleer de [DPM-capacity planner](https://www.microsoft.com/download/details.aspx?id=54301)om een goede schatting te krijgen van de grootte. 

5. Geef een sterk wacht woord op voor beperkte lokale gebruikers accounts en selecteer **volgende**.

    ![Sterk wacht woord opgeven](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecteer of u *Microsoft Update* wilt gebruiken om te controleren op updates en selecteer **volgende**.

   > [!NOTE]
   > U kunt het beste Windows Update omleiden naar Microsoft Update, waarmee beveiligings-en belang rijke updates voor Windows en andere producten zoals Microsoft Azure Backup Server worden geboden.
   >
   >

    ![Microsoft Update Opt-In](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Bekijk het *samen vatting van instellingen* en selecteer **installeren**.

    ![Samen vatting van instellingen](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. De installatie vindt plaats in fasen. In de eerste fase wordt de Microsoft Azure Recovery Services-agent geïnstalleerd op de server. De wizard controleert ook op Internet connectiviteit. Als Internet connectiviteit beschikbaar is, kunt u door gaan met de installatie. Als dat niet het geval is, moet u proxy gegevens opgeven om verbinding te maken met internet.

    De volgende stap is het configureren van de Microsoft Azure Recovery Services agent. Als onderdeel van de configuratie moet u uw kluis referenties opgeven om de machine te registreren bij de Recovery Services kluis. U geeft ook een wachtwoordzin op voor het versleutelen/ontsleutelen van de gegevens die tussen Azure en uw locatie worden verzonden. U kunt automatisch een wachtwoordzin genereren of uw eigen minimale 16-teken wachtwoordzin opgeven. Ga door met de wizard totdat de agent is geconfigureerd.

    ![Wizard Server registreren](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Zodra de registratie van de Microsoft Azure Backup-Server is voltooid, wordt de installatie en configuratie van SQL Server en de Azure Backup Server-onderdelen voortgezet. Zodra de installatie van het SQL Server onderdeel is voltooid, worden de Azure Backup Server-onderdelen geïnstalleerd.

    ![Voortgang van de installatie van Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Wanneer de installatie stap is voltooid, worden ook de bureaublad pictogrammen van het product gemaakt. Dubbel klik op het pictogram om het product te starten.

### <a name="add-backup-storage"></a>Back-upopslag toevoegen

De eerste back-upkopie wordt opgeslagen op de opslag die is gekoppeld aan de Azure Backup Server machine. Zie [opslag groepen en schijf opslag configureren](./backup-mabs-add-storage.md)voor meer informatie over het toevoegen van schijven.

> [!NOTE]
> U moet ook back-upopslag toevoegen als u van plan bent om gegevens naar Azure te verzenden. In de huidige architectuur van Azure Backup Server bevat de Azure Backup kluis de *tweede* kopie van de gegevens terwijl de lokale opslag de eerste (en verplichte) back-upkopie bevat.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>De Data Protection Manager-beveiligings agent installeren en bijwerken

MABS maakt gebruik van de System Center Data Protection Manager-beveiligings agent. [Hier volgen de stappen](/system-center/dpm/deploy-dpm-protection-agent) voor het installeren van de beveiligings agent op uw beveiligings servers.

In de volgende secties wordt beschreven hoe u beveiligingsagents voor client computers bijwerkt.

1. Selecteer **beheer** agenten in de Administrator-console van de back-upserver  >  .

2. Selecteer in het weergave paneel de client computers waarvoor u de beveiligings agent wilt bijwerken.

   > [!NOTE]
   > De kolom **agent updates** geeft aan wanneer een update van de beveiligings agent beschikbaar is voor elke beveiligde computer. In het deel venster **acties** is de actie **bijwerken** alleen beschikbaar als een beveiligde computer is geselecteerd en er updates beschikbaar zijn.
   >
   >

3. Om bijgewerkte beveiligingsagents op de geselecteerde computers te installeren, selecteert u in het deel venster **acties** de optie **bijwerken**.

4. Voor een client computer die geen verbinding heeft met het netwerk totdat de computer is verbonden met het netwerk, wordt in de kolom **agent status** de status **Update in behandeling** weer gegeven.

   Nadat een client computer met het netwerk is verbonden, wordt in de kolom **agent updates** voor de client computer de status **bijgewerkt** weer gegeven.

## <a name="move-mabs-to-a-new-server"></a>MABS verplaatsen naar een nieuwe server

Hier volgen de stappen als u MABS moet verplaatsen naar een nieuwe server, terwijl u de opslag ruimte behoudt. U kunt dit alleen doen als de gegevens zich op Modern Backup Storage bevindt.

  > [!IMPORTANT]
  >
  > * De nieuwe server naam moet dezelfde naam hebben als de oorspronkelijke Azure Backup Server-instantie. U kunt de naam van het nieuwe Azure Backup Server-exemplaar niet wijzigen als u de vorige opslag groep en de MABS-data base (DPMDB) wilt gebruiken om de herstel punten te bewaren.
  > * U moet een back-up hebben van de MABS-data base (DPMDB). U hebt deze nodig om de data base te herstellen.

1. Selecteer in het weergave paneel de client computers waarvoor u de beveiligings agent wilt bijwerken.
2. De oorspronkelijke Azure Backup Server afsluiten of offline halen.
3. Stel de machine account opnieuw in Active Directory.
4. Installeer Server 2016 op een nieuwe computer en geef deze dezelfde computer naam als de oorspronkelijke Azure Backup-Server.
5. Voeg lid van het domein.
6. Installeer Azure Backup Server v3 of hoger (Verplaats MABS-opslag groeps schijven van een oude server en importeren).
7. Herstel de DPMDB die u in stap 1 hebt gemaakt.
8. Koppel de opslag van de oorspronkelijke back-upserver aan de nieuwe server.
9. Herstel de DPMDB vanuit SQL.
10. Start CMD (als beheerder) op de nieuwe server. Ga naar de Microsoft Azure Backup installatie locatie en bin-map

    Voor beeld van pad: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Voer uit om verbinding te maken met Azure Backup `DPMSYNC -SYNC`

    Als u **nieuwe** schijven aan de DPM-opslag groep hebt toegevoegd in plaats van de oude te verplaatsen, voert u uit `DPMSYNC -Reallocatereplica` .

## <a name="network-connectivity"></a>Netwerkverbinding

Azure Backup Server moet verbinding hebben met de Azure Backup-service om het product goed te laten werken. Als u wilt controleren of de computer de verbinding met Azure heeft, gebruikt u de ```Get-DPMCloudConnection``` cmdlet in de Azure Backup Server Power shell-console. Als de uitvoer van de cmdlet waar is, bestaat de verbinding, anders is er geen verbinding.

Op hetzelfde moment moet het Azure-abonnement de status in orde hebben. Als u de status van uw abonnement wilt weten en wilt beheren, meldt u zich aan bij de [Portal voor abonnementen](https://account.windowsazure.com/Subscriptions).

Zodra u de status van de Azure-verbinding en het Azure-abonnement kent, kunt u de onderstaande tabel gebruiken om de gevolgen van de functionaliteit voor back-up/herstel te bepalen.

| Connectiviteits status | Azure-abonnement | Back-up naar Azure | Back-up op schijf maken | Herstellen vanuit Azure | Herstellen uit een schijf |
| --- | --- | --- | --- | --- | --- |
| Verbonden |Actief |Toegestaan |Toegestaan |Toegestaan |Toegestaan |
| Verbonden |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbonden |Ongedaan gemaakt |Gestopt |Gestopt |Gestopt en Azure-herstel punten zijn verwijderd |Gestopt |
| Verbroken connectiviteit > 15 dagen |Actief |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbroken connectiviteit > 15 dagen |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbroken connectiviteit > 15 dagen |Ongedaan gemaakt |Gestopt |Gestopt |Gestopt en Azure-herstel punten zijn verwijderd |Gestopt |

### <a name="recovering-from-loss-of-connectivity"></a>Herstellen van connectiviteits verlies

Als uw computer beperkte internet toegang heeft, moet u ervoor zorgen dat de firewall instellingen op de computer of de proxy de volgende Url's en IP-adressen toestaan:

* URL's
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

Als u ExpressRoute micro soft-peering gebruikt, selecteert u de volgende services/regio's:

* Azure Active Directory (12076:5060)
* Microsoft Azure regio (op basis van de locatie van uw Recovery Services kluis)
* Azure Storage (op basis van de locatie van uw Recovery Services kluis)

Ga naar [ExpressRoute-routerings vereisten](../expressroute/expressroute-routing.md)voor meer informatie.

Zodra de verbinding met Azure is hersteld op de Azure Backup Server machine, worden de bewerkingen die kunnen worden uitgevoerd, bepaald door de status van het Azure-abonnement. De bovenstaande tabel bevat details over de bewerkingen die zijn toegestaan zodra de computer is verbonden.

### <a name="handling-subscription-states"></a>Abonnements statussen verwerken

Het is mogelijk om een Azure-abonnement te nemen van een *verlopen* of *oningerichte* status naar de *actieve* status. Dit heeft echter gevolgen voor het product gedrag wanneer de status niet *actief* is:

* Een ongedaan gemaakt abonnement verliest de functionaliteit voor de periode waarin *de inrichting is* ongedaan gemaakt. Bij het inschakelen van actief wordt de product functionaliteit van Backup/Restore opnieuw *geactiveerd*. De back-upgegevens op de lokale schijf kunnen ook worden opgehaald als deze zijn opgeslagen met een voldoende lange Bewaar periode. De back-upgegevens in azure zijn echter IRRETRIEVABLY kwijt wanneer het abonnement de status *unprovision* heeft ingevoerd.
* Een *verlopen* abonnement verliest alleen de functionaliteit als deze weer *actief* is geweest. Back-ups die zijn gepland voor de periode dat het abonnement is *verlopen* , worden niet uitgevoerd.

## <a name="upgrade-mabs"></a>MABS bijwerken

Gebruik de volgende procedures om MABS bij te werken.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgrade van MABS v2 naar v3

> [!NOTE]
>
> MABS v2 is geen vereiste voor het installeren van MABS v3. U kunt echter alleen upgraden naar MABS v3 vanuit MABS v2.

Gebruik de volgende stappen om MABS bij te werken:

1. Als u een upgrade van MABS v2 naar MABS v3 wilt uitvoeren, moet u het besturings systeem upgraden naar Windows Server 2016 of Windows Server 2019, indien nodig.

2. Upgrade uw server. De stappen zijn vergelijkbaar met de [installatie](#install-and-upgrade-azure-backup-server). Voor SQL-instellingen krijgt u echter een optie om uw SQL-exemplaar te upgraden naar SQL 2017, of om uw eigen exemplaar van SQL Server 2017 te gebruiken.

   > [!NOTE]
   >
   > Sluit niet af terwijl uw SQL-exemplaar wordt bijgewerkt. Als u afsluit, wordt het exemplaar van SQL Reporting verwijderd en zal er geen poging worden gedaan om de MABS opnieuw bij te werken.

   > [!IMPORTANT]
   >
   >  Als onderdeel van de SQL 2017-upgrade maakt u een back-up van de SQL-versleutelings sleutels en verwijdert u de Reporting Services. Na de upgrade van SQL Server is Reporting service (14.0.6827.4788) geïnstalleerd & versleutelings sleutels worden hersteld.
   >
   > Als u SQL 2017 hand matig wilt configureren, raadpleegt u de sectie *SSRS-configuratie met SQL 2017* onder installatie-instructies.

3. Werk de beveiligingsagents op de beveiligde servers bij.
4. Back-ups moeten worden voortgezet zonder dat u uw productie servers opnieuw hoeft op te starten.
5. U kunt nu beginnen met het beveiligen van uw gegevens. Als u een upgrade uitvoert naar Modern Backup Storage, kunt u tijdens het beveiligen ook de volumes kiezen waarvoor u de back-ups wilt opslaan en op basis van de ingerichte ruimte controleren. [Meer informatie](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Problemen oplossen

Als Microsoft Azure Backup Server mislukt met fouten tijdens de installatie fase (of een back-up of herstel), raadpleegt u dit [document met fout codes](https://support.microsoft.com/kb/3041338)  voor meer informatie.
U kunt ook verwijzen naar [Azure backup gerelateerde Veelgestelde vragen](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Volgende stappen

Hier vindt u gedetailleerde informatie over [het voorbereiden van uw omgeving voor dpm](/system-center/dpm/prepare-environment-for-dpm). Het bevat ook informatie over de ondersteunde configuraties waarvoor Azure Backup Server kunnen worden geïmplementeerd en gebruikt. U kunt een reeks [Power shell-cmdlets](/powershell/module/dataprotectionmanager/) gebruiken voor het uitvoeren van verschillende bewerkingen.

U kunt deze artikelen gebruiken om een beter inzicht te krijgen in de beveiliging van werk belasting met behulp van Microsoft Azure Backup-Server.

* [SQL Server back-up](backup-azure-backup-sql.md)
* [Share Point server-back-up](backup-azure-backup-sharepoint.md)
* [Alternatieve server back-up](backup-azure-alternate-dpm-server.md)
