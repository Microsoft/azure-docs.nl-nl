---
title: Offline back-ups voor DPM en Azure Backup Server
description: Met Azure Backup kunt u gegevens van het netwerk verzenden met behulp van de Azure import/export-service. In dit artikel wordt de werk stroom voor offline back-ups voor DPM en Azure Backup Server uitgelegd.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701810"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>Offline back-upwerk stroom voor DPM en Azure Backup Server (MABS)

>[!IMPORTANT]
> Deze stappen zijn van toepassing op DPM 2019 UR1 (of hoger) en MABS v3 UR1 (of hoger).

System Center Data Protection Manager en Azure Backup Server (MABS) zijn geïntegreerd met Azure Backup en gebruiken verschillende ingebouwde efficiency functies waarmee netwerk-en opslag kosten worden bespaard tijdens de eerste volledige back-ups van gegevens naar Azure. Bij eerste volledige back-ups worden grote hoeveel heden gegevens overgebracht en is er meer netwerk bandbreedte nodig in vergelijking met de volgende back-ups die alleen de Deltas/toenames overdragen. Azure Backup comprimeert de eerste back-ups. Dankzij het proces van offline seeding kan Azure Backup schijven gebruiken om de gecomprimeerde initiële back-upgegevens offline te uploaden naar Azure.

Het offline-seeding proces van Azure Backup is nauw geïntegreerd met de [Azure import/export-service](../import-export/storage-import-export-service.md). Met deze service kunt u gegevens overdragen naar Azure met behulp van schijven. Als u een TBs hebt van de eerste back-upgegevens die moeten worden overgezet via een netwerk met een hoge latentie en een lage band breedte, kunt u de werk stroom voor offline seeding gebruiken om de eerste back-up op een of meer harde schijven naar een Azure-Data Center te verzenden. Dit artikel bevat een overzicht en verdere stappen voor het volt ooien van deze werk stroom voor System Center Data Protection Manager (DPM) en Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Het proces van offline back-up voor de Microsoft Azure Recovery Services-agent (MARS) verschilt van DPM en MABS. Zie [offline back-upwerk stroom in azure backup](backup-azure-backup-import-export.md)voor meer informatie over het gebruik van offline back-ups met de Mars-agent. Offline back-up wordt niet ondersteund voor systeem status back-ups die worden uitgevoerd met behulp van de Azure Backup-Agent.
>
> De MABS UR1-update brengt ook de preview voor offline back-ups met behulp van Azure Data Box in MABS. Neem contact [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) op met meer informatie.

## <a name="overview"></a>Overzicht

Met de offline-seeding-mogelijkheid van Azure Backup en de Azure import/export-service, is het eenvoudig om de gegevens offline naar Azure te uploaden met behulp van schijven. Het offline back-upproces bestaat uit de volgende stappen:

> [!div class="checklist"]
>
> * De back-upgegevens worden naar een faserings locatie geschreven in plaats van via het netwerk te verzenden.
> * De gegevens op de faserings locatie worden vervolgens naar een of meer SATA-schijven geschreven met behulp van het *AzureOfflineBackupDiskPrep* -hulp programma.
> * Er wordt automatisch een Azure import-taak gemaakt door het hulp programma.
> * De SATA-schijven worden vervolgens verzonden naar het dichtstbijzijnde Azure-Data Center.
> * Nadat het uploaden van de back-upgegevens naar Azure is voltooid, Azure Backup de back-upgegevens naar de back-upkluis gekopieerd en de incrementele back-ups worden gepland.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan voordat u de werk stroom voor offline back-ups start:

* Er is een [Recovery Services kluis](backup-azure-recovery-services-vault-overview.md) gemaakt. Als u er een wilt maken, volgt u de stappen in [een Recovery Services-kluis maken](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)zelf studie: back-up-Windows-Server-naar-Azure # Create-a-Recovery-Services-kluis).
* Zorg ervoor dat alleen de [nieuwste versie van Microsoft Azure Recovery Services agent](https://aka.ms/azurebackup_agent) is geïnstalleerd op de SC DPM of MABS en is geregistreerd bij Recovery Services kluis.
* Update pakket 1 is geïnstalleerd op SC DPM 2019 of MABS v3.

  > [!NOTE]
  > Met DPM 2019 UR1 en MABS v3 UR1 de offline seeding verifiëren met behulp van Azure Active Directory.

* Controleer op de DPM-of MABS-server of micro soft Edge of Internet Explorer 11 is geïnstalleerd en of Java script is ingeschakeld.
* Maak een Azure Storage-account in hetzelfde abonnement als de Recovery Services kluis.
* Zorg ervoor dat u over de [benodigde machtigingen](../active-directory/develop/howto-create-service-principal-portal.md) beschikt om de Azure Active Directory-toepassing te maken. De werk stroom offline back-up maakt een Azure Active Directory-toepassing in het abonnement dat is gekoppeld aan het Azure Storage-account. Het doel van de toepassing is om Azure Backup te voorzien van beveiligde en scoped toegang tot de Azure-import Service, die is vereist voor de werk stroom voor offline back-ups.
* Registreer de resource provider micro soft. ImportExport bij het abonnement met het Azure Storage-account. De resource provider registreren:
    1. Selecteer in het hoofd menu de optie **abonnementen**.
    2. Als u bent geabonneerd op meerdere abonnementen, selecteert u het abonnement dat u gebruikt voor de offline back-up. Als u slechts één abonnement gebruikt, wordt uw abonnement weer gegeven.
    3. Selecteer **resource providers** in het menu abonnement om de lijst met providers weer te geven.
    4. Schuif omlaag in de lijst met providers naar micro soft. ImportExport. Als de status NotRegistered is, selecteert u **registreren**.

       ![De resource provider registreren](./media/backup-azure-backup-server-import-export/register-import-export.png)

* Een faserings locatie, die een netwerk share kan zijn of een extra station op de computer, intern of extern, met voldoende schijf ruimte om uw eerste kopie te bewaren, wordt gemaakt. Als u bijvoorbeeld een back-up van een bestands server van 500 GB wilt maken, moet u ervoor zorgen dat de tijdelijke ruimte ten minste 500 GB is. (Er wordt een kleiner bedrag gebruikt vanwege compressie.)
* Zorg ervoor dat er voor schijven die naar Azure worden verzonden, alleen 2,5-inch SSD-of 2,5-inch of 3,5-inch SATA II/III interne harde schijven worden gebruikt. U kunt harde schijven van Maxi maal 10 TB gebruiken. Raadpleeg de [documentatie van de Azure import/export-service](../import-export/storage-import-export-requirements.md#supported-hardware) voor de meest recente set stations die door de service worden ondersteund.
* De SATA-schijven moeten zijn verbonden met een computer (waarnaar wordt verwezen als een *Kopieer computer*) van waaruit het kopiëren van back-upgegevens van de faserings locatie naar de SATA-schijven is voltooid. Zorg ervoor dat BitLocker is ingeschakeld op de Kopieer computer.

## <a name="workflow"></a>Werkstroom

De informatie in deze sectie helpt u bij het volt ooien van de werk stroom voor offline back-ups, zodat uw gegevens kunnen worden geleverd aan een Azure-Data Center en naar Azure Storage worden geüpload. Als u vragen hebt over de import service of een aspect van het proces, raadpleegt u de documentatie overzicht van het importeren van de [service](../import-export/storage-import-export-service.md) .

## <a name="initiate-offline-backup"></a>Offline back-up initiëren

1. Wanneer u een nieuwe beveiligings groep met online beveiliging maakt of online beveiliging toevoegt aan een bestaande beveiligings groep, wordt het volgende scherm weer gegeven. Als u methode voor eerste online replicatie wilt selecteren, selecteert **u overdragen met mijn eigen schijf** en selecteert u **volgende**.

    ![Scherm importeren](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. De aanmeldings pagina van Azure wordt geopend. Meld u aan met uw Azure-gebruikers account, met een machtiging voor de rol *eigenaar* van het Azure-abonnement.

    ![Aanmeldings pagina voor Azure](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. Geef de invoer op de pagina **uw eigen schijf gebruiken** .

   ![Invoer voor gebruik van uw eigen schijf](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   De beschrijving van de invoer is als volgt:

   * **Faserings locatie**: de tijdelijke opslag locatie waarnaar de eerste back-up wordt geschreven. De faserings locatie Bekan zich op een netwerk share of een lokale computer. Als de Kopieer computer en de bron computer verschillend zijn, geeft u het volledige netwerkpad van de faserings locatie op.
   * **Azure Resource Manager Storage-account**: de naam van het Resource Manager-type opslag account (algemeen gebruik v1 of algemeen gebruik v2) in een Azure-abonnement.
   * **Azure storage container**: de naam van de opslag container voor de doel-Blob in het Azure-opslag account waarin de back-upgegevens worden geïmporteerd.
   * **Azure-abonnements-id**: de id voor het Azure-abonnement waarin het Azure-opslag account is gemaakt.
   * **Azure import-taak naam**: de unieke naam waarmee de Azure import-service en Azure backup de overdracht van gegevens die op schijven zijn verzonden, volgen naar Azure.

    Sla de **faserings locatie** en de gegevens van de **Azure import-taak naam** op die u hebt ingevoerd. Het is vereist om de schijven voor te bereiden.

4. Voltooi de werk stroom om de beveiliging te maken of bij te werken. En de offline back-upkopie te initiëren, klikt u met de rechter muisknop op de **beveiligings groep** en kiest u vervolgens de optie **herstel punt maken** . Vervolgens kiest u de optie voor **online beveiliging** .

   ![Herstel punt maken](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. Controleer de taak voor het maken van een online replica in het deel venster bewaking. De taak moet worden voltooid met de waarschuwing *wachten tot de Azure import-taak* is voltooid.

   ![Herstel punt volt ooien](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. Nadat de bewerking is voltooid, kan de faserings locatie worden gebruikt voor het voorbereiden van de schijf.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA-schijven voorbereiden en verzenden naar Azure

Het hulp programma *AzureOfflineBackupDiskPrep* bereidt de SATA-stations voor die naar het dichtstbijzijnde Azure-Data Center worden verzonden. Dit hulp programma is beschikbaar in de installatiemap van de Azure Backup Agent (in het volgende pad): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Ga naar de map en kopieer de **AzureOfflineBackupDiskPrep** -map naar een andere computer waarop de SATA-schijven zijn aangesloten. Zorg ervoor dat op de computer met de aangesloten SATA-schijven:

   * De Kopieer computer heeft toegang tot de faserings locatie voor de werk stroom voor offline seeding met behulp van hetzelfde netwerkpad dat is gegeven in de werk stroom in de sectie ' offline back-up initiëren '.
   * BitLocker is ingeschakeld op de Kopieer computer.
   * Azure PowerShell 3.7.0 is geïnstalleerd op de Kopieer computer (niet vereist als u het hulp programma AzureOfflineBackupDiskPrep uitvoert op de DPM-of MABS-server).
   * De meest recente compatibele browsers (micro soft Edge of Internet Explorer 11) zijn geïnstalleerd en Java script is ingeschakeld.
   * De Kopieer computer heeft toegang tot de Azure Portal. Als dat nodig is, kan de Kopieer computer gelijk zijn aan die van de bron computer.

     > [!IMPORTANT]
     > Als de bron computer een virtuele machine is, is het verplicht een andere fysieke server of client computer als Kopieer computer te gebruiken.

1. Open een opdracht prompt met verhoogde bevoegdheid op de computer kopiëren met de map *AzureOfflineBackupDiskPrep* Utility als de huidige map. Voer de volgende opdracht uit:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Parameter | Beschrijving |
    | --- | --- |
    | s: &lt; *pad naar locatie van tijdelijke bestanden*&gt; |Deze verplichte invoer wordt gebruikt om het pad naar de faserings locatie op te geven die u hebt ingevoerd in de werk stroom in de sectie ' offline back-up initiëren '. |
    | p: &lt; *pad naar PublishSettingsFile*&gt; |Deze optionele invoer wordt gebruikt om het pad naar het Azure Publish settings-bestand op te geven. |

    Wanneer u de opdracht uitvoert, vraagt het hulp programma de selectie van de Azure import-taak aan die overeenkomt met de stations die moeten worden voor bereid. Als er slechts één import taak is gekoppeld aan de gegeven faserings locatie, ziet u een scherm zoals de volgende.

      ![Console voor schijf voorbereiding](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Voer de stationsletter zonder de afsluitende dubbele punt in voor de gekoppelde schijf die u wilt voorbereiden voor overdracht naar Azure.
1. Geef een bevestiging voor de Format teren van het station op wanneer u hierom wordt gevraagd.
1. U wordt gevraagd om u aan te melden bij uw Azure-abonnement. Geef uw referenties op.

    ![Aanmeldings scherm van Azure](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    Het hulp programma begint vervolgens met het voorbereiden van de schijf en het kopiëren van de back-upgegevens. Mogelijk moet u extra schijven toevoegen wanneer u hierom wordt gevraagd door het hulp programma als de schijf niet voldoende ruimte heeft voor de back-upgegevens. <br/>

    Aan het einde van de geslaagde uitvoering van het hulp programma biedt de opdracht prompt drie stukjes informatie:
    * Een of meer schijven die u hebt geleverd, worden voor bereid voor verzen ding naar Azure.
    * U ontvangt een bevestiging dat uw import taak is gemaakt. De import taak maakt gebruik van de naam die u hebt ingevoerd.
    * Het hulp programma geeft het verzend adres weer voor het Azure-Data Center.

     ![Voorbereiden van Azure-schijf is voltooid](./media/backup-azure-backup-server-import-export/console.png)

1. Aan het einde van de uitvoering van de opdracht ziet u ook de optie om de verzend gegevens bij te werken.

1. Verzend de schijven naar het adres dat het hulp programma heeft geleverd en behoud het tracking nummer voor toekomstige referentie.

   > [!IMPORTANT]
   > Er kunnen niet twee Azure-import taken hetzelfde tracking nummer hebben. Zorg ervoor dat de stations die worden voor bereid met het hulp programma onder één Azure import-taak samen worden verzonden in één pakket en dat er één uniek Volg nummer is voor het pakket. Combi neer geen stations die zijn voor bereid als onderdeel van verschillende Azure-import taken in één pakket.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Verzend gegevens bijwerken in de Azure import-taak

Met de volgende procedure worden de verzend gegevens van de Azure import-taak bijgewerkt. Deze informatie omvat onder meer informatie over:

* de naam van de vervoerder die de schijven aan Azure levert
* Verzend gegevens retour neren voor uw schijven

   1. Meld u aan bij uw Azure-abonnement.
   2. Selecteer in het hoofd menu **alle services** en typ importeren in het dialoog venster alle services. Wanneer u **import/export-taken** ziet, selecteert u deze.
       ![Verzend gegevens invoeren](./media/backup-azure-backup-server-import-export/search-import-job.png)

       De lijst met het menu **import/export-taken** wordt geopend en de lijst met alle import/export-taken in het geselecteerde abonnement wordt weer gegeven.

   3. Als u meerdere abonnementen hebt, selecteert u het abonnement dat is gebruikt voor het importeren van de back-upgegevens. Selecteer vervolgens de zojuist gemaakte import taak om de details ervan te openen.

       ![Verzend gegevens controleren](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. Selecteer in het menu instellingen voor de import taak de optie **Verzend gegevens beheren** en voer de retour verzendings gegevens in.

       ![Verzend gegevens opslaan](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. Wanneer u het tracking nummer van uw vervoerder hebt, selecteert u de banner op de pagina overzicht van Azure import-taak en voert u de volgende gegevens in:

      > [!IMPORTANT]
      > Zorg dat de informatie voor de vervoerder en het traceringsnummer binnen twee weken van het maken van de Azure-importtaak zijn bijgewerkt. Als u deze gegevens niet binnen twee weken controleert, kan dit ertoe leiden dat de taak wordt verwijderd en dat de stations niet worden verwerkt.

      ![Taak overzicht](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![Tracking gegevens](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tijd voor het verwerken van de stations

De hoeveelheid tijd die nodig is voor het verwerken van een Azure import-taak varieert. De verwerkings tijd is afhankelijk van factoren zoals de verzend tijd, het taak type, het type en de grootte van de gegevens die worden gekopieerd en de grootte van de geleverde schijven. De Azure import/export-service heeft geen SLA. Nadat de schijven zijn ontvangen, streeft de service naar het kopiëren van de back-upgegevens naar uw Azure Storage-account in 7 tot tien dagen. In de volgende sectie wordt beschreven hoe u de status van de Azure import-taak kunt controleren.

### <a name="monitor-azure-import-job-status"></a>Status van Azure import-taak controleren

U kunt de status van uw import taak controleren vanuit de Azure Portal door te navigeren naar de pagina **import/export-taken** en uw taak te selecteren. Zie het artikel [Storage import export service](../import-export/storage-import-export-service.md) voor meer informatie over de status van de import taken.

### <a name="complete-the-workflow"></a>De werk stroom volt ooien

Nadat de import taak is voltooid, zijn de eerste back-upgegevens beschikbaar in uw opslag account. Op het moment van de volgende geplande back-up Azure Backup kopieert de inhoud van de gegevens uit het opslag account naar de Recovery Services kluis.

Op het moment van de volgende geplande taak voor het maken van een online replica voert Data Protection Manager incrementele back-up uit via de eerste back-upkopie.

## <a name="next-steps"></a>Volgende stappen

* Zie [de Microsoft Azure import/export-service gebruiken voor het overdragen van gegevens naar de Blob-opslag](../import-export/storage-import-export-service.md)voor vragen over de Azure import/export-service werk stroom.
