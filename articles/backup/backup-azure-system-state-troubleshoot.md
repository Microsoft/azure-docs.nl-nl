---
title: Problemen met de systeem status back-up oplossen
description: In dit artikel vindt u informatie over het oplossen van problemen met de systeem status back-up voor on-premises Windows-servers.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 7c8e68da1c5da7b25d1385a82bf7dcc2f876306d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89376278"
---
# <a name="troubleshoot-system-state-backup"></a>Problemen met de systeem status back-up oplossen

In dit artikel worden oplossingen beschreven voor problemen die kunnen optreden tijdens het gebruik van systeem status back-up.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

U wordt aangeraden de volgende validatie stappen uit te voeren voordat u begint met het oplossen van problemen met de systeem status back-up:

- [Controleren of de MARS-agent (Microsoft Azure Recovery Services) up-to-date is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zorg ervoor dat er een netwerk verbinding is tussen MARS-agent en Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Controleer of Microsoft Azure Recovery Services wordt uitgevoerd (in Service-console). Start indien nodig opnieuw op en voer de bewerking opnieuw uit
- [Zorg ervoor dat er meer dan 5-10% vrije volume ruimte beschikbaar is op de locatie van de Scratch map](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Controleer of er geen ander proces of antivirussoftware conflicten veroorzaakt met Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Geplande back-up mislukt, maar handmatige back-up werkt](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Zorg ervoor dat uw besturingssysteem helemaal is bijgewerkt
- [Zorg ervoor dat niet-ondersteunde stations en bestanden met niet-ondersteunde kenmerken worden uitgesloten van een back-up](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Zorg ervoor dat de **systeem klok** op het beveiligde systeem is geconfigureerd om tijd zone te corrigeren <br>
- [Zorg ervoor dat op de server ten minste .Net Framework-versie 4.5.2 of hoger is geïnstalleerd](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Als u **de server** opnieuw wilt registreren bij een kluis, gaat u als volgt te werk: <br>
  - Controleer of de agent op de server is verwijderd en of deze uit de portal is gewist <br>
  - Dezelfde wachtwoordzin gebruiken die in eerste instantie is gebruikt voor het registreren van de server <br>
- Als dit een offline back-up is, controleert u of Azure PowerShell versie 3.7.0 is geïnstalleerd op zowel de bron-als de Kopieer computer voordat u een offline back-upbewerking start
- [Overweging wanneer back-upagent wordt uitgevoerd op een virtuele machine van Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Beperking

- Herstellen naar verschillende hardware met systeem status herstel wordt niet aanbevolen door micro soft
- Systeem status back-up ondersteunt momenteel ' on-premises ' Windows-servers. Deze functionaliteit is niet beschikbaar voor virtuele Azure-machines.

## <a name="prerequisites"></a>Vereisten

Voordat er problemen met de systeem status back-up met Azure Backup worden opgelost, moet u de volgende controle van vereisten uitvoeren.  

### <a name="verify-windows-server-backup-is-installed"></a>Controleren of de Windows Server Back-up is geïnstalleerd

Zorg ervoor dat Windows Server Back-up is geïnstalleerd en is ingeschakeld op de server. Als u de installatie status wilt controleren, voert u deze Power shell-opdracht uit:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Als voor de uitvoer de **installatie status** **beschikbaar** wordt weer gegeven, betekent dit dat Windows Server back-upfunctie beschikbaar is voor de installatie, maar niet is geïnstalleerd op de server. Als Windows Server Back-up echter niet is geïnstalleerd, gebruikt u een van de onderstaande methoden om het te installeren.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Methode 1: Windows Server Back-up installeren met behulp van Power shell

Als u Windows Server Back-up wilt installeren met behulp van Power shell, voert u de volgende opdracht uit:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Methode 2: Windows Server Back-up installeren met behulp van Serverbeheer

Als u Windows Server Back-up wilt installeren met behulp van Serverbeheer, voert u de volgende stappen uit:

1. Selecteer in **Server** beheer de optie **functies en onderdelen toevoegen**. De **wizard functies en onderdelen toevoegen** wordt weer gegeven.

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selecteer **installatie type** en selecteer **volgende**.

    ![Type installatie](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selecteer een server uit de Server groep en selecteer **volgende**. In de serverrol, behoud de standaard selectie en selecteer **volgende**.
4. Selecteer **Windows Server back-up** op het tabblad **functies** en selecteer **volgende**.

    ![Het venster onderdelen selecteren](./media/backup-azure-system-state-troubleshoot/features.png)

5. Op het tabblad **bevestiging** selecteert u **installeren** om het installatie proces te starten.
6. Op het tabblad **resultaten** wordt weer gegeven dat de functie Windows Server back-up is geïnstalleerd op uw Windows-Server.

    ![Resultaten van de installatie](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Machtiging voor systeem volume gegevens

Zorg ervoor dat het lokale systeem volledig beheer heeft over de map **systeem volume gegevens** op het volume waarop Windows is geïnstalleerd. Dit is doorgaans **C:\System Volume gegevens**. Windows Server back-up kan mislukken als de bovenstaande machtigingen niet juist zijn ingesteld.

### <a name="dependent-services"></a>Afhankelijke services

Zorg ervoor dat de onderstaande services de status actief hebben:

**Servicenaam** | **Opstart type**
--- | ---
Externe procedure aanroep (RPC) | Automatisch
COM+-gebeurtenis systeem (Event System) | Automatisch
Systeem gebeurtenis meldings service (SENS) | Automatisch
Volume Shadow Copy (VSS) | Handmatig
Micro soft software Shadow Copy Provider (SWPRV) | Handmatig

### <a name="validate-windows-server-backup-status"></a>Windows Server Back-up status valideren

Voer de volgende stappen uit om Windows Server Back-up status te valideren:

- Zorg ervoor dat WSB Power shell wordt uitgevoerd

  - Voer `Get-WBJob` uit vanuit een Power shell met verhoogde bevoegdheid en zorg ervoor dat de volgende fout niet wordt weer gegeven:

    > [!WARNING]
    > Get-WBJob: de term Get-WBJob wordt niet herkend als de naam van een cmdlet, functie, script bestand of programma. Controleer of de naam juist is gespeld of Controleer of het pad correct is en probeer het opnieuw.

    - Als deze fout is opgetreden, installeert u de functie Windows Server Back-up op de Server computer, zoals vermeld in stap 1 van de vereisten.

  - Zorg ervoor dat WSB-back-up correct werkt door de volgende opdracht uit te voeren vanaf een opdracht prompt met verhoogde bevoegdheid:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Vervang X door de stationsletter van het volume waar u de systeem status back-upinstallatie kopie wilt opslaan.

    - Regel matig de status van de taak controleren door de `Get-WBJob` opdracht uit te voeren vanuit een verhoogde Power shell
    - Nadat de back-uptaak is voltooid, controleert u de uiteindelijke status van de taak door de opdracht uit te voeren `Get-WBJob -Previous 1`

Als de taak mislukt, wordt een WSB-probleem aangegeven dat kan leiden tot storingen van de systeem status van de MARS-agent.

## <a name="common-errors"></a>Algemene fouten

### <a name="vss-writer-timeout-error"></a>Time-outfout voor VSS Writer

| Symptoom | Oorzaak | Oplossing
| -- | -- | --
| -MARS-agent mislukt met fout bericht: de WSB-taak is mislukt met VSS-fouten. Controleer de VSS-gebeurtenis Logboeken om de fout op te lossen<br/><br/> -Het volgende fouten logboek is aanwezig in de logboeken van de VSS-toepassings gebeurtenis: "een VSS Writer heeft een gebeurtenis met fout 0x800423f2 afgewezen, de time-out van de schrijver is verlopen tussen gebeurtenissen voor blok keren en ontdooien."| VSS Writer kan niet tijdig worden voltooid vanwege een gebrek aan CPU-en geheugen resources op de computer <br/><br/> De VSS Writer wordt al gebruikt door een andere back-upsoftware omdat een moment opname bewerking niet kan worden voltooid voor deze back-up | Wacht tot de CPU/het geheugen is vrijgemaakt op het systeem of afbreek de processen die te veel geheugen/CPU nemen, en probeer het opnieuw. <br/><br/>  Wacht totdat de huidige back-up is voltooid en voer de bewerking op een later tijdstip uit wanneer er geen back-ups op de computer worden uitgevoerd.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Onvoldoende schijf ruimte om schaduw kopieën te verg Roten

| Symptoom | Oplossing
| -- | --
| -MARS-agent mislukt met fout bericht: de back-up is mislukt omdat het schaduw kopie volume niet kan worden verg root vanwege onvoldoende schijf ruimte op volumes met systeem bestanden <br/><br/> -Het volgende fout/waarschuwings logboek is aanwezig in de logboeken met betrekking tot systeem gebeurtenissen: ' er is onvoldoende schijf ruimte op het volume C: voor het verg Roten van de schaduw kopie opslag voor schaduw kopieën van C: vanwege deze fout zijn alle schaduw kopieën van volume C: risico dat ze worden verwijderd. | -Maak ruimte vrij in het gemarkeerde volume in het gebeurtenis logboek, zodat er voldoende ruimte is voor het uitbreiden van schaduw kopieën terwijl de back-up wordt uitgevoerd <br/><br/> -Tijdens het configureren van de schaduw kopie ruimte kunnen we de hoeveelheid ruimte beperken die wordt gebruikt voor de schaduw kopie. Raadpleeg dit [artikel](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage) voor meer informatie

### <a name="efi-partition-locked"></a>EFI-partitie is vergrendeld

| Symptoom | Oplossing
| -- | --
| MARS-agent mislukt met fout bericht: systeem status back-up is mislukt omdat de EFI-systeem partitie is vergrendeld. Dit kan worden veroorzaakt door de toegang tot de systeem partitie door de beveiliging van derden of het maken van een back-up van software. | -Als het probleem wordt veroorzaakt door een beveiligings software van derden, moet u contact opnemen met de leverancier van het anti virus zodat ze MARS-agent kunnen toestaan <br/><br/> -Als er een back-upsoftware van derden wordt uitgevoerd, wacht dan tot deze is voltooid en probeer het vervolgens opnieuw.

## <a name="next-steps"></a>Volgende stappen

- Zie [een back-up maken van de systeem status van Windows Server](backup-azure-system-state.md) voor meer informatie over de systeem status van Windows in de implementatie van Resource Manager.
