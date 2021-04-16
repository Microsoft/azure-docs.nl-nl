---
title: Alle bestanden in een volume herstellen met MARS
description: Meer informatie over het herstellen van alle bestanden in een volume met behulp van de MARS-agent.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 1d04e9f77b9f92594def9381f973c999e96b2cb2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516498"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Alle bestanden in een volume herstellen met behulp van de MARS-agent

In dit artikel wordt uitgelegd hoe u alle back-upbestanden in een heel volume kunt herstellen met behulp van de wizard Gegevens herstellen in de MARS-agent (Microsoft Azure Recovery Services). U kunt:

- Herstel alle back-ups van bestanden in een volume op dezelfde computer van waaruit de back-ups zijn gemaakt.
- Herstel alle back-upbestanden in een volume naar een alternatieve machine.

>[!TIP]
>Met **de optie Volume** worden alle back-upgegevens in een opgegeven volume hersteld. Deze optie biedt snellere overdrachtssnelheden (maximaal 40 MBps) en wordt aanbevolen voor het herstellen van grote hoeveelheden gegevens of hele volumes.
>
>Met **de optie Afzonderlijke bestanden en mappen hebt** u snel toegang tot de herstelpuntgegevens. Het is geschikt voor het herstellen van afzonderlijke bestanden en wordt aanbevolen voor een totale grootte van minder dan 80 GB. Het biedt overdrachts- of kopieersnelheden tot 6 MBps tijdens het herstel.

## <a name="volume-level-restore-to-the-same-machine"></a>Volumeniveau herstellen naar dezelfde computer

De volgende stappen helpen u bij het herstellen van alle back-upbestanden in een volume:

1. Open de **Microsoft Azure Backup**-module. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Microsoft Azure Backup**. De bureaublad-app moet worden weergegeven in de zoekresultaten.

1. Selecteer **Gegevens herstellen om** de wizard te starten.

    ![Menu Gegevens herstellen](./media/restore-all-files-volume-mars/recover.png)

1. Selecteer op **Aan de slag** pagina Deze server **(servernaam)** Volgende om de gegevens te herstellen naar dezelfde server of  >  computer.

    ![Pagina Aan de slag](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Kies op **de pagina Herstelmodus** selecteren de optie **Volume**  >  **Volgende.**

    ![Herstelmodus selecteren](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Selecteer op **de pagina Volume en** datum selecteren het volume dat u wilt herstellen.

    Selecteer een herstelpunt in de agenda. **Vetgedrukte datums** geven de beschikbaarheid van ten minste één herstelpunt aan. Als er meerdere herstelpunten beschikbaar zijn binnen één datum, kiest u het specifieke herstelpunt in **de** vervolgkeuzelijst Tijd.

     ![Volume en datum selecteren](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Configureer **op de pagina Herstelopties** opgeven het herstelgedrag.
    1. Kies de herstelbestemming:
        - **Oorspronkelijke locatie:** herstel gegevens naar het oorspronkelijke pad.
        - **Een andere locatie:** geef een alternatieve locatie op om de gegevens naar te herstellen.
    1. Kies het gedrag voor **Wanneer items in de back-up zich al in de herstelbestemming hebben:**
        - **Kopieën maken zodat u** beide versies hebt: als er al een bestand met dezelfde naam bestaat, worden de gegevens in het herstelpunt als een kopie hersteld. De kopie heeft een gelokaliseerd bestandsnaam voorvoegsel met behulp van de lokale herstel taaktijd in een van de volgende indelingen:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Bestaande versies overschrijven met** herstelde versies: als er al een bestand met dezelfde naam bestaat, wordt de inhoud vervangen door gegevens in het herstelpunt.
        - **Herstel de items die al bestaan** op de herstelbestemming niet: als er al een bestand met dezelfde naam bestaat, wordt dit overgeslagen.
    1. **Schakel toegangsbeheerlijst (ACL)** herstellen in voor het bestand of de map die wordt hersteld als het bestand moet worden hersteld met de oorspronkelijke machtigingen in het herstelpunt.
        ![Herstelopties opgeven](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Controleer de details van het herstel in het **bevestigingsvenster** en selecteer **Herstellen.**

    ![Bevestigingsdetails](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Op de **pagina Voortgang van** herstel controleert u de voortgang van de herstel taak. De wizard kan ook veilig worden gesloten en de herstelbewerking wordt op de achtergrond voortgezet. U kunt de voortgang opnieuw bekijken door te dubbelklikken op de taak Herstel in het dashboard.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Volumeniveau herstellen naar een alternatieve machine

De volgende stappen helpen u bij het herstellen van alle back-upbestanden in een volume naar een alternatieve machine. U kunt deze stappen gebruiken om gegevens te herstellen van Azure Backup als uw hele server verloren is gegaan.

Deze stappen omvatten de volgende terminologie:

- *Bronmachine:* de oorspronkelijke machine van waaruit de back-up is gemaakt en die momenteel niet beschikbaar is.
- *Doelmachine:* de machine waarop de gegevens worden hersteld.
- *Voorbeeldkluis:* de Recovery Services-kluis waarop de bronmachine en de doelmachine zijn geregistreerd.

> [!NOTE]
> Back-ups kunnen niet worden hersteld naar een doelmachine met een eerdere versie van het besturingssysteem. Een back-up van een Windows 7-computer kan bijvoorbeeld worden hersteld op een computer met Windows 7 (of hoger). Een back-up van een Windows 10 kan niet worden hersteld naar een Windows 7-computer.

1. Open de **Microsoft Azure Backup** op de doelmachine.

1. Zorg ervoor dat de doelmachine en de bronmachine zijn geregistreerd bij dezelfde Recovery Services-kluis.

1. Selecteer **Gegevens herstellen om** de wizard Gegevens herstellen te **openen.**

    ![Schermopname van Azure Backup, met Gegevens herstellen gemarkeerd (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/recover.png)

1. Selecteer op **Aan de slag** pagina Een **andere server.**

    ![Schermopname van de wizard Gegevens herstellen Aan de slag pagina (herstellen naar alternatieve computer)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Geef het kluisreferentiebestand op dat overeenkomt met de voorbeeldkluis en selecteer **Volgende.**

    Als het kluisreferentiebestand ongeldig (of verlopen) is, [downloadt](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) u een nieuw kluisreferentiebestand uit de voorbeeldkluis in de Azure Portal. Nadat u een geldige kluisreferentie hebt verstrekt, wordt de naam van de bijbehorende back-upkluis weergegeven.

1. Selecteer op **de pagina Back-upserver** selecteren de bronmachine in de lijst met weergegeven machines en geef de wachtwoordzin op. Selecteer vervolgens **Volgende**.

    ![Schermopname van de pagina Back-upserver selecteren van de wizard Gegevens herstellen (terugzetten naar alternatieve computer)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Kies op **de pagina Herstelmodus** selecteren de optie **Volume**  >  **Volgende.**

    ![Herstelmodus selecteren](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Selecteer op **de pagina Volume en** datum selecteren het volume dat u wilt herstellen.

    Selecteer een herstelpunt in de agenda. **Vetgedrukte datums** geven de beschikbaarheid van ten minste één herstelpunt aan. Als er meerdere herstelpunten beschikbaar zijn binnen één datum, kiest u het specifieke herstelpunt **in** de vervolgkeuzelijst Tijd.

     ![Volume en datum selecteren](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Configureer **het herstelgedrag** op de pagina Herstelopties opgeven.
    1. Kies de herstelbestemming:
        - **Oorspronkelijke locatie:** herstel gegevens naar het oorspronkelijke pad.
        - **Een andere locatie:** geef een alternatieve locatie op om de gegevens naar te herstellen.
    1. Kies het gedrag voor **Wanneer items in de back-up zich al in de herstelbestemming hebben:**
        - **Kopieën maken zodat u** beide versies hebt: als er al een bestand met dezelfde naam bestaat, worden de gegevens in het herstelpunt als een kopie hersteld. De kopie heeft een gelokaliseerd bestandsnaam voorvoegsel met behulp van de lokale herstel taaktijd in een van de volgende indelingen:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Bestaande versies overschrijven met** herstelde versies: als er al een bestand met dezelfde naam bestaat, wordt de inhoud vervangen door gegevens in het herstelpunt.
        - **Herstel de items die al bestaan** op de herstelbestemming niet: als er al een bestand met dezelfde naam bestaat, wordt dit overgeslagen.
    1. **Schakel Machtigingen voor toegangsbeheerlijst (ACL)** herstellen in voor het bestand of de map die wordt hersteld als het bestand moet worden hersteld met de oorspronkelijke machtigingen in het herstelpunt.
        ![Herstelopties opgeven](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Controleer de details van het herstel in het **bevestigingsvenster** en selecteer **Herstellen.**

    ![Bevestigingsdetails](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Op de **pagina Voortgang van** herstel controleert u de voortgang van de herstel taak. De wizard kan ook veilig worden gesloten en de herstelbewerking wordt op de achtergrond voortgezet. U kunt de voortgang opnieuw bekijken door te dubbelklikken op de taak Herstel in het dashboard.

## <a name="next-steps"></a>Volgende stappen

- Nu u uw bestanden en mappen hebt hersteld, kunt u [uw back-ups beheren.](backup-azure-manage-windows-server.md)
- Vind [veelvoorkomende vragen over het maken van back-up van bestanden en mappen.](backup-azure-file-folder-backup-faq.yml)
