---
title: Blauwe scherm fouten bij het opstarten van een Azure-VM | Microsoft Docs
description: Informatie over het oplossen van het probleem dat de fout met het blauwe scherm wordt ontvangen bij het opstarten | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 9a95ddf882e5edba9daa8ff91c02d1df1f50bceb
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632973"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows toont een blauw scherm bij het opstarten van een Azure VM
In dit artikel worden blauwe scherm fouten beschreven die kunnen optreden wanneer u een virtuele Windows-machine (VM) opstart in Microsoft Azure. Het bevat stappen om u te helpen bij het verzamelen van gegevens voor een ondersteunings ticket. 


## <a name="symptom"></a>Symptoom 

Een Windows-VM start niet. Wanneer u de opstart scherm afbeeldingen in [Diagnostische gegevens over opstarten](./boot-diagnostics.md)controleert, wordt een van de volgende fout berichten weer gegeven in een blauw scherm:

- Er is een probleem opgetreden in onze PC en opnieuw moet worden opgestart. Er worden alleen fout gegevens verzameld en vervolgens kunt u de computer opnieuw opstarten.
- Er is een probleem opgetreden op de PC en opnieuw moet worden opgestart.

In deze sectie vindt u de algemene fout berichten die u kunt tegen komen bij het beheren van virtuele machines:

## <a name="cause"></a>Oorzaak

Er kunnen verschillende redenen zijn waarom u een Stop-fout ontvangt. De meest voorkomende oorzaken zijn:

- Probleem met een stuur programma
- Beschadigd systeem bestand of geheugen
- Een toepassing heeft toegang tot een niet-toegestane sector van het geheugen

## <a name="collect-memory-dump-file"></a>Geheugen dump bestand verzamelen

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Als u dit probleem wilt oplossen, moet u eerst het dump bestand voor de crash verzamelen en contact opnemen met de ondersteuning bij het dump bestand. Voer de volgende stappen uit om het dump bestand te verzamelen:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteem schijf koppelen aan een herstel-VM

1. Maak een moment opname van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.
2. [Koppel de besturingssysteem schijf aan een herstel-VM](./troubleshoot-recovery-disks-portal-windows.md). 
3. Extern bureau blad naar de herstel-VM.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Dump bestand zoeken en een ondersteunings ticket verzenden

1. Ga op de herstel-VM naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stuur programma-letter die is toegewezen aan de gekoppelde besturingssysteem schijf F is, moet u naar F:\Windows.
2. Zoek het bestand Memory. dmp en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het dump bestand. 

Als u het dump bestand niet kunt vinden, gaat u naar de volgende stap om dump logboek en seriële console in te scha kelen.

### <a name="enable-dump-log-and-serial-console"></a>Dump logboek en seriële console inschakelen

Voer het volgende script uit om dump logboek en seriële console in te scha kelen.

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).
2. Voer het volgende script uit:

    In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf F is.  Vervang deze door de juiste waarde in uw VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Zorg ervoor dat er voldoende ruimte op de schijf is om zoveel geheugen toe te wijzen als het RAM-geheugen, afhankelijk van de grootte die u voor deze VM selecteert.
    2. Als er onvoldoende ruimte is of als dit een virtuele machine met een grote grootte is (G, GS of E-serie), kunt u vervolgens de locatie wijzigen waar dit bestand wordt gemaakt en naar elke andere gegevens schijf die is gekoppeld aan de virtuele machine. Hiervoor moet u de volgende sleutel wijzigen:

    ```config-reg
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

    reg unload HKLM\BROKENSYSTEM
    ```

3. [Ontkoppel de besturingssysteem schijf en koppel de besturingssysteem schijf opnieuw aan de betreffende VM](./troubleshoot-recovery-disks-portal-windows.md).
4. Start de virtuele machine om het probleem te reproduceren. vervolgens wordt er een dump bestand gegenereerd.
5. Koppel de besturingssysteem schijf aan een herstel-VM, verzamel het dump bestand en [verzend vervolgens een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het dump bestand.
