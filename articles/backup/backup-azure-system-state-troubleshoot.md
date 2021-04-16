---
title: Problemen met back-up van systeemtoestand oplossen
description: In dit artikel leert u hoe u problemen in Systeemtoestandback-up voor on-premises Windows-servers kunt oplossen.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 07e101fe87fb3f5db0bb716f0bc9ea6f8773aa3e
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518555"
---
# <a name="troubleshoot-system-state-backup"></a>Problemen met back-up van systeemtoestand oplossen

In dit artikel worden oplossingen beschreven voor problemen die u kunt tegen komen tijdens het gebruik van Back-up van systeemtoestand.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

U wordt aangeraden de volgende validatiestappen uit te voeren voordat u begint met het oplossen van problemen met back-ups van de systeemtoestand:

- [Zorg Microsoft Azure Recovery Services-agent (MARS) up-to-date is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zorg ervoor dat er een netwerkverbinding is tussen de MARS-agent en Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Controleer of Microsoft Azure Recovery Services wordt uitgevoerd (in Service-console). Start de bewerking indien nodig opnieuw op en opnieuw
- [Zorg ervoor dat er 5-10% ruimte vrij is op de locatie van de scratch-map](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Controleer of er geen ander proces of antivirussoftware conflicten veroorzaakt met Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Geplande back-up mislukt, maar handmatige back-up werkt](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Zorg ervoor dat uw besturingssysteem helemaal is bijgewerkt
- [Zorg ervoor dat niet-ondersteunde stations en bestanden met niet-ondersteunde kenmerken worden uitgesloten van back-up](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Zorg ervoor dat de **systeemklok** op de beveiligde computer is geconfigureerd voor de juiste tijdzone <br>
- [Zorg ervoor dat op de server ten minste .Net Framework-versie 4.5.2 of hoger is geïnstalleerd](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Als u de server **opnieuw wilt registreren** bij een kluis, doet u het volgende: <br>
  - Zorg ervoor dat de agent op de server is verwijderd en uit de portal is verwijderd <br>
  - Dezelfde wachtwoordzin gebruiken die in eerste instantie is gebruikt voor het registreren van de server <br>
- Als dit een offline back-up is, controleert u of Azure PowerShell versie 3.7.0 is geïnstalleerd op zowel de bron- als kopieercomputer voordat u de offline back-upbewerking start
- [Overweging wanneer de Backup-agent wordt uitgevoerd op een virtuele Azure-machine](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Beperking

- Herstellen naar andere hardware met behulp van systeemtoestandherstel wordt niet aanbevolen door Microsoft
- Systeemtoestandback-up ondersteunt momenteel 'on-premises' Windows-servers. Deze functionaliteit is niet beschikbaar voor azure-VM's.

## <a name="prerequisites"></a>Vereisten

Voordat we problemen met systeemtoestandback-up Azure Backup, moet u de volgende controle op vereisten uitvoeren.  

### <a name="verify-windows-server-backup-is-installed"></a>Controleren Windows Server Back-up is geïnstalleerd

Zorg Windows Server Back-up is geïnstalleerd en ingeschakeld op de server. Voer deze PowerShell-opdracht uit om de installatiestatus te controleren:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Als in de uitvoer de **installatietoestand** **als** beschikbaar wordt weergegeven, betekent dit dat de Windows Server-back-upfunctie beschikbaar is voor de installatie, maar niet op de server is geïnstalleerd. Als de Windows Server Back-up is geïnstalleerd, gebruikt u een van de onderstaande methoden om deze te installeren.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Methode 1: een Windows Server Back-up powershell

Als u Windows Server Back-up powershell wilt installeren, moet u de volgende opdracht uitvoeren:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Methode 2: een Windows Server Back-up installeren met Serverbeheer

Voer de Windows Server Back-up uit om Serverbeheer te installeren:

1. Selecteer **in Serverbeheer de** optie Functies en onderdelen **toevoegen.** De **wizard Functies en onderdelen toevoegen wordt** weergegeven.

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selecteer **Installatietype** en selecteer **Volgende.**

    ![Type installatie](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selecteer een server in de servergroep en selecteer **Volgende.** Laat in serverfunctie de standaardselectie staan en selecteer **Volgende.**
4. Selecteer **Windows Server Back-up** tabblad **Functies** en selecteer **Volgende.**

    ![Venster Functies selecteren](./media/backup-azure-system-state-troubleshoot/features.png)

5. Selecteer op **het tabblad** Bevestiging de optie **Installeren om** het installatieproces te starten.
6. Op het **tabblad** Resultaten wordt de functie Windows Server Back-up is geïnstalleerd op uw Windows-server.

    ![Resultaten van de installatie](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Machtiging voor systeemvolumegegevens

Zorg ervoor dat het lokale SYSTEEM volledige controle heeft over de map **System Volume Information** in het volume waarop Windows is geïnstalleerd. Meestal is dit **C:\System Volume Information.** Windows Server-back-up kan mislukken als de bovenstaande machtigingen niet juist zijn ingesteld.

### <a name="dependent-services"></a>Afhankelijke services

Zorg ervoor dat de onderstaande services de status Actief hebben:

**Servicenaam** | **Opstarttype**
--- | ---
Externe procedureoproep (RPC) | Automatisch
COM+ Gebeurtenissysteem (EventSystem) | Automatisch
System Event Notification Service (SENS) | Automatisch
Volume Shadow Copy (VSS) | Handmatig
Microsoft Software Shadow Copy Provider (SWPRV) | Handmatig

### <a name="validate-windows-server-backup-status"></a>Status Windows Server Back-up valideren

Voer de Windows Server Back-up uit om de status te valideren:

- Zorg ervoor dat WSB PowerShell wordt uitgevoerd

  - Voer uit vanuit een PowerShell met verhoogde bevoegdheid en zorg ervoor dat `Get-WBJob` de volgende fout niet wordt weergegeven:

    > [!WARNING]
    > Get-WBJob: de term 'Get-WBJob' wordt niet herkend als de naam van een cmdlet, functie, scriptbestand of programma. Controleer de spelling van de naam of als er een pad is opgenomen, controleer of het pad juist is en probeer het opnieuw.

    - Als deze fout mislukt, installeert u de Windows Server Back-up opnieuw op de servermachine, zoals vermeld in stap 1 van de vereisten.

  - Zorg ervoor dat de WSB-back-up goed werkt door de volgende opdracht uit te voeren vanaf een opdrachtprompt met verhoogde opdracht:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Vervang X door de stationletter van het volume waar u de systeemtoestand wilt opslaan back-up installatie afbeelding.

    - Controleer regelmatig de status van de taak door de opdracht `Get-WBJob` uit te voeren vanuit PowerShell met verhoogde bevoegdheid
    - Nadat de back-up is voltooid, controleert u de definitieve status van de taak door de opdracht uit `Get-WBJob -Previous 1` te voeren

Als de taak mislukt, duidt dit op een WSB-probleem dat tot een fout in de systeemtoestandback-ups van de MARS-agent zou leiden.

## <a name="common-errors"></a>Algemene fouten

### <a name="vss-writer-timeout-error"></a>Time-outfout voor VSS Writer

| Symptoom | Oorzaak | Oplossing
| -- | -- | --
| - Mars-agent mislukt met foutbericht: 'WSB-taak is mislukt met VSS-fouten. VsS-gebeurtenislogboeken controleren om de fout op te lossen<br/><br/> - Het volgende foutenlogboek is aanwezig in de gebeurtenislogboeken van de VSS-toepassing: 'A VSS writer has rejected an event with error 0x800423f2, the writer's timeout expired between the Freeze and Thaw events.' (Een VSS-schrijver heeft een gebeurtenis geweigerd met fout 0x800423f2, de time-out van de schrijver is verlopen tussen de gebeurtenissen Freeze en Thaw.| VSS Writer kan niet op tijd worden voltooid vanwege een gebrek aan CPU- en geheugenbronnen op de computer <br/><br/> Een andere back-upsoftware maakt al gebruik van de VSS Writer, omdat een momentopnamebewerking voor deze back-up niet kan worden voltooid | Wacht tot de CPU/het geheugen op het systeem is vrij, of afbreken de processen die te veel geheugen/CPU gebruiken en probeer de bewerking opnieuw. <br/><br/>  Wacht tot de lopende back-up is voltooid en probeer de bewerking op een later moment uit wanneer er geen back-ups worden uitgevoerd op de computer.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Onvoldoende schijfruimte om schaduwkopielen te maken

| Symptoom | Oplossing
| -- | --
| - MARS-agent mislukt met foutbericht: Back-up is mislukt omdat het volume van de schaduwkopie niet kan groeien vanwege onvoldoende schijfruimte op volumes met systeembestanden <br/><br/> - Het volgende fout-/waarschuwingslogboek is aanwezig in de gebeurtenislogboeken van het Volsnap-systeem: "Er is onvoldoende schijfruimte op volume C: om de schaduwkopieopslag voor schaduwkopielen van C te laten groeien: als gevolg van deze fout lopen alle schaduwkopieen van volume C: het risico te worden verwijderd" | - Ruimte vrij maken in het gemarkeerde volume in het gebeurtenislogboek, zodat er voldoende ruimte is om schaduwkopielen te laten groeien terwijl de back-up wordt uitgevoerd <br/><br/> - Tijdens het configureren van schaduwkopieruimte kunnen we de hoeveelheid ruimte die wordt gebruikt voor schaduwkopie beperken. Zie dit artikel voor meer [informatie](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>EFI-partitie vergrendeld

| Symptoom | Oplossing
| -- | --
| Mars-agent mislukt met foutbericht: 'Back-up van systeemtoestand is mislukt omdat de EFI-systeempartitie is vergrendeld. Dit kan het gevolg zijn van toegang tot systeempartities door een beveiliging van derden of het maken van back-upsoftware' | - Als het probleem wordt veroorzaakt door een beveiligingssoftware van derden, moet u contact opnemen met de leverancier van het antivirusprogramma, zodat deze de MARS-agent kan toestaan <br/><br/> - Als er back-upsoftware van derden wordt uitgevoerd, wacht u tot deze is uitgevoerd en maakt u vervolgens een nieuwe back-up

## <a name="next-steps"></a>Volgende stappen

- Zie Back-up maken van Windows Server-systeemtoestand Resource Manager windows-systeemtoestand voor meer informatie over de [windows-systeemtoestand](backup-azure-system-state.md) in Resource Manager implementatie
