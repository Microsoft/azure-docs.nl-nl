---
title: Bestanden herstellen naar Windows Server met behulp van de MARS-agent
description: In dit artikel leert u hoe u gegevens die zijn opgeslagen in Azure, kunt herstellen naar een Windows-server of Windows-computer met de MARS-agent (Microsoft Azure Recovery Services).
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 7ca0787ec38e1bc22b62e756c7ee56c5c9e93493
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517331"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Bestanden herstellen naar Windows Server met behulp van de MARS-agent

In dit artikel wordt uitgelegd hoe u gegevens herstelt vanuit een back-upkluis. Als u gegevens wilt herstellen, gebruikt u de wizard Gegevens herstellen in Microsoft Azure Recovery Services-agent (MARS). U kunt:

* Herstel gegevens naar dezelfde computer van waaruit de back-ups zijn gemaakt.
* Gegevens herstellen naar een alternatieve machine

Gebruik de functie Direct herstellen om een momentopname van een schrijfbaar herstelpunt te maken als herstelvolume. Vervolgens kunt u het herstelvolume verkennen en bestanden kopiëren naar een lokale computer, op die manier bestanden selectief herstellen.

> [!NOTE]
> De [update van januari 2017 Azure Backup](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) is vereist als u Direct herstellen wilt gebruiken om gegevens te herstellen. Daarnaast moeten de back-upgegevens worden beveiligd in kluizen in de localen die worden vermeld in het ondersteuningsartikel. Raadpleeg de [update van januari 2017 Azure Backup voor](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) de meest recente lijst met localen die ondersteuning bieden voor direct herstellen.
>

Gebruik Direct herstellen met Recovery Services-kluizen in de Azure Portal. Als u gegevens hebt opgeslagen in Backup-kluizen, zijn deze geconverteerd naar Recovery Services-kluizen. Als u Direct herstellen wilt gebruiken, downloadt u de MARS-update en volgt u de procedures met de vermelding Direct herstellen.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Direct herstellen gebruiken om gegevens te herstellen op dezelfde computer

Als u per ongeluk een bestand hebt verwijderd en dit wilt herstellen op dezelfde computer (van waaruit de back-up wordt gemaakt), helpen de volgende stappen u bij het herstellen van de gegevens.

1. Open de **Microsoft Azure Backup**-module. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Microsoft Azure Backup**.

    De bureaublad-app moet worden weergegeven in de zoekresultaten.

2. Selecteer **Gegevens herstellen om** de wizard te starten.

    ![Schermopname van Azure Backup, met Gegevens herstellen gemarkeerd (herstellen op dezelfde computer)](./media/backup-azure-restore-windows-server/recover.png)

3. Selecteer op **Aan de slag** pagina Deze server **( ) `<server name>` Volgende** om de gegevens te herstellen naar dezelfde server of  >  **computer.**

    ![Schermopname van de wizard Gegevens herstellen Aan de slag pagina (herstellen naar dezelfde computer)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Kies op **de pagina Herstelmodus** selecteren **de optie Afzonderlijke bestanden en mappen** > **Volgende.**

    ![Schermopname van de pagina Herstelmodus selecteren van de wizard Gegevens herstellen (herstellen op dezelfde computer)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Voor de optie voor het herstellen van afzonderlijke bestanden en mappen is .NET Framework 4.5.2 of hoger vereist. Als u de optie  Afzonderlijke bestanden en mappen niet ziet, moet u .NET Framework upgraden naar versie 4.5.2 of hoger en het opnieuw proberen.

   > [!TIP]
   > Met **de optie Afzonderlijke bestanden en mappen** hebt u snelle toegang tot de herstelpuntgegevens. Het is geschikt voor het herstellen van afzonderlijke bestanden en wordt aanbevolen voor een totale grootte van minder dan 80 GB. Het biedt overdrachts- of kopieersnelheden tot 6 MBps tijdens het herstel. Met **de optie Volume** worden alle back-upgegevens in een opgegeven volume hersteld. Deze optie biedt snellere overdrachtssnelheden (tot 40 MBps) en wordt aanbevolen voor het herstellen van grote hoeveelheden gegevens of hele volumes.

5. Selecteer op **de pagina Volume en** datum selecteren het volume dat de bestanden en mappen bevat die u wilt herstellen.

    Selecteer een herstelpunt in de agenda. **Vetgedrukte datums** geven de beschikbaarheid van ten minste één herstelpunt aan. Als er meerdere herstelpunten beschikbaar zijn binnen één datum, kiest u het specifieke herstelpunt in **de** vervolgkeuzelijst Tijd.

    ![Schermopname van de wizard Gegevens herstellen, pagina Volume en datum selecteren (terugzetten op dezelfde computer)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Nadat u het herstelpunt voor herstel heeft gekozen, selecteert u **Mount**.

    Azure Backup het lokale herstelpunt en gebruikt dit als herstelvolume.

7. Selecteer op **de pagina Bladeren en** bestanden herstellen de optie **Bladeren** om de Windows Verkenner te openen en zoek de bestanden en mappen die u wilt.

    ![Schermopname van de pagina Bladeren in en herstellen van de wizard Gegevens herstellen (terugzetten op dezelfde computer)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. Kopieer Windows Verkenner bestanden en mappen die u wilt herstellen en plak deze op een lokale locatie op de server of computer. U kunt de bestanden rechtstreeks vanuit het herstelvolume openen of streamen en controleren of u de juiste versies herstelt.

    ![Schermopname van Windows Verkenner, met Kopiëren gemarkeerd (herstellen naar dezelfde computer)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Wanneer u klaar bent, selecteert **u** ontkoppelen op de pagina Bestanden bladeren en **herstellen.** Selecteer vervolgens **Ja** om te bevestigen dat u het volume wilt ontkoppelen.

    ![Schermopname van de pagina Bladeren door en herstellen van de wizard Gegevens herstellen (terugzetten op dezelfde computer) - Herstelvolume ontkoppelen bevestigen](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Als u Ontkoppelen **niet** selecteert, blijft het herstelvolume zes uur na het moment waarop het is bevestigd, aan het herstelvolume bevestigd. De tijd voor het maken van een bestand wordt echter verlengd tot maximaal 24 uur in het geval van een lopende bestandskopie. Er worden geen back-upbewerkingen uitgevoerd terwijl het volume is bevestigd. Een back-upbewerking die is gepland om te worden uitgevoerd op het moment dat het volume wordt bevestigd, wordt uitgevoerd nadat het herstelvolume is ontkoppeld.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Direct herstellen gebruiken om gegevens te herstellen naar een alternatieve machine

Als uw hele server verloren is gegaan, kunt u nog steeds gegevens herstellen van Azure Backup naar een andere computer. De volgende stappen illustreren de werkstroom.

Deze stappen omvatten de volgende terminologie:

* *Bronmachine:* de oorspronkelijke computer van waaruit de back-up is gemaakt en die momenteel niet beschikbaar is.
* *Doelmachine:* de computer waarop de gegevens worden hersteld.
* *Voorbeeldkluis:* de Recovery Services-kluis waarvoor de bronmachine en de doelmachine zijn geregistreerd.

> [!NOTE]
> Back-ups kunnen niet worden hersteld naar een doelmachine met een eerdere versie van het besturingssysteem. Een back-up van een Windows 7-computer kan bijvoorbeeld worden hersteld op een computer met Windows 7 (of hoger). Een back-up van een Windows 10 kan niet worden hersteld naar een Windows 7-computer.
>
>

1. Open de **Microsoft Azure Backup** op de doelmachine.

2. Zorg ervoor dat de doelmachine en de bronmachine zijn geregistreerd bij dezelfde Recovery Services-kluis.

3. Selecteer **Gegevens herstellen om** de wizard Gegevens herstellen te **openen.**

    ![Schermopname van Azure Backup, met Gegevens herstellen gemarkeerd (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/recover.png)

4. Selecteer op **Aan de slag** pagina Een **andere server.**

    ![Schermopname van de wizard Gegevens herstellen Aan de slag pagina (herstellen naar alternatieve computer)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Geef het kluisreferentiebestand op dat overeenkomt met de voorbeeldkluis en selecteer **Volgende.**

    Als het kluisreferentiebestand ongeldig (of verlopen) is, [downloadt](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) u een nieuw kluisreferentiebestand uit de voorbeeldkluis in de Azure Portal. Nadat u een geldige kluisreferentie hebt verstrekt, wordt de naam van de bijbehorende back-upkluis weergegeven.

6. Selecteer op **de pagina Back-upserver** selecteren de bronmachine in de lijst met weergegeven machines en geef de wachtwoordzin op. Selecteer vervolgens **Volgende**.

    ![Schermopname van de pagina Back-upserver selecteren van de wizard Gegevens herstellen (terugzetten naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Selecteer op **de pagina Herstelmodus** selecteren **de optie Afzonderlijke bestanden en mappen**  >  **Volgende.**

    ![Schermopname van de pagina Herstelmodus selecteren van de wizard Gegevens herstellen (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Selecteer op **de pagina Volume en** datum selecteren het volume dat de bestanden en mappen bevat die u wilt herstellen.

    Selecteer een herstelpunt in de agenda. **Vetgedrukte datums** geven de beschikbaarheid van ten minste één herstelpunt aan. Als er meerdere herstelpunten beschikbaar zijn binnen één datum, kiest u het specifieke herstelpunt in **de** vervolgkeuzelijst Tijd.

    ![Schermopname van de wizard Gegevens herstellen Pagina Volume en datum selecteren (herstellen naar alternatieve computer)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Selecteer **Mount** om het herstelpunt lokaal als herstelvolume op uw doelmachine te monteren.

10. Selecteer op **de pagina Bladeren en** bestanden herstellen de optie **Bladeren** om de Windows Verkenner te openen en zoek de bestanden en mappen die u wilt.

    ![Schermopname van de pagina Bladeren en herstellen van de wizard Gegevens herstellen (terugzetten naar alternatieve computer)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Kopieer Windows Verkenner bestanden en mappen van het herstelvolume en plak deze in de locatie van uw doelmachine. U kunt de bestanden rechtstreeks vanuit het herstelvolume openen of streamen en controleren of de juiste versies zijn hersteld.

    ![Schermopname van Windows Verkenner, met Kopiëren gemarkeerd (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Wanneer u klaar bent, selecteert **u** ontkoppelen op de pagina Bestanden bladeren en **herstellen.** Selecteer vervolgens **Ja** om te bevestigen dat u het volume wilt ontkoppelen.

    ![Ontkoppel het volume (terugzetten naar een alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Als u Ontkoppelen **niet** selecteert, blijft het herstelvolume zes uur na het moment waarop het is bevestigd, aan het herstelvolume bevestigd. De tijd voor het maken van een bestand wordt echter verlengd tot maximaal 24 uur in het geval van een lopende bestandskopie. Er worden geen back-upbewerkingen uitgevoerd terwijl het volume is bevestigd. Een back-upbewerking die is gepland om te worden uitgevoerd op het moment dat het volume wordt bevestigd, wordt uitgevoerd nadat het herstelvolume is ontkoppeld.
    >

## <a name="next-steps"></a>Volgende stappen

* Nu u uw bestanden en mappen hebt hersteld, kunt u [uw back-ups beheren.](backup-azure-manage-windows-server.md)

* Zoek [veelvoorkomende vragen over het maken van een back-up van bestanden en mappen.](backup-azure-file-folder-backup-faq.yml)
