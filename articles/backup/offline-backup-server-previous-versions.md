---
title: Offline back-up voor Data Protection Manager (DPM) en Microsoft Azure Backup Server (MABS)-vorige versies
description: Met Azure Backup kunt u gegevens van het netwerk verzenden met behulp van de Azure import/export-service. In dit artikel wordt de werk stroom voor offline back-ups voor eerdere versies van DPM en Azure Backup Server uitgelegd.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0405ab66b7714f00349419e94bb064267ca711a6
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702182"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>Offline back-upwerk stroom voor DPM en Azure Backup Server (vorige versies)

>[!IMPORTANT]
>Deze stappen zijn van toepassing op DPM 2019 RTM en eerdere versies en MABS v3 RTM en eerdere versies.

Azure Backup heeft verschillende ingebouwde efficiency waarmee netwerk-en opslag kosten worden bespaard tijdens de eerste volledige back-ups van gegevens naar Azure. Bij eerste volledige back-ups worden grote hoeveel heden gegevens overgebracht en is er meer netwerk bandbreedte nodig in vergelijking met de volgende back-ups die alleen de Deltas/toenames overdragen. Azure Backup comprimeert de eerste back-ups. Dankzij het proces van offline seeding kan Azure Backup schijven gebruiken om de gecomprimeerde initiële back-upgegevens offline te uploaden naar Azure.

Het offline-seeding proces van Azure Backup is nauw geïntegreerd met de [Azure import/export-service](../import-export/storage-import-export-service.md). Met deze service kunt u gegevens overdragen naar Azure met behulp van schijven. Als u een TBs hebt van de eerste back-upgegevens die moeten worden overgezet via een netwerk met een hoge latentie en een lage band breedte, kunt u de werk stroom voor offline seeding gebruiken om de eerste back-up op een of meer harde schijven naar een Azure-Data Center te verzenden. Dit artikel bevat een overzicht en verdere stappen voor het volt ooien van deze werk stroom voor System Center Data Protection Manager (DPM) en Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Het proces van offline back-up voor de Microsoft Azure Recovery Services-agent (MARS) verschilt van DPM en MABS. Zie [offline back-upwerk stroom in azure backup](backup-azure-backup-import-export.md)voor meer informatie over het gebruik van offline back-ups met de Mars-agent. Offline back-up wordt niet ondersteund voor systeem status back-ups die worden uitgevoerd met behulp van de Azure Backup-Agent.
>

## <a name="overview"></a>Overzicht

Met de offline-seeding-mogelijkheid van Azure Backup en de Azure import/export-service, is het eenvoudig om de gegevens offline naar Azure te uploaden met behulp van schijven. Het offline back-upproces bestaat uit de volgende stappen:

> [!div class="checklist"]
>
> * De back-upgegevens worden naar een faserings locatie geschreven in plaats van via het netwerk te verzenden.
> * De gegevens op de faserings locatie worden vervolgens naar een of meer SATA-schijven geschreven met behulp van het *AzureOfflineBackupDiskPrep* -hulp programma.
> * Er wordt automatisch een Azure import-taak gemaakt door het hulp programma.
> * De SATA-schijven worden vervolgens verzonden naar het dichtstbijzijnde Azure-Data Center.
> * Nadat het uploaden van de back-upgegevens naar Azure is voltooid, Azure Backup de back-upgegevens naar de back-upkluis gekopieerd en de incrementele back-ups worden gepland.

## <a name="supported-configurations"></a>Ondersteunde configuraties

Offline back-ups worden ondersteund voor alle implementatie modellen van Azure Backup die een back-up van gegevens van on-premises naar de micro soft-Cloud maken. Deze modellen omvatten:

> [!div class="checklist"]
>
> * Back-ups maken van bestanden en mappen met de MARS-agent of de Azure Backup-Agent.
> * Back-ups maken van alle werk belastingen en bestanden met DPM.
> * Back-ups maken van alle werk belastingen en bestanden met MABS.

>[!NOTE]
>Azure CSP-abonnementen worden niet ondersteund voor gebruik met offline seeding voor DPM 2019 RTM en eerdere versies, en MABS v3 RTM en eerdere versies. Online back-ups via het netwerk worden nog steeds ondersteund.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan voordat u de werk stroom voor offline back-ups start:

* Er is een [Recovery Services kluis](backup-azure-recovery-services-vault-overview.md) gemaakt. Als u er een wilt maken, volgt u de stappen in [een Recovery Services kluis maken](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Een Azure Backup Agent of MABS of DPM is geïnstalleerd op Windows Server of een Windows-client, indien van toepassing, en de computer is geregistreerd bij de Recovery Services kluis. Zorg ervoor dat alleen de [meest recente versie van Azure backup](https://go.microsoft.com/fwlink/?linkid=229525) wordt gebruikt.
* [Down load het Azure Publish settings-bestand](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) op de computer van waaruit u een back-up van uw gegevens wilt maken. Het abonnement waaruit u het publicatie-instellingen bestand downloadt, kan afwijken van het abonnement dat de Recovery Services kluis bevat. Als uw abonnement is gekoppeld aan soevereine Azure-Clouds, gebruikt u de volgende koppelingen voor het downloaden van het Azure Publish settings-bestand.

    | Soevereine Cloud regio | Bestands koppeling van Azure Publish-instellingen |
    | --- | --- |
    | Verenigde Staten | [Koppeling](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Koppeling](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Een Azure-opslag account met het Resource Manager-implementatie model is gemaakt in het abonnement van waaruit u het bestand met publicatie-instellingen hebt gedownload. Maak in het opslag account een nieuwe BLOB-container, die wordt gebruikt als de bestemming.

  ![Een opslag account maken met Resource Manager-ontwikkeling](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* Een faserings locatie, die een netwerk share kan zijn of een extra station op de computer, intern of extern, met voldoende schijf ruimte om uw eerste kopie te bewaren, wordt gemaakt. Als u bijvoorbeeld een back-up van een bestands server van 500 GB wilt maken, moet u ervoor zorgen dat de tijdelijke ruimte ten minste 500 GB is. (Er wordt een kleiner bedrag gebruikt vanwege compressie.)
* Zorg ervoor dat er voor schijven die naar Azure worden verzonden, alleen 2,5-inch SSD-of 2,5-inch of 3,5-inch SATA II/III interne harde schijven worden gebruikt. U kunt harde schijven van Maxi maal 10 TB gebruiken. Raadpleeg de [documentatie van de Azure import/export-service](../import-export/storage-import-export-requirements.md#supported-hardware) voor de meest recente set stations die door de service worden ondersteund.
* De SATA-schijven moeten zijn verbonden met een computer (waarnaar wordt verwezen als een *Kopieer computer*) van waaruit het kopiëren van back-upgegevens van de faserings locatie naar de SATA-schijven is voltooid. Zorg ervoor dat BitLocker is ingeschakeld op de Kopieer computer.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>De server voorbereiden voor het offline back-upproces

>[!NOTE]
> Als u de vermelde hulpprogram ma's, zoals *AzureOfflineBackupCertGen.exe*, niet kunt vinden in uw installatie van de Mars-agent, schrijft u AskAzureBackupTeam@microsoft.com deze naar om toegang te krijgen.

* Open een opdracht prompt met verhoogde bevoegdheid op de server en voer de volgende opdracht uit:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Het hulp programma maakt een Azure offline back-up Active Directory toepassing als deze niet bestaat.

    Als een toepassing al bestaat, wordt u door dit uitvoer bare programma gevraagd het certificaat hand matig te uploaden naar de toepassing in de Tenant. Volg de stappen in [deze sectie](#manually-upload-an-offline-backup-certificate) om het certificaat hand matig te uploaden naar de toepassing.

* Het hulp programma *AzureOfflineBackupCertGen.exe* genereert een *OfflineApplicationParams.xml* bestand. Kopieer dit bestand naar de server met MABS of DPM.
* Installeer de [nieuwste Mars-agent](https://aka.ms/azurebackup_agent) op het DPM-exemplaar of de Azure backup-server.
* Registreer de server bij Azure.
* Voer de volgende opdracht uit:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* Met de vorige opdracht maakt u het bestand `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>Een offline back-upcertificaat hand matig uploaden

Volg deze stappen om het offline back-upcertificaat hand matig te uploaden naar een eerder gemaakte Azure Active Directory toepassing die bedoeld is voor offline back-ups.

1. Meld u aan bij de Azure-portal.
1. Ga naar **Azure Active Directory** > **App-registraties**.
1. Zoek op het tabblad **toepassingen in eigendom** een toepassing met de indeling van de weergave naam `AzureOfflineBackup _<Azure User Id` .

    ![Zoek naar de toepassing op het tabblad toepassingen in eigendom](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. Selecteer de toepassing. Ga onder **beheren** in het linkerdeel venster naar **certificaten & geheimen**.
1. Controleer op bestaande certificaten of open bare sleutels. Als er geen is, kunt u de toepassing veilig verwijderen door de knop **verwijderen** te selecteren op de **overzichts** pagina van de toepassing. Vervolgens kunt u de stappen opnieuw uitvoeren om [de server voor te bereiden voor het offline back-](#prepare-the-server-for-the-offline-backup-process) upproces en de volgende stappen over te slaan. Ga anders verder met de volgende stappen van het DPM-exemplaar of Azure Backup server waarvoor u offline back-ups wilt configureren.
1. Typ *Certlm. msc* uit het **Start** - **Run**. Selecteer in het venster **certificaten-lokale computer** het tabblad **certificaten-lokale computer**  >  **persoonlijk** . Zoek naar het certificaat met de naam `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
1. Selecteer het certificaat, klik met de rechter muisknop op **alle taken** en selecteer vervolgens **exporteren**, zonder een persoonlijke sleutel, in de. CER-indeling.
1. Ga naar de Azure-toepassing voor offline back-ups in de Azure Portal.
1. Selecteer   >  **certificaten beheren & geheimen**  >  **Upload certificaat**. Upload het certificaat dat u in de vorige stap hebt geëxporteerd.

    ![Het certificaat uploaden](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. Open het REGI ster op de server door in het venster uitvoeren **regedit** in te voeren.
1. Ga naar de register vermelding *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Klik met de rechter muisknop op **CloudBackupProvider** en voeg een nieuwe teken reeks waarde toe met de naam `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Voer een van de volgende stappen uit om de gebruikers-ID van Azure te vinden:
    >
    >* Voer de opdracht uit vanuit de met Azure verbonden Power shell `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` .
    >* Ga naar het registerpad `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` .

1. Klik met de rechter muisknop op de teken reeks die u in de vorige stap hebt toegevoegd en selecteer **wijzigen**. Geef in de waarde de vinger afdruk op van het certificaat dat u in stap 7 hebt geëxporteerd. Selecteer vervolgens **OK**.
1. Als u de waarde van de vinger afdruk wilt ophalen, dubbelklikt u op het certificaat. Selecteer het tabblad **Details** en schuif omlaag totdat u het veld vinger afdruk ziet. Selecteer **vinger afdruk** en kopieer de waarde.

    ![Waarde kopiëren uit het veld vinger afdruk](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. Ga door naar de [werk stroom](#workflow) sectie om door te gaan met het offline back-upproces.

## <a name="workflow"></a>Werkstroom

De informatie in deze sectie helpt u bij het volt ooien van de werk stroom voor offline back-ups, zodat uw gegevens kunnen worden geleverd aan een Azure-Data Center en naar Azure Storage worden geüpload. Als u vragen hebt over de import service of een aspect van het proces, raadpleegt u de [documentatie overzicht](../import-export/storage-import-export-service.md) van het importeren van de service.

### <a name="initiate-offline-backup"></a>Offline back-up initiëren

1. Wanneer u een back-up plant, ziet u de volgende pagina in Windows Server, een Windows-client of DPM.

    ![Pagina importeren](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    Hier is de overeenkomstige pagina in DPM. <br/>

    ![De pagina DPM en Azure Backup Server importeren](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    De vakken die u invult, zijn:

   * **Faserings locatie**: de tijdelijke opslag locatie waarnaar de eerste back-up wordt geschreven. De faserings locatie Bekan zich op een netwerk share of een lokale computer. Als de Kopieer computer en de bron computer verschillend zijn, geeft u het volledige netwerkpad van de faserings locatie op.
   * **Azure Publish-instellingen**: het lokale pad naar het bestand met publicatie-instellingen.
   * **Azure import-taak naam**: de unieke naam waarmee de Azure import/export-service en Azure Backup het volgen van de overdracht van gegevens die op schijven zijn verzonden naar Azure.
   * **Azure-abonnements-id**: de id van het Azure-abonnement voor het abonnement waarvandaan u het Azure Publish settings-bestand hebt gedownload.
   * **Azure Storage account**: de naam van het opslag account in het Azure-abonnement dat is gekoppeld aan het Azure Publish settings-bestand.
   * **Azure storage container**: de naam van de doel-Storage-Blob in het Azure-opslag account waarin de back-upgegevens worden geïmporteerd.

   Sla de **faserings locatie** en de gegevens van de **Azure import-taak naam** op die u hebt ingevoerd. Het is vereist om de schijven voor te bereiden.

1. Voltooi de werk stroom. Als u de offline back-upkopie wilt initiëren, selecteert u **Nu back-up maken** in de beheer console van de Azure backup-agent. Als onderdeel van deze stap wordt de eerste back-up naar het faserings gebied geschreven.

    ![Nu back-up maken](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    Als u de bijbehorende werk stroom in DPM of Azure Backup Server wilt volt ooien, klikt u met de rechter muisknop op de **beveiligings groep**. Selecteer de optie **herstel punt maken** . Selecteer vervolgens de optie voor **online beveiliging** .

    ![DPM en MABS maken nu een back-up](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    Nadat de bewerking is voltooid, kan de faserings locatie worden gebruikt voor het voorbereiden van de schijf.

    ![Voortgang van back-up](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA-schijven voorbereiden en verzenden naar Azure

Het hulp programma *AzureOfflineBackupDiskPrep* wordt gebruikt om de SATA-schijven voor te bereiden die worden verzonden naar het dichtstbijzijnde Azure-Data Center. Dit hulp programma is beschikbaar in de installatiemap van de Recovery Services agent in het volgende pad:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Ga naar de map en kopieer de Directory *AzureOfflineBackupDiskPrep* naar een kopie van de computer waarop de SATA-schijven die moeten worden voor bereid, zijn verbonden. Zorg ervoor dat:

   * De Kopieer computer heeft toegang tot de faserings locatie voor de werk stroom voor offline seeding met behulp van hetzelfde netwerkpad dat is gegeven in de werk stroom in de sectie ' offline back-up initiëren '.
   * BitLocker is ingeschakeld op de Kopieer computer.
   * De Kopieer computer heeft toegang tot de Azure Portal. Als dat nodig is, kan de Kopieer computer gelijk zijn aan die van de bron computer.

     > [!IMPORTANT]
     > Als de bron computer een virtuele machine is, is het verplicht een andere fysieke server of client computer als Kopieer computer te gebruiken.

1. Open een opdracht prompt met verhoogde bevoegdheid op de computer kopiëren met de map *AzureOfflineBackupDiskPrep* Utility als de huidige map. Voer de volgende opdracht uit:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parameter | Beschrijving |
    | --- | --- |
    | s: &lt; *pad naar locatie van tijdelijke bestanden*&gt; |Deze verplichte invoer wordt gebruikt om het pad naar de faserings locatie op te geven die u hebt ingevoerd in de werk stroom in de sectie ' offline back-up initiëren '. |
    | p: &lt; *pad naar PublishSettingsFile*&gt; |Deze optionele invoer wordt gebruikt om het pad naar het Azure Publish settings-bestand op te geven dat u in de werk stroom hebt ingevoerd in de sectie ' offline back-up initiëren '. |

    > [!NOTE]
    > Het &lt; pad naar de AzurePublishSettingFile- &gt; waarde is verplicht wanneer de Kopieer computer en de bron computer verschillend zijn.
    >
    >

    Wanneer u de opdracht uitvoert, vraagt het hulp programma de selectie van de Azure import-taak aan die overeenkomt met de stations die moeten worden voor bereid. Als er slechts één import taak is gekoppeld aan de gegeven faserings locatie, ziet u een pagina zoals deze.

    ![Invoer van hulp programma voor Azure-schijf voorbereiding](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Voer de stationsletter zonder de afsluitende dubbele punt in voor de gekoppelde schijf die u wilt voorbereiden voor overdracht naar Azure. Wanneer u hierom wordt gevraagd, geeft u een bevestiging voor de Format teren van het station.

    Het hulp programma begint vervolgens met het voorbereiden van de schijf en het kopiëren van de back-upgegevens. Mogelijk moet u extra schijven toevoegen wanneer u hierom wordt gevraagd door het hulp programma als de schijf niet voldoende ruimte heeft voor de back-upgegevens. <br/>

    Wanneer het hulp programma is voltooid, zijn een of meer schijven die u hebt geleverd voor bereid voor verzen ding naar Azure. Een import taak met de naam die u hebt ingevoerd tijdens de werk stroom in de sectie ' offline back-up initiëren ' wordt ook gemaakt in Azure. Ten slotte wordt in het hulp programma het verzend adres weer gegeven naar het Azure-Data Center waar de schijven moeten worden verzonden.

    ![Voorbereiden van Azure-schijf is voltooid](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. Aan het einde van de uitvoering van de opdracht ziet u ook de optie om de verzend gegevens bij te werken.

    ![Optie verzend gegevens bijwerken](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. U kunt de details direct opgeven. Het hulp programma begeleidt u bij het proces waarbij een serie invoer is betrokken. Als u geen informatie hebt, zoals het tracking nummer of andere details die betrekking hebben op verzen ding, kunt u de sessie beëindigen. De stappen voor het bijwerken van de verzend gegevens vindt u verderop in dit artikel.

1. Verzend de schijven naar het adres dat het hulp programma heeft geleverd. Bewaar het tracking nummer voor toekomstige referentie.

   > [!IMPORTANT]
   > Er kunnen niet twee Azure-import taken hetzelfde tracking nummer hebben. Zorg ervoor dat de stations die worden voor bereid met het hulp programma onder één Azure import-taak samen worden verzonden in één pakket en dat er één uniek Volg nummer is voor het pakket. Combi neer geen stations die zijn voor bereid als onderdeel van verschillende Azure-import taken in één pakket.

1. Wanneer u de informatie over het tracerings nummer hebt, gaat u naar de bron computer, die wacht op het importeren van de taak is voltooid. Voer de volgende opdracht uit in een opdracht prompt met verhoogde bevoegdheid met de map *AzureOfflineBackupDiskPrep* Utility als de huidige map.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   U kunt eventueel de volgende opdracht uitvoeren vanaf een andere computer, zoals de Kopieer computer, met de map *AzureOfflineBackupDiskPrep* Utility als de huidige map.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parameter | Beschrijving |
    | --- | --- |
    | h | Deze verplichte invoer wordt gebruikt om de verzend gegevens voor een Azure import-taak bij te werken. |
    | s: &lt; *pad naar locatie van tijdelijke bestanden*&gt; | Deze verplichte invoer wordt gebruikt wanneer de opdracht niet wordt uitgevoerd op de bron computer. Het wordt gebruikt om het pad naar de faserings locatie op te geven die u hebt ingevoerd in de werk stroom in de sectie ' offline back-up initiëren '. |
    | p: &lt; *pad naar PublishSettingsFile*&gt; | Deze verplichte invoer wordt gebruikt wanneer de opdracht niet wordt uitgevoerd op de bron computer. Het wordt gebruikt om het pad naar het Azure Publish settings-bestand op te geven dat u in de werk stroom hebt ingevoerd in de sectie ' offline back-up initiëren '. |

    Het hulp programma detecteert automatisch de import taak die op de bron computer wacht of de import taken die zijn gekoppeld aan de faserings locatie wanneer de opdracht wordt uitgevoerd op een andere computer. Vervolgens wordt de optie geboden om de verzend gegevens bij te werken via een serie invoer.

    ![Verzend gegevens invoeren](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. Nadat alle invoer gegevens zijn verstrekt, controleert u de details aandachtig en voert u de verzend gegevens door die u hebt verstrekt door **Ja** in te voeren.

    ![Verzend gegevens controleren](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. Nadat de verzend gegevens zijn bijgewerkt, biedt het hulp programma een lokale locatie waar de verzend gegevens die u hebt ingevoerd, worden opgeslagen.

    ![Verzend gegevens opslaan](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > Zorg ervoor dat de stations het Azure-Data Center binnen twee weken na het verstrekken van de verzend gegevens bereiken met behulp van het *AzureOfflineBackupDiskPrep* -hulp programma. Als u dit niet doet, kan dit leiden tot de stations die niet worden verwerkt.

Nadat u de vorige stappen hebt voltooid, is het Azure-Data Center klaar om de stations te ontvangen en ze verder te verwerken om de back-upgegevens over te dragen van de stations naar het klassieke Azure-opslag account dat u hebt gemaakt.

### <a name="time-to-process-the-drives"></a>Tijd voor het verwerken van de stations

De hoeveelheid tijd die nodig is voor het verwerken van een Azure import-taak varieert. De verwerkings tijd is afhankelijk van factoren zoals de verzend tijd, het taak type, het type en de grootte van de gegevens die worden gekopieerd en de grootte van de geleverde schijven. De Azure import/export-service heeft geen SLA. Nadat de schijven zijn ontvangen, streeft de service naar het kopiëren van de back-upgegevens naar uw Azure Storage-account in 7 tot tien dagen. In de volgende sectie wordt beschreven hoe u de status van de Azure import-taak kunt controleren.

### <a name="monitor-azure-import-job-status"></a>Status van Azure import-taak controleren

Hoewel uw stations onderweg of in het Azure-Data Center worden gekopieerd naar het opslag account, toont de Azure Backup Agent of DPM of de MABS-console op de bron computer de volgende taak status voor uw geplande back-ups:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

De status van de import taak controleren:

1. Open een opdracht prompt met verhoogde bevoegdheid op de bron computer en voer de volgende opdracht uit:

     `AzureOfflineBackupDiskPrep.exe u:`

1. In de uitvoer wordt de huidige status van de import taak weer gegeven.

    ![Status van import taak controleren](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Zie [de status van Azure import/export-taken weer geven](../import-export/storage-import-export-view-drive-status.md)voor meer informatie over de verschillende statussen van de Azure import-taak.

### <a name="finish-the-workflow"></a>De werk stroom volt ooien

Nadat de import taak is voltooid, zijn de eerste back-upgegevens beschikbaar in uw opslag account. Op het moment van de volgende geplande back-up Azure Backup kopieert de inhoud van de gegevens uit het opslag account naar de Recovery Services kluis.

   ![Gegevens kopiëren naar Recovery Services kluis](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

Op het moment van de volgende geplande back-up voert Azure Backup incrementele back-up uit via de eerste back-upkopie.

## <a name="next-steps"></a>Volgende stappen

* Zie [de Microsoft Azure import/export-service gebruiken voor het overdragen van gegevens naar de Blob-opslag](../import-export/storage-import-export-service.md)voor vragen over de Azure import/export-service werk stroom.
