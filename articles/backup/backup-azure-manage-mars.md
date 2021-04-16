---
title: Back-ups van MARS-agent beheren en bewaken
description: Meer informatie over het beheren en bewaken Microsoft Azure back-ups van de Mars-agent (Recovery Services) met behulp van de Azure Backup service.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4306f01d608542f7453b32b32a1a6894c2379159
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515019"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Back-ups Microsoft Azure Recovery Services-agent (MARS) beheren met behulp van de Azure Backup service

In dit artikel wordt beschreven hoe u bestanden en mappen beheert waar een back-up van wordt Microsoft Azure Recovery Services-agent.

## <a name="modify-a-backup-policy"></a>Een back-upbeleid wijzigen

Wanneer u het back-upbeleid wijzigt, kunt u nieuwe items toevoegen, bestaande items uit de back-up verwijderen of een back-up van bestanden uitsluiten met uitsluitingsinstellingen.

- **Items toevoegen:** gebruik deze optie alleen voor het toevoegen van nieuwe items om een back-up van te maken. Als u bestaande items wilt verwijderen, **gebruikt u de optie Items** verwijderen of **Uitsluitingsinstellingen.**  
- **Items verwijderen gebruikt** u deze optie om een back-up van items te maken.
  - Gebruik **Uitsluitingsinstellingen** voor het verwijderen van alle items binnen een volume in plaats **van Items verwijderen.**
  - Als u alle selecties in een volume wilt wissen, worden oude back-ups van de items bewaard volgens de retentie-instellingen op het moment van de laatste back-up, zonder wijzigingsbereik.
  - Als u deze items opnieuwselecteert, wordt er een eerste volledige back-up gemaakt en worden nieuwe beleidswijzigingen niet toegepast op oude back-ups.
  - Als u de selectie van het hele volume ongedaan wilt maken, blijft de back-up in het verleden behouden zonder dat er een bereik is voor het wijzigen van het bewaarbeleid.
- **Uitsluitingsinstellingen** gebruiken deze optie om specifieke items uit te sluiten van een back-up.

### <a name="add-new-items-to-existing-policy"></a>Nieuwe items toevoegen aan bestaand beleid

1. Selecteer **back-up** **plannen in Acties.**

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)

2. Selecteer **op het tabblad Beleidsitem** selecteren de optie **Back-upschema voor uw bestanden** en mappen wijzigen en selecteer **Volgende.**

    ![Beleidsitems selecteren](./media/backup-azure-manage-mars/select-policy-items.png)

3. Selecteer **op het tabblad Back-up plannen wijzigen of** stoppen de optie Wijzigingen aanbrengen in **back-upitems of tijden** en selecteer **Volgende.**

    ![Back-up wijzigen of plannen](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Selecteer **op het tabblad Items selecteren** voor back-up de optie Items **toevoegen** om de items toe te voegen waar u een back-up van wilt maken.

    ![Items voor het toevoegen van back-ups wijzigen of plannen](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Selecteer **in het venster Items** selecteren de bestanden of mappen die u wilt toevoegen en selecteer **OK.**

    ![De items selecteren](./media/backup-azure-manage-mars/select-item.png)

6. Voltooi de volgende stappen en selecteer **Voltooien om** de bewerking te voltooien.

### <a name="add-exclusion-rules-to-existing-policy"></a>Uitsluitingsregels toevoegen aan bestaand beleid

U kunt uitsluitingsregels toevoegen om bestanden en mappen over te slaan van wie u geen back-up wilt maken. U kunt dit doen tijdens het definiëren van een nieuw beleid of het wijzigen van een bestaand beleid.

1. Selecteer back-up plannen in **het deelvenster Acties.** Ga naar **Items selecteren naar Back-up en** selecteer **Uitsluitingsinstellingen.**

    ![Uitsluitingsinstellingen](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Selecteer **in Uitsluitingsinstellingen** de **optie Uitsluiting toevoegen.**

    ![Uitsluiting toevoegen](./media/backup-azure-manage-mars/add-exclusion.png)

3. In **Items selecteren om uit te sluiten** bladert u door de bestanden en mappen en selecteert u items die u wilt uitsluiten en selecteert u **OK.**

    ![De items selecteren die moeten worden uitgesloten](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Standaard worden **alle submappen** binnen de geselecteerde mappen uitgesloten. U kunt dit wijzigen door Ja of **Nee** **te selecteren.** U kunt de bestandstypen bewerken en opgeven die moeten worden uitgesloten, zoals hieronder wordt weergegeven:

    ![Submaptypen selecteren](./media/backup-azure-manage-mars/subfolders-type.png)

5. Voltooi de volgende stappen en selecteer **Voltooien om** de bewerking te voltooien.

### <a name="remove-items-from-existing-policy"></a>Items verwijderen uit bestaand beleid

1. Selecteer back-up plannen in **het deelvenster Acties.** Ga naar **Items selecteren om een back-up te maken.** Selecteer in de lijst de bestanden en mappen die u wilt verwijderen uit het back-upschema en selecteer **Items verwijderen.**

    ![De items selecteren die u wilt verwijderen](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Wees voorzichtig wanneer u een volume volledig uit het beleid verwijdert.  Als u deze opnieuw moet toevoegen, wordt dit beschouwd als een nieuw volume. De volgende geplande back-up voert een eerste back-up (volledige back-up) uit in plaats van incrementele back-up. Als u items later tijdelijk wilt verwijderen en toevoegen, is het raadzaam **uitsluitingsinstellingen** te gebruiken in plaats van **Items** verwijderen om te zorgen voor incrementele back-up in plaats van volledige back-up.

2. Voltooi de volgende stappen en selecteer **Voltooien om** de bewerking te voltooien.

## <a name="stop-protecting-files-and-folder-backup"></a>Stoppen met het beveiligen van back-ups van bestanden en mappen

Er zijn twee manieren om te stoppen met het beveiligen van back-ups van bestanden en mappen:

- **Stop de beveiliging en behoudt back-upgegevens.**
  - Met deze optie wordt de beveiliging van alle toekomstige back-uptaken gestopt.
  - Azure Backup-service blijven alle bestaande herstelpunten behouden.  
  - U kunt de back-upgegevens herstellen voor niet-herstelde herstelpunten.
  - Als u besluit de beveiliging te hervatten, kunt u de optie *Back-upschema opnieuw inschakelen* gebruiken. Daarna worden gegevens bewaard op basis van het nieuwe bewaarbeleid.
- **Stop de beveiliging en verwijder back-upgegevens**.
  - Met deze optie wordt voorkomen dat alle toekomstige back-uptaken uw gegevens beveiligen en worden alle herstelpunten verwijderd.
  - U ontvangt een e-mail met de waarschuwing Back-upgegevens verwijderen met het bericht Uw *gegevens voor dit back-upitem zijn verwijderd. Deze gegevens zijn tijdelijk* beschikbaar voor 14 dagen, waarna deze permanent worden verwijderd en de aanbevolen actie Het back-upitem binnen *14* dagen opnieuw veiligt om uw gegevens te herstellen.
  - Als u de beveiliging wilt hervatten, moet u binnen 14 dagen na de verwijderbewerking opnieuw beveiliging uitvoeren.

### <a name="stop-protection-and-retain-backup-data"></a>Beveiliging stoppen en back-upgegevens behouden

1. Open de MARS-beheerconsole, ga naar het **deelvenster Acties** en **selecteer Back-up plannen.**

    ![Back-up plannen selecteren](./media/backup-azure-manage-mars/mars-actions.png)
1. Selecteer op **de pagina Beleidsitem** selecteren **de optie Een back-upschema** voor uw bestanden en mappen wijzigen en selecteer **Volgende.**

    ![Beleidsitem selecteren](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Selecteer op de pagina Een geplande back-up wijzigen of stoppen de optie Gebruik van dit back-upschema stoppen, maar bewaar de **opgeslagen** back-ups totdat een planning **opnieuw wordt geactiveerd.** Selecteer vervolgens **Volgende**.

    ![Een geplande back-up stoppen.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Controleer **in Geplande back-up** onderbreken de informatie en selecteer **Voltooien.**

    ![Een geplande back-up onderbreken.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Controleer **in Voortgang van back-up** wijzigen of de geplande back-up is onderbroken de status Geslaagd heeft en selecteer **Sluiten** om te voltooien.

### <a name="stop-protection-and-delete-backup-data"></a>Beveiliging stoppen en back-upgegevens verwijderen

1. Open de MARS-beheerconsole, ga naar het **deelvenster Acties** en selecteer **Back-up plannen.**
2. Selecteer op **de pagina Een geplande back-up** wijzigen of stoppen de optie Gebruik van dit back-upschema stoppen en verwijder alle **opgeslagen back-ups.** Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer op **de pagina Een geplande back-up** stoppen de optie **Voltooien.**

    ![Een geplande back-up stoppen en Voltooien selecteren](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd een beveiligingspincode (persoonlijk identificatienummer) in te voeren die u handmatig moet genereren. Hiervoor moet u zich eerst aanmelden bij de Azure Portal.
5. Ga naar **Instellingen voor Recovery**  >  **Services-kluisEigenschappen**  >  .
6. Selecteer **genereren onder Pincode** voor **beveiliging.** Kopieer deze pincode. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheerconsole en selecteer **OK.**

    ![Genereer een beveiligingspinpin.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de **pagina Voortgang van back-up** wijzigen wordt het volgende bericht weergegeven: Verwijderde back-upgegevens worden *14 dagen bewaard. Na die tijd worden back-upgegevens permanent verwijderd.*  

    ![Back-up maken wijzigen](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, volgt u de volgende stappen vanuit de portal.

## <a name="re-enable-protection"></a>Beveiliging opnieuw inschakelen

Als u de beveiliging hebt gestopt tijdens het bewaren van gegevens en hebt besloten om de beveiliging te hervatten, kunt u het back-upschema opnieuw inschakelen met behulp van het back-upbeleid wijzigen.

1. Selecteer **back-up** **plannen bij Acties.**
1. Selecteer **Back-upschema opnieuw inschakelen. U kunt ook back-upitems of -tijden wijzigen** en **Volgende selecteren.**<br>

    ![Back-upschema opnieuw inschakelen](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Selecteer **in Items selecteren voor back-up** de optie **Volgende.**

    ![Items selecteren voor back-up](./media/backup-azure-manage-mars/re-enable-next.png)
1. Geef **in Back-upschema opgeven** het back-upschema op en selecteer **Volgende.**
1. Geef **in Bewaarbeleid selecteren** de retentieduur op en selecteer **Volgende.**
1. Controleer ten slotte in **het** bevestigingsscherm de beleidsdetails en selecteer **Voltooien.**

## <a name="re-generate-passphrase"></a>Wachtwoordzin opnieuw genereren

Een wachtwoordzin wordt gebruikt voor het versleutelen en ontsleutelen van gegevens tijdens het maken van een back-up of het herstellen van uw on-premises of lokale computer met behulp van de MARS-agent naar of van Azure. Als u de wachtwoordzin bent kwijtgeraakt of vergeten, kunt u de wachtwoordzin opnieuw maken (mits uw computer nog steeds is geregistreerd bij de Recovery Services-kluis en de back-up is geconfigureerd) door de volgende stappen uit te voeren:

1. Ga in de console van de MARS-agent naar **Deelvenster Acties Eigenschappen** wijzigen  >  **>.** Ga vervolgens naar het **tabblad Versleuteling.**<br>
1. Schakel **het selectievakje Wachtwoordzin** wijzigen in.<br>
1. Voer een nieuwe wachtwoordzin in of selecteer **Wachtwoordzin genereren.**
1. Selecteer **Bladeren** om de nieuwe wachtwoordzin op te slaan.

    ![Wachtwoordzin genereren.](./media/backup-azure-manage-mars/passphrase.png)

1. Selecteer **OK om** wijzigingen toe te passen.  Als de [beveiligingsfunctie](./backup-azure-security-feature.md#enable-security-features) is ingeschakeld op de Azure Portal voor de Recovery Services-kluis, wordt u gevraagd de pincode voor beveiliging in te voeren. Volg de stappen in dit artikel om de pincode te [ontvangen.](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)<br>
1. Plak de pincode voor beveiliging in de portal en selecteer **OK om** de wijzigingen toe te passen.<br>

    ![Plak de pincode voor beveiliging](./media/backup-azure-manage-mars/passphrase2.png)
1. Zorg ervoor dat de wachtwoordzin veilig wordt opgeslagen op een andere locatie (dan de bronmachine), bij voorkeur in de Azure Key Vault. Houd alle wachtwoordzinnen bij als er van meerdere computers een back-up wordt gemaakt met de MARS-agents.

## <a name="managing-backup-data-for-unavailable-machines"></a>Back-upgegevens voor niet-beschikbare machines beheren

In deze sectie wordt een scenario besproken waarin uw bronmachine die is beveiligd met MARS niet meer beschikbaar is omdat deze is verwijderd, beschadigd, is geïnfecteerd met malware/ransomware of buiten gebruik is gesteld.

Voor deze machines zorgt de Azure Backup-service ervoor dat het meest recente herstelpunt niet verloopt (dat wil zeggen, niet wordt hersteld) volgens de bewaarregels die zijn opgegeven in het back-upbeleid. Daarom kunt u de machine veilig herstellen.  Houd rekening met de volgende scenario's die u kunt uitvoeren op de back-upgegevens:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Scenario 1: De bronmachine is niet beschikbaar en u hoeft geen back-upgegevens meer te bewaren

- U kunt de back-upgegevens uit de Azure Portal met behulp van de stappen in [dit artikel](backup-azure-delete-vault.md#delete-protected-items-on-premises).

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Scenario 2: De bronmachine is niet beschikbaar en u moet back-upgegevens behouden

Het beheer van het back-upbeleid voor MARS wordt uitgevoerd via de MARS-console en niet via de portal. Als u de retentie-instellingen voor bestaande herstelpunten wilt uitbreiden voordat ze verlopen, moet u de computer herstellen, de MARS-console installeren en het beleid uitbreiden.

- Voer de volgende stappen uit om de computer te herstellen:
  1. [De virtuele machine herstellen naar een alternatieve doelmachine](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Maak de doelmachine opnieuw met dezelfde hostnaam als de bronmachine
  1. Installeer de agent en registreer u opnieuw bij dezelfde kluis en met dezelfde wachtwoordzin
  1. Start de MARS-client om de retentieduur te verlengen op basis van uw vereisten
- Uw herstelde machine, beveiligd met MARS, blijft back-ups maken.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Antivirus configureren voor de MARS-agent

U wordt aangeraden de volgende configuratie voor uw antivirussoftware te gebruiken om conflicten met de werking van de MARS-agent te voorkomen.

1. **Paduitsluitingen toevoegen:** sluit de volgende paden uit van realtime bewaking door de antivirussoftware om een verslechtering van de prestaties en mogelijke conflicten te voorkomen:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` en submappen
    1. **Scratch-map:** als de scratchmap zich niet op de standaardlocatie bevindt, voegt u die ook toe aan de uitsluitingen.  [Kijk hier voor stappen om](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-) de locatie van de scratchmap te bepalen.
1. **Binaire uitsluitingen toevoegen:** om degradatie van back-up- en consoleactiviteiten te voorkomen, sluit u processen voor de volgende binaire bestanden uit van realtime bewaking door de antivirussoftware:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Hoewel het uitsluiten van deze paden voldoende is voor de meeste antivirussoftware, kunnen sommige nog steeds de MARS Agent-bewerkingen verstoren. Als u onverwachte fouten ziet, verwijdert u de antivirussoftware tijdelijk en controleert u of het probleem is opgelost. Als dit het probleem verhelpt, neem dan contact op met de leverancier van uw antivirussoftware voor hulp bij de juiste configuratie van hun product.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de ondersteuningsmatrix voor de [MARS-agent](./backup-support-matrix-mars-agent.md)voor informatie over ondersteunde scenario's en beperkingen.
- Meer informatie over het [bewaargedrag voor back-upbeleid op aanvraag.](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)
- Zie Veelgestelde vragen over MARS-agent voor meer [veelgestelde vragen.](backup-azure-file-folder-backup-faq.yml)
