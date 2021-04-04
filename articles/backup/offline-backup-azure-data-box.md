---
title: Offline back-ups met behulp van Azure Data Box
description: Meer informatie over hoe u Azure Data Box kunt gebruiken om grote initiële back-upgegevens offline te brengen van de MARS-agent naar een Recovery Services kluis.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752580"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Offline back-up Azure Backup met behulp van Azure Data Box

U kunt [Azure data Box](../databox/data-box-overview.md) gebruiken om de back-ups van uw grote initiële Microsoft Azure Recovery Services (Mars) offline te brengen (zonder gebruik van een netwerk) naar een Recovery Services kluis. Dit proces bespaart tijd en netwerk bandbreedte, waardoor een grote hoeveelheid back-upgegevens online kan worden verplaatst via een netwerk met een hoge latentie. Deze uitbrei ding is momenteel beschikbaar als preview-versie. Offline back-up op basis van Azure Data Box biedt twee verschillende voor delen ten opzichte van [offline back-ups op basis van de Azure import/export-service](./backup-azure-backup-import-export.md):

- Het is niet nodig om uw eigen Azure-compatibele schijven en connectors te kopen. Azure Data Box stuurt de schijven die zijn gekoppeld aan de geselecteerde [Data Box SKU](https://azure.microsoft.com/services/databox/data/).
- Azure Backup (MARS-agent) kunnen back-upgegevens rechtstreeks naar de ondersteunde Sku's van Azure Data Box schrijven. Op deze manier hoeft u geen tijdelijke locatie in te richten voor de eerste back-upgegevens. U hebt ook geen hulpprogram ma's nodig om die gegevens op de schijven te Format teren en te kopiëren.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box met de MARS-agent

In dit artikel wordt uitgelegd hoe u Azure Data Box kunt gebruiken om grote initiële back-upgegevens offline te brengen van de MARS-agent naar een Recovery Services kluis.

## <a name="supported-platforms"></a>Ondersteunde platforms

Het proces voor het seeden van gegevens van de MARS-agent met behulp van Azure Data Box wordt ondersteund op de volgende Windows-Sku's.

| **Besturingssysteem**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Werkstation**                        |                                                              |
| Windows 10 64-bits                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64-bits                    | Enterprise, Pro                                             |
| Windows 8 64-bits                      | Enterprise, Pro                                             |
| Windows 7 64-bits                      | Ultimate, Enterprise, Professional, Home Premium Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64-bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64-bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64-bits         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64-bits            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64-bits    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64-bits | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64-bits    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64-bits     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bits        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Grootte van back-upgegevens en ondersteunde Data Box Sku's

| Grootte van back-upgegevens (na compressie door MARS) * per server | Ondersteund Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Azure Data Box schijf](../databox/data-box-disk-overview.md) |
| >7,2 TB en <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* Typische compressie tarieven variëren tussen 10% en 20%. <br>
* * Als u verwacht meer dan 80 TB aan eerste back-upgegevens voor één MARS-server, neemt u contact op met [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>De eerste back-upgegevens van één server moeten zich in één Azure Data Box exemplaar of Azure Data Box schijf bevinden en kunnen niet worden gedeeld tussen meerdere apparaten van dezelfde of verschillende Sku's. Een Azure Data Box apparaat kan echter eerste back-ups van meerdere servers bevatten.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-subscription-and-required-permissions"></a>Azure-abonnement en de vereiste machtigingen

- Voor het proces is een Azure-abonnement vereist.
- Het proces vereist dat de gebruiker die het offline back-upbeleid uitvoert, een eigenaar van het Azure-abonnement is.
- De Data Box-taak en de Recovery Services kluis (waartoe de gegevens moeten worden geseedd) moeten zich in dezelfde abonnementen bevinden.
- We raden aan dat het doel opslag account dat is gekoppeld aan de Azure Data Box-taak en de Recovery Services kluis zich in dezelfde regio bevinden. Dit is echter niet nodig.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 ophalen

*Dit is de belangrijkste vereiste voor het proces*. Voordat u Azure PowerShell installeert, moet u voor versie 3.7.0 de volgende controles uitvoeren.

#### <a name="step-1-check-the-powershell-version"></a>Stap 1: de Power shell-versie controleren

1. Open Windows Power shell en voer de volgende opdracht uit:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Als in de uitvoer een versie wordt weer gegeven die hoger is dan 3.7.0, voert u ' stap 2 ' uit. Ga anders verder met stap 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Stap 2: de Power shell-versie verwijderen

Verwijder de huidige versie van Power shell.

1. Verwijder de afhankelijke modules door de volgende opdracht uit te voeren in Power shell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Voer de volgende opdracht uit om ervoor te zorgen dat alle afhankelijke modules goed worden verwijderd:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Stap 3: Power shell-versie 3.7.0 installeren

Nadat u hebt gecontroleerd of er geen AzureRM-modules aanwezig zijn, installeert u versie 3.7.0 met behulp van een van de volgende methoden:

- Gebruik [deze koppeling](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)vanuit github.

U kunt ook het volgende doen:

- Voer de volgende opdracht uit in het Power shell-venster:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell kan ook zijn geïnstalleerd met behulp van een MSI-bestand. Als u deze wilt verwijderen, verwijdert u deze met behulp van de optie **Program Ma's verwijderen** in het configuratie scherm.

### <a name="order-and-receive-the-data-box-device"></a>Bestel en ontvang het Data Box apparaat

Het offline back-upproces met MARS en Azure Data Box vereist dat de Data Box apparaten de status bezorgd hebben voordat u offline back-ups met behulp van de MARS-agent gaat activeren. Als u de meest geschikte SKU voor uw vereiste wilt best Ellen, raadpleegt u [Data Size van back-up en ondersteunde data Box sku's](#backup-data-size-and-supported-data-box-skus). Volg de stappen in de [zelf studie: bestel een Azure data Box schijf](../databox/data-box-disk-deploy-ordered.md) om de data Box-apparaten te best Ellen en te ontvangen.

> [!IMPORTANT]
> Selecteer *BlobStorage* voor het **soort account** niet. De MARS-agent vereist een account dat pagina-blobs ondersteunt. dit wordt niet ondersteund wanneer *BlobStorage* is geselecteerd. Selecteer **opslag v2 (algemeen gebruik v2)** als het **soort account** wanneer u het doel opslag account voor uw Azure data Box-taak maakt.

![Account type kiezen in details van exemplaar](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>De MARS-agent installeren en instellen

1. Zorg ervoor dat u eerdere installaties van de MARS-agent verwijdert.
1. Down load de nieuwste MARS-agent van [deze website](https://aka.ms/azurebackup_agent).
1. Voer *MARSAgentInstaller.exe* uit en voer *alleen* de stappen uit om [de agent te installeren en te registreren](./install-mars-agent.md#install-and-register-the-agent) bij de Recovery Services kluis waar u uw back-ups wilt opslaan.

   > [!NOTE]
   > De Recovery Services kluis moet zich in hetzelfde abonnement benemen als de Azure Data Box taak.

   Nadat de agent is geregistreerd bij de Recovery Services kluis, volgt u de stappen in de volgende secties.

## <a name="set-up-azure-data-box-devices"></a>Azure Data Box-apparaten instellen

Afhankelijk van de Azure Data Box SKU die u hebt besteld, voert u de stappen uit die in de volgende secties worden besproken. De stappen laten zien hoe u de Data Box-apparaten instelt en voorbereidt voor de MARS-agent om de eerste back-upgegevens te identificeren en over te dragen.

### <a name="set-up-azure-data-box-disks"></a>Azure Data Box schijven instellen

Als u een of meer Azure Data Box schijven hebt besteld (Maxi maal 8 TB elk), volgt u de stappen die hier worden beschreven om [uw data Box schijf uit te pakken, te verbinden en te ontgrendelen](../databox/data-box-disk-deploy-set-up.md).

>[!NOTE]
>Het is mogelijk dat de server met de MARS-agent geen USB-poort heeft. In dat geval kunt u uw Azure Data Box schijf aansluiten op een andere server of client en de hoofdmap van het apparaat beschikbaar maken als een netwerk share.

### <a name="set-up-azure-data-box"></a>Azure Data Box instellen

Als u een Azure Data Box-exemplaar (tot 100 TB) hebt besteld, volgt u de onderstaande stappen [om uw data Box-exemplaar in te stellen](../databox/data-box-deploy-set-up.md).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Uw Azure Data Box-exemplaar koppelen als een lokaal systeem

De MARS-agent wordt uitgevoerd in de context van het lokale systeem, zodat hiervoor hetzelfde bevoegdheids niveau moet worden gegeven aan het koppelingspad waar het Azure Data Box-exemplaar is verbonden.

Om ervoor te zorgen dat u uw Data Box-apparaat als lokaal systeem kunt koppelen met behulp van het NFS-protocol:

1. Schakel de client in voor de NFS-functie op de Windows-Server waarop de MARS-agent is geïnstalleerd. Geef de alternatieve bron- *Wim op: D: \Sources\Install.Wim: 4*.
1. Down load PsExec van de [Sysinternals](/sysinternals/downloads/psexec) -pagina naar de server waarop de Mars-agent is geïnstalleerd.
1. Open een opdracht prompt met verhoogde bevoegdheid en voer de volgende opdracht uit met de map die *PSExec.exe* als de huidige map bevat.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Het opdracht venster dat wordt geopend vanwege de vorige opdracht bevindt zich in de context van het lokale systeem. Gebruik dit opdracht venster om de stappen uit te voeren om de Azure page BLOB-share te koppelen als een netwerk station op uw Windows-Server.
1. Volg de stappen in [verbinding maken met data Box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) om uw server te verbinden met de Mars-agent met de data Box apparaat via NFS. Voer de volgende opdracht uit op de opdracht prompt van het lokale systeem om de share Azure-pagina-blobs te koppelen.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Nadat de share is gekoppeld, controleert u of u toegang hebt tot X: vanaf uw server. Als dat mogelijk is, gaat u verder met de volgende sectie van dit artikel.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Initiële back-upgegevens overdragen naar Azure Data Box-apparaten

1. Open de **Microsoft Azure backup** -toepassing op uw server.
1. Selecteer in het deel venster **acties** de optie **back-up plannen**.

    ![Back-up plannen selecteren](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Volg de stappen in de **wizard Back-up plannen**.

1. Voeg items toe door de knop **items toevoegen** te selecteren. Behoud de totale grootte van de items binnen de [grootte limieten die worden ondersteund door de Azure data Box SKU](#backup-data-size-and-supported-data-box-skus) die u hebt besteld en ontvangen.

    ![Items toevoegen aan back-up](./media/offline-backup-azure-data-box/add-items.png)

1. Selecteer het juiste back-upschema en het Bewaar beleid voor **bestanden en mappen** en de **systeem status**. De systeem status is alleen van toepassing op Windows-servers en niet voor Windows-clients.
1. Selecteer op de pagina **eerste back-uptype selecteren (bestanden en mappen)** van de wizard de optie **overdracht met Microsoft Azure data Box schijven** en selecteer **volgende**.

    ![Eerste back-uptype kiezen](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Meld u aan bij Azure wanneer u hierom wordt gevraagd met de gebruikers referenties met de eigenaar van het Azure-abonnement. Nadat u dit hebt gedaan, ziet u een pagina die er ongeveer uit komt te zien.

    ![Resources maken en vereiste machtigingen Toep assen](./media/offline-backup-azure-data-box/creating-resources.png)

   De MARS-agent haalt vervolgens de Data Box taken op in het abonnement die de status bezorgd hebben.

    ![Data Box-taken voor abonnements-ID ophalen](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selecteer de juiste Data Box order waarvoor u de Data Box schijf hebt uitgepakt, verbonden en ontgrendeld. Selecteer **Next**.

    ![Data Box orders selecteren](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selecteer **apparaat detecteren** op de pagina **detectie van data Box apparaten** . Met deze actie wordt de MARS-agent gescand op lokaal gekoppelde Azure Data Box schijven en worden ze gedetecteerd.

    ![Detectie van Data Box apparaat](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Als u het Azure Data Box-exemplaar als een netwerk share hebt verbonden (vanwege een niet-beschik baarheid van USB-poorten of omdat u het 100-TB Data Box apparaat hebt besteld en gekoppeld), mislukt de detectie eerst. U krijgt de mogelijkheid om het netwerkpad naar het Data Box apparaat in te voeren.

    ![Voer het netwerkpad in](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Geef het netwerkpad naar de hoofdmap van de Azure Data Box schijf op. Deze map moet een map met de naam *PageBlob* bevatten.
    >
    >![Hoofdmap van Azure Data Box schijf](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Als het pad van de schijf `\\mydomain\myserver\disk1\` en *Disk1* bijvoorbeeld een map bevat met de naam *PageBlob*, is het pad dat u op de wizard pagina van de Mars-agent hebt ingevoerd `\\mydomain\myserver\disk1\` .
    >
    >Als u [een apparaat met een Azure Data Box 100-TB instelt](#set-up-azure-data-box-devices), voert u `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` het netwerkpad naar het apparaat in.

1. Selecteer **volgende** en selecteer **volt ooien** op de volgende pagina om het back-up-en bewaar beleid met de configuratie van offline back-up op te slaan met behulp van Azure data box.

   Op de volgende pagina wordt bevestigd dat het beleid is opgeslagen.

    ![Het beleid is opgeslagen](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selecteer **sluiten** op de vorige pagina.

1. Selecteer **Nu back-up** maken in het deel venster **acties** van de Mars agent-console. Selecteer **een back** -up op de wizard pagina.

    ![Wizard nu back-up maken](./media/offline-backup-azure-data-box/backup-now.png)

De MARS-agent begint met het maken van een back-up van de gegevens die u hebt geselecteerd voor het Azure Data Box apparaat. Dit proces kan enkele uren tot enkele dagen duren. De hoeveelheid tijd is afhankelijk van het aantal bestanden en de verbindings snelheid tussen de server en de MARS-agent en de Azure Data Box schijf.

Nadat het maken van de back-up van de gegevens is voltooid, ziet u een pagina op de MARS-agent die er ongeveer als volgt uitziet.

![Voortgang van back-up weer gegeven](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Stappen na de back-up

In deze sectie worden de stappen beschreven die u moet uitvoeren nadat de back-up van de gegevens naar de Azure Data Box Disk is voltooid.

- Volg de stappen in dit artikel om [de Azure data Box schijf naar Azure te verzenden](../databox/data-box-disk-deploy-picked-up.md). Als u een apparaat met Azure Data Box 100-TB hebt gebruikt, volgt u deze stappen om [het Azure data Box-apparaat naar Azure te verzenden](../databox/data-box-deploy-picked-up.md).

- [Controleer de data Box taak](../databox/data-box-disk-deploy-upload-verify.md) in de Azure Portal. Nadat de Azure Data Box taak is voltooid, verplaatst de MARS-agent automatisch de gegevens van het opslag account naar de Recovery Services kluis op het moment van de volgende geplande back-up. Vervolgens wordt de back-uptaak als *taak voltooid* gemarkeerd als een herstel punt is gemaakt.

    >[!NOTE]
    >De MARS-agent activeert back-ups op de tijden die zijn gepland tijdens het maken van het beleid. Met deze taken wordt de vlag ' wachten tot Azure Data Box taak is voltooid ' geactiveerd totdat de taak is voltooid.

- Nadat de MARS-agent een herstel punt heeft gemaakt dat overeenkomt met de eerste back-up, kunt u het opslag account of de specifieke inhoud verwijderen die aan de Azure Data Box-taak is gekoppeld.

## <a name="troubleshooting"></a>Problemen oplossen

Met de Microsoft Azure Recovery Services-agent (MARS) wordt een Azure Active Directory-toepassing (Azure AD) voor u gemaakt in uw Tenant. Voor deze toepassing is een certificaat vereist voor verificatie dat wordt gemaakt en geüpload wanneer u een offline seeding-beleid configureert. We gebruiken Azure PowerShell om het certificaat te maken en te uploaden naar de Azure AD-toepassing.

### <a name="problem"></a>Probleem

Wanneer u offline back-ups configureert, is het mogelijk dat u een probleem ondervindt vanwege een fout in de Azure PowerShell-cmdlet. U kunt mogelijk niet meerdere certificaten toevoegen aan dezelfde Azure AD-toepassing die door de MAB-agent is gemaakt. Dit probleem heeft gevolgen voor u als u een offline seeding-beleid voor dezelfde of een andere server hebt geconfigureerd.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Controleren of het probleem wordt veroorzaakt door deze specifieke hoofd oorzaak

Voer een van de volgende stappen uit om te zien of het probleem hetzelfde is als de eerder beschreven.

#### <a name="step-1-of-verification"></a>Stap 1 van verificatie

Controleer of het volgende fout bericht wordt weer gegeven in de MAB-console wanneer u offline back-up hebt geconfigureerd.

![Kan geen offline back-upbeleid maken voor het huidige Azure-account](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Stap 2 van verificatie

1. Open de map **temp** in het installatiepad. Het standaardpad voor de tijdelijke map is *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Zoek naar het *CBUICurr* -bestand en open het bestand.

1. Ga in het *CBUICurr* -bestand naar de laatste regel en controleer of het probleem hetzelfde is als in dit fout bericht: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Tijdelijke oplossing

Als tijdelijke oplossing om dit probleem op te lossen, voert u de volgende stappen uit en probeert u de beleids configuratie opnieuw uit te voeren.

#### <a name="step-1-of-workaround"></a>Stap 1 van de tijdelijke oplossing

Meld u aan bij Power shell die wordt weer gegeven in de gebruikers interface van MAB met behulp van een ander account met beheerders toegang voor het abonnement dat de Data Box-taak heeft gemaakt.

#### <a name="step-2-of-workaround"></a>Stap 2 van de tijdelijke oplossing

Als voor geen enkele andere server offline seeding is geconfigureerd en er geen andere server afhankelijk is van de `AzureOfflineBackup_<Azure User Id>` toepassing, moet u deze toepassing verwijderen. Selecteer **Azure Portal**  >  **Azure Active Directory**  >  **app-registraties**.

>[!NOTE]
> Controleer of de toepassing geen `AzureOfflineBackup_<Azure User Id>` andere offline seeding heeft geconfigureerd en of er geen andere server afhankelijk is van deze toepassing. Ga naar **instellingen**  >  **sleutels** onder de sectie **open bare sleutels** . Er mogen geen andere open bare sleutels worden toegevoegd. Zie de volgende scherm afbeelding voor naslag informatie.
>
>![Open bare sleutels](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Stap 3

Voer de volgende acties uit vanaf de-server die u wilt configureren voor offline back-ups.

1. Ga naar het tabblad **computer certificaat toepassing beheren**  >   en zoek naar het certificaat met de naam `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. Selecteer het certificaat, klik met de rechter muisknop op **alle taken** en selecteer **exporteren** zonder persoonlijke sleutel in de. CER-indeling.

3. Ga naar de Azure offline back-uptoepassing die u in stap 2 hebt genoemd. **Instellingen**  >  **sleutels** selecteren  >  **open bare sleutel uploaden**. Upload het certificaat dat u in de vorige stap hebt geëxporteerd.

    ![Open bare sleutel uploaden](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Open het REGI ster in de-server door in het venster uitvoeren **regedit** in te voeren.

5. Ga naar het REGI ster *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Klik met de rechter muisknop op **CloudBackupProvider** en voeg een nieuwe teken reeks waarde toe met de naam `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Voer een van de volgende acties uit om de gebruikers-ID van Azure op te halen:
    >
    >- Voer de opdracht uit vanuit de met Azure verbonden Power shell `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` .
    > - Ga naar het registerpad `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` met de naam *CurrentUserId*.

6. Klik met de rechter muisknop op de teken reeks die u in de vorige stap hebt toegevoegd en selecteer **wijzigen**. Geef in de waarde de vinger afdruk op van het certificaat dat u in stap 2 hebt geëxporteerd. Selecteer **OK**.

7. Als u de waarde van de vinger afdruk wilt ophalen, dubbelklikt u op het certificaat. Selecteer het tabblad **Details** en schuif omlaag totdat u het veld vinger afdruk ziet. Selecteer **vinger afdruk** en kopieer de waarde.

    ![Vingerafdruk veld van certificaat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Vragen

Neem contact op met de voor vragen of informatie over problemen die u ondervindt [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
