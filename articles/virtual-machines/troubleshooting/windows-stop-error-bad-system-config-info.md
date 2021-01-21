---
title: Windows-Stop fout-0x00000074 onjuiste systeem configuratie gegevens
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij Windows niet kan worden opgestart en opnieuw moet worden opgestart vanwege onjuiste systeem configuratie gegevens in een virtuele Azure-machine (VM).
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 8d501bcc745ef19d15564951b8c0f29f9e2678ab
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661303"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows-Stop fout-0x00000074 onjuiste systeem configuratie gegevens

Dit artikel bevat stappen voor het oplossen van problemen waarbij Windows niet kan worden opgestart en opnieuw moet worden opgestart vanwege onjuiste systeem configuratie gegevens in een virtuele Azure-machine (VM).

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname de Windows-stop code **#0x00000074** of **BAD_SYSTEM_CONFIG_INFO** weergeeft.

*Er is een probleem opgetreden op de PC en opnieuw moet worden opgestart. U kunt opnieuw opstarten.* 
 *Ga voor meer informatie over dit probleem en mogelijke oplossingen naar http://windows.com/stopcode* 
 *Als u een ondersteunings medewerker belt, geeft u deze informatie:* 
 *Stop code: BAD_SYSTEM_CONFIG_INFO*

  ![De Windows-stop code 0x00000074, die ook wordt weer gegeven als ' BAD_SYSTEM_CONFIG_INFO '. Windows informeert de gebruiker dat er een probleem is opgetreden in de computer en moet opnieuw worden opgestart.](./media/windows-stop-error-bad-system-config-info/stop-code-0x00000074.png)

## <a name="cause"></a>Oorzaak

De **BAD_SYSTEM_CONFIG_INFO** stop code treedt op als het **systeem** register onderdeel beschadigd lijkt te zijn. Deze fout kan een van de volgende oorzaken hebben:

- De register component is niet goed afgesloten.
- De register component is beschadigd.
- Er ontbreken register sleutels of-waarden.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces:

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. Een herstel-VM maken en openen.
1. Controleer op beschadiging van Hive.
1. Schakel seriële console-en geheugen dump verzameling in.
1. Bouw de virtuele machine opnieuw op.

   > [!NOTE]
   > Als deze fout optreedt, is het gast besturingssysteem (OS) niet operationeel. U kunt problemen oplossen in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
1. Controleer op beschadiging van Hive.
1. Gebruik Verbinding met extern bureaublad om verbinding te maken met de herstel-VM.
1. Kopieer de `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` map en sla deze op in een goede schijf partitie of op een andere veilige locatie. Maak een back-up van deze map als voorzorgsmaatregel, omdat u essentiële register bestanden gaat bewerken. 

   > [!NOTE]
   > Maak een kopie van de `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` map als back-up voor het geval u de wijzigingen die u in het REGI ster aanbrengt, wilt terugdraaien.

### <a name="check-for-hive-corruption"></a>Controleren op beschadiging van Hive

Met de onderstaande instructies kunt u bepalen of de oorzaak is veroorzaakt door beschadiging van een Hive, of dat de component niet goed is afgesloten. Als de component niet goed is gesloten, kunt u het bestand ontgrendelen en uw virtuele machine herstellen.

1. Open de **REGI ster-editor** op de virtuele machine voor herstel. Typ ' REGEDIT ' in de Windows-zoek balk om het bestand te zoeken.
1. Selecteer in de REGI ster-editor **HKEY_LOCAL_MACHINE** om deze te markeren en selecteer vervolgens **bestand > component laden...** in het menu.
1. Blader naar `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` en selecteer **openen**.
1. Wanneer u wordt gevraagd een naam in te voeren, voert u **BROKENSYSTEM** in.

   1. Als de Hive niet kan worden geopend of als deze leeg is, is het onderdeel beschadigd. Als de component is beschadigd, [opent u een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

      ![Er treedt een fout op met de melding dat de REGI ster-editor de Hive niet kan laden.](./media/windows-stop-error-bad-system-config-info/cannot-load-hive-error.png)

   1. Als de component normaal wordt geopend, is de component niet goed afgesloten. Ga verder met stap 5.

1. Als u een Hive wilt herstellen die niet goed is gesloten, markeert u **BROKENSYSTEM** en selecteert u vervolgens **File > Hive verwijderen...** om het bestand te ontgrendelen.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>De seriële console en geheugen dump verzameling inschakelen

**Aanbevolen**: voordat u de virtuele machine opnieuw bouwt, moet u de seriële console en geheugen dump verzameling inschakelen door het volgende script uit te voeren:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden als beheerder.
1. Voer de volgende opdrachten uit:

   **De seriële console inschakelen**:
   
   ```ps
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Controleer of de beschik bare ruimte op de besturingssysteem schijf groter is dan de geheugen grootte (RAM) op de virtuele machine.

   Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt en verwijst u naar die locatie naar een gegevens schijf die is gekoppeld aan de VM met voldoende beschik bare ruimte. Als u de locatie wilt wijzigen, vervangt u **% System root%** door de stationsletter van de gegevens schijf, zoals **F:**, in de volgende opdrachten.

   Voorgestelde configuratie voor het inschakelen van de OS-dump:

   **Register component laden vanaf de beschadigde besturingssysteem schijf:**

   ```ps
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Inschakelen op ControlSet001:**

   ```ps
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Inschakelen op ControlSet002:**

   ```ps
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Beschadigde besturingssysteem schijf verwijderen:**

   ```ps
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.