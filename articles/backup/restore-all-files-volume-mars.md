---
title: Alle bestanden in een volume met MARS terugzetten
description: Meer informatie over het herstellen van alle bestanden in een volume met behulp van de MARS-agent.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613974"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Alle bestanden in een volume terugzetten met behulp van de MARS-agent

In dit artikel wordt uitgelegd hoe u alle back-upbestanden herstelt in een volledig volume met behulp van de wizard gegevens herstellen in de MARS-agent (Microsoft Azure Recovery Services). U kunt:

- Herstel alle back-ups van bestanden in een volume op dezelfde computer waarvan de back-ups zijn gemaakt.
- Herstel alle back-upbestanden op een andere computer met een volume.

>[!TIP]
>De **volume** optie herstelt alle back-ups van gegevens in een opgegeven volume. Deze optie biedt snellere overdrachts snelheden (Maxi maal 40 MBps) en wordt aanbevolen voor het herstellen van grote hoeveel heden gegevens of volledige volumes.
>
>Met de **optie afzonderlijke bestanden en mappen** kunt u snel toegang krijgen tot de herstel punt gegevens. Het is geschikt voor het herstellen van afzonderlijke bestanden en wordt aanbevolen voor een totale grootte van minder dan 80 GB. Het biedt overdrachts-of kopieer snelheden van Maxi maal 6 MBps tijdens het herstel.

## <a name="volume-level-restore-to-the-same-machine"></a>Herstel op volume niveau naar dezelfde computer

De volgende stappen helpen u bij het herstellen van alle back-upbestanden in een volume:

1. Open de **Microsoft Azure Backup**-module. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Microsoft Azure backup**. De bureau blad-app moet worden weer gegeven in de zoek resultaten.

1. Selecteer **gegevens herstellen** om de wizard te starten.

    ![Menu Data herstellen](./media/restore-all-files-volume-mars/recover.png)

1. Selecteer op de pagina **aan** de slag de optie **deze server (Server naam)**  >  **volgende** om de gegevens op dezelfde server of computer te herstellen.

    ![Pagina Aan de slag](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Kies op de pagina **herstel modus selecteren** de optie **volume**  >  **volgende**.

    ![Herstel modus selecteren](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Selecteer op de pagina **volume en datum selecteren** het volume dat u wilt herstellen.

    Selecteer een herstel punt in de agenda. **Vetgedrukte** datums geven de beschik baarheid van ten minste één herstel punt aan. Als er meerdere herstel punten beschikbaar zijn binnen één datum, kiest u het specifieke herstel punt in de vervolg keuzelijst **tijd** .

     ![Volume en datum selecteren](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Configureer op de pagina **herstel opties opgeven** het gedrag voor het herstellen.
    1. Kies het herstel doel:
        - **Oorspronkelijke locatie**: de gegevens worden teruggezet naar het oorspronkelijke pad.
        - **Andere locatie**: Geef een alternatieve locatie op waarnaar u de gegevens wilt herstellen.
    1. Kies het gedrag voor **wanneer items in de back-up al aanwezig zijn in het herstel doel**:
        - **Maak kopieën zodat u beide versies hebt**: als er al een bestand met dezelfde naam bestaat, worden de gegevens in het herstel punt teruggezet als een kopie. De kopie heeft een gelokaliseerd bestands naam voorvoegsel met de lokale herstel taak tijd in een van de volgende indelingen:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Bestaande versies overschrijven met herstelde versies**: als er al een bestand met dezelfde naam bestaat, wordt de inhoud vervangen door gegevens in het herstel punt.
        - **De items die al bestaan op de herstel bestemming niet herstellen**: als er al een bestand met dezelfde naam bestaat, wordt het overgeslagen.
    1. **Schakel de machtigingen voor het terugzetten van de toegangs beheer lijst (ACL) in voor het bestand of de map die wordt hersteld** als het bestand moet worden hersteld met de oorspronkelijke machtigingen in het herstel punt.
        ![Herstel opties opgeven](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Controleer de details van het herstel in het **bevestigings** venster en selecteer **herstellen**.

    ![Bevestigings Details](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Controleer op de pagina **herstel voortgang** de voortgang van de herstel taak. De wizard kan ook veilig worden gesloten en de herstel bewerking wordt op de achtergrond voortgezet. U kunt de voortgang weer geven door te dubbel klikken op de herstel taak in het dash board.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Herstel op volume niveau naar een alternatieve computer

De volgende stappen helpen u bij het herstellen van alle back-upbestanden op een volume naar een andere computer. U kunt deze stappen gebruiken om gegevens van Azure Backup te herstellen als uw hele server verloren is gegaan.

Deze stappen omvatten de volgende terminologie:

- *Bron machine* : de oorspronkelijke machine waarvan de back-up is gemaakt en die momenteel niet beschikbaar is.
- *Doel computer* : de computer waarop de gegevens worden hersteld.
- Voor beeld van de *kluis* : de Recovery Services kluis waarmee de bron computer en de doel computer zijn geregistreerd.

> [!NOTE]
> Back-ups kunnen niet worden hersteld naar een doel computer waarop een eerdere versie van het besturings systeem wordt uitgevoerd. U kunt bijvoorbeeld een back-up die is gemaakt van een Windows 7-computer herstellen op een computer met Windows 7 (of hoger). Een back-up die is gemaakt vanaf een Windows 10-computer, kan niet worden hersteld naar een computer met Windows 7.

1. Open de module **Microsoft Azure backup** op de doel computer.

1. Zorg ervoor dat de doel computer en de bron machine zijn geregistreerd bij dezelfde Recovery Services kluis.

1. Selecteer **gegevens herstellen** om de **wizard gegevens herstellen** te openen.

    ![Scherm opname van Azure Backup, waarbij gegevens herstellen is gemarkeerd (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/recover.png)

1. Selecteer op de pagina **aan** de slag **een andere server**.

    ![Scherm afbeelding van de wizard aan de slag met pagina Herstel gegevens (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Geef het kluis referentie bestand op dat overeenkomt met de voor beeld-kluis en selecteer **volgende**.

    Als het kluis referentie bestand ongeldig is (of is verlopen), [downloadt u een nieuw kluis referentie bestand van de voor beeld-kluis](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) in de Azure Portal. Nadat u een geldige kluis referentie hebt verstrekt, wordt de naam van de bijbehorende back-upkluis weer gegeven.

1. Selecteer op de pagina **back-upserver selecteren** de bron machine uit de lijst met weer gegeven computers en geef de wachtwoordzin op. Selecteer vervolgens **Volgende**.

    ![Scherm opname van de wizard gegevens herstellen pagina back-upserver selecteren (herstellen naar alternatieve machine)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Kies op de pagina **herstel modus selecteren** de optie **volume**  >  **volgende**.

    ![Herstel modus selecteren](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Selecteer op de pagina **volume en datum selecteren** het volume dat u wilt herstellen.

    Selecteer een herstel punt in de agenda. **Vetgedrukte** datums geven de beschik baarheid van ten minste één herstel punt aan. Als er meerdere herstel punten beschikbaar zijn binnen één datum, kiest u het specifieke herstel punt in de vervolg keuzelijst **tijd** .

     ![Volume en datum selecteren](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Configureer op de pagina **herstel opties opgeven** het gedrag voor het herstellen.
    1. Kies het herstel doel:
        - **Oorspronkelijke locatie**: de gegevens worden teruggezet naar het oorspronkelijke pad.
        - **Andere locatie**: Geef een alternatieve locatie op waarnaar u de gegevens wilt herstellen.
    1. Kies het gedrag voor **wanneer items in de back-up al aanwezig zijn in het herstel doel**:
        - **Maak kopieën zodat u beide versies hebt**: als er al een bestand met dezelfde naam bestaat, worden de gegevens in het herstel punt teruggezet als een kopie. De kopie heeft een gelokaliseerd bestands naam voorvoegsel met de lokale herstel taak tijd in een van de volgende indelingen:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Bestaande versies overschrijven met herstelde versies**: als er al een bestand met dezelfde naam bestaat, wordt de inhoud vervangen door gegevens in het herstel punt.
        - **De items die al bestaan op de herstel bestemming niet herstellen**: als er al een bestand met dezelfde naam bestaat, wordt het overgeslagen.
    1. **Schakel de machtigingen voor het terugzetten van de toegangs beheer lijst (ACL) in voor het bestand of de map die wordt hersteld** als het bestand moet worden hersteld met de oorspronkelijke machtigingen in het herstel punt.
        ![Herstel opties opgeven](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Controleer de details van het herstel in het **bevestigings** venster en selecteer **herstellen**.

    ![Bevestigings Details](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Controleer op de pagina **herstel voortgang** de voortgang van de herstel taak. De wizard kan ook veilig worden gesloten en de herstel bewerking wordt op de achtergrond voortgezet. U kunt de voortgang weer geven door te dubbel klikken op de herstel taak in het dash board.

## <a name="next-steps"></a>Volgende stappen

- Nu u uw bestanden en mappen hebt hersteld, kunt u [uw back-ups beheren](backup-azure-manage-windows-server.md).
- Vind [Veelgestelde vragen over het maken van back-ups van bestanden en mappen](backup-azure-file-folder-backup-faq.md).
