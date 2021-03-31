---
title: Een offline back-up met de Azure import/export-service
description: Meer informatie over hoe u Azure Backup kunt gebruiken om gegevens uit het netwerk te verzenden met behulp van de Azure import/export-service. In dit artikel wordt uitgelegd hoe u de offline seeding van de eerste back-upgegevens maakt met behulp van de Azure import/export-service.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704360"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Werk stroom voor offline back-up in Azure Backup

Azure Backup heeft verschillende ingebouwde efficiency waarmee netwerk-en opslag kosten worden bespaard tijdens de eerste volledige back-ups van gegevens naar Azure. Bij eerste volledige back-ups worden grote hoeveel heden gegevens overgebracht en is er meer netwerk bandbreedte nodig in vergelijking met de volgende back-ups die alleen de Deltas/toenames overdragen. Dankzij het proces van offline seeding kan Azure Backup schijven gebruiken om de offline back-upgegevens te uploaden naar Azure.

De Azure Backup offline-seeding proces is nauw geïntegreerd met de [Azure import/export-service](../import-export/storage-import-export-service.md). Met deze service kunt u de eerste back-upgegevens naar Azure overdragen met behulp van schijven. Als u een TBs hebt van de eerste back-upgegevens die moeten worden overgedragen via een netwerk met een hoge latentie en een lage band breedte, kunt u de werk stroom voor offline seeding gebruiken om de eerste back-upkopie te verzenden op een of meer harde schijven naar een Azure-Data Center. De volgende afbeelding bevat een overzicht van de stappen in de werk stroom.

  ![Overzicht van werk stroom proces voor offline import](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Het offline back-upproces bestaat uit de volgende stappen:

1. In plaats van de back-upgegevens via het netwerk te verzenden, schrijft u de back-upgegevens naar een faserings locatie.
1. Gebruik het hulp programma *AzureOfflineBackupDiskPrep* om de gegevens op de faserings locatie te schrijven naar een of meer SATA-schijven.
1. Als onderdeel van de voorbereidende werkzaamheden maakt het hulp programma *AzureOfflineBackupDiskPrep* een Azure import-taak. Verzend de SATA-schijven naar het dichtstbijzijnde Azure-Data Center en Raadpleeg de import taak om de activiteiten te verbinden.
1. In het Azure-Data Center worden de gegevens op de schijven gekopieerd naar een Azure-opslag account.
1. Azure Backup kopieert de back-upgegevens van het opslag account naar de Recovery Services kluis en worden incrementele back-ups gepland.

## <a name="supported-configurations"></a>Ondersteunde configuraties

De volgende Azure Backup-functies of-workloads bieden ondersteuning voor het gebruik van offline back-ups voor:

> [!div class="checklist"]
>
> * Back-ups van bestanden en mappen met de Microsoft Azure Recovery Services-agent (MARS), ook wel de Azure Backup-Agent genoemd.
> * Back-ups maken van alle werk belastingen en bestanden met System Center Data Protection Manager (DPM).
> * Back-ups maken van alle werk belastingen en bestanden met Microsoft Azure Backup Server.

   > [!NOTE]
   > Offline back-up wordt niet ondersteund voor systeem status back-ups die worden uitgevoerd met behulp van de Azure Backup-Agent.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Vereisten

  > [!NOTE]
  > De volgende vereisten en werk stroom zijn alleen van toepassing op offline back-ups van bestanden en mappen met behulp van de [nieuwste Azure Recovery Services-agent](https://aka.ms/azurebackup_agent). Als u offline back-ups wilt uitvoeren voor workloads met System Center DPM of Azure Backup Server, raadpleegt u [offline back-upwerk stroom voor dpm en Azure backup server](backup-azure-backup-server-import-export.md).

Voordat u de werk stroom voor offline back-ups start, moet u de volgende vereisten volt ooien:

* Maak een [Recovery Services kluis](backup-azure-recovery-services-vault-overview.md). Als u een kluis wilt maken, volgt u de stappen in [een Recovery Services kluis maken](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Zorg ervoor dat alleen de [nieuwste versie van de Azure backup-agent](https://aka.ms/azurebackup_agent) is geïnstalleerd op de Windows-Server of Windows-client, indien van toepassing, en de computer is geregistreerd bij de Recovery Services kluis.
* Azure PowerShell 3.7.0 is vereist op de computer waarop de Azure Backup-Agent wordt uitgevoerd. Down load en [Installeer de 3.7.0-versie van Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Op de computer waarop de Azure Backup-Agent wordt uitgevoerd, moet u ervoor zorgen dat micro soft Edge of Internet Explorer 11 is geïnstalleerd en dat Java script is ingeschakeld.
* Maak een Azure-opslag account in hetzelfde abonnement als de Recovery Services kluis.
* Zorg ervoor dat u over de [benodigde machtigingen](../active-directory/develop/howto-create-service-principal-portal.md) beschikt om de Azure Active Directory-toepassing te maken. De werk stroom offline back-up maakt een Azure Active Directory-toepassing in het abonnement dat is gekoppeld aan het Azure-opslag account. Het doel van de toepassing is om Azure Backup te voorzien van beveiligde en scoped toegang tot de Azure import/export-service. deze is vereist voor de werk stroom voor offline back-ups.
* Registreer de resource provider *micro soft. ImportExport* bij het abonnement dat het Azure-opslag account bevat. De resource provider registreren:
    1. Selecteer **abonnementen** in het hoofd menu.
    1. Als u bent geabonneerd op meerdere abonnementen, selecteert u het abonnement dat u voor de offline back-up wilt gebruiken. Als u slechts één abonnement gebruikt, wordt uw abonnement weer gegeven.
    1. Selecteer **resource providers** in het menu abonnement om de lijst met providers weer te geven.
    1. Schuif in de lijst met providers omlaag naar *micro soft. ImportExport*. Als de **status** **NotRegistered** is, selecteert u **registreren**.

        ![De resourceprovider registreren](./media/backup-azure-backup-import-export/registerimportexport.png)

* Een faserings locatie, die een netwerk share kan zijn of een extra station op de computer, intern of extern, met voldoende schijf ruimte om uw eerste kopie te bewaren, wordt gemaakt. Als u bijvoorbeeld een back-up van een bestands server van 500 GB wilt maken, moet u ervoor zorgen dat de tijdelijke ruimte ten minste 500 GB is. (Er wordt een kleiner bedrag gebruikt vanwege compressie.)
* Gebruik bij het verzenden van schijven naar Azure alleen 2,5-inch SSD-of 2,5-inch of 3,5-inch SATA II/III interne harde schijven. U kunt harde schijven van Maxi maal 10 TB gebruiken. Raadpleeg de [documentatie van de Azure import/export-service](../import-export/storage-import-export-requirements.md#supported-hardware) voor de meest recente set stations die door de service worden ondersteund.
* De SATA-schijven moeten zijn verbonden met een computer (waarnaar wordt verwezen als een *Kopieer computer*) van waaruit het kopiëren van back-upgegevens van de faserings locatie naar de SATA-schijven is voltooid. Zorg ervoor dat BitLocker is ingeschakeld op de Kopieer computer.

## <a name="workflow"></a>Werkstroom

In deze sectie wordt de werk stroom voor offline back-ups beschreven, zodat uw gegevens kunnen worden geleverd aan een Azure-Data Center en naar Azure Storage worden geüpload. Als u vragen hebt over de import service of een aspect van het proces, raadpleegt u de [documentatie van Azure import/export-service](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Offline back-up initiëren

1. Wanneer u een back-up plant op de Recovery Services-agent, ziet u deze pagina.

    ![Pagina importeren](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Selecteer de optie **overdracht met mijn eigen schijven**.

    > [!NOTE]
    > Gebruik de optie Azure Data Box om de eerste back-upgegevens offline te zetten. Met deze optie bespaart u de inspanningen die nodig zijn om uw eigen Azure-compatibele schijven te verkrijgen. Het levert micro soft-eigen, veilige en tamperproof Azure Data Box apparaten waarmee back-upgegevens rechtstreeks kunnen worden geschreven door de Recovery Services-agent.

1. Selecteer **volgende** en vul de vakken zorgvuldig in.

    ![Voer de details van uw schijf in](./media/backup-azure-backup-import-export/your-disk-details.png)

   De vakken die u invult, zijn:

    * **Faserings locatie**: de tijdelijke opslag locatie waarnaar de eerste back-up wordt geschreven. De faserings locatie Bekan zich op een netwerk share of een lokale computer. Als de Kopieer computer en de bron computer verschillend zijn, geeft u het volledige netwerkpad van de faserings locatie op.
    * **Azure Resource Manager Storage-account**: de naam van het Resource Manager-type opslag account (algemeen gebruik v1 of algemeen gebruik v2) in een Azure-abonnement.
    * **Azure storage container**: de naam van de doel-Blob-opslag container in het Azure Storage-account waar de back-upgegevens worden geïmporteerd voordat deze naar de Recovery Services kluis worden gekopieerd.
    * **Azure-abonnements-id**: de id voor het Azure-abonnement waarin het Azure-opslag account is gemaakt.
    * **Azure import-taak naam**: de unieke naam waarmee de Azure import/export-service en Azure Backup het volgen van de overdracht van gegevens die op schijven zijn verzonden naar Azure.
  
   Nadat u de vakken hebt ingevuld, selecteert u **volgende**. Sla de **faserings locatie** en de naam gegevens van de **Azure-import taak** op. Het is vereist om de schijven voor te bereiden.

1. Meld u aan bij uw Azure-abonnement wanneer u hierom wordt gevraagd. U moet zich aanmelden zodat Azure Backup de Azure Active Directory-toepassing kunt maken. Voer de vereiste machtigingen in voor toegang tot de Azure import/export-service.

    ![Aanmeldings pagina voor het Azure-abonnement](./media/backup-azure-backup-import-export/azure-login.png)

1. Voltooi de werk stroom. Selecteer **Nu back-up maken** op de console Azure backup agent.

    ![Nu back-up maken](./media/backup-azure-backup-import-export/backupnow.png)

1. Selecteer op de pagina **bevestiging** van de wizard **back-up**. De eerste back-up wordt naar het faserings gebied geschreven als onderdeel van de installatie.

   ![Bevestig dat u nu een back-up wilt maken](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Nadat de bewerking is voltooid, kan de faserings locatie worden gebruikt voor het voorbereiden van de schijf.

   ![Wizard pagina nu back-up maken](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA-schijven voorbereiden en verzenden naar Azure

Het hulp programma *AzureOfflineBackupDiskPrep* bereidt de SATA-stations voor die naar het dichtstbijzijnde Azure-Data Center worden verzonden. Dit hulp programma is beschikbaar in de installatiemap van de Azure Backup Agent in het volgende pad:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Ga naar de map en kopieer de *AzureOfflineBackupDiskPrep* -map naar een andere computer waarop de SATA-schijven zijn aangesloten. Zorg ervoor dat op de computer met de aangesloten SATA-schijven:

   * De Kopieer computer heeft toegang tot de faserings locatie voor de werk stroom voor offline seeding met behulp van hetzelfde netwerkpad dat is gegeven in de werk stroom in de sectie ' offline back-up initiëren '.
   * BitLocker is ingeschakeld op de Kopieer computer.
   * Azure PowerShell 3.7.0 is geïnstalleerd.
   * De meest recente compatibele browsers (micro soft Edge of Internet Explorer 11) zijn geïnstalleerd en Java script is ingeschakeld.
   * De Kopieer computer heeft toegang tot de Azure Portal. Als dat nodig is, kan de Kopieer computer gelijk zijn aan die van de bron computer.

     > [!IMPORTANT]
     > Als de bron computer een virtuele machine is, moet de Kopieer computer een andere fysieke server of client computer van de bron computer zijn.

1. Open een opdracht prompt met verhoogde bevoegdheid op de computer kopiëren met de map *AzureOfflineBackupDiskPrep* Utility als de huidige map. Voer de volgende opdracht uit:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parameter | Beschrijving |
    | --- | --- |
    | s: &lt; *pad naar locatie van tijdelijke bestanden*&gt; |Deze verplichte invoer wordt gebruikt om het pad naar de faserings locatie op te geven die u hebt ingevoerd in de werk stroom in de sectie ' offline back-up initiëren '. |
    | p: &lt; *pad naar PublishSettingsFile*&gt; |Deze optionele invoer wordt gebruikt om het pad naar het Azure Publish settings-bestand op te geven.  |

    Wanneer u de opdracht uitvoert, vraagt het hulp programma de selectie van de Azure import-taak aan die overeenkomt met de stations die moeten worden voor bereid. Als er slechts één import taak is gekoppeld aan de gegeven faserings locatie, ziet u een pagina zoals deze.

    ![Invoer van hulp programma voor Azure-schijf voorbereiding](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Voer de stationsletter zonder de afsluitende dubbele punt in voor de gekoppelde schijf die u wilt voorbereiden voor overdracht naar Azure.
1. Geef een bevestiging voor de Format teren van het station op wanneer u hierom wordt gevraagd.
1. U wordt gevraagd om u aan te melden bij uw Azure-abonnement. Voer uw referenties in.

    ![Aanmelden bij het Azure-abonnement](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Het hulp programma begint vervolgens met het voorbereiden van de schijf en het kopiëren van de back-upgegevens. Mogelijk moet u extra schijven toevoegen wanneer u hierom wordt gevraagd door het hulp programma als de schijf niet voldoende ruimte heeft voor de back-upgegevens. <br/>

    Aan het einde van de geslaagde uitvoering van het hulp programma biedt de opdracht prompt drie stukjes informatie:

   1. Een of meer schijven die u hebt geleverd, worden voor bereid voor verzen ding naar Azure.
   1. U ontvangt een bevestiging dat uw import taak is gemaakt. De import taak maakt gebruik van de naam die u hebt ingevoerd.
   1. Het hulp programma geeft het verzend adres weer voor het Azure-Data Center.

      ![Voorbereiden van Azure-schijf is voltooid](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Aan het einde van de uitvoering van de opdracht kunt u de verzend gegevens bijwerken.

1. Verzend de schijven naar het adres dat het hulp programma heeft geleverd. Bewaar het tracking nummer voor toekomstige referentie.

   > [!IMPORTANT]
   > Er kunnen niet twee Azure-import taken hetzelfde tracking nummer hebben. Zorg ervoor dat de stations die worden voor bereid met het hulp programma onder één Azure import-taak samen worden verzonden in één pakket en dat er één uniek Volg nummer is voor het pakket. Combi neer geen stations die zijn voor bereid als onderdeel van afzonderlijke Azure-import taken in één pakket.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Verzend gegevens bijwerken in de Azure import-taak

Met de volgende procedure worden de verzend gegevens van de Azure import-taak bijgewerkt. Deze informatie omvat onder meer informatie over:

* De naam van de provider die de schijven levert aan Azure.
* Verzend gegevens retour neren voor uw schijven.

1. Meld u aan bij uw Azure-abonnement.
1. Selecteer in het hoofd menu **alle services**. Voer in het dialoog venster **alle services** het selectie vakje **importeren** in. Wanneer u **import/export-taken** ziet, selecteert u deze.

    ![Verzend gegevens invoeren](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Het menu **import/export-taken** wordt geopend en de lijst met alle import/export-taken in het geselecteerde abonnement wordt weer gegeven.

1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat is gebruikt om de back-upgegevens te importeren. Selecteer vervolgens de zojuist gemaakte import taak om de details ervan te openen.

    ![Verzend gegevens controleren](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. Selecteer in het menu **instellingen** voor de import taak de optie **Verzend gegevens beheren**. Voer de verzend gegevens voor de retour nering in.

    ![Verzend gegevens opslaan](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Wanneer u het tracking nummer van uw vervoerder hebt, selecteert u de banner op de pagina overzicht van Azure import-taak en voert u de volgende gegevens in.

   > [!IMPORTANT]
   > Zorg dat de informatie voor de vervoerder en het traceringsnummer binnen twee weken van het maken van de Azure-importtaak zijn bijgewerkt. Als u deze informatie binnen twee weken niet kunt controleren, kan dit ertoe leiden dat de taak wordt verwijderd en dat er geen stations worden verwerkt.

   ![Waarschuwing update tracerings gegevens](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informatie over de transporteur en het tracking nummer](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tijd voor het verwerken van de stations

De hoeveelheid tijd die nodig is voor het verwerken van een Azure import-taak varieert. De verwerkings tijd is gebaseerd op factoren zoals de verzend tijd, het taak type, het type en de grootte van de gegevens die worden gekopieerd en de grootte van de geleverde schijven. De Azure import/export-service heeft geen SLA. Nadat de schijven zijn ontvangen, streeft de service naar het volt ooien van de back-upgegevens naar uw Azure Storage-account in 7 tot tien dagen.

### <a name="monitor-azure-import-job-status"></a>Status van Azure import-taak controleren

U kunt de status van uw import taak controleren vanuit het Azure Portal. Ga naar de pagina **import/export-taken** en selecteer uw taak. Zie [Wat is de Azure import/export-service?](../import-export/storage-import-export-service.md)voor meer informatie over de status van de import taken.

### <a name="finish-the-workflow"></a>De werk stroom volt ooien

Nadat de import taak is voltooid, zijn de eerste back-upgegevens beschikbaar in uw opslag account. Op het moment van de volgende geplande back-up Azure Backup kopieert de inhoud van de gegevens uit het opslag account naar de Recovery Services kluis.

   ![Gegevens kopiëren naar Recovery Services kluis](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Op het moment van de volgende geplande back-up voert Azure Backup een incrementele back-up uit.

### <a name="clean-up-resources"></a>Resources opschonen

Nadat de eerste back-up is voltooid, kunt u de gegevens die zijn geïmporteerd in de Azure Storage container en de back-upgegevens op de faserings locatie veilig verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Zie [de Microsoft Azure import/export-service gebruiken voor het overdragen van gegevens naar de Blob-opslag](../import-export/storage-import-export-service.md)voor vragen over de Azure import/export-service werk stroom.
