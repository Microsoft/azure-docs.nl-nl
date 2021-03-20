---
title: Azure-bestands shares herstellen
description: Informatie over het gebruik van de Azure Portal om een volledige bestands share of specifieke bestanden te herstellen vanaf een herstel punt dat is gemaakt door Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 35ca0936ae6efba716adb51f43326cdd5bfa2d98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89376771"
---
# <a name="restore-azure-file-shares"></a>Azure-bestands shares herstellen

In dit artikel wordt uitgelegd hoe u de Azure Portal kunt gebruiken om een volledige bestands share of specifieke bestanden te herstellen vanaf een herstel punt dat is gemaakt door [Azure backup](./backup-overview.md).

In dit artikel leert u het volgende:

* Een volledige Azure-bestands share herstellen.
* Afzonderlijke bestanden of mappen herstellen.
* Volg de status van de herstel bewerking.

## <a name="steps-to-perform-a-restore-operation"></a>Stappen voor het uitvoeren van een herstel bewerking

Voer de volgende stappen uit om een herstel bewerking uit te voeren.

### <a name="select-the-file-share-to-restore"></a>Selecteer de bestands share die u wilt herstellen

1. Open in de [Azure Portal](https://portal.azure.com/)de Recovery Services kluis die u hebt gebruikt voor het configureren van de back-up voor de bestands share.

1. Selecteer in het deel venster Overzicht de optie **Back-upitems** onder de sectie **beveiligde items** .

    ![Back-upitems selecteren](./media/restore-afs/backup-items.png)

1. Nadat u **Back-upitems** hebt geselecteerd, wordt er een nieuw deel venster met alle typen back-upbeheer weer gegeven naast het deel venster Overzicht.

    ![Typen back-upbeheer](./media/restore-afs/backup-management.png)

1. In het deel venster **Back-upitems** , onder **type back-upbeheer**, selecteert u **Azure Storage (Azure files)**. U ziet een lijst met alle bestands shares en bijbehorende opslag accounts waarvan een back-up is gemaakt met behulp van deze kluis.

    ![Lijst met alle bestands shares](./media/restore-afs/file-shares.png)

1. Selecteer in de lijst met Azure-bestands shares de bestands share waarvoor u de herstel bewerking wilt uitvoeren.

### <a name="full-share-recovery"></a>Herstel van volledige share

U kunt deze terugzet optie gebruiken om de volledige bestands share te herstellen op de oorspronkelijke locatie of op een andere locatie.

1. Selecteer de optie **share terugzetten** in het deel venster **back-upitem** dat wordt weer gegeven nadat u de bestands share hebt geselecteerd om te herstellen in stap 5 van de sectie [Selecteer de bestands share die u wilt terugzetten](#select-the-file-share-to-restore) .

   ![Share terugzetten selecteren](./media/restore-afs/restore-share.png)

1. Nadat u **share terugzetten** hebt geselecteerd, wordt het deel venster **herstellen** geopend. Als u het herstel punt dat u wilt gebruiken voor het uitvoeren van de herstel bewerking wilt selecteren, kiest u het tekstvak koppelings tekst onder het vak **herstel punt** **selecteren** .

    ![Selecteer een herstel punt door selecteren te kiezen](./media/restore-afs/select-restore-point.png)

1. Het deel venster **herstel punt context selecteren** wordt aan de rechter kant geopend met een overzicht van de herstel punten die beschikbaar zijn voor de geselecteerde bestands share. Selecteer het herstel punt dat u wilt gebruiken om de herstel bewerking uit te voeren en selecteer **OK**.

    ![Herstel punt selecteren](./media/restore-afs/restore-point.png)

    >[!NOTE]
    >Standaard worden in het deel venster **herstel punt selecteren** de herstel punten van de afgelopen 30 dagen weer gegeven. Als u wilt zoeken naar de herstel punten die zijn gemaakt tijdens een specifieke duur, geeft u het bereik op door de juiste **Start tijd** en **eind tijd** te selecteren en de knop **vernieuwen** te selecteren.

1. De volgende stap is het kiezen van de **herstel locatie**. Geef in de sectie **herstel bestemming** op waar of hoe de gegevens moeten worden hersteld. Selecteer een van de volgende twee opties met behulp van de wissel knop:

    * **Oorspronkelijke locatie**: herstel de volledige bestands share naar dezelfde locatie als de oorspronkelijke bron.
    * **Alternatieve locatie**: herstel de volledige bestands share naar een andere locatie en behoud de oorspronkelijke bestands share.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>Herstellen naar de oorspronkelijke locatie (volledige share herstel)

1. Selecteer **oorspronkelijke locatie** als **herstel doel** en selecteer of u wilt overs Laan of overschrijven als er conflicten zijn, door de desbetreffende optie te kiezen in de vervolg keuzelijst **in het geval van conflicten** .

1. Selecteer **herstellen** om de herstel bewerking te starten.

    ![Selecteer herstellen om te starten](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>Herstellen naar een andere locatie (volledige share herstel)

1. Selecteer **alternatieve locatie** als **herstel bestemming**.
1. Selecteer het doel-opslag account waar u de inhoud waarvan een back-up is gemaakt, wilt herstellen uit de vervolg keuzelijst **opslag account** .
1. In de vervolg keuzelijst **Bestands share selecteren** worden de bestands shares weer gegeven die aanwezig zijn in het opslag account dat u in stap 2 hebt geselecteerd. Selecteer de bestands share waar u de inhoud van de back-up wilt terugzetten.
1. Geef in het vak **mapnaam** de naam op van de map die u in de doel bestands share wilt maken met de herstelde inhoud.
1. Selecteer of u wilt overs Laan of overschrijven als er conflicten zijn.
1. Nadat u de juiste waarden in alle vakken hebt opgegeven, selecteert u **herstellen** om de herstel bewerking te starten.

    ![Alternatieve locatie selecteren](./media/restore-afs/alternate-location.png)

### <a name="item-level-recovery"></a>Herstel op itemniveau

U kunt deze terugzet optie gebruiken om afzonderlijke bestanden of mappen op de oorspronkelijke locatie of op een andere locatie te herstellen.

1. Selecteer de optie **bestands herstel** in het deel venster **back-upitem** dat wordt weer gegeven nadat u de bestands share hebt geselecteerd die u wilt herstellen in stap 5 van de sectie [Selecteer de bestands share die u wilt terugzetten](#select-the-file-share-to-restore) .

    ![Bestands herstel selecteren](./media/restore-afs/file-recovery.png)

1. Nadat u **bestands herstel** hebt geselecteerd, wordt het deel venster **herstellen** geopend. Als u het herstel punt wilt selecteren dat u wilt gebruiken voor het uitvoeren van de herstel bewerking, selecteert u het **tekstvak koppelings** tekst onder het tekstvak **herstel punt** selecteren.

    ![Selecteer een herstel punt door de koppeling selecteren te kiezen](./media/restore-afs/select-restore-point.png)

1. Het deel venster **herstel punt context selecteren** wordt aan de rechter kant geopend met een overzicht van de herstel punten die beschikbaar zijn voor de geselecteerde bestands share. Selecteer het herstel punt dat u wilt gebruiken om de herstel bewerking uit te voeren en selecteer **OK**.

    ![Herstel punt selecteren](./media/restore-afs/restore-point.png)

1. De volgende stap is het kiezen van de **herstel locatie**. Geef in de sectie **herstel bestemming** op waar of hoe de gegevens moeten worden hersteld. Selecteer een van de volgende twee opties met behulp van de wissel knop:

    * **Oorspronkelijke locatie**: Zet geselecteerde bestanden of mappen terug naar dezelfde bestands share als de oorspronkelijke bron.
    * **Alternatieve locatie**: geselecteerde bestanden of mappen terugzetten op een andere locatie en de oorspronkelijke bestands share-inhoud blijven gebruiken.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>Herstellen naar de oorspronkelijke locatie (herstel op item niveau)

1. Selecteer **oorspronkelijke locatie** als **herstel doel** en selecteer of u wilt overs Laan of overschrijven als er conflicten optreden door de juiste optie te kiezen in de vervolg keuzelijst **in het geval van conflicten** .

    ![Oorspronkelijke locatie voor herstel op item niveau](./media/restore-afs/original-location-item-level.png)

1. Selecteer de knop **bestand toevoegen** om de bestanden of mappen te selecteren die u wilt herstellen. Hiermee opent u een context venster aan de rechter kant, waarin de inhoud wordt weer gegeven van het herstel punt van de bestands share dat u hebt geselecteerd voor herstel.

    ![Kies bestand toevoegen](./media/restore-afs/add-file.png)

1. Schakel het selectie vakje in dat overeenkomt met het bestand dat of de map die u wilt herstellen en kies **selecteren**.

    ![Bestand of map selecteren](./media/restore-afs/select-file-folder.png)

1. Herhaal stap 2 tot en met 4 om meerdere bestanden of mappen te selecteren die u wilt herstellen.
1. Nadat u alle items hebt geselecteerd die u wilt herstellen, selecteert u **herstellen** om de herstel bewerking te starten.

    ![Selecteer herstellen om te starten](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>Herstellen naar een andere locatie (herstel op item niveau)

1. Selecteer **alternatieve locatie** als **herstel bestemming**.
1. Selecteer het doel-opslag account waar u de inhoud waarvan een back-up is gemaakt, wilt herstellen uit de vervolg keuzelijst **opslag account** .
1. In de vervolg keuzelijst **Bestands share selecteren** worden de bestands shares weer gegeven die aanwezig zijn in het opslag account dat u in stap 2 hebt geselecteerd. Selecteer de bestands share waar u de inhoud van de back-up wilt terugzetten.
1. Geef in het vak **mapnaam** de naam op van de map die u in de doel bestands share wilt maken met de herstelde inhoud.
1. Selecteer of u wilt overs Laan of overschrijven als er conflicten zijn.
1. Selecteer de knop **bestand toevoegen** om de bestanden of mappen te selecteren die u wilt herstellen. Hiermee opent u een context venster aan de rechter kant waarmee de inhoud wordt weer gegeven van het herstel punt dat u hebt geselecteerd voor herstel.

    ![Selecteer items om terug te zetten naar een alternatieve locatie](./media/restore-afs/restore-to-alternate-location.png)

1. Schakel het selectie vakje in dat overeenkomt met het bestand dat of de map die u wilt herstellen en kies **selecteren**.

    ![Herstel bestemming selecteren](./media/restore-afs/recovery-destination.png)

1. Herhaal stap 6 tot en met 8 om meerdere bestanden of mappen te selecteren die u wilt herstellen.
1. Nadat u alle items hebt geselecteerd die u wilt herstellen, selecteert u **herstellen** om de herstel bewerking te starten.

    ![Selecteer OK nadat u alle bestanden hebt geselecteerd](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Een terugzet bewerking volgen

Nadat u de herstel bewerking hebt geactiveerd, maakt de back-upservice een taak voor tracering. Azure Backup worden meldingen over de taak weer gegeven in de portal. Selecteer de Hyper Link meldingen om de bewerkingen voor de taak weer te geven.

![Hyper Link meldingen selecteren](./media/restore-afs/notifications-link.png)

U kunt de voortgang van de herstel bewerking ook controleren vanuit de Recovery Services kluis:

1. Open de Recovery Services kluis van waaruit u de herstel bewerking hebt geactiveerd.
1. Selecteer in het deel venster overzicht **back-uptaken** onder het gedeelte **bewaking** om de status van de bewerkingen die worden uitgevoerd op verschillende werk belastingen weer te geven.

    ![Back-uptaken selecteren](./media/restore-afs/backup-jobs.png)

1. Selecteer de naam van de werk belasting die overeenkomt met uw bestands share om meer informatie over de herstel bewerking weer te geven, zoals **overgedragen gegevens** en het **aantal herstelde bestanden**.

    ![Details van hersteld weer geven](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beheren van back-ups van Azure-bestands shares](manage-afs-backup.md).
