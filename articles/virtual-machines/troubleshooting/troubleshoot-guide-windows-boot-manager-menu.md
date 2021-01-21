---
title: Virtuele Windows-machine kan niet worden opgestart vanwege Windows-opstart beheer
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij Windows Boot Manager het opstarten van een virtuele machine van Azure voor komt.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5f83f4871d5cde23194ff51a90a22031b526cf91
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632560"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows-VM kan niet worden opgestart vanwege Windows-opstart beheer

Dit artikel bevat stappen om problemen op te lossen waarbij Windows Boot Manager het opstarten van een virtuele Azure-machine (VM) niet kan voor komen.

## <a name="symptom"></a>Symptoom

De virtuele machine wacht op een prompt van de gebruiker en wordt niet opgestart, tenzij hand matig wordt geïnstrueerd.

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat in de scherm opname de Windows Boot Manager wordt weer gegeven met het bericht *Kies een besturings systeem dat moet worden gestart of druk op TAB om een hulp programma te selecteren:*.

Afbeelding 1
 
![Windows Boot Manager ' Kies een besturings systeem dat moet worden gestart of druk op TAB om een hulp programma te selecteren: '](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Oorzaak

De fout wordt veroorzaakt door een BCD-vlag *displaybootmenu* in het Windows-opstart beheer. Wanneer de markering is ingeschakeld, vraagt Windows-opstart beheer de gebruiker tijdens het opstart proces om te selecteren welke loader hij wil uitvoeren, waardoor een opstart vertraging optreedt. In azure kan deze functie worden toegevoegd aan de tijd die nodig is om een virtuele machine op te starten.

## <a name="solution"></a>Oplossing

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Overzicht van het proces:

1. Configureer voor een snellere opstart tijd met behulp van seriële console.
2. Een herstel-VM maken en openen.
3. Configureer voor een snellere opstart tijd op een herstel-VM.
4. **Aanbevolen**: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in.
5. Bouw de virtuele machine opnieuw op.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Configureren voor een snellere opstart tijd met seriële console

Als u toegang hebt tot de seriële console, zijn er twee manieren om sneller opstart tijd te verkrijgen. Verlaag de *displaybootmenu* wacht tijd of verwijder de markering helemaal.

1. Volg de instructies om toegang te krijgen tot de [Azure Serial console voor Windows](./serial-console-windows.md) om toegang te krijgen tot de op tekst gebaseerde console.

   > [!NOTE]
   > Als u geen toegang hebt tot de seriële console, gaat u verder met [het maken en openen van een herstel-VM](#create-and-access-a-repair-vm).

2. **Optie A**: wacht tijd verminderen

   a. De wacht tijd is standaard ingesteld op 30 seconden, maar kan worden gewijzigd in een snellere tijd (bijvoorbeeld 5 seconden).

   b. Gebruik de volgende opdracht in de seriële console om de time-outwaarde aan te passen:

      `bcdedit /set {bootmgr} timeout 5`

3. **Optie B**: de BCD-vlag verwijderen

   a. Voer de volgende opdracht in om te voor komen dat de prompt voor het opstart scherm van de weer gave wordt gebruikt:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Als u een seriële console niet kunt gebruiken om een snellere opstart tijd in de bovenstaande stappen te configureren, kunt u in plaats daarvan door gaan met de volgende stappen. U kunt nu problemen oplossen in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
2. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Configureren voor een snellere opstart tijd op een herstel-VM

1. Open een opdracht prompt met verhoogde bevoegdheden.
2. Voer het volgende in om DisplayBootMenu in te scha kelen:

   Gebruik deze opdracht voor **virtuele machines van de eerste generatie**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Gebruik deze opdracht voor **virtuele machines van de tweede generatie**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Vervang de symbolen groter dan of kleiner dan en de tekst hierin, bijvoorbeeld ' < tekst hier > '.

3. Wijzig de time-outwaarde in 5 seconden:

   Gebruik deze opdracht voor **virtuele machines van de eerste generatie**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Gebruik deze opdracht voor **virtuele machines van de tweede generatie**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Vervang de symbolen groter dan of kleiner dan en de tekst hierin, bijvoorbeeld ' < tekst hier > '.

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
