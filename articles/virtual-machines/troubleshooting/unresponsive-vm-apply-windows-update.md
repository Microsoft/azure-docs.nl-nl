---
title: Azure VM reageert niet met C01A001D-fout bij het Toep assen van Windows Update
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij Windows Update een fout genereert en niet meer reageert op een virtuele Azure-machine.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 55dcd65e901a5ce8665ef8d3a2a29d7943581df9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200716"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>De VM reageert niet met de fout ' C01A001D ' bij het Toep assen van Windows Update

Dit artikel bevat stappen voor het oplossen van problemen waarbij Windows Update (KB) een fout genereert en niet meer reageert op een virtuele Azure-machine.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, wordt de Windows Update (KB) wordt weer gegeven, maar mislukt met fout code: ' C01A001D '.

![Windows Update reageert niet](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Oorzaak

Een kern bestand kan niet worden gemaakt in het bestands systeem. Het besturings systeem kan geen bestanden naar de schijf schrijven.

## <a name="resolution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

1. [Een herstel-VM maken en openen](#create-and-access-a-repair-vm).
2. [Maak ruimte vrij op de harde schijf](#free-up-space-on-the-hard-disk).
3. [Aanbevolen: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Bouw de virtuele machine opnieuw](#rebuild-the-vm)op.

> [!NOTE]
> Als deze fout optreedt, is het gast besturingssysteem niet operationeel. U moet problemen oplossen in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Volg [de stappen 1-3 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
2. Maak verbinding met de herstel-VM met behulp van Verbinding met extern bureaublad.

### <a name="free-up-space-on-the-hard-disk"></a>Ruimte vrijmaken op de harde schijf

Als de schijf niet al 1 TB is, moet u het formaat ervan wijzigen. Zodra de schijf 1 TB is, voert u een schijf opruiming en een defragmentatie van het station uit.

1. Controleer of de schijf vol is. Als de schijf kleiner is dan 1 TB, [kunt u deze uitbreiden tot Maxi maal 1 TB met behulp van Power shell](../windows/expand-os-disk.md).
2. Als de schijf 1 TB is, voert u een schijf opruiming uit.
    - [Ontkoppel de gegevens schijf van de verbroken virtuele machine](../windows/detach-disk.md).
    - [Koppel de gegevens schijf aan een WERKENDE VM](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
    - Gebruik het [hulp programma schijf opruiming](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) om ruimte vrij te maken.
3. Nadat u het formaat hebt gewijzigd en opgeschoond, defragmenteert u het station:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Afhankelijk van het niveau van de fragmentatie kan dit uren duren.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Aanbevolen: voordat u de virtuele machine opnieuw bouwt, schakelt u seriële console-en geheugen dump verzameling in

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).
2. Voer de volgende opdrachten uit:

    Seriële console inschakelen:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Zorg ervoor dat de beschik bare ruimte op de besturingssysteem schijf ten minste gelijk is aan de grootte van het VM-geheugen (RAM).

    Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt en verwijst u het naar een gegevens schijf die is gekoppeld aan de virtuele machine en met voldoende vrije ruimte. Als u de locatie wilt wijzigen, vervangt u door `%SystemRoot%` de stationsletter (bijvoorbeeld ' F: ') van de gegevens schijf in de onderstaande opdrachten:

    **Voorgestelde configuratie van OS-dump inschakelen:**

    Beschadigde besturingssysteem schijf laden:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Inschakelen op ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Inschakelen op ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Beschadigde besturingssysteem schijf verwijderen:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.
