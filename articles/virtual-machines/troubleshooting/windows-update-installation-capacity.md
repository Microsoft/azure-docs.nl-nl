---
title: Problemen oplossen met opstarten vanaf een besturings systeem – Windows Update installatie capaciteit
description: Stappen voor het oplossen van problemen waarbij Windows Update (KB) een fout krijgt en niet meer reageert in een Azure-VM.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 0c0ec45eee86031e1533b97ccf352de0ecf70e38
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633151"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Problemen oplossen met opstarten vanaf een besturings systeem – Windows Update installatie capaciteit

Dit artikel bevat stappen voor het oplossen van problemen in een Azure virtual machine (VM) waar Windows Update (KB) een fout krijgt en niet meer reageert.

## <a name="symptom"></a>Symptoom

Wanneer u Diagnostische gegevens over opstarten gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname wordt weer gegeven Windows Update (KB) in uitvoering, maar mislukt met de fout code: **C01A001D**. In de volgende afbeelding ziet u Windows Update (KB) vastgelopen in het bericht ' fout C01A001D bij het Toep assen van de update bewerking # # # # # # # # # # (# # # # # #) ':

![Windows Update (KB) is vastgelopen in het bericht ' fout C01A001D bij het Toep assen van update bewerking X van Y (Z) '.](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Oorzaak

In dit geval kan het besturings systeem (OS) de installatie van een Windows Update (KB) niet volt ooien omdat een kern bestand niet kan worden gemaakt op het bestands systeem. Op basis van deze foutcode kan het besturingssysteem geen bestanden naar de schijf schrijven.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces:

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. Een herstel-VM maken en openen.
1. Vrije ruimte op schijf.
1. Schakel seriële console-en geheugen dump verzameling in.
1. Bouw de virtuele machine opnieuw op.

> [!NOTE]
> Als deze fout optreedt, is het gast besturingssysteem niet operationeel. Los dit probleem op in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
1. Maak met behulp van Verbinding met extern bureaublad verbinding met de herstel-VM.

### <a name="free-up-space-on-the-disk"></a>Ruimte vrijmaken op de schijf

Om het probleem op te lossen:

- Wijzig de grootte van de schijf tot 1 TB als deze nog niet is ingesteld op de maximum grootte van 1 TB.
- Voer een schijf opruiming uit.
- Defragmenteer het station.

1. Controleer of de schijf vol is. Als de schijf kleiner is dan 1 TB, kunt u deze uitbreiden tot Maxi maal 1 TB [met behulp van Power shell](../windows/expand-os-disk.md).
1. Als de schijf al 1 TB is, moet u een schijf opruiming uitvoeren.
   1. Gebruik het [hulp programma schijf opruiming](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) om ruimte vrij te maken.
1. Nadat het formaat is gewijzigd en het opschonen is voltooid, kunt u het station ontfragmenten met de volgende opdracht:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
Afhankelijk van het niveau van de fragmentatie kan de defragmentatie enkele uren in beslag nemen.

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

    **De beschadigde besturingssysteem schijf laden:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Inschakelen op ControlSet001**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Inschakelen op ControlSet002**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Beschadigde besturingssysteem schijf verwijderen**:

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.
