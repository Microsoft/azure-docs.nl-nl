---
title: Bestanden herstellen naar Windows Server met behulp van de MARS-agent
description: In dit artikel vindt u informatie over het herstellen van gegevens die zijn opgeslagen in azure naar een Windows-Server of Windows-computer met de Microsoft Azure Recovery Services-agent (MARS).
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 79a4d32d6dbca5ca5be5d46c6b44a07ef42de061
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91813250"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Bestanden herstellen naar Windows Server met behulp van de MARS-agent

In dit artikel wordt uitgelegd hoe u gegevens terugzet vanuit een back-upkluis. Als u gegevens wilt herstellen, gebruikt u de wizard gegevens herstellen in de MARS-agent (Microsoft Azure Recovery Services). U kunt:

* Gegevens herstellen op dezelfde computer waarvan de back-ups zijn gemaakt.
* Gegevens herstellen naar een alternatieve machine

Gebruik de functie voor direct terugzetten om een moment opname van een Beschrijf bare herstel punt te koppelen als een herstel volume. U kunt vervolgens het herstel volume verkennen en bestanden kopiëren naar een lokale computer, op die manier om bestanden selectief te herstellen.

> [!NOTE]
> De [Update van januari 2017 Azure backup](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) is vereist als u direct terugzetten wilt gebruiken om gegevens te herstellen. Daarnaast moeten de back-upgegevens worden beveiligd in kluizen in de land instellingen die worden vermeld in het ondersteunings artikel. Raadpleeg de [Update van januari 2017 Azure backup](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) voor de meest recente lijst met land instellingen die ondersteuning bieden voor direct terugzetten.
>

Gebruik direct terugzetten met Recovery Services kluizen in de Azure Portal. Als u gegevens hebt opgeslagen in back-upkluizen, zijn ze geconverteerd naar Recovery Services-kluizen. Als u direct terugzetten wilt gebruiken, downloadt u de MARS-update en volgt u de procedures die het direct terugzetten vermelden.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Direct terugzetten gebruiken om gegevens op dezelfde computer te herstellen

Als u een bestand per ongeluk hebt verwijderd en op dezelfde computer wilt herstellen (van waaruit de back-up is gemaakt), kunt u de gegevens herstellen met de volgende stappen.

1. Open de **Microsoft Azure Backup**-module. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Microsoft Azure backup**.

    De bureau blad-app moet worden weer gegeven in de zoek resultaten.

2. Selecteer **gegevens herstellen** om de wizard te starten.

    ![Scherm opname van Azure Backup, waarbij de gegevens voor herstel zijn gemarkeerd (herstel op dezelfde machine)](./media/backup-azure-restore-windows-server/recover.png)

3. Selecteer op de pagina **aan** de slag de optie **deze server ( `<server name>` )** om de gegevens op dezelfde server of computer te herstellen  >  .

    ![Scherm afbeelding van de wizard aan de slag met pagina Herstel gegevens (herstellen op dezelfde computer)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Kies op de pagina **herstel modus selecteren** de optie **afzonderlijke bestanden en mappen** > **volgende**.

    ![Scherm afbeelding van de wizard gegevens herstellen Selecteer de pagina herstel modus (herstellen op dezelfde machine)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > De optie voor het herstellen van afzonderlijke bestanden en mappen vereist .NET Framework 4.5.2 of hoger. Als u de optie **afzonderlijke bestanden en mappen** niet ziet, moet u .NET Framework bijwerken naar versie 4.5.2 of hoger en het opnieuw proberen.

   > [!TIP]
   > Met de optie **afzonderlijke bestanden en mappen** kunt u snel toegang krijgen tot de herstel punt gegevens. Het is geschikt voor het herstellen van afzonderlijke bestanden en wordt aanbevolen voor een totale grootte van minder dan 80 GB. Het biedt overdrachts-of kopieer snelheden van Maxi maal 6 MBps tijdens het herstel. De **volume** optie herstelt alle back-ups van gegevens in een opgegeven volume. Deze optie biedt snellere overdrachts snelheden (Maxi maal 40 MBps) en wordt aanbevolen voor het herstellen van grote hoeveel heden gegevens of volledige volumes.

5. Selecteer op de pagina **volume en datum selecteren** het volume dat de bestanden en mappen bevat die u wilt herstellen.

    Selecteer een herstel punt in de agenda. **Vetgedrukte** datums geven de beschik baarheid van ten minste één herstel punt aan. Als er meerdere herstel punten beschikbaar zijn binnen één datum, kiest u het specifieke herstel punt in de vervolg keuzelijst **tijd** .

    ![Scherm afbeelding van de wizard gegevens herstellen Selecteer de pagina volume en datum (herstellen op dezelfde machine)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Nadat u het herstel punt hebt gekozen dat u wilt herstellen, selecteert u **koppelen**.

    Azure Backup koppelt het lokale herstel punt en gebruikt dit als een herstel volume.

7. Selecteer op de pagina **Bladeren en herstel bestanden** **Bladeren** om Windows Verkenner te openen en de gewenste bestanden en mappen te vinden.

    ![Scherm afbeelding van de wizard gegevens herstellen bladeren en bestanden herstellen (herstellen op dezelfde machine)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. Kopieer in Windows Verkenner de bestanden en mappen die u wilt herstellen en plak ze op een lokale locatie op de server of computer. U kunt de bestanden rechtstreeks vanuit het herstel volume openen of streamen en controleren of u de juiste versies herstelt.

    ![Scherm opname van Windows Verkenner, waarbij kopiëren is gemarkeerd (herstellen op dezelfde machine)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Wanneer u klaar bent, selecteert u op de pagina **Bladeren en herstel bestanden** de optie **ontkoppelen**. Selecteer vervolgens **Ja** om te bevestigen dat u het volume wilt ontkoppelen.

    ![Scherm afbeelding van de wizard gegevens herstellen bladeren en bestanden herstellen (herstellen naar dezelfde machine)-het herstel volume bevestigen ontkoppelen](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Als u niet **ontkoppelen** selecteert, blijft het herstel volume gedurende 6 uur vanaf het moment waarop het is gekoppeld. De koppel tijd wordt echter Maxi maal 24 uur verlengd in het geval van een doorlopende bestands kopie. Er worden geen back-upbewerkingen uitgevoerd terwijl het volume is gekoppeld. Elke back-upbewerking die is gepland om te worden uitgevoerd op het moment dat het volume wordt gekoppeld, wordt uitgevoerd nadat het herstel volume is ontkoppeld.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Direct terugzetten gebruiken om gegevens naar een andere computer te herstellen

Als uw hele server verloren is gegaan, kunt u nog steeds gegevens herstellen van Azure Backup naar een andere computer. De volgende stappen illustreren de werk stroom.

Deze stappen omvatten de volgende terminologie:

* *Bron machine* : de oorspronkelijke machine waarvan de back-up is gemaakt en die momenteel niet beschikbaar is.
* *Doel computer* : de computer waarop de gegevens worden hersteld.
* Voor beeld van de *kluis* : de Recovery Services kluis waarmee de bron computer en de doel computer zijn geregistreerd.

> [!NOTE]
> Back-ups kunnen niet worden hersteld naar een doel computer waarop een eerdere versie van het besturings systeem wordt uitgevoerd. U kunt bijvoorbeeld een back-up die is gemaakt van een Windows 7-computer herstellen op een computer met Windows 7 (of hoger). Een back-up die is gemaakt vanaf een Windows 10-computer, kan niet worden hersteld naar een computer met Windows 7.
>
>

1. Open de module **Microsoft Azure backup** op de doel computer.

2. Zorg ervoor dat de doel computer en de bron machine zijn geregistreerd bij dezelfde Recovery Services kluis.

3. Selecteer **gegevens herstellen** om de **wizard gegevens herstellen** te openen.

    ![Scherm opname van Azure Backup, waarbij gegevens herstellen is gemarkeerd (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/recover.png)

4. Selecteer op de pagina **aan** de slag **een andere server**.

    ![Scherm afbeelding van de wizard aan de slag met pagina Herstel gegevens (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Geef het kluis referentie bestand op dat overeenkomt met de voor beeld-kluis en selecteer **volgende**.

    Als het kluis referentie bestand ongeldig is (of is verlopen), [downloadt u een nieuw kluis referentie bestand van de voor beeld-kluis](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) in de Azure Portal. Nadat u een geldige kluis referentie hebt verstrekt, wordt de naam van de bijbehorende back-upkluis weer gegeven.

6. Selecteer op de pagina **back-upserver selecteren** de bron machine uit de lijst met weer gegeven computers en geef de wachtwoordzin op. Selecteer vervolgens **Volgende**.

    ![Scherm opname van de wizard gegevens herstellen pagina back-upserver selecteren (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Selecteer op de pagina **herstel modus selecteren** de optie **afzonderlijke bestanden en mappen**  >  **volgende**.

    ![Scherm afbeelding van de wizard gegevens herstellen Selecteer de pagina herstel modus (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Selecteer op de pagina **volume en datum selecteren** het volume dat de bestanden en mappen bevat die u wilt herstellen.

    Selecteer een herstel punt in de agenda. **Vetgedrukte** datums geven de beschik baarheid van ten minste één herstel punt aan. Als er meerdere herstel punten beschikbaar zijn binnen één datum, kiest u het specifieke herstel punt in de vervolg keuzelijst **tijd** .

    ![Scherm afbeelding van de wizard gegevens herstellen Selecteer de pagina volume en datum (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Selecteer **koppelen** om lokaal het herstel punt als een herstel volume op uw doel computer te koppelen.

10. Selecteer op de pagina **Bladeren en herstel bestanden** **Bladeren** om Windows Verkenner te openen en de gewenste bestanden en mappen te vinden.

    ![Scherm afbeelding van de wizard gegevens herstellen bladeren en bestanden herstellen (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Kopieer in Windows Verkenner de bestanden en mappen van het herstel volume en plak ze op de locatie van de doel machine. U kunt de bestanden rechtstreeks vanuit het herstel volume openen of streamen en controleren of de juiste versies zijn hersteld.

    ![Scherm opname van Windows Verkenner, waarbij kopiëren is gemarkeerd (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Wanneer u klaar bent, selecteert u op de pagina **Bladeren en herstel bestanden** de optie **ontkoppelen**. Selecteer vervolgens **Ja** om te bevestigen dat u het volume wilt ontkoppelen.

    ![Het volume ontkoppelen (herstellen naar een alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Als u niet **ontkoppelen** selecteert, blijft het herstel volume gedurende 6 uur vanaf het moment waarop het is gekoppeld. De koppel tijd wordt echter Maxi maal 24 uur verlengd in het geval van een doorlopende bestands kopie. Er worden geen back-upbewerkingen uitgevoerd terwijl het volume is gekoppeld. Elke back-upbewerking die is gepland om te worden uitgevoerd op het moment dat het volume wordt gekoppeld, wordt uitgevoerd nadat het herstel volume is ontkoppeld.
    >

## <a name="next-steps"></a>Volgende stappen

* Nu u uw bestanden en mappen hebt hersteld, kunt u [uw back-ups beheren](backup-azure-manage-windows-server.md).

* Vind [Veelgestelde vragen over het maken van back-ups van bestanden en mappen](backup-azure-file-folder-backup-faq.md).
