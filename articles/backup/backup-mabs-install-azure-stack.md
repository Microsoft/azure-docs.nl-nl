---
title: Azure Backup Server installeren op Azure Stack
description: In dit artikel leert u hoe u Azure Backup Server kunt gebruiken voor het beveiligen of maken van back-up van workloads in Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: d7c685a16db50e51a54387dde49ffb137fcfd626
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519473"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Backup Server installeren op Azure Stack

In dit artikel wordt uitgelegd hoe u Azure Backup Server op Azure Stack. Met Azure Backup Server kunt u IaaS-workloads (Infrastructure as a Service) beveiligen, zoals virtuele machines die worden uitgevoerd in Azure Stack. Een voordeel van het gebruik Azure Backup Server om uw workloads te beveiligen, is dat u alle workloadbeveiliging vanuit één console kunt beheren.

> [!NOTE]
> Raadpleeg de documentatie over beveiligingsfuncties voor Azure Backup informatie over [beveiligingsmogelijkheden.](backup-azure-security-feature.md)
>

## <a name="azure-backup-server-protection-matrix"></a>Beveiligingsmatrix voor Azure Backup Server

Azure Backup Server bebeveiligen de volgende werkbelastingen Azure Stack virtuele machine.

| Beveiligde gegevensbron | Beveiliging en herstel |
| --------------------- | ----------------------- |
| Windows Server Semi Annual-kanaal - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| Windows Server 2016 - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| Windows Server 2012 - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| SQL Server 2016 | Database |
| SQL Server 2014 | Database |
| SQL Server 2012 SP1 | Database |
| SharePoint 2016 | Farm, database, front-end, webserver |
| SharePoint 2013 | Farm, database, front-end, webserver |
| SharePoint 2010 | Farm, database, front-end, webserver |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Vereisten voor de Azure Backup Server omgeving

Houd rekening met de aanbevelingen in deze sectie bij het installeren van Azure Backup Server in Azure Stack omgeving. Het Azure Backup Server controleert of uw omgeving aan de vereiste vereisten is aangepast, maar u bespaart tijd door u voor te bereiden voordat u installeert.

### <a name="determining-size-of-virtual-machine"></a>De grootte van de virtuele machine bepalen

Als u Azure Backup Server virtuele machine Azure Stack machine wilt uitvoeren, gebruikt u grootte A2 of groter. Als u hulp nodig hebt bij het kiezen van de grootte van een virtuele machine, downloadt u [Azure Stack VM-groottecalculator](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuele netwerken op Azure Stack virtuele machines

Alle virtuele machines die in een Azure Stack worden gebruikt, moeten deel uitmaken van hetzelfde virtuele Azure-netwerk en hetzelfde Azure-abonnement.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM-prestaties

Als het account wordt gedeeld met andere virtuele machines, zijn de grootte van het opslagaccount en de IOPS-limieten van invloed op Azure Backup Server VM-prestaties. Daarom moet u een afzonderlijk opslagaccount gebruiken voor de Azure Backup Server virtuele machine. De Azure Backup agent die wordt uitgevoerd op de Azure Backup Server heeft tijdelijke opslag nodig voor:

- eigen gebruik (een cachelocatie),
- gegevens die zijn hersteld vanuit de cloud (lokaal faseringsgebied)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Tijdelijke Azure Backup configureren

Elke Azure Stack virtuele machine wordt geleverd met tijdelijke schijfopslag, die beschikbaar is voor de gebruiker als volume `D:\` . Het lokale faseringsgebied dat nodig is Azure Backup kan worden geconfigureerd om zich in te bevinden en de `D:\` cachelocatie kan worden geplaatst op `C:\` . Op deze manier hoeft er geen opslagruimte te worden verwijderd van de gegevensschijven die zijn gekoppeld aan de Azure Backup Server virtuele machine.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Back-upgegevens opslaan op lokale schijf en in Azure

Azure Backup Server worden back-upgegevens opgeslagen op Azure-schijven die zijn gekoppeld aan de virtuele machine, voor operationeel herstel. Zodra de schijven en opslagruimte aan de virtuele machine zijn gekoppeld, Azure Backup Server de opslag voor u beheren. De hoeveelheid opslag van back-upgegevens is afhankelijk van het aantal en de grootte van de schijven die zijn gekoppeld aan [Azure Stack virtuele machine.](/azure-stack/user/azure-stack-storage-overview) Elke grootte van Azure Stack virtuele machine heeft een maximum aantal schijven dat aan de virtuele machine kan worden gekoppeld. A2 is bijvoorbeeld vier schijven. A3 bestaat uit acht schijven. A4 bestaat uit 16 schijven. Ook hier bepaalt de grootte en het aantal schijven de totale back-upopslaggroep.

> [!IMPORTANT]
> U moet **gegevens** voor operationeel herstel (back-up) Azure Backup Server meer dan vijf dagen bewaren op gekoppelde schijven.
>

Het opslaan van back-upgegevens in Azure vermindert de back-upinfrastructuur op Azure Stack. Als gegevens meer dan vijf dagen oud zijn, moeten deze worden opgeslagen in Azure.

Als u back-upgegevens wilt opslaan in Azure, maakt of gebruikt u een Recovery Services-kluis. Wanneer u de back-up van de Azure Backup Server voorbereidt, configureert [u de Recovery Services-kluis](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Telkens wanneer een back-up van een taak wordt uitgevoerd, wordt er een herstelpunt in de kluis gemaakt. Elke Recovery Services-kluis bevat maximaal 9999 herstelpunten. Afhankelijk van het aantal gemaakte herstelpunten en hoe lang ze worden bewaard, kunt u back-upgegevens vele jaren bewaren. U kunt bijvoorbeeld maandelijkse herstelpunten maken en deze vijf jaar bewaren.

### <a name="scaling-deployment"></a>Implementatie schalen

Als u uw implementatie wilt schalen, hebt u de volgende opties:

- Omhoog schalen: verhoog de grootte van Azure Backup Server virtuele machine van de A-serie naar de [D-serie](/azure-stack/user/azure-stack-manage-vm-disks)en verhoog de lokale opslag volgens de instructies Azure Stack virtuele machine.
- Gegevens offloaden: verzend oudere gegevens naar Azure en behoud alleen de nieuwste gegevens in de opslag die is gekoppeld aan de Azure Backup Server.
- Uitschalen: voeg meer Azure Backup servers toe om de workloads te beveiligen.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3.5 SP1 of hoger moet op de virtuele machine zijn geïnstalleerd.

### <a name="joining-a-domain"></a>Lid worden van een domein

De Azure Backup Server virtuele machine moet lid zijn van een domein. Een domeingebruiker met beheerdersbevoegdheden moet Azure Backup Server installeren op de virtuele machine.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Een IaaS-VM gebruiken in Azure Stack

Wanneer u een server voor Azure Backup Server kiest, begint u met een windows Server 2012 R2 Datacenter- of Windows Server 2016 Datacenter-galerie. Het artikel [Uw eerste virtuele Windows-machine maken in de Azure Portal](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)bevat een zelfstudie om aan de slag te gaan met de aanbevolen virtuele machine. De aanbevolen minimale vereisten voor de virtuele servermachine (VM) moeten zijn: A2 Standard met twee kernen en 3,5 GB RAM-geheugen.

Het beveiligen van workloads Azure Backup Server kent veel nuances. De [beveiligingsmatrix voor MABS](./backup-mabs-protection-matrix.md) helpt deze nuances uit te leggen. Lees dit artikel volledig voordat u de machine implementeert.

> [!NOTE]
> Azure Backup Server is ontworpen om te worden uitgevoerd op een toegewezen virtuele machine met één doel. U kunt de volgende Azure Backup Server installeren:
>
> - Een computer die wordt uitgevoerd als een domeincontroller
> - Een computer waarop de toepassingsserverfunctie is geïnstalleerd
> - Een computer waarop Exchange Server wordt uitgevoerd
> - Een computer die een knooppunt van een cluster is

Voeg altijd Azure Backup Server toe aan een domein. Als u de Azure Backup Server naar een ander domein wilt verplaatsen, installeert u eerst Azure Backup Server en voegt u deze vervolgens toe aan het nieuwe domein. Zodra u Azure Backup Server implementeert, kunt u deze niet verplaatsen naar een nieuw domein.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Opslagreplicatie instellen

Met de optie Opslagreplicatie van Recovery Services-kluis kunt u kiezen tussen geografisch redundante opslag en lokaal redundante opslag. Recovery Services-kluizen maken standaard gebruik van geografisch redundante opslag. Als deze kluis uw primaire kluis is, laat u de opslagoptie ingesteld op geografisch redundante opslag. Kies lokaal redundante opslag als u een goedkopere optie wilt die minder duurzaam is. Meer informatie over [geografisch redundante,](../storage/common/storage-redundancy.md#geo-redundant-storage) [lokaal redundante](../storage/common/storage-redundancy.md#locally-redundant-storage)en [zone-redundante opslagopties](../storage/common/storage-redundancy.md#zone-redundant-storage) vindt u in [Azure Storage overzicht van replicatie.](../storage/common/storage-redundancy.md)

De instelling voor opslagreplicatie bewerken:

1. Selecteer uw kluis om het kluisdashboard en het menu Instellingen te openen. Als het **menu Instellingen** niet wordt geopend, selecteert u **Alle instellingen** in het kluisdashboard.
2. Selecteer **back-upconfiguratie van** **back-upinfrastructuur in het** menu Instellingen om het menu  >   **Back-upconfiguratie te** openen. Kies in **het** menu Back-upconfiguratie de optie voor opslagreplicatie voor uw kluis.

    ![Lijst met back-upkluizen](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Het installatieprogramma Azure Backup Server downloaden

Er zijn twee manieren om het installatieprogramma Azure Backup Server downloaden. U kunt het installatieprogramma Azure Backup Server downloaden via [het Microsoft Downloadcentrum.](https://www.microsoft.com/download/details.aspx?id=55269) U kunt het Azure Backup Server downloaden tijdens het configureren van een Recovery Services-kluis. De volgende stappen helpen u bij het downloaden van het installatieprogramma van de Azure Portal tijdens het configureren van een Recovery Services-kluis.

1. Meld u Azure Stack virtuele machine aan bij uw [Azure-abonnement in Azure Portal](https://portal.azure.com/).
2. Selecteer alle services in het menu aan **de linkerkant.**

    ![Kies de optie Alle services in het hoofdmenu](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Typ Recovery **Services in** het dialoogvenster *Alle services.* Wanneer u begint te typen, wordt de lijst met resources gefilterd met uw invoer. Zodra u deze ziet, selecteert **u Recovery Services-kluizen.**

    ![Typ Recovery Services in het dialoogvenster Alle services](./media/backup-mabs-install-azure-stack/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

4. Selecteer uw kluis in de lijst met Recovery Services-kluizen om het dashboard te openen.

    ![Selecteer uw kluis om het dashboard te openen](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. Selecteer back-up in Aan de slag menu van de kluis **om** de wizard Aan de slag openen.

    ![Aan de slag met back-up](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Het menu Back-up wordt geopend.

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. Selecteer in het back-upmenu in het menu **Waar wordt** uw workload **uitgevoerd? de optie On-premises.** Selecteer in **de vervolgkeuzelijst** Waar wilt u een back-up van maken? de workloads die u wilt beveiligen met behulp van Azure Backup Server. Als u niet zeker weet welke workloads u moet selecteren, kiest u **Hyper-V Virtual Machines** selecteert u **vervolgens Infrastructuur voorbereiden.**

    ![on-premises en workloads als doelstellingen](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Het **menu Infrastructuur voorbereiden** wordt geopend.

7. Selecteer downloaden **in het** menu Infrastructuur voorbereiden **om** een webpagina te openen om de installatiebestanden Azure Backup Server downloaden.

    ![Aan de slag wizard wijzigen](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    De Microsoft-webpagina die als host dient voor de downloadbare bestanden voor Azure Backup Server, wordt geopend.

8. Kies op Microsoft Azure Backup server downloadpagina een taal en selecteer **Downloaden.**

    ![Downloadcentrum wordt geopend](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Het Azure Backup Server bestaat uit acht bestanden: een installatieprogramma en zeven .bin-bestanden. Controleer **bestandsnaam om** alle vereiste bestanden te selecteren en selecteer **Volgende.** Download alle bestanden naar dezelfde map.

    ![Downloadcentrum, geselecteerde bestanden](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    De downloadgrootte van alle installatiebestanden is groter dan 3 GB. Bij een downloadkoppeling van 10 Mbps kan het downloaden van alle installatiebestanden tot 60 minuten duren. De bestanden worden gedownload naar de opgegeven downloadlocatie.

## <a name="extract-azure-backup-server-install-files"></a>Bestanden Azure Backup Server extraheren

Nadat u alle bestanden naar uw virtuele Azure Stack machine hebt gedownload, gaat u naar de downloadlocatie. De eerste fase van het installeren Azure Backup Server is het uitpakken van de bestanden.

![MABS-installatieprogramma downloaden](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Als u de installatie wilt starten, selecteert u in de lijst met gedownloade **bestandenMicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Er is ten minste 4 GB vrije ruimte nodig om de installatiebestanden te extraheren.
    >

2. Selecteer in Azure Backup Server wizard Volgende **om** door te gaan.

    ![Microsoft Azure Backup installatiewizard](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Kies het pad voor de Azure Backup Server bestanden en selecteer **Volgende.**

   ![Doel voor bestanden selecteren](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Controleer de extractielocatie en selecteer **Extraheren.**

   ![Extractielocatie verifiëren](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. De wizard extraheert de bestanden en leest het installatieproces.

   ![Wizard extraheert bestanden](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Zodra het extractieproces is voltooid, selecteert u **Voltooien.** Standaard is **Uitvoeren setup.exe** geselecteerd. Wanneer u **Voltooien selecteert,** Setup.exe server Microsoft Azure Backup op de opgegeven locatie.

   ![Setup extraheert Microsoft Azure Backup Server-bestanden](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Het softwarepakket installeren

In de vorige stap selecteert u **Voltooien om de** extractiefase af te sluiten en de wizard Azure Backup Server starten.

![Microsoft Azure Backup installatiewizard wordt gestart](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server code deelt met Data Protection Manager. U ziet verwijzingen naar Data Protection Manager en DPM in het Azure Backup Server installatieprogramma. Hoewel Azure Backup Server en Data Protection Manager afzonderlijke producten zijn, zijn deze producten nauw verwant.

1. Als u de installatiewizard wilt starten, **selecteert Microsoft Azure Backup Server**.

   ![Selecteer Microsoft Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Selecteer volgende **op** het **welkomstscherm.**

    ![Azure Backup Server - Welkom](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Selecteer in **het scherm Controles** van vereisten Controleren **om** te bepalen of aan de hardware- en softwarevoorwaarden voor Azure Backup Server is voldaan.

    ![Azure Backup Server - Controle op vereisten](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Als uw omgeving aan de vereiste vereisten voldoet, ziet u een bericht waarin wordt aangegeven dat de machine aan de vereisten voldoet. Selecteer **Next**.  

    ![Azure Backup Server - Controle van vereisten is geslaagd](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Als uw omgeving niet voldoet aan de vereiste vereisten, worden de problemen opgegeven. De vereisten die niet zijn voldaan, worden ook vermeld in DpmSetup.log. Los de vereiste fouten op en voer vervolgens **Opnieuw controleren uit.** De installatie kan pas worden voortgezet als aan alle vereisten is voldaan.

    ![Azure Backup Server: niet voldaan aan de installatievoorwaarden](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server vereist SQL Server. Het Azure Backup Server-installatiepakket wordt gebundeld met de juiste SQL Server binaire bestanden. Als u uw eigen SQL-installatie wilt gebruiken, kunt u dat doen. De aanbevolen keuze is echter dat het installatieprogramma een nieuw exemplaar van de SQL Server. Selecteer Controleren en installeren om ervoor te zorgen dat uw keuze **met uw omgeving werkt.**

   > [!NOTE]
   > Azure Backup Server werkt niet met een extern SQL Server exemplaar. Het exemplaar dat wordt gebruikt door Azure Backup Server moet lokaal zijn.
   >

    ![Azure Backup Server SQL-instellingen](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Nadat u de controle heeft gecontroleerd, selecteert u Volgende als de virtuele machine de vereiste Azure Backup Server om de virtuele **machine te installeren.**

    ![Azure Backup Server : aan de vereisten is voldaan](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Als er een fout optreedt met een aanbeveling om de machine opnieuw op te starten, start u de machine opnieuw op. Nadat u de computer opnieuw hebt opgestart, start u het installatieprogramma opnieuw op. Wanneer u het scherm **SQL-instellingen ziet,** selecteert **u Opnieuw controleren.**

5. Geef in **Installatie-instellingen** een locatie op voor de installatie van Microsoft Azure Backup serverbestanden en selecteer **Volgende.**

    ![Geef de locatie op voor de installatie van bestanden](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    De scratchlocatie is vereist om een back-up te maken naar Azure. Zorg ervoor dat de grootte van de scratchlocatie gelijk is aan ten minste 5% van de gegevens die zijn gepland om een back-up te maken naar Azure. Voor schijfbeveiliging moeten afzonderlijke schijven worden geconfigureerd zodra de installatie is voltooid. Zie Gegevensopslag voorbereiden voor meer informatie [over opslaggroepen.](/system-center/dpm/plan-long-and-short-term-data-storage)

6. Geef in **het scherm Beveiligingsinstellingen** een sterk wachtwoord op voor beperkte lokale gebruikersaccounts en selecteer **Volgende.**

    ![Scherm Beveiligingsinstellingen](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Selecteer op **Microsoft Update scherm Opt-In** of u  de Microsoft Update wilt gebruiken om te controleren op updates en selecteer **Volgende.**

   > [!NOTE]
   > U wordt aangeraden Windows Update om te leiden naar Microsoft Update, die beveiliging en belangrijke updates biedt voor Windows en andere producten, zoals Microsoft Azure Backup Server.
   >

    ![Microsoft Update Opt-In scherm](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Controleer de *Samenvatting van instellingen en* selecteer **Installeren.**

    ![Overzicht van instellingen](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Wanneer Azure Backup Server is geïnstalleerd, start het installatieprogramma onmiddellijk het installatieprogramma Microsoft Azure Recovery Services-agent.

9. Het Microsoft Azure recovery services-agent wordt geopend en controleert op internetverbinding. Als er een internetverbinding beschikbaar is, gaat u door met de installatie. Als er geen verbinding is, geeft u proxygegevens op om verbinding te maken met internet. Nadat u de proxyinstellingen hebt opgegeven, selecteert u **Volgende.**

    ![Proxyconfiguratie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Selecteer Installeren om Microsoft Azure Recovery Services-agent **te installeren.**

    ![Agentinstallatie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    De Microsoft Azure Recovery Services-agent, ook wel de Azure Backup-agent genoemd, configureert de Azure Backup Server naar de Recovery Services-kluis. Na de configuratie maakt Azure Backup Server altijd een back-up van gegevens naar dezelfde Recovery Services-kluis.

11. Zodra de Microsoft Azure Recovery Services-agent is geïnstalleerd, selecteert u Volgende om de volgende fase te starten: Azure Backup Server registreren bij de Recovery Services-kluis. 

    ![De installatie van de agent is voltooid](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Het installatieprogramma start de **wizard Server registreren.**

12. Schakel over naar uw Azure-abonnement en uw Recovery Services-kluis. Selecteer downloaden in het menu **Infrastructuur** voorbereiden **om kluisreferenties** te downloaden. Als de **knop Downloaden** in stap 2 niet actief is, selecteert u Al gedownload of gebruikt u de meest recente **Azure Backup Server de** knop te activeren. De kluisreferenties worden gedownload naar de locatie waar u downloads opgeslagen. Let op deze locatie, want u hebt deze nodig voor de volgende stap.

    ![Kluisreferenties downloaden](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. Selecteer in het menu **Kluisidentificatie** de optie **Bladeren** om de referenties voor de Recovery Services-kluis te zoeken.

    ![Menu Kluisidentificatie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Ga in **het dialoogvenster Kluisreferenties** selecteren naar de downloadlocatie, selecteer uw kluisreferenties en selecteer **Openen.**

    Het pad naar de referenties wordt weergegeven in het menu Kluisidentificatie. Selecteer **Volgende om** naar versleutelingsinstellingen te **gaan.**

14. Geef in **het dialoogvenster Versleutelingsinstelling** een wachtwoordzin op voor de back-upversleuteling en een locatie voor het opslaan van de wachtwoordzin en selecteer **Volgende.**

    ![Versleutelingsinstellingen](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    U kunt uw eigen wachtwoordzin verstrekken of de wachtwoordzingenerator gebruiken om er een voor u te maken. De wachtwoordzin is van u en Microsoft kan deze wachtwoordzin niet opslaan of beheren. Sla uw wachtwoordzin op een toegankelijke locatie op om u voor te bereiden op een noodlot.

    Wanneer u Volgende **selecteert,** wordt Azure Backup Server geregistreerd bij de Recovery Services-kluis. Het installatieprogramma blijft de SQL Server en de Azure Backup Server.

    ![Setup installeert SQL en Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Wanneer het installatieprogramma is voltooid, **geeft de Status** aan dat alle software is geïnstalleerd.

    ![Software is geïnstalleerd](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Wanneer de installatie is voltooid, worden de Azure Backup Server-console en Azure Backup Server PowerShell-pictogrammen gemaakt op het server-bureaublad.

## <a name="add-backup-storage"></a>Back-upopslag toevoegen

De eerste back-upkopie wordt bewaard in de opslag die is gekoppeld aan de Azure Backup Server machine. Zie Moderne back-upopslag toevoegen voor meer informatie [over het toevoegen van schijven.](/system-center/dpm/add-storage)

> [!NOTE]
> U moet back-upopslag toevoegen, zelfs als u van plan bent om gegevens naar Azure te verzenden. In de Azure Backup Server bevat de Recovery Services-kluis de tweede kopie van de gegevens terwijl de lokale opslag de eerste (en verplichte) back-upkopie bevat. 
>
>

## <a name="network-connectivity"></a>Netwerkverbinding

Azure Backup Server vereist connectiviteit met de Azure Backup service om het product goed te laten werken. Als u wilt controleren of de machine verbinding heeft met Azure, gebruikt u de ```Get-DPMCloudConnection``` cmdlet in Azure Backup Server PowerShell-console. Als de uitvoer van de cmdlet TRUE is, bestaat er connectiviteit, anders is er geen connectiviteit.

Tegelijkertijd moet het Azure-abonnement een goede status hebben. Als u de status van uw abonnement wilt weten en wilt beheren, meld u zich aan bij de [abonnementsportal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

Zodra u de status van de Azure-connectiviteit en het Azure-abonnement weet, kunt u de onderstaande tabel gebruiken om erachter te komen wat de impact is op de aangeboden back-up-/herstelfunctionaliteit.

| Connectiviteitstoestand | Azure-abonnement | Back-up naar Azure | Back-up naar schijf | Herstellen vanuit Azure | Herstellen uit een schijf |
| --- | --- | --- | --- | --- | --- |
| Verbonden |Actief |Toegestaan |Toegestaan |Toegestaan |Toegestaan |
| Verbonden |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbonden |Deprovisioned |Gestopt |Gestopt |Gestopt en Azure-herstelpunten verwijderd |Gestopt |
| Verbindingsverbinding > 15 dagen verloren |Actief |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbindingsverbinding > 15 dagen verloren |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbindingsverbinding > 15 dagen verloren |Deprovisioned |Gestopt |Gestopt |Gestopt en Azure-herstelpunten verwijderd |Gestopt |

### <a name="recovering-from-loss-of-connectivity"></a>Herstellen van verlies van connectiviteit

Als uw computer beperkte internettoegang heeft, moet u ervoor zorgen dat de firewallinstellingen op de computer of proxy de volgende URL's en IP-adressen toestaan:

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


Zodra de verbinding met Azure is hersteld naar Azure Backup Server, bepaalt de status van het Azure-abonnement welke bewerkingen kunnen worden uitgevoerd. Zodra de server **Verbonden** is, gebruikt u de tabel in [Netwerkverbinding om](backup-mabs-install-azure-stack.md#network-connectivity) de beschikbare bewerkingen te bekijken.

### <a name="handling-subscription-states"></a>Abonnements states verwerken

Het is mogelijk om een Azure-abonnement te wijzigen van *De* status Verlopen of *Deprovisioned* in *De status* Actief. Hoewel de status van het abonnement niet Actief *is:*

- Wanneer een abonnement *deprovisioned heeft,* verliest het de functionaliteit. Als u het abonnement *herstelt naar Actief,* wordt de back-up-/herstelfunctionaliteit hersteld. Als back-upgegevens op de lokale schijf zijn bewaard met een voldoende lange bewaarperiode, kunnen die back-upgegevens worden opgehaald. Back-upgegevens in Azure gaan echter onherstelbaar verloren zodra het abonnement de *status Deprovisioned heeft.*
- Wanneer een abonnement is *verlopen,* verliest het de functionaliteit. Geplande back-ups worden niet uitgevoerd terwijl een abonnement *is verlopen.*

## <a name="troubleshooting"></a>Problemen oplossen

Als Microsoft Azure Backup-server mislukt met fouten tijdens de installatiefase (of back-up of herstel), bekijkt u het [document met foutcodes.](https://support.microsoft.com/kb/3041338)
U kunt ook verwijzen [naar Azure Backup veelgestelde vragen](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Volgende stappen

Het artikel [Preparing your environment for DPM](/system-center/dpm/prepare-environment-for-dpm)(Uw omgeving voorbereiden voor DPM) bevat informatie over ondersteunde Azure Backup Server configuraties.

U kunt de volgende artikelen gebruiken om meer inzicht te krijgen in de beveiliging van workloads met behulp Microsoft Azure Backup Server.

- [SQL Server back-up maken](./backup-mabs-sql-azure-stack.md)
- [Back-up van SharePoint-server](./backup-mabs-sharepoint-azure-stack.md)
- [Alternatieve serverback-up](backup-azure-alternate-dpm-server.md)
