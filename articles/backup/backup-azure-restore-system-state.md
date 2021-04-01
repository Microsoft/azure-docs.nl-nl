---
title: Systeem status herstellen naar een Windows-Server
description: Stapsgewijze uitleg voor het herstellen van de Windows Server-systeem status vanuit een back-up in Azure.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 4ef23d6ff16c263e310304cc240c2090751640b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97008466"
---
# <a name="restore-system-state-to-windows-server"></a>Systeem status herstellen naar Windows Server

In dit artikel wordt uitgelegd hoe u back-ups van Windows Server-systeem status kunt herstellen vanuit een Azure Recovery Services kluis. Als u de systeem status wilt herstellen, moet u een systeem status back-up hebben (gemaakt met behulp van de instructies in een back-up van de [systeem status](backup-azure-system-state.md#back-up-windows-server-system-state)en ervoor zorgen dat u de [meest recente versie van de Microsoft Azure Recovery Services-agent (Mars)](https://aka.ms/azurebackup_agent)hebt geïnstalleerd. Het herstellen van de Windows Server-systeem status gegevens van een Azure Recovery Services kluis is een proces dat uit twee stappen bestaat:

1. De systeem status herstellen als bestanden van Azure Backup. Bij het herstellen van de systeem status als bestanden van Azure Backup, kunt u het volgende doen:
   * Herstel de systeem status op dezelfde server als de back-ups zijn gemaakt of
   * Herstel het systeem status bestand naar een andere server.

2. Pas de herstelde systeem status bestanden toe op een Windows-Server met behulp van het hulp programma Windows Server Back-up.

## <a name="recover-system-state-files-to-the-same-server"></a>Systeemstatusbestanden herstellen op dezelfde server

In de volgende stappen wordt uitgelegd hoe u de configuratie van uw Windows-Server terugdraait naar een eerdere status. Het kan erg waardevol zijn om uw server configuratie terug te brengen naar een bekende, stabiele status. Met de volgende stappen herstelt u de systeem status van de server van een Recovery Services kluis.

1. Open de **Microsoft Azure Backup**-module. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Microsoft Azure backup**.

    De bureau blad-app moet worden weer gegeven in de zoek resultaten.

2. Selecteer **gegevens herstellen** om de wizard te starten.

    ![Gegevens herstellen](./media/backup-azure-restore-windows-server/recover.png)

3. Selecteer in het deel venster **aan** de slag de optie **deze server ( `<server name>` )** en selecteer **volgende** om de gegevens op dezelfde server of computer te herstellen.

    ![Selecteer deze optie voor de server om de gegevens op dezelfde computer te herstellen](./media/backup-azure-restore-system-state/samemachine.png)

4. Kies in het deel venster **herstel modus selecteren** de optie **systeem status** en selecteer vervolgens **volgende**.

    ![Door bestanden bladeren](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. Selecteer een herstel punt in de agenda in het deel venster **volume en datum selecteren** .

    U kunt herstellen vanaf een herstel punt in de tijd. **Vetgedrukte** datums geven de beschik baarheid van ten minste één herstel punt aan. Wanneer u een datum selecteert en er meerdere herstel punten beschikbaar zijn, kiest u het specifieke herstel punt in het vervolg keuzemenu **tijd** .

    ![Volume en datum](./media/backup-azure-restore-system-state/select-date.png)

6. Wanneer u het herstel punt hebt gekozen dat u wilt herstellen, selecteert u **volgende**.

    Azure Backup koppelt het lokale herstel punt en gebruikt dit als een herstel volume.

7. Geef in het volgende deel venster de bestemming op voor de herstelde systeem status bestanden. Selecteer vervolgens **Bladeren** om Windows Verkenner te openen en de gewenste bestanden en mappen te vinden. Met deze optie kunt **u kopieën maken, zodat u met beide versies** kopieën van afzonderlijke bestanden in een bestaand systeem status bestands archief maakt, in plaats van de kopie van het hele archief van de systeem status te maken.

    ![Herstel opties](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Controleer de details van herstel in het **bevestigings** venster en selecteer **herstellen**.

   ![Selecteer herstellen om de herstel actie te bevestigen](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopieer de *WindowsImageBackup* -map in de herstel bestemming naar een niet-kritiek volume van de-server. Normaal gesp roken is het volume van het Windows-besturings systeem het essentiële volume.

10. Nadat het herstel is voltooid, volgt u de stappen in de sectie, [herstelde systeem status Toep assen op een Windows-Server](#apply-restored-system-state-on-a-windows-server)om het systeem status herstel proces te volt ooien.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Systeem status bestanden herstellen naar een alternatieve server

Als uw Windows-Server beschadigd of niet toegankelijk is en u deze wilt herstellen naar een stabiele status door de systeem status van Windows Server te herstellen, kunt u de systeem status van de beschadigde server herstellen vanaf een andere server. Gebruik de volgende stappen om de systeem status te herstellen op een afzonderlijke server.  

De terminologie die in deze stappen wordt gebruikt, omvat:

* *Bron machine* : de oorspronkelijke machine waarvan de back-up is gemaakt en die momenteel niet beschikbaar is.
* *Doel computer* : de computer waarop de gegevens worden hersteld.
* Voor beeld van de *kluis* : de Recovery Services kluis waarmee de *bron computer* en de *doel computer* zijn geregistreerd.

> [!NOTE]
> Back-ups die zijn gemaakt van de ene machine kunnen niet worden hersteld naar een computer waarop een eerdere versie van het besturings systeem wordt uitgevoerd. Back-ups die zijn gemaakt van een Windows Server 2016-computer, kunnen bijvoorbeeld niet worden hersteld naar Windows Server 2012 R2. De inverse is echter mogelijk. U kunt back-ups van Windows Server 2012 R2 gebruiken om Windows Server 2016 te herstellen.
>

1. Open de module **Microsoft Azure backup** op de *doel computer*.
2. Zorg ervoor dat de *doel computer* en de *bron machine* zijn geregistreerd bij dezelfde Recovery Services kluis.
3. Selecteer **gegevens herstellen** om de werk stroom te initiëren.
4. **Een andere server** selecteren

    ![Andere server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Geef het kluis referentie bestand op dat overeenkomt met de voor *beeld-kluis*. Als het kluis referentie bestand ongeldig is (of is verlopen), downloadt u een nieuw kluis referentie bestand van de voor *beeld-kluis* in de Azure Portal. Zodra het kluis referentie bestand is ingevuld, wordt de Recovery Services kluis die aan het kluis referentie bestand is gekoppeld weer gegeven.

6. Selecteer in het deel venster back-upserver selecteren de *bron machine* in de lijst met weer gegeven machines.
7. Kies in het deel venster herstel modus selecteren de optie **systeem status** en selecteer **volgende**.

    ![Zoeken](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. Selecteer een herstel punt in de agenda in het deel venster **volume en datum selecteren** . U kunt herstellen vanaf een herstel punt in de tijd. **Vetgedrukte** datums geven de beschik baarheid van ten minste één herstel punt aan. Wanneer u een datum selecteert en er meerdere herstel punten beschikbaar zijn, kiest u het specifieke herstel punt in het vervolg keuzemenu **tijd** .

    ![Items zoeken](./media/backup-azure-restore-system-state/select-date.png)

9. Wanneer u het herstel punt hebt gekozen dat u wilt herstellen, selecteert u **volgende**.

10. Geef in het deel venster **systeem status herstel modus selecteren** het doel op waar u de systeem status bestanden wilt herstellen en selecteer vervolgens **volgende**.

    ![Versleuteling](./media/backup-azure-restore-system-state/recover-as-files.png)

    Met deze optie kunt **u kopieën maken, zodat u met beide versies** kopieën van afzonderlijke bestanden in een bestaand systeem status bestands archief maakt, in plaats van de kopie van het hele archief van de systeem status te maken.

11. Controleer de details van herstel in het bevestigings venster en selecteer **herstellen**.

    ![Selecteer de knop herstellen om het herstel proces te bevestigen](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopieer de *WindowsImageBackup* -map naar een niet-kritiek volume van de server (bijvoorbeeld D: \) . Normaal gesp roken is het volume van het Windows-besturings systeem het essentiële volume.

13. Om het herstel proces te volt ooien, gebruikt u de volgende sectie om [de herstelde systeem status bestanden toe te passen op een Windows-Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Een herstelde systeemstatus toepassen op een Windows-server

Nadat u de systeem status hebt hersteld als bestanden met behulp van Azure Recovery Services agent, gebruikt u het hulp programma Windows Server Back-up om de herstelde systeem status toe te passen op Windows Server. Het hulp programma Windows Server Back-up is al beschikbaar op de server. In de volgende stappen wordt uitgelegd hoe de herstelde systeem status moet worden toegepast.

1. Open de module Windows Server Back-up. Als u niet weet waar de module is geïnstalleerd, zoekt u op de computer of server naar **Windows Server back-up**.

    De bureau blad-app wordt weer gegeven in de zoek resultaten. Als het niet wordt weer gegeven, of als u fouten tegen komt wanneer u de toepassing opent, moet u de **Windows Server back-up-functies** en afhankelijke onderdelen daaronder installeren die beschikbaar zijn in de **wizard functies toevoegen** in **Serverbeheer**.

1. Selecteer in de module de optie **lokale back-up**.

    ![Lokale back-up selecteren om van daaruit te herstellen](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

1. Selecteer op de lokale back-upconsole in het **deel venster acties** de optie **herstellen** om de wizard herstellen te openen.

1. Selecteer de optie, **een back-up die is opgeslagen op een andere locatie** en selecteer **volgende**.

   ![kiezen om naar een andere server te herstellen](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

1. Wanneer u het locatie type opgeeft, selecteert u **externe gedeelde map** als uw systeem status back-up is hersteld naar een andere server. Als uw systeem status lokaal is hersteld, selecteert u **lokale stations**.

    ![selecteren of u wilt herstellen van de lokale server of een andere](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

1. Voer het pad naar de *WindowsImageBackup* -map in of kies het lokale station dat deze map bevat (bijvoorbeeld D:\WindowsImageBackup), hersteld als onderdeel van het herstel van de systeem status bestanden met behulp van Azure Recovery Services agent en selecteer **volgende**.

    ![pad naar het gedeelde bestand](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

1. Selecteer de systeem status versie die u wilt herstellen en selecteer **volgende**.

1. Selecteer in het deel venster herstel type selecteren de optie **systeem status** en selecteer **volgende**.

1. Voor de locatie van de systeem status herstel selecteert u **oorspronkelijke locatie** en selecteert u **volgende**.

    Als u een domein controller herstelt, ziet u de volgende extra optie:

    ![Locatie voor herstel van de systeem status](./media/backup-azure-restore-system-state/location-for-system-state-recovery.png)

    >[!NOTE]
    >Selecteer alleen ' bindend terugzetten van Active Directory bestanden ' als u een bindende terugzet bewerking van alle Active Directory gegevens wilt uitvoeren.

1. Controleer de details van de bevestiging, Controleer de instellingen voor opnieuw opstarten, selecteer **herstellen** om de herstelde systeem status bestanden toe te passen.

    ![Start de herstel systeem status bestanden](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

    >[!NOTE]
    >Selecteer de optie de **server automatisch opnieuw opstarten** als u de herstel bewerking in de modus Active Directory terugzetten uitvoert.

1. Nadat u een herstel bewerking hebt voltooid, moet u de server opnieuw opstarten in de normale modus. Open een opdracht prompt en typ het volgende: `bcdedit /deletevalue safeboot`
1. Start de server opnieuw op.

## <a name="special-considerations-for-system-state-recovery-on-a-domain-controller"></a>Speciale overwegingen voor herstel van de systeem status op een domein controller

Back-up van de systeem status bevat Active Directory gegevens. Gebruik de volgende stappen om Active Directory-domein-service (AD DS) te herstellen van de huidige status naar een eerdere status. Dit type herstel bewerking kan in twee scenario's worden uitgevoerd:

* Alle Active Directory gegevens worden teruggezet wanneer er zich geen werkende domein controllers in het forest bevinden
* Een deel van de Active Directory gegevens herstellen wanneer deze objecten zijn verwijderd of beschadigd

Dit artikel heeft alleen betrekking op het eerste scenario, waarmee u een nonauthorative terugzet van AD DS en een bindende terugzet bewerking van de map SYSVOL.  Als u het tweede scenario wilt uitvoeren (waarbij de domein controllers nog steeds functioneel zijn, maar u bepaalde AD-objecten moet herstellen), raadpleegt u [deze instructies](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).

1. Volg de onderstaande stappen om de [systeem status bestanden te herstellen op een andere server](#recover-system-state-files-to-an-alternate-server).
1. Gebruik de volgende opdrachten om de server opnieuw op te starten in de *modus Active Directory-Services herstellen*. In een opdracht prompt met verhoogde bevoegdheid:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

1. Als u Active Directory wilt herstellen als onderdeel van een systeem status herstel, kunt u een van de twee volgende methoden kiezen:

    * Volg de bovenstaande instructies om de [herstelde systeem status op een Windows-Server](#apply-restored-system-state-on-a-windows-server) met het hulp programma Windows Server back-up toe te passen.

        >[!NOTE]
        >Als u alle Active Directory gegevens herstelt (en er zich geen werkende domein controllers in het forest bevinden), selecteert u in stap 9 hierboven **een bindende terugzet bewerking van Active Directory-bestanden uitvoeren**.

    * Gebruik het hulp programma [Wbadmin](/windows-server/administration/windows-commands/wbadmin-start-systemstaterecovery) om de herstel bewerking uit te voeren vanaf de opdracht regel.

        U hebt de versie-id nodig van de back-up die u wilt gebruiken. U kunt een lijst met versie-id's ophalen door de volgende opdracht uit te voeren:

        ```cmd
        wbadmin get versions -backuptarget <servername\sharename>
        ```

        Vervolgens gebruikt u die versie-id om het herstel uit te voeren.

        Als u bijvoorbeeld een nonauthorative- [herstel van AD DS en een bindende terugzet bewerking van de map SYSVOL](/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) wilt uitvoeren met behulp van de back-up van 04/30/2020 om 9:00 uur, die is opgeslagen op de gedeelde bron `\\servername\share` voor `server01` , typt u:

        ```cmd
        wbadmin start systemstaterecovery -version:04/30/2020-09:00 -backupTarget:\\servername\share -machine:server01 -authsysvol
        ```

1. Wanneer u de herstel bewerking hebt voltooid, moet u de server opnieuw opstarten in de normale modus. Open een opdracht prompt en typ het volgende: `bcdedit /deletevalue safeboot`
1. Start de server opnieuw op.

Zie [back up and Restore Active Directory Domain controllers](active-directory-backup-restore.md)voor meer informatie.

## <a name="troubleshoot-failed-system-state-restore"></a>Mislukt herstel van systeemstatus oplossen

Als het vorige proces van het Toep assen van de systeem status niet is voltooid, gebruikt u de Windows herstel omgeving (Win RE) om uw Windows-Server te herstellen. In de volgende stappen wordt uitgelegd hoe u kunt herstellen met Win RE. Gebruik deze optie alleen als Windows Server niet normaal wordt opgestart na een systeem status herstel. Met het volgende proces worden niet-systeem gegevens gewist, wees voorzichtig.

1. Start uw Windows-Server op in de Windows herstel omgeving (Win RE).

2. Selecteer problemen oplossen in de drie beschik bare opties.

    ![Problemen met selecteren](./media/backup-azure-restore-system-state/winre-1.png)

3. In het scherm **Geavanceerde opties** selecteert u **opdracht prompt** en geeft u de gebruikers naam en het wacht woord voor de server beheerder op.

   ![Opdracht prompt selecteren](./media/backup-azure-restore-system-state/winre-2.png)

4. Geef de gebruikers naam en het wacht woord van de server beheerder op.

    ![Wachtwoord invoeren](./media/backup-azure-restore-system-state/winre-3.png)

5. Wanneer u de opdracht prompt in de beheerders modus opent, voert u de volgende opdracht uit om de back-upversie van de systeem status te verkrijgen.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Back-upversies van systeem status ophalen](./media/backup-azure-restore-system-state/winre-4.png)

6. Voer de volgende opdracht uit om alle volumes te verkrijgen die beschikbaar zijn in de back-up.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Alle beschik bare volumes ophalen](./media/backup-azure-restore-system-state/winre-5.png)

7. Met de volgende opdracht worden alle volumes hersteld die deel uitmaken van de systeem status back-up. Houd er rekening mee dat met deze stap alleen de essentiële volumes die deel uitmaken van de systeem status, worden hersteld. Alle niet-systeem gegevens worden gewist.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Alle volumes herstellen](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Volgende stappen

* Nu u uw bestanden en mappen hebt hersteld, kunt u [uw back-ups beheren](backup-azure-manage-windows-server.md).
