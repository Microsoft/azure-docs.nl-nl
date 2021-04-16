---
title: Offline back-up met behulp van Azure Data Box
description: Meer informatie over het gebruik van Azure Data Box om grote initiële back-upgegevens offline te seeden van de MARS-agent naar een Recovery Services-kluis.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 78adc479ce5733e208d2334d30d7b88e4edf8d6b
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576088"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup offline back-up maken met behulp van Azure Data Box

U kunt deze [Azure Data Box](../databox/data-box-overview.md) om uw grote initiële back-ups van Microsoft Azure Recovery Services (MARS) offline (zonder gebruik van het netwerk) te seeden naar een Recovery Services-kluis. Dit proces bespaart tijd en netwerkbandbreedte die anders zou worden verbruikt bij het online verplaatsen van grote hoeveelheden back-upgegevens via een netwerk met hoge latentie. Deze uitbreiding is momenteel beschikbaar als preview-versie. Offline back-up op basis van Azure Data Box biedt twee verschillende voordelen ten opzichte van [offline back-up op basis van de Azure Import/Export-service:](./backup-azure-backup-import-export.md)

- U hoeft geen eigen met Azure compatibele schijven en connectors aan te schaffen. Azure Data Box schijven die zijn gekoppeld aan de geselecteerde [SKU Data Box verzonden.](https://azure.microsoft.com/services/databox/data/)
- Azure Backup (MARS Agent) kan back-upgegevens rechtstreeks schrijven naar de ondersteunde SKU's van Azure Data Box. Hierdoor hoeft u geen faseringslocatie in terichten voor uw eerste back-upgegevens. U hebt ook geen hulpprogramma's nodig om die gegevens op te maken en naar de schijven te kopiëren.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box met de MARS-agent

In dit artikel wordt uitgelegd hoe u Azure Data Box grote initiële back-upgegevens offline van de MARS-agent naar een Recovery Services-kluis kunt seeden.

## <a name="supported-platforms"></a>Ondersteunde platforms

Het proces voor het seeden van gegevens van de MARS-agent met behulp van Azure Data Box wordt ondersteund op de volgende Windows-SKU's.

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
| Windows Server 2008 SP2 64-bits        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Grootte van back-upgegevens en ondersteunde Data Box-SKU's

| Grootte van back-upgegevens (na compressie door MARS)* per server | Ondersteunde Azure Data Box-SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Azure Data Box schijf](../databox/data-box-disk-overview.md) |
| >7,2 TB en <=80 TB**                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

*Typische compressiesnelheden variëren tussen 10% en 20%. <br>
**Als u verwacht meer dan 80 TB aan initiële back-upgegevens voor één MARS-server te hebben, neem dan contact op met [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Initiële back-upgegevens van één server moeten zich in één Azure Data Box-exemplaar of Azure Data Box-schijf en kunnen niet worden gedeeld tussen meerdere apparaten van dezelfde of verschillende SKU's. Maar een Azure Data Box kan initiële back-ups van meerdere servers bevatten.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-subscription-and-required-permissions"></a>Azure-abonnement en de vereiste machtigingen

- Voor het proces is een Azure-abonnement vereist.
- Het proces vereist dat de gebruiker die is aangewezen om het offline back-upbeleid uit te voeren eigenaar is van het Azure-abonnement.
- De Data Box en de Recovery Services-kluis (waarop de gegevens moeten worden geseed) moeten zich in dezelfde abonnementen hebben.
- We raden u aan het doelopslagaccount dat is gekoppeld aan de Azure Data Box taak en de Recovery Services-kluis zich in dezelfde regio te vinden. Dit is echter niet nodig.

### <a name="get-azure-powershell-370"></a>Get Azure PowerShell 3.7.0

*Dit is de belangrijkste vereiste voor het proces*. Voordat u Azure PowerShell versie 3.7.0 installeert, moet u de volgende controles uitvoeren.

#### <a name="step-1-check-the-powershell-version"></a>Stap 1: de PowerShell-versie controleren

1. Open Windows PowerShell en voer de volgende opdracht uit:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Als in de uitvoer een versie wordt weergegeven die hoger is dan 3.7.0, doet u 'Stap 2'. Ga anders naar Stap 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Stap 2: de PowerShell-versie verwijderen

Verwijder de huidige versie van PowerShell.

1. Verwijder de afhankelijke modules door de volgende opdracht uit te voeren in PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Voer de volgende opdracht uit om ervoor te zorgen dat alle afhankelijke modules zijn verwijderd:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Stap 3: Installeer PowerShell versie 3.7.0

Nadat u hebt gecontroleerd of er geen AzureRM-modules aanwezig zijn, installeert u versie 3.7.0 met een van de volgende methoden:

- Gebruik deze koppeling [vanuit](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)GitHub.

U kunt ook het volgende doen:

- Voer de volgende opdracht uit in het PowerShell-venster:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell kan ook zijn geïnstalleerd met behulp van een msi-bestand. Als u deze wilt verwijderen, verwijdert u deze met behulp van **de optie Programma's** verwijderen in Configuratiescherm.

### <a name="order-and-receive-the-data-box-device"></a>Het apparaat bestellen en Data Box ontvangen

Het offline back-upproces met MARS en Azure Data Box vereist dat de Data Box-apparaten de status Geleverd hebben voordat u offline back-ups activeert met behulp van de MARS-agent. Zie Back-upgegevensgrootte en ondersteunde SKU's om de meest geschikte SKU [voor uw Data Box te bestellen.](#backup-data-size-and-supported-data-box-skus) Volg de stappen in [Zelfstudie: Een schijf Azure Data Box bestellen en](../databox/data-box-disk-deploy-ordered.md) ontvangen van uw Data Box apparaten.

> [!IMPORTANT]
> Selecteer *BlobStorage niet* als **account.** De MARS-agent vereist een account dat pagina-blobs ondersteunt. Dit wordt niet ondersteund wanneer *BlobStorage* is geselecteerd. Selecteer **Opslag V2 (algemeen gebruik v2)** als soort **account** wanneer u het doelopslagaccount voor uw Azure Data Box maken.

![Soort account kiezen in exemplaardetails](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>De MARS-agent installeren en instellen

1. Zorg ervoor dat u eerdere installaties van de MARS-agent verwijdert.
1. Download de nieuwste MARS-agent [van deze website.](https://aka.ms/azurebackup_agent)
1. Voer *MARSAgentInstaller.exe* uit en  voer alleen de stappen uit om de [agent](./install-mars-agent.md#install-and-register-the-agent) te installeren en te registreren bij de Recovery Services-kluis waar u uw back-ups wilt opslaan.

   > [!NOTE]
   > De Recovery Services-kluis moet zich in hetzelfde abonnement als de Azure Data Box maken.

   Nadat de agent is geregistreerd bij de Recovery Services-kluis, volgt u de stappen in de volgende secties.

## <a name="set-up-azure-data-box-devices"></a>Apparaten Azure Data Box instellen

Afhankelijk van de Azure Data Box SKU die u hebt besteld, moet u de stappen in de betreffende secties die volgen, volgen. De stappen laten zien hoe u de apparaten kunt instellen en voorbereiden Data Box de MARS-agent om de eerste back-upgegevens te identificeren en over te dragen.

### <a name="set-up-azure-data-box-disks"></a>Een Azure Data Box instellen

Als u een of meer Azure Data Box schijven (elk maximaal 8 TB) hebt besteld, volgt u de stappen die hier worden vermeld om uw Data Box uit te pakken, te [verbinden en te ontgrendelen.](../databox/data-box-disk-deploy-set-up.md)

>[!NOTE]
>Het is mogelijk dat de server met de MARS-agent geen USB-poort heeft. In dat geval kunt u uw Azure Data Box verbinden met een andere server of client en de hoofdmap van het apparaat beschikbaar maken als een netwerk share.

### <a name="set-up-azure-data-box"></a>Een Azure Data Box

Als u een Azure Data Box (maximaal 100 TB) hebt besteld, volgt u de stappen hier om uw Data Box [instellen.](../databox/data-box-deploy-set-up.md)

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Uw Azure Data Box als een lokaal systeem

De MARS-agent werkt in de context van het lokale systeem, dus moet hetzelfde bevoegdheidsniveau worden opgegeven voor het pad waar het Azure Data Box is verbonden.

Om ervoor te zorgen dat u uw Data Box als een lokaal systeem kunt met behulp van het NFS-protocol:

1. Schakel de client in voor de NFS-functie op de Windows-server waar de MARS-agent is geïnstalleerd. Geef de alternatieve bron *WIM:D:\Sources\Install.wim:4 op.*
1. Download PsExec van de [pagina Sysinternals](/sysinternals/downloads/psexec) naar de server met de MARS-agent geïnstalleerd.
1. Open een opdrachtprompt met verhoogde opdracht en voer de volgende opdracht uit met de map *PSExec.exe* als de huidige map.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Het opdrachtvenster dat wordt geopend vanwege de vorige opdracht, staat in de context Lokaal systeem. Gebruik dit opdrachtvenster om de stappen uit te voeren voor het toevoegen van de Azure-pagina-blob-share als een netwerkstation op uw Windows-server.
1. Volg de stappen in [Verbinding maken met Data Box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) om uw server via NFS te verbinden met de MARS-agent Data Box apparaat. Voer de volgende opdracht uit op de opdrachtprompt lokaal systeem om de Azure-pagina-blobs-share te maken.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Nadat de share is bevestigd, controleert u of u toegang hebt tot X: vanaf uw server. Als dat mogelijk is, gaat u verder met de volgende sectie van dit artikel.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Initiële back-upgegevens overdragen naar Azure Data Box apparaten

1. Open de **Microsoft Azure Backup** toepassing op uw server.
1. Selecteer **back-up** plannen in **het deelvenster Acties.**

    ![Back-up plannen selecteren](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Volg de stappen in de **wizard Back-up plannen.**

1. Voeg items toe door de knop **Items toevoegen te** selecteren. Houd de totale grootte van de items binnen de [groottelimieten die](#backup-data-size-and-supported-data-box-skus) worden ondersteund door de Azure Data Box SKU die u hebt besteld en ontvangen.

    ![Items toevoegen aan back-up](./media/offline-backup-azure-data-box/add-items.png)

1. Selecteer het juiste back-upschema en bewaarbeleid voor **Bestanden en mappen** en **Systeemtoestand.** De systeemtoestand is alleen van toepassing op Windows-servers en niet op Windows-clients.
1. Selecteer op de pagina Eerste back-uptype **kiezen (bestanden** en mappen) van de wizard de optie Overdragen met Microsoft Azure Data Box **schijven** en selecteer **Volgende.**

    ![Eerste back-uptype kiezen](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Meld u aan bij Azure wanneer u hier om wordt gevraagd met behulp van de gebruikersreferenties die eigenaarstoegang hebben voor het Azure-abonnement. Nadat u dit hebt gedaan, ziet u een pagina die lijkt op deze pagina.

    ![Resources maken en vereiste machtigingen toepassen](./media/offline-backup-azure-data-box/creating-resources.png)

   De MARS-agent haalt vervolgens de Data Box taken op in het abonnement met de status Geleverd.

    ![Taken Data Box voor abonnements-id ophalen](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selecteer de juiste Data Box waarvoor u uw schijf hebt uitgepakt, verbonden en Data Box ontgrendeld. Selecteer **Next**.

    ![Selecteer Data Box bestellingen](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selecteer **Apparaat detecteren** op Data Box pagina **Apparaatdetectie.** Deze actie zorgt ervoor dat de MARS-agent scant op lokaal Azure Data Box schijven en deze detecteert.

    ![Data Box apparaatdetectie](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Als u het Azure Data Box-exemplaar hebt verbonden als een netwerk share (vanwege niet-beschikbaarheid van USB-poorten of omdat u het Data Box-apparaat hebt besteld en gekoppeld), mislukt de detectie in eerste instantie. U krijgt de optie om het netwerkpad naar het Data Box invoeren.

    ![Voer het netwerkpad in](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Geef het netwerkpad op naar de hoofdmap van de Azure Data Box schijf. Deze map moet een map bevatten met de naam *PageBlob*.
    >
    >![Hoofdmap van Azure Data Box schijf](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Als het pad van de schijf bijvoorbeeld is en disk1 een map bevat met de naam PageBlob, is het pad dat u op de `\\mydomain\myserver\disk1\` wizardpagina MARS-agent  optreedt.  `\\mydomain\myserver\disk1\`
    >
    >Als u [een apparaat van Azure Data Box van 100 TB](#set-up-azure-data-box-devices)hebt ingesteld, voert u in als het `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` netwerkpad naar het apparaat.

1. Selecteer **Volgende** en selecteer Voltooien **op de** volgende pagina om het back-up- en retentiebeleid met de configuratie van offline back-up op te slaan met behulp van Azure Data Box.

   Op de volgende pagina wordt bevestigd dat het beleid is opgeslagen.

    ![Beleid is opgeslagen](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selecteer **Sluiten** op de vorige pagina.

1. Selecteer **Nu een back-up maken** in het **deelvenster** Acties van de MARS Agent-console. Selecteer **Back-up** maken op de wizardpagina.

    ![Wizard Nu een back-up maken](./media/offline-backup-azure-data-box/backup-now.png)

De MARS-agent begint met het maken van een back-up van de gegevens die u hebt geselecteerd op Azure Data Box apparaat. Dit proces kan enkele uren tot enkele dagen duren. De tijd is afhankelijk van het aantal bestanden en de verbindingssnelheid tussen de server met de MARS-agent en de Azure Data Box schijf.

Nadat de back-up van de gegevens is voltooid, ziet u een pagina op de MARS-agent die lijkt op deze.

![Voortgang van back-up weergegeven](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Stappen na back-up

In deze sectie wordt uitgelegd welke stappen moeten worden genomen nadat de back-up van de gegevens naar Azure Data Box Disk is geslaagd.

- Volg de stappen in dit artikel om de [Azure Data Box verzenden naar Azure](../databox/data-box-disk-deploy-picked-up.md). Als u een apparaat van Azure Data Box van 100 TB hebt gebruikt, volgt u deze stappen om het Azure Data Box [naar Azure te verzenden.](../databox/data-box-deploy-picked-up.md)

- [Controleer de Data Box taak](../databox/data-box-disk-deploy-upload-verify.md) in de Azure Portal. Nadat de Azure Data Box voltooid, verplaatst de MARS-agent de gegevens automatisch van het opslagaccount naar de Recovery Services-kluis op het moment van de volgende geplande back-up. Vervolgens wordt de back-up job *als Taak voltooid* markeert als een herstelpunt is gemaakt.

    >[!NOTE]
    >De MARS-agent activeert back-ups op de tijden die zijn gepland tijdens het maken van het beleid. Deze taken markeren 'Wachten tot Azure Data Box taak is voltooid' tot het moment dat de taak is voltooid.

- Nadat de MARS-agent een herstelpunt heeft gemaakt dat overeenkomt met de eerste back-up, kunt u het opslagaccount of de specifieke inhoud die is gekoppeld aan de Azure Data Box verwijderen.

## <a name="troubleshooting"></a>Problemen oplossen

De Microsoft Azure Recovery Services-agent (MARS) maakt een Azure Active Directory -toepassing (Azure AD) voor u in uw tenant. Deze toepassing vereist een certificaat voor verificatie dat wordt gemaakt en geüpload wanneer u een offline seeding-beleid configureert. We gebruiken Azure PowerShell om het certificaat te maken en te uploaden naar de Azure AD-toepassing.

### <a name="problem"></a>Probleem

Wanneer u offline back-ups configureert, kan er een probleem zijn vanwege een fout in de cmdlet Azure PowerShell back-up. U kunt mogelijk niet meerdere certificaten toevoegen aan dezelfde Azure AD-toepassing die is gemaakt door de MAB-agent. Dit probleem is van invloed op u als u een offline seeding-beleid hebt geconfigureerd voor dezelfde of een andere server.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Controleer of het probleem wordt veroorzaakt door deze specifieke hoofdoorzaak

Als u wilt zien of uw probleem hetzelfde is als het probleem dat eerder is beschreven, moet u een van de volgende stappen volgen.

#### <a name="step-1-of-verification"></a>Stap 1 van de verificatie

Controleer of het volgende foutbericht wordt weergegeven in de MAB-console wanneer u offline back-up hebt geconfigureerd.

![Kan geen offline back-upbeleid maken voor het huidige Azure-account](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Stap 2 van de verificatie

1. Open de **map Temp** in het installatiepad. Het standaardpad voor de tijdelijke map is *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp.* Zoek naar het *CBUICurr-bestand* en open het bestand.

1. Schuif in *het CBUICurr-bestand* naar de laatste regel en controleer of het probleem hetzelfde is als het probleem in dit foutbericht: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Tijdelijke oplossing

Als tijdelijke oplossing voor dit probleem kunt u de volgende stappen volgen en de beleidsconfiguratie opnieuw proberen uit te voeren.

#### <a name="step-1-of-workaround"></a>Stap 1 van de tijdelijke oplossing

Meld u aan bij PowerShell dat wordt weergegeven in de MAB-gebruikersinterface met behulp van een ander account met beheerderstoegang voor het abonnement waarin de Data Box gemaakt.

#### <a name="step-2-of-workaround"></a>Stap 2 van de tijdelijke oplossing

Als er geen andere server offline seeding heeft geconfigureerd en er geen andere server afhankelijk is van de `AzureOfflineBackup_<Azure User Id>` toepassing, verwijdert u deze toepassing. Selecteer **Azure Portal**  >  **Azure Active Directory**  >  **App-registraties**.

>[!NOTE]
> Controleer of er geen andere offline seeding is geconfigureerd voor de toepassing en of er geen andere `AzureOfflineBackup_<Azure User Id>` server afhankelijk is van deze toepassing. Ga naar  >  **Instellingensleutels** in de **sectie Openbare** sleutels. Er mogen geen andere openbare sleutels aan worden toegevoegd. Zie de volgende schermopname ter referentie.
>
>![Openbare sleutels](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Stap 3

Voer de volgende acties uit vanaf de server die u probeert te configureren voor offline back-up.

1. Ga naar het **tabblad Computercertificaattoepassing**  >  **persoonlijk** beheren en zoek naar het certificaat met de naam `CB_AzureADCertforOfflineSeeding_<Timestamp>` .

2. Selecteer het certificaat, klik met de rechtermuisknop **op Alle taken** en selecteer **Exporteren** zonder een persoonlijke sleutel in de CER-indeling.

3. Ga naar de offline back-uptoepassing van Azure die wordt vermeld in stap 2. Selecteer **Instellingensleutels**  >    >  **Openbare sleutel uploaden.** Upload het certificaat dat u in de vorige stap hebt geëxporteerd.

    ![Openbare sleutel uploaden](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Open het register op de server door **regedit** in te voeren in het run-venster.

5. Ga naar het register *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Klik met de rechtermuisknop **op CloudBackupProvider** en voeg een nieuwe tekenreekswaarde toe met de naam `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Voer een van de volgende acties uit om de Azure-gebruikers-id op te halen:
    >
    >- Voer vanuit de met Azure verbonden PowerShell de opdracht `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` uit.
    > - Ga naar het registerpad `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` met de naam *CurrentUserId.*

6. Klik met de rechtermuisknop op de tekenreeks die u in de vorige stap hebt toegevoegd en selecteer **Wijzigen.** Geef in de waarde de vingerafdruk op van het certificaat dat u in stap 2 hebt geëxporteerd. Selecteer **OK**.

7. Dubbelklik op het certificaat om de waarde van de vingerafdruk op te halen. Selecteer het **tabblad Details** en schuif omlaag totdat u het vingerafdrukveld ziet. Selecteer **Vingerafdruk en** kopieer de waarde.

    ![Vingerafdrukveld van certificaat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Vragen

Neem contact op met voor vragen of verduidelijkingen over problemen waarmee u te maken [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) hebt.
