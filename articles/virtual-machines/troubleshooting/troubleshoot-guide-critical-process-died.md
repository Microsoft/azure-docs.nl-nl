---
title: Windows-Stop fout-
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 8279aee4a0df8155b23e6f984d259bf92555dc95
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632830"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows-Stop fout-#0x000000EF kritiek proces is gestorven

Dit artikel bevat stappen voor het oplossen van problemen waarbij een kritiek proces sterft tijdens het opstarten van een Azure-VM.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat in de scherm opname de fout *#0x000000EF* wordt weer gegeven met het *kritieke proces* van het bericht.

![' Er is een probleem opgetreden in de PC en opnieuw moet worden opgestart. Er worden alleen fout gegevens verzameld en vervolgens kunt u de computer opnieuw opstarten. (# #% voltooid) Als u meer wilt weten, kunt u later online zoeken naar deze fout: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Oorzaak

Dit wordt meestal veroorzaakt door een essentieel systeem proces dat niet kan worden opgestart. Meer informatie over kritieke proces problemen vindt u op "[bug check 0xEF: CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces:

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. Een herstel-VM maken en openen.
2. Herstel eventuele beschadiging van het besturings systeem.
3. **Aanbevolen**: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in.
4. Bouw de virtuele machine opnieuw op.

> [!NOTE]
> Wanneer deze opstart fout optreedt, is het gast besturingssysteem niet operationeel. U gaat problemen oplossen in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
2. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="fix-any-os-corruption"></a>Eventuele beschadiging van het besturings systeem herstellen

1. Open een opdracht prompt met verhoogde bevoegdheden.
2. Voer de volgende System File Checker (SFC) opdracht uit:

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Waarbij < opstart schijf station > het opstart volume van de herstel-VM is (meestal C:) en < gebroken schijf station > de stationsletter is voor de gekoppelde schijf van de verbroken virtuele machine. Vervang de symbolen groter dan/kleiner dan en de tekst erin, bijvoorbeeld ' < tekst hier > ', met de juiste letter.

3. Gebruik vervolgens [stap 5 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen en te zien of deze wordt opgestart.
4. Als de virtuele machine nog steeds niet wordt opgestart, gaat u verder met het verzamelen van het geheugen dump bestand.

### <a name="collect-the-memory-dump-file"></a>Het geheugen dump bestand verzamelen

Als het probleem zich blijft voordoen nadat u SFC hebt uitgevoerd, is analyse van een geheugen dump bestand vereist om de oorzaak van het probleem te bepalen. Voer de volgende stappen uit om het geheugen dump bestand te verzamelen:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De besturingssysteem schijf koppelen aan een nieuwe herstel-VM

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een nieuwe herstel-VM voor te bereiden.
2. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Het dump bestand zoeken en een ondersteunings ticket verzenden

3. Ga op de virtuele machine herstellen naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stuur programma-letter die is toegewezen aan de gekoppelde besturingssysteem schijf *F*, gaat u naar *F:\Windows*.
4. Zoek het bestand *Memory. dmp* en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand.

   > [!NOTE]
   > Als u het dump bestand niet kunt vinden, voert u de onderstaande stappen uit om geheugen dump verzameling en seriële console in te scha kelen, gaat u terug naar deze sectie en herhaalt u de stappen in de bovenstaande taak om het geheugen dump bestand te verzamelen.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Aanbevolen: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in

Voer het volgende script uit om geheugen dump verzameling en seriële console in te scha kelen:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).
2. Voer de volgende opdrachten uit:

   Seriële console inschakelen

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Vervang de symbolen groter dan of kleiner dan en de tekst hierin, bijvoorbeeld ' < tekst hier > '.

3. Controleer of de beschik bare ruimte op de besturingssysteem schijf net zo groot is als de geheugen grootte (RAM) op de virtuele machine.

Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt en verwijst naar alle gegevens schijven die zijn gekoppeld aan de VM met voldoende vrije ruimte. Als u de locatie wilt wijzigen, vervangt u '% System root% ' door de stationsletter (bijvoorbeeld ' F: ') van de gegevens schijf in de onderstaande opdrachten.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Voorgestelde configuratie voor het inschakelen van de dump van het besturings systeem

**Beschadigde besturingssysteem schijf laden**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Inschakelen op ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Inschakelen op ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Beschadigde besturingssysteem schijf verwijderen:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>De oorspronkelijke VM opnieuw samen stellen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.
