---
title: Back-ups van de MARS-agent beheren en controleren
description: Meer informatie over het beheren en bewaken van back-ups van agents van Microsoft Azure Recovery Services (MARS) met behulp van de Azure Backup-service.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 25f0c41b535f9403d0a7027687cc5261cd437275
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368593"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Back-ups van de agent voor Microsoft Azure Recovery Services (MARS) beheren met behulp van de Azure Backup-Service

In dit artikel wordt beschreven hoe u bestanden en mappen beheert waarvan een back-up is gemaakt met de Microsoft Azure Recovery Services-agent.

## <a name="modify-a-backup-policy"></a>Een back-upbeleid wijzigen

Wanneer u het back-upbeleid wijzigt, kunt u nieuwe items toevoegen, bestaande items verwijderen uit een back-up of bestanden uitsluiten van het maken van een back-up met behulp van uitsluitings instellingen.

- **Items toevoegen** gebruik deze optie alleen voor het toevoegen van nieuwe items om een back-up te maken. Als u bestaande items wilt verwijderen, gebruikt u de optie **items verwijderen** of **uitsluitings instellingen** .  
- **Items verwijderen** gebruik deze optie om items te verwijderen waarvan u een back-up wilt maken.
  - Gebruik **uitsluitings instellingen** voor het verwijderen van alle items binnen een volume in plaats van **items te verwijderen**.
  - Als alle selecties in een volume worden gewist, worden oude back-ups van de items bewaard op basis van de Bewaar instellingen op het moment van de laatste back-up, zonder aanpassings bereik.
  - Als u deze items opnieuw selecteert, worden de wijzigingen in de eerste back-up en nieuwe beleids regels niet toegepast op oude back-ups.
  - Als u de selectie van het hele volume uitschakelt, blijft de vorige back-up zonder enige bereik voor het wijzigen van het Bewaar beleid.
- **Instellingen voor uitsluiting** gebruik deze optie om specifieke items uit te sluiten waarvan u een back-up wilt maken.

### <a name="add-new-items-to-existing-policy"></a>Nieuwe items toevoegen aan het bestaande beleid

1. Selecteer in **acties** de optie **back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)

2. Selecteer in het tabblad **beleids item selecteren** **de optie back-upschema voor uw bestanden en mappen wijzigen** en selecteer **volgende**.

    ![Beleids items selecteren](./media/backup-azure-manage-mars/select-policy-items.png)

3. Selecteer **wijzigingen in back-upitems of tijden** **wijzigen of stoppen** in het tabblad planning maken en selecteer **volgende**.

    ![Back-up wijzigen of plannen](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Selecteer in het tabblad **items selecteren voor back-up** de optie **items toevoegen** om de items waarvan u een back-up wilt maken toe te voegen.

    ![Back-upitems wijzigen of plannen](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Selecteer in het venster **items selecteren** de optie bestanden of mappen die u wilt toevoegen en selecteer **OK**.

    ![De items selecteren](./media/backup-azure-manage-mars/select-item.png)

6. Voer de volgende stappen uit en selecteer **volt ooien** om de bewerking te volt ooien.

### <a name="add-exclusion-rules-to-existing-policy"></a>Uitsluitings regels toevoegen aan het bestaande beleid

U kunt uitsluitings regels toevoegen om bestanden en mappen over te slaan waarvan u geen back-up wilt maken. U kunt dit doen wanneer u een nieuw beleid definieert of een bestaand beleid wijzigt.

1. Selecteer in het deel venster acties de optie **back-up plannen**. Ga naar **Selecteer items om een back-up te maken** en selecteer **uitsluitings instellingen**.

    ![Uitsluitings instellingen](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Selecteer in **uitsluitings instellingen** de optie **uitsluiting toevoegen**.

    ![Uitsluiting toevoegen](./media/backup-azure-manage-mars/add-exclusion.png)

3. In **items selecteren** die u wilt uitsluiten, bladert u naar de bestanden en mappen en selecteert u de items die u wilt uitsluiten en selecteert u **OK**.

    ![Selecteer de items die u wilt uitsluiten](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Standaard worden alle **submappen** in de geselecteerde mappen uitgesloten. U kunt dit wijzigen door **Ja** of **Nee** te selecteren. U kunt de bestands typen die moeten worden uitgesloten, bewerken en opgeven, zoals hieronder wordt weer gegeven:

    ![Selecteer typen submappen](./media/backup-azure-manage-mars/subfolders-type.png)

5. Voer de volgende stappen uit en selecteer **volt ooien** om de bewerking te volt ooien.

### <a name="remove-items-from-existing-policy"></a>Items uit bestaand beleid verwijderen

1. Selecteer in het deel venster acties de optie **back-up plannen**. Ga naar **items selecteren waarvan u een back-up wilt maken**. Selecteer in de lijst de bestanden en mappen die u wilt verwijderen uit het back-upschema en selecteer **items verwijderen**.

    ![Selecteer de items die u wilt verwijderen](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Ga voorzichtig te werk wanneer u een volume volledig uit het beleid verwijdert.  Als u het opnieuw wilt toevoegen, wordt het beschouwd als een nieuw volume. Bij de volgende geplande back-up wordt een eerste back-up (volledige back-up) in plaats van incrementele back-up uitgevoerd. Als u later tijdelijk items moet verwijderen en toevoegen, is het raadzaam om **uitsluitings instellingen** te gebruiken in plaats van **items te verwijderen** om een incrementele back-up te garanderen in plaats van een volledige back-up.

2. Voer de volgende stappen uit en selecteer **volt ooien** om de bewerking te volt ooien.

## <a name="stop-protecting-files-and-folder-backup"></a>De beveiliging van bestanden en mappen stoppen

Er zijn twee manieren om het maken van back-ups van bestanden en mappen te stoppen:

- **Stop de beveiliging en behoud de back-upgegevens**.
  - Met deze optie worden alle toekomstige back-uptaken van beveiliging gestopt.
  - Azure Backup service blijft alle bestaande herstel punten behouden.  
  - U kunt de back-upgegevens voor niet-verlopen herstel punten herstellen.
  - Als u besluit de beveiliging te hervatten, kunt u de optie *back-upschema opnieuw inschakelen* gebruiken. Daarna blijven gegevens bewaard op basis van het nieuwe Bewaar beleid.
- **Stop de beveiliging en verwijder de back-upgegevens**.
  - Met deze optie worden alle toekomstige back-uptaken gestopt om uw gegevens te beschermen en alle herstel punten te verwijderen.
  - U ontvangt een waarschuwing voor het verwijderen van back-upgegevens met een bericht *dat uw gegevens voor dit back-upitem zijn verwijderd. Deze gegevens zijn tijdelijk beschikbaar gedurende 14 dagen, waarna deze permanent worden verwijderd* en aanbevolen actie *het back-upitem opnieuw beveiligen binnen 14 dagen om uw gegevens te herstellen.*
  - Als u de beveiliging wilt hervatten, moet u binnen 14 dagen opnieuw beveiligen vanaf de verwijderings bewerking.

### <a name="stop-protection-and-retain-backup-data"></a>Beveiliging stoppen en back-upgegevens behouden

1. Open de MARS-beheer console, ga naar het **deel venster acties** en **Selecteer back-up plannen**.

    ![Back-up plannen selecteren](./media/backup-azure-manage-mars/mars-actions.png)
1. Selecteer op de pagina **beleids item selecteren** de optie **een back-upschema voor uw bestanden en mappen wijzigen** en selecteer **volgende**.

    ![Selecteer een beleids item](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Op de pagina **een geplande back-up wijzigen of stoppen** selecteert **u stoppen met het gebruik van dit back-upschema, maar behoud de opgeslagen back-ups totdat een schema opnieuw wordt geactiveerd**. Selecteer vervolgens **Volgende**.

    ![Een geplande back-up stoppen.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Controleer de gegevens in **geplande back-ups onderbreken** en selecteer **volt ooien**.

    ![Een geplande back-up onderbreken.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Controleer in voortgang van het maken van de **back-up** de optie voor het plannen van back-ups met de status geslaagd en selecteer **sluiten** om te volt ooien.

### <a name="stop-protection-and-delete-backup-data"></a>Beveiliging stoppen en back-upgegevens verwijderen

1. Open de MARS-beheer console, ga naar het deel venster **acties** en selecteer **back-up plannen**.
2. Op de pagina **een geplande back-up wijzigen of stoppen** selecteert **u stoppen met het gebruik van dit back-upschema en alle opgeslagen back-ups verwijderen**. Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer **volt ooien** op de pagina **een geplande back-up stoppen** .

    ![Een geplande back-up stoppen en volt ooien selecteren](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd om een beveiligings pincode (persoonlijk identificatie nummer) in te voeren, die u hand matig moet genereren. Als u dit wilt doen, meldt u zich eerst aan bij de Azure Portal.
5. Ga naar **Recovery Services** eigenschappen van de kluis  >  **instellingen**  >  .
6. Onder **BEVEILIGINGS pincode** selecteert u **genereren**. Deze pincode kopiëren. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheer console en selecteer **OK**.

    ![Een beveiligings pincode genereren.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de pagina **voortgang van back-up wijzigen** wordt het volgende bericht weer gegeven: *verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd.*  

    ![Voortgang van back-up wijzigen](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen uit in de portal.

## <a name="re-enable-protection"></a>Beveiliging opnieuw inschakelen

Als u de beveiliging hebt gestopt terwijl u de gegevens behoudt en hebt besloten om de beveiliging te hervatten, kunt u het back-upschema opnieuw inschakelen met behulp van het back-upbeleid wijzigen.

1. Selecteer bij **acties** de optie **back-up plannen**.
1. Selecteer **back-upschema opnieuw inschakelen. U kunt ook back-upitems of tijden wijzigen** en **volgende** selecteren.<br>

    ![Back-upschema opnieuw inschakelen](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Selecteer **volgende** in **items selecteren waarvan u een back-up wilt maken**.

    ![Items selecteren waarvan een back-up moet worden gemaakt](./media/backup-azure-manage-mars/re-enable-next.png)
1. In **back-upschema opgeven** geeft u het back-upschema op en selecteert u **volgende**.
1. Geef in **Bewaar beleid selecteren** de Bewaar duur op en selecteer **volgende**.
1. Controleer ten slotte de details van het beleid in het **bevestigings** scherm en selecteer **volt ooien**.

## <a name="re-generate-passphrase"></a>Wachtwoordzin opnieuw genereren

Een wachtwoordzin wordt gebruikt voor het versleutelen en ontsleutelen van gegevens tijdens het maken van een back-up of het herstellen van uw on-premises of lokale computer met de MARS-agent naar of van Azure. Als u de wachtwoordzin kwijtraakt of verg eten bent, kunt u de wachtwoordzin opnieuw genereren (mits uw computer nog steeds is geregistreerd bij de Recovery Services kluis en de back-up is geconfigureerd) door de volgende stappen te volgen:

1. Ga in de Mars agent-console naar het **deel venster acties** en  >  **Wijzig de eigenschappen** >. Ga vervolgens naar het **tabblad versleuteling**.<br>
1. Selecteer selectie vakje **wachtwoordzin wijzigen** .<br>
1. Voer een nieuwe wachtwoordzin in of selecteer **wachtwoordzin genereren**.
1. Selecteer **Bladeren** om de nieuwe wachtwoordzin op te slaan.

    ![Wachtwoordzin genereren.](./media/backup-azure-manage-mars/passphrase.png)

1. Selecteer **OK** om wijzigingen toe te passen.  Als de [beveiligings functie](./backup-azure-security-feature.md#enable-security-features) is ingeschakeld op de Azure portal voor de Recovery Services kluis, wordt u gevraagd om de beveiligings pincode in te voeren. Volg de stappen in dit [artikel](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)om de pincode te ontvangen.<br>
1. Plak de beveiligings pincode vanuit de portal en selecteer **OK** om de wijzigingen toe te passen.<br>

    ![De beveiligings pincode plakken](./media/backup-azure-manage-mars/passphrase2.png)
1. Zorg ervoor dat de wachtwoordzin veilig wordt opgeslagen op een andere locatie (anders dan de bron machine), bij voor keur in het Azure Key Vault. Houd alle wachtwoordzin bij als er een back-up van meerdere machines met de MARS-agents wordt gemaakt.

## <a name="managing-backup-data-for-unavailable-machines"></a>Back-upgegevens beheren voor niet-beschik bare computers

In deze sectie wordt een scenario beschreven waarin uw bron machine die is beveiligd met MARS, niet meer beschikbaar is, omdat deze is verwijderd, beschadigd, geïnfecteerd met malware/Ransomware of uit bedrijf genomen.

Voor deze computers zorgt de Azure Backup-service ervoor dat het meest recente herstel punt niet verloopt (dat wil zeggen, niet wordt weggehaald) volgens de Bewaar regels die zijn opgegeven in het back-upbeleid. Daarom kunt u de computer veilig herstellen.  Houd rekening met de volgende scenario's die u kunt uitvoeren op de gegevens waarvan een back-up is gemaakt:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Scenario 1: de bron machine is niet beschikbaar en u hoeft geen back-upgegevens meer te bewaren

- U kunt de gegevens waarvan een back-up is gemaakt, verwijderen uit de Azure Portal met behulp van de stappen die in [dit artikel](backup-azure-delete-vault.md#delete-protected-items-on-premises)worden beschreven.

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Scenario 2: de bron machine is niet beschikbaar en u moet de back-upgegevens behouden

Het beheer van het back-upbeleid voor MARS wordt uitgevoerd via de MARS-console en niet via de portal. Als u de Bewaar instellingen voor bestaande herstel punten wilt uitbreiden voordat ze verlopen, moet u de machine herstellen, de MARS-console installeren en het beleid uitbreiden.

- Voer de volgende stappen uit om de machine te herstellen:
  1. [De VM herstellen naar een andere doel computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. De doel computer opnieuw maken met dezelfde hostnaam als de bron machine
  1. De agent installeren en opnieuw registreren bij dezelfde kluis en met dezelfde wachtwoordzin
  1. Start de MARS-client om de Bewaar periode uit te breiden volgens uw vereisten
- De zojuist herstelde computer die wordt beveiligd met MARS, blijft back-ups maken.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Anti virus configureren voor de MARS-agent

We raden u aan de volgende configuratie voor uw antivirus software te configureren om conflicten te voor komen met de werking van de MARS-agent.

1. **Uitsluitingen van paden toevoegen**: als u de prestaties en mogelijke conflicten wilt voor komen, moet u de volgende paden uitsluiten van realtime-bewaking door de antivirus software:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` en submappen
    1. **Scratch map**: als de Scratch map zich niet op de standaard locatie bevindt, voegt u die ook toe aan de uitsluitingen.  [Zie hier voor de stappen](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible) om de locatie van de Scratch map te bepalen.
1. **Binaire uitsluitingen toevoegen**: als u wilt voor komen dat back-up-en console-activiteiten worden verminderd, moet u processen uitsluiten voor de volgende binaire bestanden in realtime-bewaking door de antivirus software:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>Ondanks dat deze paden voldoende zijn voor de meeste antivirus software, kunnen er nog steeds problemen met de MARS-agent optreden. Als er onverwachte fouten optreden, verwijdert u de antivirus software tijdelijk en controleert u of het probleem zich blijft voordoen. Als het probleem hiermee is opgelost, neemt u contact op met de leverancier van uw antivirus software voor hulp met de juiste configuratie van het product.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [ondersteunings matrix voor de Mars-agent](./backup-support-matrix-mars-agent.md)voor meer informatie over ondersteunde scenario's en beperkingen.
- Meer informatie over het [Bewaar gedrag van back-upbeleid op aanvraag](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- Zie [Veelgestelde vragen over de Mars-agent](backup-azure-file-folder-backup-faq.md)voor meer informatie.
