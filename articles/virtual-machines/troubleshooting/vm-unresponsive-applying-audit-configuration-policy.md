---
title: De virtuele machine reageert niet tijdens het Toep assen van het configuratie beleid voor het controle beleid
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij de virtuele machine (VM) niet meer reageert tijdens het Toep assen van het beleid voor controle beleid, waardoor het opstarten van een Azure-VM wordt voor komen.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: f286542c91ba473d13595d8e8299b1bbd8c93856
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632602"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>De virtuele machine reageert niet tijdens het Toep assen van het configuratie beleid voor het controle beleid

Dit artikel bevat stappen voor het oplossen van problemen waarbij de virtuele machine (VM) niet meer reageert tijdens het Toep assen van het beleid voor controle beleid, waardoor het opstarten van een Azure-VM wordt voor komen.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname wordt weer gegeven dat het besturings systeem (OS) niet meer reageert tijdens het opstarten met het bericht **beleids configuratie beleid Toep assen**.

  ![Het besturings systeem wordt opgestart met het volgende bericht: ' configuratie beleid voor controle beleid Toep assen '](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  ![Het opstarten van het besturings systeem in Windows Server 2012 met het bericht: ' beleid voor controle beleid Toep assen '](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>Oorzaak

Er zijn conflicterende vergren delingen wanneer het beleid probeert oude gebruikers profielen op te schonen.

> [!NOTE]
> Dit geldt alleen voor Windows Server 2012 en Windows Server 2012 R2.

Hier volgt het problematische beleid: *Computerconfiguratie\beleid\beheersjablonen\windows-onderdelen\Extern sjablonen \ systeem/gebruiker Profiles\Delete gebruikers profielen ouder dan het opgegeven aantal dagen bij het opnieuw opstarten van het systeem.*

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. Een herstel-VM maken en openen.
1. Schakel het beleid uit.
1. Schakel seriële console-en geheugen dump verzameling in.
1. Bouw de virtuele machine opnieuw op.
1. Verzamel het geheugen dump bestand en verzend een ondersteunings ticket.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
1. Maak met behulp van Verbinding met extern bureaublad verbinding met de herstel-VM.

### <a name="disable-the-policy"></a>Het beleid uitschakelen

1. Open de **REGI ster-editor** op de virtuele machine herstellen.
1. Zoek de sleutel **HKEY_LOCAL_MACHINE** en selecteer **bestand > laad component** in het menu.

   ![De navigatie in de REGI ster-editor om een Hive te laden.](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - U kunt component laden gebruiken om register sleutels van een offline systeem te laden. In dit geval is het systeem de defecte schijf die is gekoppeld aan de reparatie-VM.
   - Instellingen voor het hele systeem worden opgeslagen op **HKEY_LOCAL_MACHINE** en kunnen worden afgekort tot **HKLM**.

1. Open het bestand op de gekoppelde schijf `\windows\system32\config\SOFTWARE` .

   - Wanneer u wordt gevraagd een naam op te geven, voert u **BROKENSOFTWARE** in.
   - Als u wilt controleren of **BROKENSOFTWARE** is geladen, vouwt u **HKEY_LOCAL_MACHINE** uit en zoekt u naar de toegevoegde **BROKENSOFTWARE** -sleutel.

1. Ga naar **BROKENSOFTWARE** en controleer of de sleutel **CleanupProfiles** bestaat in de geladen component.

   - Als de sleutel bestaat, wordt het **CleanupProfiles** -beleid ingesteld. De waarde vertegenwoordigt het Bewaar beleid dat wordt gemeten in dagen.
   - Als de sleutel niet bestaat, wordt het **CleanupProfiles** -beleid niet ingesteld. In deze situatie gaat u verder met het [verzenden van een ondersteunings ticket met een geheugen dump bestand](#collect-the-memory-dump-file-and-submit-a-support-ticket).

1. Verwijder de **CleanupProfiles** -sleutel met behulp van deze opdracht:

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. Verwijder de **BROKENSOFTWARE** -component met behulp van deze opdracht:

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>De seriële console en geheugen dump verzameling inschakelen

**Aanbevolen**: voordat u de virtuele machine opnieuw bouwt, moet u de seriële console en geheugen dump verzameling inschakelen door het volgende script uit te voeren:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden als beheerder.
1. Vermeld de BCD-opslag gegevens en bepaal de id van de opstart lader, die u in de volgende stap gaat gebruiken.

   1. Voor een virtuele machine van de eerste generatie voert u de volgende opdracht in en noteert u de id die wordt weer gegeven:

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - Vervang in de opdracht door `<BOOT PARTITON>` de letter van de partitie in de gekoppelde schijf die de opstartmap bevat.

        ![In afbeelding 4 ziet u de uitvoer van de vermelding van het BCD-archief in een virtuele machine van de eerste generatie, die wordt vermeld onder Windows boot loader, het id-nummer.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. Voor een VM van de tweede generatie voert u de volgende opdracht in en noteert u de id die wordt weer gegeven:

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - Vervang in de opdracht door `<LETTER OF THE EFI SYSTEM PARTITION>` de letter van de EFI-systeem partitie.
      - Het kan handig zijn om de schijf beheer console te starten om de juiste systeem partitie te identificeren die is aangeduid als **EFI-systeem partitie**.
      - De id mag een unieke GUID zijn of de standaard- **BOOTMGR** zijn.

1. Voer de volgende opdrachten uit:

   **De seriële console inschakelen**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Controleer of de beschik bare ruimte op de besturingssysteem schijf groter is dan de geheugen grootte (RAM) op de virtuele machine.

   Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt en verwijst u naar die locatie naar een gegevens schijf die is gekoppeld aan de VM met voldoende beschik bare ruimte. Als u de locatie wilt wijzigen, vervangt u **% System root%** door de stationsletter van de gegevens schijf, zoals **F:**, in de volgende opdrachten.

   Voorgestelde configuratie voor het inschakelen van de OS-dump:

   **Register component laden vanaf de beschadigde besturingssysteem schijf:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Inschakelen op ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Inschakelen op ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Beschadigde besturingssysteem schijf verwijderen:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-virtual-machine"></a>De virtuele machine opnieuw bouwen

1. Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.

1. Test of uw VM normaal wordt opgestart om te zien of het probleem is opgelost.

   - Als het probleem nog niet is opgelost, gaat u verder met het [verzamelen van een dump bestand en verzendt u een ondersteunings ticket](#collect-the-memory-dump-file-and-submit-a-support-ticket).
   - Als het probleem is opgelost, zijn er geen verdere stappen nood zakelijk.

Als het probleem is opgelost, wordt het beleid nu lokaal uitgeschakeld. Gebruik voor een permanente oplossing niet het CleanupProfiles-beleid op Vm's, omdat hiermee automatisch gebruikers profielen worden verwijderd. Gebruik een andere methode om profiel opschoon bewerkingen uit te voeren, zoals een geplande taak of een script.

**Dit beleid niet gebruiken:** 
 *Machine\Admin Templates\System\User Profiles\Delete gebruikers profielen ouder dan het opgegeven aantal dagen bij het opnieuw opstarten van het systeem.*

### <a name="the-issue-should-now-be-fixed"></a>Het probleem zou nu moeten worden opgelost

Test uw VM om er zeker van te zijn dat deze werkt zoals normaal. Als u nog steeds problemen ondervindt, kunt u door gaan naar de volgende sectie voor meer informatie.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>Het geheugen dump bestand verzamelen en een ondersteunings ticket verzenden

Als u dit probleem wilt oplossen, moet u eerst het geheugen dump bestand voor de crash verzamelen en vervolgens contact opnemen met de ondersteuning met het geheugen dump bestand. Voer de volgende stappen uit om het dump bestand te verzamelen:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De besturingssysteem schijf koppelen aan een nieuwe herstel-VM

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een nieuwe herstel-VM voor te bereiden.
1. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Het dump bestand zoeken en een ondersteunings ticket verzenden

1. Ga op de virtuele machine herstellen naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stuur programma-letter die is toegewezen aan de gekoppelde besturingssysteem schijf, wordt aangeduid als *F*, moet u naar gaan `F:\Windows` .
1. Zoek het `memory.dmp` bestand en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand.
1. Als u problemen ondervindt met het vinden `memory.dmp` van het bestand, gebruikt u in plaats daarvan [niet-maskeer bare INTERRUPT (NMI)-aanroepen in de seriële console](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Volg de hand leiding voor het [genereren van een crash dump bestand met behulp van NMI-aanroepen](/windows/client-management/generate-kernel-or-complete-crash-dump).