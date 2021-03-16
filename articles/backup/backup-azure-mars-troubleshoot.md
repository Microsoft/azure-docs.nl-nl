---
title: Problemen met de Azure Backup-Agent oplossen
description: In dit artikel vindt u informatie over het oplossen van problemen met de installatie en registratie van de Azure Backup-Agent.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 3203d5604f1bd5db9cf579af01b2ae6f34032d89
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467609"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Problemen met de Microsoft Azure Recovery Services-agent (MARS) oplossen

In dit artikel wordt beschreven hoe u fouten kunt oplossen die tijdens de configuratie, registratie, back-up en herstellen kunnen worden weer geven.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

U wordt aangeraden het volgende te controleren voordat u begint met het oplossen van problemen met micro soft de Azure Recovery Services-agent (MARS):

- [Zorg ervoor dat de Mars-agent up-to-date is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Zorg ervoor dat u verbinding hebt met het netwerk tussen de Mars-agent en Azure](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Zorg ervoor dat MARS wordt uitgevoerd (in de service console). Als dat het geval is, start u de bewerking opnieuw en probeert u het opnieuw.
- [Zorg ervoor dat 5% tot 10% beschik bare volume ruimte beschikbaar is op de locatie van de Scratch map](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Controleer of er geen ander proces of antivirussoftware conflicten veroorzaakt met Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Zie [back-uptaken voltooid met waarschuwing](#backup-jobs-completed-with-warning) als de back-uptaak is voltooid met waarschuwingen
- Zie [back-ups worden niet volgens schema uitgevoerd](#backups-dont-run-according-to-schedule)als de geplande back-up mislukt, maar hand matige back-up werkt.
- Zorg ervoor dat uw besturings systeem over de meest recente updates beschikt.
- [Zorg ervoor dat niet-ondersteunde stations en bestanden met niet-ondersteunde kenmerken worden uitgesloten van een back-up](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Zorg ervoor dat de klok op het beveiligde systeem is geconfigureerd voor de juiste tijd zone.
- [Zorg ervoor dat .NET Framework 4.5.2 of hoger is geïnstalleerd op de server](https://www.microsoft.com/download/details.aspx?id=30653).
- Als u de server opnieuw wilt registreren bij een kluis:
  - Zorg ervoor dat de agent wordt verwijderd van de server en dat deze wordt gewist uit de portal.
  - Gebruik dezelfde wachtwoordzin die voor het eerst is gebruikt om de server te registreren.
- Zorg ervoor dat Azure PowerShell 3.7.0 is geïnstalleerd op zowel de bron-als de Kopieer computer voordat u de back-up start, voor offline back-ups.
- Als de back-upagent wordt uitgevoerd op een virtuele machine van Azure, raadpleegt u [dit artikel](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Er zijn ongeldige kluisreferenties ingevoerd

**Fout bericht: er** zijn ongeldige kluis referenties gegeven. Het bestand is beschadigd of de nieuwste referenties zijn niet gekoppeld aan de Recovery Service. (ID: 34513)

| Oorzaak | Aanbevolen acties |
| ---     | ---    |
| **De kluis referenties zijn niet geldig** <br/> <br/> Kluis referentie bestanden zijn mogelijk beschadigd, zijn mogelijk verlopen of hebben een andere bestands extensie dan *. vaultCredentials*. (Het is bijvoorbeeld mogelijk dat ze meer dan tien dagen vóór de registratie tijd hebben gedownload.)| [Down load nieuwe referenties](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) van de Recovery Services kluis op de Azure Portal. Voer vervolgens de volgende stappen uit: <ul><li> Als u MARS al hebt geïnstalleerd en geregistreerd, opent u de MMC-console Microsoft Azure Backup Agent. Selecteer vervolgens **server registreren** in het deel venster **acties** om de registratie met de nieuwe referenties te volt ooien. <br/> <li> Als de nieuwe installatie mislukt, probeert u opnieuw te installeren met de nieuwe referenties.</ul> **Opmerking**: als er meerdere kluis referentie bestanden zijn gedownload, is alleen het meest recente bestand geldig voor de komende 10 dagen. U wordt aangeraden een nieuw kluis referentie bestand te downloaden.
| **De registratie van Proxy Server/firewall is geblokkeerd** <br/>of <br/>**Geen Internet verbinding** <br/><br/> Als uw computer of proxy server beperkte internet connectiviteit heeft en u geen toegang hebt tot de benodigde Url's, mislukt de registratie.| Voer de volgende stappen uit:<br/> <ul><li> Werk samen met uw IT-team om te controleren of het systeem verbinding heeft met internet.<li> Als u geen proxy server hebt, moet u ervoor zorgen dat de proxy optie niet is geselecteerd bij het registreren van de agent. [Controleer de proxy-instellingen](#verifying-proxy-settings-for-windows).<li> Als u een firewall/proxy server hebt, moet u samen werken met uw netwerk team om ervoor te zorgen dat deze Url's en IP-adressen toegang hebben:<br/> <br> **URL's**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**IP-adressen**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Probeer de registratie opnieuw uit te voeren nadat u de voor gaande stappen voor probleem oplossing hebt door lopen.<br></br> Als uw verbinding via Azure ExpressRoute is, controleert u of de instellingen zijn geconfigureerd zoals beschreven in [ondersteuning voor Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Registratie wordt geblokkeerd door antivirus software** | Als er antivirus software op de server is geïnstalleerd, voegt u de benodigde uitsluitings regels toe aan de antivirus scan voor deze bestanden en mappen: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> De map Scratch. De standaard locatie is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> De bin-map in C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Extra aanbevelingen

- Ga naar C:/Windows/Temp en controleer of er meer dan 60.000 of 65.000 bestanden met de extensie. tmp zijn. Als dat het geval is, verwijdert u deze bestanden.
- Zorg ervoor dat de datum en tijd van de computer overeenkomen met de lokale tijd zone.
- Zorg ervoor dat [deze sites](install-mars-agent.md#verify-internet-access) worden toegevoegd aan uw vertrouwde sites in Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Proxy-instellingen voor Windows controleren

1. Down load PsExec van de [Sysinternals](/sysinternals/downloads/psexec) -pagina.
1. Voer `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` uit vanaf een opdracht prompt met verhoogde bevoegdheid.

   Met deze opdracht wordt Internet Explorer geopend.
1. Ga naar **hulpprogram ma's**  >  **Internet opties**  >  **verbindingen**  >  **LAN-instellingen**.
1. Controleer de proxy-instellingen voor het systeem account.
1. Als er geen proxy is geconfigureerd en er proxy Details worden opgegeven, verwijdert u de details.
1. Als er een proxy is geconfigureerd en de proxy Details onjuist zijn, controleert u of de **Proxy-IP** en **poort** gegevens juist zijn.
1. Sluit Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Kan het kluis referentie bestand niet downloaden

| Fout   | Aanbevolen acties |
| ---     | ---    |
|Kan het kluis referentie bestand niet downloaden. (ID: 403) | <ul><li> Down load de kluis referenties met behulp van een andere browser of voer de volgende stappen uit: <ul><li> Start Internet Explorer. Selecteer F12. </li><li> Ga naar het tabblad **netwerk** en wis de cache en cookies. </li> <li> Vernieuw de pagina.<br></li></ul> <li> Controleer of het abonnement is uitgeschakeld/verlopen.<br></li> <li> Controleer of een firewall regel de down load blokkeert. <br></li> <li> Zorg ervoor dat u geen limiet hebt bereikt voor de kluis (50 machines per kluis).<br></li>  <li> Zorg ervoor dat de gebruiker beschikt over de Azure Backup machtigingen die nodig zijn om de kluis referenties te downloaden en een server bij de kluis te registreren. Zie [op rollen gebaseerd toegangs beheer van Azure gebruiken om Azure backup herstel punten te beheren](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>De Microsoft Azure Recovery Services-agent kan geen verbinding maken met Microsoft Azure Backup

| Fout  | Mogelijke oorzaak | Aanbevolen acties |
| ---     | ---     | ---    |
| <br /><ul><li>De Microsoft Azure Recovery-Service agent kan geen verbinding maken met Microsoft Azure Backup. (ID: 100050) Controleer uw netwerk instellingen en zorg ervoor dat u verbinding kunt maken met internet.<li>(407) proxy-verificatie vereist. |De verbinding wordt geblokkeerd door een proxy. |  <ul><li>Ga in Internet Explorer naar **extra**  >  **Internet opties**  >  **beveiliging**  >  **Internet**. Selecteer **aangepast niveau** en schuif omlaag naar de sectie voor het **downloaden van bestanden** . Selecteer **Inschakelen**.<p>Mogelijk moet u ook [url's en IP-adressen](install-mars-agent.md#verify-internet-access) toevoegen aan uw vertrouwde sites in Internet Explorer.<li>Wijzig de instellingen voor het gebruik van een proxy server. Geef vervolgens de proxyserver gegevens op.<li> Als uw computer beperkte internet toegang heeft, moet u ervoor zorgen dat de firewall-instellingen op de computer of de proxy deze [url's en IP-adressen](install-mars-agent.md#verify-internet-access)toestaan. <li>Als er antivirus software op de server is geïnstalleerd, sluit u deze bestanden uit via de antivirus scan: <ul><li>CBEngine.exe (in plaats van dpmra.exe).<li>CSC.exe (gerelateerd aan .NET Framework). Er is een CSC.exe voor elke .NET Framework versie die op de server is geïnstalleerd. Sluit CSC.exe-bestanden uit voor alle versies van .NET Framework op de betreffende server. <li>De Scratch-map of cache locatie. <br>De standaard locatie voor de Scratch-map of het pad naar de cache is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>De bin-map in C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Het opgegeven kluis referentie bestand kan niet worden gebruikt omdat het niet wordt gedownload vanuit de kluis die is gekoppeld aan deze server

| Fout  | Mogelijke oorzaak | Aanbevolen acties |
| ---     | ---     | ---    |
| Het opgegeven kluis referentie bestand kan niet worden gebruikt omdat het niet wordt gedownload vanuit de kluis die is gekoppeld aan deze server. (ID: 100110) Geef de juiste kluis referenties op. | Het kluis referentie bestand is afkomstig uit een andere kluis dan die waarop deze server al is geregistreerd. | Zorg ervoor dat de doel computer en de bron machine zijn geregistreerd bij dezelfde Recovery Services kluis. Als de doel server al is geregistreerd bij een andere kluis, gebruikt u de optie **server registreren** om u te registreren bij de juiste kluis.  

## <a name="backup-jobs-completed-with-warning"></a>Back-uptaken voltooid met waarschuwing

- Wanneer de MARS-agent tijdens het maken van de back-up bestanden en mappen doorloopt, kunnen er diverse omstandigheden optreden die ervoor kunnen zorgen dat de back-up is gemarkeerd als voltooid met waarschuwingen. Tijdens deze omstandigheden wordt een taak weer gegeven als voltooid met waarschuwingen. Dat is prima, maar dit betekent dat er geen back-up kan worden gemaakt van ten minste één bestand. De taak heeft dat bestand dus overgeslagen, maar er is een back-up gemaakt van alle andere bestanden in de gegevens bron.

  ![De back-uptaak is voltooid met waarschuwingen](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Voor waarden die ervoor kunnen zorgen dat de back-ups bestanden overs Laan zijn:
  - Niet-ondersteunde bestands kenmerken (bijvoorbeeld: in een OneDrive-map, gecomprimeerde stroom, reparsepunten). Raadpleeg de [ondersteunings matrix](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)voor de volledige lijst.
  - Problemen met het bestands systeem
  - Een ander proces zorgt ervoor dat de toegang van de MARS-agent tot de bestanden kan verhinderen (bijvoorbeeld door antivirus software op bestanden te houden)
  - Bestanden die zijn vergrendeld door een toepassing  

- De back-upservice markeert deze bestanden als mislukt in het logboek bestand, met de volgende naam Conventie: *LastBackupFailedFilesxxxx.txt* in de map *C:\Program Files\Microsoft Azure Recovery service Agent\temp* .
- Om het probleem op te lossen, raadpleegt u het logboek bestand om de aard van het probleem te begrijpen:

  | Foutcode             | Rechtvaardig                                             | Aanbevelingen                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Het bestand of de map is beschadigd en onleesbaar. | Voer **chkdsk** uit op het bron volume.                             |
  | 0x80070002, 0x80070003 | Het systeem kan het opgegeven bestand niet vinden.         | [Zorg ervoor dat de map met krassen niet vol is](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  Controleren of het volume waar de Scratch ruimte is geconfigureerd, bestaat (niet verwijderd)  <br><br>   [Zorg ervoor dat de MARS-agent wordt uitgesloten van de antivirus software die op de computer is geïnstalleerd](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | De toegang is geweigerd                                    | [Controleren of de toegang wordt geblokkeerd door anti virus of software van derden](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | De toegang tot het cloud bestand is geweigerd.                | OneDrive-bestanden, Git-bestanden of andere bestanden die de status offline kunnen hebben op de computer |

- U kunt [uitsluitings regels toevoegen aan het bestaande beleid](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) gebruiken om niet-ondersteunde, ontbrekende of verwijderde bestanden uit uw back-upbeleid uit te sluiten om geslaagde back-ups te maken.

- Vermijd het verwijderen en opnieuw maken van beveiligde mappen met dezelfde namen in de map op het hoogste niveau. Dit kan ertoe leiden dat de back-up wordt voltooid met waarschuwingen met de fout er *is een kritieke inconsistentie gedetecteerd. wijzigingen kunnen daarom niet worden gerepliceerd.*  Als u mappen wilt verwijderen en opnieuw wilt maken, kunt u overwegen om dit te doen in submappen onder de map beveiligd het hoogste niveau.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Instellen van de versleutelingssleutel voor veilige back-ups is mislukt

| Fout | Mogelijke oorzaken | Aanbevolen acties |
| ---     | ---     | ---    |
| <br />Kan de versleutelings sleutel voor beveiligde back-ups niet instellen. De activering is niet volledig voltooid, maar de wachtwoordzin voor versleuteling is opgeslagen in het volgende bestand. |<li>De server is al geregistreerd bij een andere kluis.<li>Tijdens de configuratie is de wachtwoordzin beschadigd.| Hef de registratie van de server bij de kluis op en Registreer deze opnieuw met een nieuwe wachtwoordzin.

## <a name="the-activation-did-not-complete-successfully"></a>De activering is niet voltooid

| Fout  | Mogelijke oorzaken | Aanbevolen acties |
|---------|---------|---------|
|<br />De activering is niet voltooid. De huidige bewerking is mislukt vanwege een interne service fout [0x1FC07]. Probeer de bewerking na enige tijd opnieuw. Neem contact op met Microsoft Ondersteuning als het probleem zich blijft voordoen.     | <li> De map Scratch bevindt zich op een volume dat niet genoeg ruimte heeft. <li> De Scratch-map is onjuist verplaatst. <li> Het bestand OnlineBackup. KEK ontbreekt.         | <li>Voer een upgrade uit naar de [meest recente versie](https://aka.ms/azurebackup_agent) van de Mars-agent.<li>Verplaats de Scratch-map of cache locatie naar een volume met een vrije ruimte tussen 5% en 10% van de totale grootte van de back-upgegevens. Raadpleeg de stappen in [Veelgestelde vragen over het maken van back-ups van bestanden en mappen](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)om de cache locatie correct te verplaatsen.<li> Zorg ervoor dat het bestand OnlineBackup. KEK aanwezig is. <br>*De standaard locatie voor de Scratch-map of het pad naar de cache is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Wachtwoordzin voor versleuteling niet correct geconfigureerd

| Fout  | Mogelijke oorzaken | Aanbevolen acties |
|---------|---------|---------|
| <br />Fout 34506. De wachtwoordzin voor versleuteling die op deze computer is opgeslagen, is niet op de juiste wijze geconfigureerd.    | <li> De map Scratch bevindt zich op een volume dat niet genoeg ruimte heeft. <li> De Scratch-map is onjuist verplaatst. <li> Het bestand OnlineBackup. KEK ontbreekt.        | <li>Voer een upgrade uit naar de [meest recente versie](https://aka.ms/azurebackup_agent) van de Mars-agent.<li>Verplaats de Scratch-map of cache locatie naar een volume met een vrije ruimte tussen 5% en 10% van de totale grootte van de back-upgegevens. Raadpleeg de stappen in [Veelgestelde vragen over het maken van back-ups van bestanden en mappen](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)om de cache locatie correct te verplaatsen.<li> Zorg ervoor dat het bestand OnlineBackup. KEK aanwezig is. <br>*De standaard locatie voor de Scratch-map of het pad naar de cache is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Back-ups worden niet volgens schema uitgevoerd

Als geplande back-ups niet automatisch worden geactiveerd, maar hand matige back-ups correct werken, voert u de volgende handelingen uit:

- Zorg ervoor dat het back-upschema van Windows Server geen conflict veroorzaakt met het back-upschema voor Azure-bestanden en-mappen.

- Zorg ervoor dat de online back-upstatus is ingesteld op **inschakelen**. Voer de volgende stappen uit om de status te controleren:

  1. In taak planner vouwt u **micro soft** uit en selecteert u **Online Backup**.
  1. Dubbel klik op **micro soft-OnlineBackup** en ga naar het tabblad **Triggers** .
  1. Controleer of de status is ingesteld op **ingeschakeld**. Als dat niet het geval is, selecteert u **bewerken**, selecteert u **ingeschakeld** en selecteert u **OK**.

- Zorg ervoor dat het gebruikers account dat is geselecteerd voor het uitvoeren van de taak, de groep **systeem** of **lokale beheerders** op de server is. Als u het gebruikers account wilt controleren, gaat u naar het tabblad **Algemeen** en controleert u de **beveiligings** opties.

- Zorg ervoor dat Power Shell 3,0 of hoger is geïnstalleerd op de server. Als u de Power shell-versie wilt controleren, voert u deze opdracht uit en controleert u of het `Major` versie nummer 3 of hoger is:

  `$PSVersionTable.PSVersion`

- Zorg ervoor dat dit pad deel uitmaakt van de `PSMODULEPATH` omgevings variabele:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Als het Power shell-uitvoerings beleid voor `LocalMachine` is ingesteld op `restricted` , kan de Power shell-cmdlet waarmee de back-uptaak wordt geactiveerd, mislukken. Voer deze opdrachten uit in de modus met verhoogde bevoegdheid om het uitvoerings beleid te controleren en in te stellen op `Unrestricted` of `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Zorg ervoor dat de MSOnlineBackup-bestanden van de Power shell-module ontbreken of beschadigd zijn. Als er ontbrekende of beschadigde bestanden aanwezig zijn, voert u de volgende stappen uit:

  1. Kopieer vanaf elke computer met een MARS-agent die goed werkt de map MSOnlineBackup uit C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Plak op de problematische computer de gekopieerde bestanden op dezelfde maplocatie (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Als de computer al een MSOnlineBackup-map bevat, plakt u de bestanden erin of vervangt u de bestaande bestanden.

> [!TIP]
> Om ervoor te zorgen dat wijzigingen consistent worden toegepast, start u de server opnieuw op nadat u de voor gaande stappen hebt uitgevoerd.

## <a name="resource-not-provisioned-in-service-stamp"></a>De resource is niet ingericht in de service stempel

Fout | Mogelijke oorzaken | Aanbevolen acties
--- | --- | ---
De huidige bewerking is mislukt vanwege een interne service fout "resource niet ingericht in service Stamp". Voer de bewerking na enige tijd opnieuw uit. (ID: 230006) | De naam van de beveiligde server is gewijzigd. | <li> Wijzig de oorspronkelijke naam van de server in de kluis. <br> <li> Registreer de server opnieuw bij de kluis met de nieuwe naam.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>De taak kan niet worden gestart omdat een andere taak werd uitgevoerd

Als u een waarschuwings bericht ziet in de taak geschiedenis van de **Mars-console**, met de tekst ' de taak  >  kan niet worden gestart omdat er een andere taak werd uitgevoerd ', kan dit worden veroorzaakt door een dubbel exemplaar van de taak die wordt geactiveerd door de taak planner.

![De taak kan niet worden gestart omdat een andere taak werd uitgevoerd](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Ga als volgt te werk om het probleem op te lossen:

1. Start de module taak planner door *taskschd. msc* te typen in het venster uitvoeren
1. Navigeer in het linkerdeel venster naar de **bibliotheek** van  ->  **micro soft**  ->  **OnlineBackup** voor de taak planner.
1. Voor elke taak in deze bibliotheek dubbelklikt u op de taak om eigenschappen te openen en voert u de volgende stappen uit:
    1. Schakel over naar het tabblad **Instellingen**.

         ![Tabblad Instellingen](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Wijzig de optie voor **als de taak al wordt uitgevoerd, de volgende regel van toepassing** is. Kies **geen nieuw exemplaar starten**.

         ![Wijzig de regel zodat deze geen nieuw exemplaar start](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Problemen met herstellen oplossen

Het herstel volume kan ook na enkele minuten niet worden gekoppeld Azure Backup. En tijdens het proces kunnen er fout berichten worden weer gegeven. Voer de volgende stappen uit om normaal te beginnen met herstellen:

1. Annuleer het koppel proces als het enkele minuten actief is.

2. Controleer of u de meest recente versie van de back-upagent hebt. Als u de versie wilt controleren, selecteert u in het deel venster **acties** van de Mars-console de optie **About Microsoft Azure Recovery Services agent**. Controleer of het **versie** nummer gelijk is aan of hoger is dan de versie die in [dit artikel](https://go.microsoft.com/fwlink/?linkid=229525)wordt vermeld. Selecteer deze koppeling om [de nieuwste versie te downloaden](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Ga naar **Apparaatbeheer**-  >  **Opslag controllers** en zoek **micro soft iSCSI-initiator**. Als u het zoekt, gaat u rechtstreeks naar stap 7.

4. Als u de micro soft iSCSI initiator-service niet kunt vinden, kunt u een vermelding vinden onder **Apparaatbeheer**  >  **Opslag controllers** met de naam **Onbekend apparaat** met hardware-id **ROOT\ISCSIPRT**.

5. Klik met de rechter muisknop op **Onbekend apparaat** en selecteer **update stuur programma software**.

6. Werk het stuur programma bij door de optie te selecteren om  **automatisch te zoeken naar bijgewerkte stuur programma-software**. Deze update moet het **onbekende apparaat** wijzigen in **micro soft iSCSI-initiator**:

    ![Scherm opname van Azure Backup Apparaatbeheer, waarbij opslag controllers zijn gemarkeerd](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Ga naar de  >  micro soft iSCSI-initiator service **(lokaal)** voor taak beheer  >  :

    ![Scherm opname van Azure Backup taak beheer, met Services (lokaal) gemarkeerd](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Start de micro soft iSCSI initiator-service opnieuw. Als u dit wilt doen, klikt u met de rechter muisknop op de service en selecteert u **stoppen**. Klik nogmaals met de rechter muisknop en selecteer **starten**.

9. Herstel opnieuw proberen met behulp van [direct terugzetten](backup-instant-restore-capability.md).

Als het herstel nog steeds mislukt, start u de server of client opnieuw op. Als u de computer niet opnieuw wilt opstarten of als het herstel nog steeds mislukt, zelfs nadat u de server opnieuw hebt opgestart, probeert u [vanaf een andere computer te herstellen](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Cache problemen oplossen

De back-upbewerking kan mislukken als de cachemap (ook wel Scratch map genoemd) onjuist is geconfigureerd, ontbrekende vereisten of beperkte toegang heeft.

### <a name="prerequisites"></a>Vereisten

Voor MARS-agent bewerkingen moet de cachemap voldoen aan de volgende vereisten:

- [Zorg ervoor dat 5% tot 10% beschik bare volume ruimte beschikbaar is op de locatie van de Scratch map](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Zorg ervoor dat de locatie van de map Scratch geldig en toegankelijk is](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Zorg ervoor dat de bestands kenmerken van de cachemap worden ondersteund](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Zorg ervoor dat de toegewezen opslag ruimte voor schaduw kopieën voldoende is voor het back-upproces](#increase-shadow-copy-storage)
- [Zorg ervoor dat er geen andere processen zijn (bijvoorbeeld anti-virus software) om de toegang tot de cachemap te beperken](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Schaduw kopie opslag verhogen

Back-upbewerkingen kunnen mislukken als er voldoende opslag ruimte voor schaduw kopieën is die vereist is om de gegevens bron te beveiligen. U kunt dit probleem oplossen door de opslag ruimte voor schaduw kopieën op het beveiligde volume met behulp van **vssadmin** te verhogen, zoals hieronder wordt weer gegeven:

- Controleer de huidige schaduw opslag ruimte van de opdracht prompt met verhoogde bevoegdheid:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Verg root de schaduw opslag ruimte met de volgende opdracht:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Een ander proces of antivirus software die de toegang tot de cachemap blokkeert

Als er antivirus software op de server is geïnstalleerd, voegt u de benodigde uitsluitings regels toe aan de antivirus scan voor deze bestanden en mappen:  

- De map Scratch. De standaard locatie is `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- De bin-map op `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Algemene problemen

In deze sectie worden de veelvoorkomende fouten besproken die u tegen komt tijdens het gebruik van de MARS-agent.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Foutbericht | Aanbevolen actie
--|--
De Microsoft Azure Recovery Services-agent heeft geen toegang gekregen tot de controlesom van de back-up die is opgeslagen op de scratchlocatie | U kunt dit probleem oplossen door de volgende stappen uit te voeren en de server opnieuw op te starten <br/> - [Controleren of er een anti-virus of andere processen zijn die de Scratch locatie bestanden vergren delen](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controleer of de Scratch locatie geldig is en toegankelijk is voor de MARS-agent.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Foutbericht | Aanbevolen actie
--|--
De Microsoft Azure Recovery Services-agent heeft geen toegang gekregen tot de scratchlocatie om de VHD te initialiseren | U kunt dit probleem oplossen door de volgende stappen uit te voeren en de server opnieuw op te starten <br/> - [Controleren of de bestanden op de Scratch locatie worden vergrendeld door anti virus of andere processen](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controleer of de Scratch locatie geldig is en toegankelijk is voor de MARS-agent.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Foutbericht | Aanbevolen actie
--|--
Het maken van de back-up is mislukt vanwege onvoldoende opslag ruimte op het volume waar de Scratch map zich bevindt | Controleer de volgende stappen om dit probleem op te lossen en voer de bewerking opnieuw uit:<br/>- [Zorg ervoor dat de MARS-agent het meest recent is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Opslag problemen controleren en oplossen die van invloed zijn op de Scratch ruimte van de back-up](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Foutbericht | Aanbevolen actie
--|--
Kan wijzigingen niet vinden in een bestand. Dit kan verschillende redenen hebben. Voer de bewerking opnieuw uit | Controleer de volgende stappen om dit probleem op te lossen en voer de bewerking opnieuw uit:<br/> - [Zorg ervoor dat de MARS-agent het meest recent is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Opslag problemen controleren en oplossen die van invloed zijn op de Scratch ruimte van de back-up](#prerequisites)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een back-up van Windows Server met de Azure backup-agent](tutorial-backup-windows-server-to-azure.md).
- Als u een back-up wilt herstellen, raadpleegt u [bestanden herstellen op een Windows-computer](backup-azure-restore-windows-server.md).
