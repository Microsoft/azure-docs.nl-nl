---
title: Opstart fout – "Dit is geen opstart bare schijf"
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij de schijf niet kan worden opgestart in een virtuele machine van Azure
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 14da41815e177ece64c72ac27a7cb126e69fdc62
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633185"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Opstart fout: dit is geen opstart bare schijf

Dit artikel bevat stappen voor het oplossen van problemen waarbij de schijf niet kan worden opgestart in een Azure virtual machine (VM).

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname een prompt met het bericht ' Dit is geen opstart bare schijf ' wordt weer gegeven. Plaats een opstart bare diskette en druk op een wille keurige toets om het opnieuw te proberen....

   Afbeelding 1

   ![In afbeelding 1 ziet u het bericht * "Dit is geen opstart bare schijf. Plaats een opstart bare diskette en druk op een wille keurige toets om het opnieuw te proberen... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Oorzaak

Dit fout bericht betekent dat het opstart proces van het besturings systeem kan geen actieve systeem partitie vinden. Deze fout kan ook betekenen dat er een ontbrekende verwijzing is in het archief van de Boot Configuration Data (BCD), waardoor de Windows-partitie niet kan worden gevonden.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. Een herstel-VM maken en openen.
2. Stel de partitie status in op actief.
3. Herstel de schijf partitie.
4. **Aanbevolen**: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in.
5. Bouw de oorspronkelijke VM opnieuw op.

   > [!NOTE]
   > Wanneer deze opstart fout optreedt, is het gast besturingssysteem niet operationeel. U kunt problemen oplossen in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
2. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="set-partition-status-to-active"></a>Partitie status instellen op actief

Virtuele machines van de eerste generatie moeten eerst verifiëren dat de besturingssysteem partitie, die het BCD-archief bevat, is gemarkeerd als *actief*. Als u een virtuele machine van de tweede generatie hebt, gaat u verder met het [herstellen van de schijf partitie](#fix-the-disk-partition), omdat de *status* markering is afgeschaft tijdens de volgende generatie.

1. Open een opdracht prompt met verhoogde bevoegdheden *(cmd.exe)*.
2. Voer *Disk Part* in om het hulp programma Disk Part te starten.
3. Voer een *lijst schijf* in om de schijven op het systeem weer te geven en de gekoppelde VHD van het station te identificeren.
4. Zodra de gekoppelde VHD van het besturings systeem is gevonden, voert u de *Sel-schijf #* in om de schijf te selecteren.  Zie afbeelding 2, waarbij schijf 1 de gekoppelde VHD van het besturings systeem is.

   Afbeelding 2

   ![Afbeelding 2 toont het venster * Disk part * met de uitvoer van de lijst schijf opdracht, schijf 0 en schijf 1 die in de tabel worden weer gegeven.  Geeft ook de uitvoer van de opdracht sel disk 1 weer, schijf 1 is de geselecteerde schijf](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Zodra de schijf is geselecteerd, voert u *lijst partitie* in om de partities van de geselecteerde schijf weer te geven.
6. Zodra de opstart partitie is geïdentificeerd, voert u *Sel Partition #* in om de partitie te selecteren.  Normaal gesp roken is de opstart partitie ongeveer 350 MB groot.  Zie afbeelding 3, waarbij partitie 1 de opstart partitie is.

   Afbeelding 3

   ![In afbeelding 3 wordt het venster * Disk part * weer gegeven met de uitvoer van de * List-partitie * opdracht. Partitie 1 en partitie 2 worden in de tabel weer gegeven. Ook wordt de uitvoer van de * sel-partitie 1 *-opdracht weer gegeven wanneer partitie 1 de geselecteerde schijf is.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Voer ' detail Partition ' in om de status van de partitie te controleren. Zie afbeelding 4, waarbij de partitie *actief is: Nee* of afbeelding 5, waarbij de partitie ' actief: Ja ' is.

   Afbeelding 4

   ![In afbeelding 4 wordt het venster * Disk part * weer gegeven met de uitvoer van de * detail partitie * opdracht wanneer partitie 1 is ingesteld op * actief: Nee *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Afbeelding 5

   ![In afbeelding 5 wordt het venster * Disk part * weer gegeven met de uitvoer van de * detail partitie * opdracht, wanneer partitie 1 is ingesteld op * actief: Ja *.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Als de partitie **niet actief** is, voert u *actief* in om de vlag *actief* te wijzigen.
9. Controleer of de status wijziging correct is uitgevoerd door *Details partitie* te typen.

   Afbeelding 6

   ![In afbeelding 6 wordt het venster Disk part weer gegeven met de uitvoer van * opdracht Details Partition * Wanneer partitie 1 is ingesteld op * actief: Ja *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Voer *Exit* in om het hulp programma Disk Part te sluiten en de configuratie wijzigingen op te slaan.

### <a name="fix-the-disk-partition"></a>De schijf partitie herstellen

1. Open een opdracht prompt met verhoogde bevoegdheden (cmd.exe).
2. Gebruik de volgende opdracht om *chkdsk* op de schijven uit te voeren en fouten op te lossen:

   `chkdsk <DRIVE LETTER>: /f`

   Als u de opdracht optie/f toevoegt, worden eventuele fouten op de schijf hersteld. Zorg ervoor dat u vervangt door <DRIVE LETTER> de letter van de gekoppelde VHD van het besturings systeem.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Aanbevolen: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in

Voer het volgende script uit om geheugen dump verzameling en seriële console in te scha kelen:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).
2. Voer de volgende opdrachten uit:

   Seriële console inschakelen

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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
