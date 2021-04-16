---
title: Problemen met de Azure Backup agent oplossen
description: In dit artikel leert u hoe u problemen met de installatie en registratie van de Azure Backup-agent kunt oplossen.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: c662bf8c8d9490691f45254bef01618f17bd6e2a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518181"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Problemen met de MARS Microsoft Azure agent (Recovery Services) oplossen

In dit artikel wordt beschreven hoe u fouten kunt oplossen die kunnen optreden tijdens de configuratie, registratie, back-up en herstel.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

U wordt aangeraden het volgende te controleren voordat u begint met het oplossen van problemen met De MARS-agent (Azure Recovery Services) van Microsoft:

- [Zorg ervoor dat de MARS-agent up-to-date is.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zorg ervoor dat u een netwerkverbinding hebt tussen de MARS-agent en Azure](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Zorg ervoor dat MARS wordt uitgevoerd (in de serviceconsole). Als dat nodig is, start u de bewerking opnieuw en doet u het opnieuw.
- [Zorg ervoor dat er 5% tot 10% vrije volumeruimte beschikbaar is op de locatie van de scratchmap.](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Controleer of er geen ander proces of antivirussoftware conflicten veroorzaakt met Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Zie Backup [Jobs Completed with Warning](#backup-jobs-completed-with-warning) (Back-uptaken voltooid met waarschuwing) als de back-uptaken zijn voltooid met waarschuwingen
- Zie Back-ups worden niet uitgevoerd volgens schema als de geplande back-up mislukt, maar handmatige [back-ups wel werken.](#backups-dont-run-according-to-schedule)
- Zorg ervoor dat uw besturingssysteem de nieuwste updates heeft.
- [Zorg ervoor dat niet-ondersteunde stations en bestanden met niet-ondersteunde kenmerken zijn uitgesloten van back-up](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Zorg ervoor dat de klok op het beveiligde systeem is geconfigureerd voor de juiste tijdzone.
- [Zorg .NET Framework 4.5.2 of hoger is geïnstalleerd op de server](https://www.microsoft.com/download/details.aspx?id=30653).
- Als u probeert de server opnieuw te registreren bij een kluis:
  - Zorg ervoor dat de agent op de server is verwijderd en uit de portal is verwijderd.
  - Gebruik dezelfde wachtwoordzin die in eerste instantie is gebruikt om de server te registreren.
- Zorg ervoor dat voor offline back-ups Azure PowerShell 3.7.0 is geïnstalleerd op zowel de bron- als de kopieercomputer voordat u de back-up start.
- Zie dit artikel als de Backup-agent wordt uitgevoerd op een virtuele [Azure-machine.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

## <a name="invalid-vault-credentials-provided"></a>Er zijn ongeldige kluisreferenties ingevoerd

**Foutbericht:** Ongeldige kluisreferenties opgegeven. Het bestand is beschadigd of de nieuwste referenties zijn niet gekoppeld aan de Recovery Service. (Id: 34513)

| Oorzaak | Aanbevolen acties |
| ---     | ---    |
| **Kluisreferenties zijn niet geldig** <br/> <br/> Kluisreferentiebestanden zijn mogelijk beschadigd, zijn mogelijk verlopen of hebben mogelijk een andere bestandsextensie dan *.vaultCredentials.* (Het is bijvoorbeeld mogelijk dat ze meer dan tien dagen vóór het moment van registratie zijn gedownload.)| [Download nieuwe referenties uit](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) de Recovery Services-kluis op Azure Portal. Neem vervolgens de volgende stappen, waar van toepassing: <ul><li> Als u MARS al hebt geïnstalleerd en geregistreerd, opent u de Microsoft Azure Backup Agent MMC-console. Selecteer vervolgens **Server registreren** in het **deelvenster Acties** om de registratie met de nieuwe referenties te voltooien. <br/> <li> Als de nieuwe installatie mislukt, probeert u opnieuw te installeren met de nieuwe referenties.</ul> **Opmerking:** als er meerdere kluisreferentiebestanden zijn gedownload, is alleen het meest recente bestand geldig voor de komende tien dagen. U wordt aangeraden een nieuw kluisreferentiebestand te downloaden.
| **De registratie wordt geblokkeerd door de proxyserver/firewall** <br/>of <br/>**Geen internetverbinding** <br/><br/> Als uw computer of proxyserver beperkte internetverbinding heeft en u de toegang voor de benodigde URL's niet controleert, mislukt de registratie.| Ga als volgende te werk:<br/> <ul><li> Werk samen met uw IT-team om ervoor te zorgen dat het systeem verbinding heeft met internet.<li> Als u geen proxyserver hebt, controleert u of de proxyoptie niet is geselecteerd wanneer u de agent registreert. [Controleer uw proxyinstellingen.](#verifying-proxy-settings-for-windows)<li> Als u wel een firewall/proxyserver hebt, moet u samenwerken met uw netwerkteam om ervoor te zorgen dat deze URL's en IP-adressen toegang hebben:<br/> <br> **URL's**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**IP-adressen**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Probeer u opnieuw te registreren nadat u de voorgaande stappen voor probleemoplossing hebt voltooid.<br></br> Als uw verbinding via een Azure ExpressRoute, moet u ervoor zorgen dat de instellingen zijn geconfigureerd zoals beschreven in [Azure ExpressRoute ondersteuning.](backup-support-matrix-mars-agent.md#azure-expressroute-support)
| **De registratie wordt geblokkeerd door antivirussoftware** | Als er antivirussoftware op de server is geïnstalleerd, voegt u de benodigde uitsluitingsregels toe aan de antivirusscan voor deze bestanden en mappen: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> De scratch-map. De standaardlocatie is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> De map bin op C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Extra aanbevelingen

- Ga naar C:/Windows/Temp en controleer of er meer dan 60.000 of 65.000 bestanden zijn met de extensie .tmp. Verwijder deze bestanden als deze er zijn.
- Zorg ervoor dat de datum en tijd van de machine overeenkomen met de lokale tijdzone.
- Zorg [ervoor dat deze sites](install-mars-agent.md#verify-internet-access) worden toegevoegd aan uw vertrouwde sites in Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Proxy-instellingen voor Windows controleren

1. Download PsExec op de [pagina Sysinternals.](/sysinternals/downloads/psexec)
1. Voer `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` uit vanaf een opdrachtprompt met verhoogde opdracht.

   Met deze opdracht wordt Internet Explorer.
1. Ga naar **Extra**  >  **Internetopties**  >  **LAN-instellingen**  >  **verbindingen.**
1. Controleer de proxyinstellingen voor het systeemaccount.
1. Als er geen proxy is geconfigureerd en er proxydetails zijn opgegeven, verwijdert u de details.
1. Als er een proxy is geconfigureerd en de proxygegevens onjuist zijn, controleert u of de **proxy-IP-** en **poortgegevens** juist zijn.
1. Sluit Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Kan kluisreferentiebestand niet downloaden

| Fout   | Aanbevolen acties |
| ---     | ---    |
|Kan het kluisreferentiebestand niet downloaden. (Id: 403) | <ul><li> Download de kluisreferenties via een andere browser of neem de volgende stappen: <ul><li> Start Internet Explorer. Selecteer F12. </li><li> Ga naar het **tabblad Netwerk** en leeg de cache en cookies. </li> <li> Vernieuw de pagina.<br></li></ul> <li> Controleer of het abonnement is uitgeschakeld/verlopen.<br></li> <li> Controleer of het downloaden wordt geblokkeerd door een firewallregel. <br></li> <li> Zorg ervoor dat u de limiet voor de kluis (50 machines per kluis) niet hebt bereikt.<br></li>  <li> Zorg ervoor dat de gebruiker over de Azure Backup beschikt die vereist zijn voor het downloaden van kluisreferenties en het registreren van een server bij de kluis. Zie [Op rollen gebaseerd toegangsbeheer van Azure gebruiken om herstelpunten Azure Backup beheren.](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>De Microsoft Azure Recovery Services-agent kan geen verbinding maken met Microsoft Azure Backup

| Fout  | Mogelijke oorzaak | Aanbevolen acties |
| ---     | ---     | ---    |
| <br /><ul><li>De Microsoft Azure Recovery Service-agent kan geen verbinding maken met Microsoft Azure Backup. (Id: 100050) Controleer uw netwerkinstellingen en zorg ervoor dat u verbinding kunt maken met internet.<li>(407) Proxyverificatie vereist. |Een proxy blokkeert de verbinding. |  <ul><li>Ga Internet Explorer naar Extra   >  **Internetopties**  >  **Beveiliging**  >  **Internet**. Selecteer **Aangepast niveau en** schuif omlaag naar de sectie Bestand **downloaden.** Selecteer **Inschakelen**.<p>Mogelijk moet u ook [URL's en IP-adressen toevoegen](install-mars-agent.md#verify-internet-access) aan uw vertrouwde sites in Internet Explorer.<li>Wijzig de instellingen om een proxyserver te gebruiken. Geef vervolgens de details van de proxyserver op.<li> Als uw computer beperkte toegang tot internet heeft, moet u ervoor zorgen dat de firewallinstellingen op de computer of proxy deze [URL's en IP-adressen toestaan.](install-mars-agent.md#verify-internet-access) <li>Als er antivirussoftware op de server is geïnstalleerd, sluit u deze bestanden uit van de antivirusscan: <ul><li>CBEngine.exe (in plaats van dpmra.exe).<li>CSC.exe (gerelateerd aan .NET Framework). Er is een CSC.exe voor elke .NET Framework versie die op de server is geïnstalleerd. Sluit CSC.exe-bestanden uit voor alle versies van .NET Framework op de betreffende server. <li>De scratchmap of cachelocatie. <br>De standaardlocatie voor de scratchmap of het cachepad is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>De map bin op C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Het opgegeven kluisreferentiebestand kan niet worden gebruikt, omdat het niet wordt gedownload uit de kluis die aan deze server is gekoppeld

| Fout  | Mogelijke oorzaak | Aanbevolen acties |
| ---     | ---     | ---    |
| Het opgegeven kluisreferentiebestand kan niet worden gebruikt, omdat het niet wordt gedownload uit de kluis die aan deze server is gekoppeld. (Id: 100110) Geef de juiste kluisreferenties op. | Het kluisreferentiebestand is afkomstig uit een andere kluis dan de kluis waar deze server al is geregistreerd. | Zorg ervoor dat de doelmachine en de bronmachine zijn geregistreerd bij dezelfde Recovery Services-kluis. Als de doelserver al is geregistreerd bij een andere kluis, gebruikt u de **optie Server** registreren om u te registreren bij de juiste kluis.  

## <a name="backup-jobs-completed-with-warning"></a>Back-uptaken voltooid met waarschuwing

- Wanneer de MARS-agent tijdens het maken van de back-up bestanden en mappen door een itereert, kunnen er verschillende omstandigheden zijn die ertoe kunnen leiden dat de back-up wordt gemarkeerd als voltooid met waarschuwingen. Tijdens deze voorwaarden wordt een taak als voltooid met waarschuwingen weer gegeven. Dat is prima, maar het betekent dat er geen back-up van ten minste één bestand kan worden opgeslagen. De taak heeft dat bestand dus overgeslagen, maar een back-up van alle andere bestanden in kwestie op de gegevensbron.

  ![Back-up van taak voltooid met waarschuwingen](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- De volgende voorwaarden kunnen ertoe leiden dat de back-ups bestanden overslaan:
  - Niet-ondersteunde bestandskenmerken (bijvoorbeeld in een OneDrive-map, Gecomprimeerde stroom, reparsepunten). Raadpleeg de ondersteuningsmatrix voor de [volledige lijst.](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)
  - Een probleem met een bestandssysteem
  - Een ander proces verstoort (bijvoorbeeld: antivirussoftware die grepen in bestanden vasthoudt, kan voorkomen dat de MARS-agent toegang heeft tot de bestanden)
  - Bestanden die zijn vergrendeld door een toepassing  

- De back-upservice markeert deze bestanden als mislukt in het logboekbestand, met de volgende naamconventie: *LastBackupFailedFilesxxxx.txt* onder de map *C:\Program Files\Microsoft Azure Recovery Service Agent\temp.*
- Als u het probleem wilt oplossen, bekijkt u het logboekbestand om de aard van het probleem te begrijpen:

  | Foutcode             | Redenen                                             | Aanbevelingen                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Het bestand of de map is beschadigd en onleesbaar. | Voer **chkdsk uit** op het bronvolume.                             |
  | 0x80070002, 0x80070003 | Het systeem kan het opgegeven bestand niet vinden.         | [Zorg ervoor dat de scratchmap niet vol is](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)  <br><br>  Controleer of het volume waar scratchruimte is geconfigureerd bestaat (niet verwijderd)  <br><br>   [Zorg ervoor dat de MARS-agent is uitgesloten van de antivirusprogramma's die op de computer zijn geïnstalleerd](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Toegang wordt geweigerd                                    | [Controleer of de toegang wordt geblokkeerd door antivirussoftware of andere software van derden](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | Toegang tot het cloudbestand wordt geweigerd.                | OneDrive-bestanden, Git-bestanden of andere bestanden die offline kunnen zijn op de computer |

- U kunt [Uitsluitingsregels toevoegen](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) aan bestaand beleid gebruiken om niet-ondersteunde, ontbrekende of verwijderde bestanden uit te sluiten van uw back-upbeleid om geslaagde back-ups te garanderen.

- Vermijd het verwijderen en opnieuw maken van beveiligde mappen met dezelfde namen in de map op het hoogste niveau. Dit kan ertoe leiden dat de back-up wordt voltooit met waarschuwingen met de fout Er is een kritieke *inconsistentie gedetecteerd, waardoor wijzigingen niet kunnen worden gerepliceerd.*  Als u mappen wilt verwijderen en opnieuw wilt maken, kunt u dit doen in submappen onder de beveiligde map op het hoogste niveau.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Instellen van de versleutelingssleutel voor veilige back-ups is mislukt

| Fout | Mogelijke oorzaken | Aanbevolen acties |
| ---     | ---     | ---    |
| <br />Instellen van de versleutelingssleutel voor beveiligde back-ups is mislukt. De activering is niet volledig geslaagd, maar de wachtwoordzin voor versleuteling is opgeslagen in het volgende bestand. |<li>De server is al geregistreerd bij een andere kluis.<li>Tijdens de configuratie is de wachtwoordzin beschadigd.| De registratie van de server bij de kluis ongedaan maken en opnieuw registreren met een nieuwe wachtwoordzin.

## <a name="the-activation-did-not-complete-successfully"></a>De activering is niet voltooid

| Fout  | Mogelijke oorzaken | Aanbevolen acties |
|---------|---------|---------|
|<br />De activering is niet voltooid. De huidige bewerking is mislukt vanwege een interne servicefout [0x1FC07]. Probeer de bewerking na enige tijd opnieuw. Neem contact op met Microsoft Ondersteuning als het probleem zich blijft voordoen.     | <li> De scratchmap bevindt zich op een volume dat onvoldoende ruimte heeft. <li> De scratchmap is onjuist verplaatst. <li> Het bestand OnlineBackup.KEK ontbreekt.         | <li>Upgrade naar de [nieuwste versie van](https://aka.ms/azurebackup_agent) de MARS-agent.<li>Verplaats de scratchmap of cachelocatie naar een volume met vrije ruimte tussen 5% en 10% van de totale grootte van de back-upgegevens. Als u de cachelocatie op de juiste manier wilt verplaatsen, raadpleegt u de stappen in Veelvoorkomende vragen over het maken van [back-up van bestanden en mappen.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Zorg ervoor dat het bestand OnlineBackup.KEK aanwezig is. <br>*De standaardlocatie voor de scratchmap of het cachepad is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.*        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Wachtwoordzin voor versleuteling niet correct geconfigureerd

| Fout  | Mogelijke oorzaken | Aanbevolen acties |
|---------|---------|---------|
| <br />Fout 34506. De wachtwoordzin voor versleuteling die op deze computer is opgeslagen, is niet juist geconfigureerd.    | <li> De scratchmap bevindt zich op een volume dat onvoldoende ruimte heeft. <li> De scratchmap is onjuist verplaatst. <li> Het bestand OnlineBackup.KEK ontbreekt.        | <li>Upgrade naar de [nieuwste versie van](https://aka.ms/azurebackup_agent) de MARS-agent.<li>Verplaats de scratchmap of cachelocatie naar een volume met vrije ruimte dat tussen 5% en 10% van de totale grootte van de back-upgegevens ligt. Als u de cachelocatie correct wilt verplaatsen, raadpleegt u de stappen in Veelvoorkomende vragen over het maken van [een back-up van bestanden en mappen.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Zorg ervoor dat het bestand OnlineBackup.KEK aanwezig is. <br>De standaardlocatie voor de scratchmap of het *cachepad is C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.*         |

## <a name="backups-dont-run-according-to-schedule"></a>Back-ups worden niet volgens planning uitgevoerd

Als geplande back-ups niet automatisch worden geactiveerd, maar handmatige back-ups correct werken, kunt u de volgende acties uitvoeren:

- Zorg ervoor dat het back-upschema van Windows Server niet in strijd is met het back-upschema voor Azure-bestanden en -mappen.

- Zorg ervoor dat de online back-upstatus is ingesteld op **Inschakelen.** Ga als volgende stappen te werk om de status te controleren:

  1. Vouw in Task Scheduler **Microsoft uit en** selecteer **Onlineback-up.**
  1. Dubbelklik op **Microsoft-OnlineBackup en** ga naar het **tabblad Triggers.**
  1. Controleer of de status is ingesteld **op Ingeschakeld.** Als dat niet het juiste is, **selecteert u Bewerken,** **selecteert u Ingeschakeld** en selecteert u **vervolgens OK.**

- Zorg ervoor dat het gebruikersaccount dat is geselecteerd voor het uitvoeren van de taak de groep **SYSTEEM** of Lokale **administrators** op de server is. Als u het gebruikersaccount wilt controleren, gaat u naar het **tabblad Algemeen** en controleert u **de beveiligingsopties.**

- Zorg ervoor dat PowerShell 3.0 of hoger is geïnstalleerd op de server. Als u de PowerShell-versie wilt controleren, moet u deze opdracht uitvoeren en controleren of het `Major` versienummer 3 of hoger is:

  `$PSVersionTable.PSVersion`

- Zorg ervoor dat dit pad deel uitmaakt van de `PSMODULEPATH` omgevingsvariabele:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Als het PowerShell-uitvoeringsbeleid voor is ingesteld op , kan de `LocalMachine` `restricted` PowerShell-cmdlet die de back-uptaak activeert, mislukken. Voer deze opdrachten uit in de verhoogde modus om het uitvoeringsbeleid te controleren en in te stellen op `Unrestricted` of `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Zorg ervoor dat er geen MSOnlineBackup-bestanden van de PowerShell-module ontbreken of beschadigd zijn. Als er bestanden ontbreken of beschadigd zijn, moet u deze stappen volgen:

  1. Vanaf elke computer met een MARS-agent die goed werkt, kopieert u de map MSOnlineBackup uit C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Plak op de problematische computer de gekopieerde bestanden op dezelfde maplocatie (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Als de computer al een map MSOnlineBackup heeft, plakt u de bestanden in de map of vervangt u eventuele bestaande bestanden.

> [!TIP]
> Om ervoor te zorgen dat wijzigingen consistent worden toegepast, start u de server opnieuw op nadat u de voorgaande stappen heeft uitgevoerd.

## <a name="resource-not-provisioned-in-service-stamp"></a>Resource niet ingericht in servicestempel

Fout | Mogelijke oorzaken | Aanbevolen acties
--- | --- | ---
De huidige bewerking is mislukt vanwege een interne servicefout 'Resource niet ingericht in servicestempel'. Voer de bewerking na enige tijd opnieuw uit. (Id: 230006) | De naam van de beveiligde server is gewijzigd. | <li> Wijzig de naam van de server weer in de oorspronkelijke naam zoals deze is geregistreerd bij de kluis. <br> <li> Registreer de server opnieuw bij de kluis met de nieuwe naam.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>De taak kan niet worden gestart omdat er een andere taak wordt uitgevoerd

Als u een waarschuwingsbericht ziet in de taakgeschiedenis van de **MARS-console** met de melding 'Taak kan niet worden gestart omdat er een andere taak wordt uitgevoerd', kan dit worden veroorzaakt door een duplicaat van de taak die wordt geactiveerd door de  >  Task Scheduler.

![De taak kan niet worden gestart omdat er een andere taak wordt uitgevoerd](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Ga als volgt te werk om het probleem op te lossen:

1. Start de Task Scheduler door *taskschd.msc* te typen in het venster Uitvoeren
1. Navigeer in het **linkerdeelvenster naar Task Scheduler Bibliotheek**  ->  **Microsoft**  ->  **OnlineBackup**.
1. Dubbelklik voor elke taak in deze bibliotheek op de taak om eigenschappen te openen en voer de volgende stappen uit:
    1. Schakel over naar het tabblad **Instellingen**.

         ![Tabblad Instellingen](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Wijzig de optie Als **de taak al wordt uitgevoerd, is de volgende regel van toepassing.** Kies **Geen nieuw exemplaar starten.**

         ![Wijzig de regel om een nieuw exemplaar niet te starten](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Problemen met herstellen oplossen

Azure Backup kan het herstelvolume zelfs na enkele minuten niet worden bevestigd. En mogelijk ontvangt u tijdens het proces foutberichten. Als u wilt beginnen met normaal herstellen, moet u de volgende stappen volgen:

1. Annuleer het bevestigingsproces als het enkele minuten actief is.

2. Controleer of u de nieuwste versie van de Backup-agent hebt. Als u de versie wilt controleren, **selecteert** u in het deelvenster Acties van de MARS-console **About Microsoft Azure Recovery Services Agent**. Controleer of het **versienummer** gelijk is aan of hoger is dan de versie die in dit [artikel wordt vermeld.](https://go.microsoft.com/fwlink/?linkid=229525) Selecteer deze koppeling om [de nieuwste versie te downloaden.](https://go.microsoft.com/fwLink/?LinkID=288905)

3. Ga naar **Apparaatbeheer**  >  **Storage-controllers** en zoek **Microsoft iSCSI-initiator**. Als u deze vindt, gaat u rechtstreeks naar stap 7.

4. Als u de Microsoft iSCSI Initiator-service niet kunt vinden, zoekt u een vermelding onder **Apparaatbeheer** Storage-controllers met de naam Onbekend apparaat met  >   hardware-id **ROOT\ISCSIPRT.** 

5. Klik met de rechtermuisknop **op Onbekend apparaat** en selecteer **Stuurprogrammasoftware bijwerken.**

6. Werk het stuurprogramma bij door de optie automatisch zoeken naar bijgewerkte **stuurprogrammasoftware te selecteren.** Met deze update wordt **Onbekend apparaat gewijzigd** in Microsoft **iSCSI-initiator**:

    ![Schermopname van Azure Backup Apparaatbeheer, met Opslagcontrollers gemarkeerd](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Ga naar **Taakbeheer**  >  **Services (lokaal)**  >  **Microsoft iSCSI-initiatorservice:**

    ![Schermopname van Azure Backup Taakbeheer, met Services (lokaal) gemarkeerd](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Start de Microsoft iSCSI Initiator-service opnieuw. Klik hiervoor met de rechtermuisknop op de service en selecteer **Stoppen.** Klik er vervolgens opnieuw met de rechtermuisknop op en selecteer **Starten.**

9. Herstel opnieuw proberen met behulp van [Direct herstellen.](backup-instant-restore-capability.md)

Als het herstel nog steeds mislukt, start u de server of client opnieuw op. Als u niet opnieuw wilt opstarten of als het herstel nog steeds mislukt, zelfs nadat u de server opnieuw hebt opgestart, probeert u te herstellen [vanaf een andere computer.](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="troubleshoot-cache-problems"></a>Cacheproblemen oplossen

De back-upbewerking kan mislukken als de cachemap (ook wel scratchmap genoemd) onjuist is geconfigureerd, aan vereisten ontbreekt of beperkte toegang heeft.

### <a name="prerequisites"></a>Vereisten

Om marsagentbewerkingen te laten slagen, moet de cachemap voldoen aan de volgende vereisten:

- [Zorg ervoor dat er 5% tot 10% vrije volumeruimte beschikbaar is op de locatie van de scratchmap](backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Controleren of de locatie van de scratchmap geldig en toegankelijk is](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)
- [Zorg ervoor dat bestandskenmerken in de cachemap worden ondersteund](backup-azure-file-folder-backup-faq.yml#are-there-any-attributes-of-the-cache-folder-that-aren-t-supported-)
- [Zorg ervoor dat de toegewezen opslagruimte voor schaduwkopie voldoende is voor het back-upproces](#increase-shadow-copy-storage)
- [Zorg ervoor dat er geen andere processen (bijvoorbeeld antivirussoftware) zijn die de toegang tot de cachemap beperken](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Opslag voor schaduwkopie verhogen

Back-upbewerkingen kunnen mislukken als er onvoldoende opslagruimte voor schaduwkopie is die nodig is om de gegevensbron te beveiligen. U kunt dit probleem oplossen door de opslagruimte voor schaduwkopie op het beveiligde volume te vergroten met **behulp van vssadmin,** zoals hieronder wordt weergegeven:

- Controleer de huidige schaduwopslagruimte vanaf de opdrachtprompt met verhoogde opdracht:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Verhoog de schaduwopslagruimte met de volgende opdracht:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Een ander proces of antivirussoftware blokkeert de toegang tot de cachemap

Als er antivirussoftware op de server is geïnstalleerd, voegt u de benodigde uitsluitingsregels toe aan de antivirusscan voor deze bestanden en mappen:  

- De scratch-map. De standaardlocatie is `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- De bin-map op `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Algemene problemen

In deze sectie worden de veelvoorkomende fouten beland die optreden tijdens het gebruik van de MARS-agent.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Foutbericht | Aanbevolen actie
--|--
De Microsoft Azure Recovery Services-agent heeft geen toegang gekregen tot de controlesom van de back-up die is opgeslagen op de scratchlocatie | Voer de volgende stappen uit en start de server opnieuw op om dit probleem op te lossen <br/> - [Controleer of er een antivirusprogramma of andere processen zijn die de scratchlocatiebestanden vergrendelen](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controleer of de scratchlocatie geldig is en toegankelijk is voor de MARS-agent.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Foutbericht | Aanbevolen actie
--|--
De Microsoft Azure Recovery Services-agent heeft geen toegang gekregen tot de scratchlocatie om de VHD te initialiseren | Voer de volgende stappen uit en start de server opnieuw op om dit probleem op te lossen <br/> - [Controleer of antivirus- of andere processen de scratchlocatiebestanden vergrendelen](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Controleer of de scratchlocatie geldig is en toegankelijk is voor de MARS-agent.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Foutbericht | Aanbevolen actie
--|--
Back-up is mislukt vanwege onvoldoende opslag in het volume waar de scratchmap zich bevindt | U kunt dit probleem oplossen door de volgende stappen te controleren en de bewerking opnieuw uit te voeren:<br/>- [Zorg ervoor dat de MARS-agent de meest recente is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Opslagproblemen controleren en oplossen die van invloed zijn op de scratchruimte van de back-up](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Foutbericht | Aanbevolen actie
--|--
Kan wijzigingen niet vinden in een bestand. Dit kan verschillende redenen hebben. Voer de bewerking opnieuw uit | U kunt dit probleem oplossen door de volgende stappen te controleren en de bewerking opnieuw uit te voeren:<br/> - [Zorg ervoor dat de MARS-agent de meest recente is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Opslagproblemen controleren en oplossen die van invloed zijn op de scratchruimte van de back-up](#prerequisites)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een back-up van Windows Server met de Azure Backup agent](tutorial-backup-windows-server-to-azure.md).
- Zie Bestanden herstellen op een [Windows-computer](backup-azure-restore-windows-server.md)als u een back-up wilt herstellen.
