---
title: Opstart fout oplossen-schijf lees fout opgetreden
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij de schijf niet kan worden gelezen in een Azure-VM.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: 75d1cf8638f922bb0275322568eb1399db4f49e8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629722"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Opstart fout oplossen-schijf lees fout opgetreden

Dit artikel bevat stappen voor het oplossen van problemen waarbij de schijf niet kan worden gelezen op een virtuele machine (VM) van Azure.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname wordt weer gegeven met het bericht ' er is een lees fout opgetreden bij het lezen van de schijf. Druk op Ctrl+Alt+Del om opnieuw op te starten’.

   ![Fout bericht: er is een fout opgetreden bij het lezen van de schijf. Druk op CTRL + ALT + DEL om opnieuw op te starten.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Oorzaak

Dit fout bericht geeft aan dat de schijf structuur beschadigd en onleesbaar is. Als u een virtuele machine van de eerste generatie gebruikt, is het ook mogelijk dat de schijf partitie met de opstart configuratie gegevens niet is ingesteld op **actief**.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. Een herstel-VM maken en openen.
1. Selecteer een oplossing:
   - [Partitie status instellen op actief](#set-partition-status-to-active)
   - [De schijf partitie herstellen](#fix-the-disk-partition)
1. Schakel seriële console-en geheugen dump verzameling in.
1. Bouw de virtuele machine opnieuw op.

> [!NOTE]
> Wanneer deze opstart fout optreedt, is het gast besturingssysteem (OS) niet operationeel. U kunt problemen oplossen in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
1. Maak met behulp van Verbinding met extern bureaublad verbinding met de herstel-VM.

### <a name="set-partition-status-to-active"></a>Partitie status instellen op actief

Virtuele machines van de eerste generatie moeten eerst controleren of de besturingssysteem partitie die de BCD-archief bevat, is gemarkeerd als **actief**. Als u een virtuele machine van de tweede generatie hebt, gaat u verder met het [herstellen van de schijf partitie](#fix-the-disk-partition), omdat de status markering is afgeschaft tijdens de volgende generatie.

1. Open een opdracht prompt met verhoogde bevoegdheden (cmd.exe).
1. Voer **Disk Part** in om het hulp programma **Disk Part** te starten.
1. Voer een **lijst schijf** in om de schijven op het systeem weer te geven en de gekoppelde virtuele harde schijf (VHD) te identificeren.
1. Zodra de gekoppelde VHD van het besturings systeem is gevonden, voert u de **Sel-schijf #** in om de schijf te selecteren. Zie de volgende afbeelding voor een voor beeld van waar schijf 1 de gekoppelde VHD van het besturings systeem is.

   ![Het venster Disk part met de uitvoer van de * * lijst schijf * * opdracht, waarbij schijf 0 en schijf 1 in de tabel worden weer gegeven. In het venster wordt ook de uitvoer van de * * sel-schijf 1 * *-opdracht weer gegeven, waarbij schijf 1 de geselecteerde schijf is](./media/disk-read-error-occurred/2.png)

1. Zodra de schijf is geselecteerd, voert u **lijst partitie** in om de partities van de geselecteerde schijf weer te geven.
1. Zodra de opstart partitie is geïdentificeerd, voert u **Sel Partition #** in om de partitie te selecteren. De opstart partitie is vaak ongeveer 350 MB groot.  Zie de volgende afbeelding bijvoorbeeld, waarbij partitie 1 de opstart partitie is.

   ![Het venster Disk part met de uitvoer van de * * lijst partitie * * opdracht, waarbij partitie 1 en partitie 2 worden weer gegeven in de tabel. In het venster wordt ook de uitvoer van de * * sel-partitie 1 * *-opdracht weer gegeven, waarbij partitie 1 de geselecteerde schijf is.](./media/disk-read-error-occurred/3.png)

1. Voer de **detail partitie** in om de status van de partitie te controleren. Zie de volgende scherm afbeeldingen voor voor beelden van de partitie die is ingesteld op **actief: Nee** of **actief: Ja**.

   **Actief: Nee**

   ![Het venster Disk part met de uitvoer van de * * detail partitie * * opdracht, waarbij partitie 1 is ingesteld op * * Active: No * *.](./media/disk-read-error-occurred/4.png)

   **Actief: Ja**

   ![Het venster Disk part met de uitvoer van de * * detail partitie * * opdracht, waarbij partitie 1 is ingesteld op * * actief: Ja * *.](./media/disk-read-error-occurred/5.png)

1. Als de partitie niet is ingesteld op **actief**, voert u **actief** in om de vlag actief te wijzigen.
1. Voer de **detail partitie** in om te controleren of de status wijziging correct is voltooid en controleer of de uitvoer **actief is: Ja**. 
1. Voer **Exit** in om het hulp programma Disk Part te sluiten en de configuratie wijzigingen op te slaan.

### <a name="fix-the-disk-partition"></a>De schijf partitie herstellen

1. Open een opdracht prompt met verhoogde bevoegdheden (cmd.exe).
1. Gebruik de volgende opdracht om **chkdsk** uit te voeren op de schijven en fout oplossingen uit te voeren:

   `chkdsk <DRIVE LETTER>: /f`

   Als u de **/f** opdracht optie toevoegt, worden eventuele fouten op de schijf hersteld. Zorg ervoor dat **< stationsletter >** vervangen door de letter van de gekoppelde VHD van het besturings systeem.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>De seriële console en geheugen dump verzameling inschakelen

**Aanbevolen**: voordat u de virtuele machine opnieuw bouwt, moet u de seriële console en geheugen dump verzameling inschakelen door het volgende script uit te voeren:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden als beheerder.
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
   
### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.
