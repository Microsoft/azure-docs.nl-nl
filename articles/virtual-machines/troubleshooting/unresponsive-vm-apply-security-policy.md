---
title: De Azure-VM reageert niet tijdens het Toep assen van het beveiligings beleid op het systeem
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij het laad scherm vastloopt wanneer de virtuele machine niet reageert tijdens het Toep assen van beveiligings beleid op het systeem in een Azure-VM.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 047c8afbfe7b489e5c3ac0ccb677f6fc021443a8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632636"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>De Azure-VM reageert niet tijdens het Toep assen van het beveiligings beleid op het systeem

Dit artikel bevat stappen voor het oplossen van problemen waarbij het besturings systeem vastloopt en reageert niet meer tijdens het Toep assen van een beveiligings beleid in een Azure-VM.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](boot-diagnostics.md) gebruikt om de scherm opname van de VM weer te geven, ziet u dat de scherm opname het besturings systeem bevat dat is vastgelopen tijdens het opstarten van het bericht:

> Het beveiligings beleid wordt toegepast op het systeem.

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Scherm opname van het opstart scherm van Windows Server 2012 R2 is vastgelopen.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Scherm opname van het opstart scherm van het besturings systeem is vastgelopen.":::

## <a name="cause"></a>Oorzaak

Er is een verdwaald aan mogelijke oorzaken van dit probleem. U kunt de bron pas zien nadat er een geheugen dump analyse is uitgevoerd.

## <a name="resolution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. [Een herstel-VM maken en openen](#create-and-access-a-repair-vm)
2. [Seriële console-en geheugen dump verzameling inschakelen](#enable-serial-console-and-memory-dump-collection)
3. [De virtuele machine opnieuw bouwen](#rebuild-the-vm)
4. [Het geheugen dump bestand verzamelen](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om een herstel-VM voor te bereiden.
2. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Seriële console-en geheugen dump verzameling inschakelen

Voer dit script uit om geheugen dump verzameling en seriële console in te scha kelen:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).
2. Vermeld de BCD-opslag gegevens en bepaal de id van de opstart lader, die u in de volgende stap gaat gebruiken.

     1. Voor een virtuele machine van de eerste generatie voert u de volgende opdracht in en noteert u de id die wordt weer gegeven:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        Vervang in de opdracht door \<BOOT PARTITON> de letter van de partitie in de gekoppelde schijf die de opstartmap bevat.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Diagram toont de uitvoer van het weer geven van het BCD-archief in een virtuele machine van de eerste generatie, die een lijst onder het id-nummer van Windows Boot Loader bevat.":::

     2. Voor een VM van de tweede generatie voert u de volgende opdracht in en noteert u de id die wordt weer gegeven:

        ```console
        bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
        ```

        - Vervang in de opdracht door \<LETTER OF THE EFI SYSTEM PARTITION> de letter van de EFI-systeem partitie.
        - Het kan handig zijn om de schijf beheer console te starten om de juiste systeem partitie te identificeren als EFI-systeem partitie.
        - De id mag een unieke GUID zijn of de standaard ' BOOTMGR ' zijn.
3. Voer de volgende opdrachten uit om seriële console in te scha kelen:

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

    - Vervang in de opdracht door \<VOLUME LETTER WHERE THE BCD FOLDER IS> de letter van de map BCD.
    - Vervang in de opdracht door \<BOOT LOADER IDENTIFIER> de id die u hebt gevonden in de vorige stap.
4. Controleer of de beschik bare ruimte op de besturingssysteem schijf groter is dan de geheugen grootte (RAM) op de virtuele machine.

    1. Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt. In plaats van het bestand op de besturingssysteem schijf te maken, kunt u dit naar een andere gegevens schijf die is gekoppeld aan de VM met voldoende beschik bare ruimte. Als u de locatie wilt wijzigen, vervangt u '% System root% ' door de stationsletter (bijvoorbeeld ' F: ') van de gegevens schijf in de onderstaande opdrachten.
    2. Voer de onderstaande opdrachten in (aanbevolen dump configuratie):

        Beschadigde besturingssysteem schijf laden:

        ```console
        REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
        ```

        Inschakelen op ControlSet001:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Inschakelen op ControlSet002:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Beschadigde besturingssysteem schijf verwijderen:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.

### <a name="collect-the-memory-dump-file"></a>Het geheugen dump bestand verzamelen

Als u dit probleem wilt oplossen, moet u eerst het geheugen dump bestand voor de crash verzamelen en contact opnemen met ondersteuning met het geheugen dump bestand. Voer de volgende stappen uit om het dump bestand te verzamelen:

1. Koppel de besturingssysteem schijf aan een nieuwe herstel-VM:

    - Gebruik [stap 1-3 van de VM-reparatie opdrachten](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om een nieuwe herstel-VM voor te bereiden.
    - Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

2. Zoek het dumpbestand en dien een ondersteuningsticket in:

    - Ga op de virtuele machine herstellen naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf `F` is, gaat u naar `F:\Windows`.
    - Zoek het bestand Memory. dmp en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand.
    - Als u problemen ondervindt bij het vinden van het bestand Memory. dmp, wilt u in plaats daarvan mogelijk [niet-maskeer bare interrupt-aanroepen (NMI) gebruiken in de seriële console](serial-console-windows.md#use-the-serial-console-for-nmi-calls) . U kunt de hand leiding volgen voor het [genereren van een crash dump bestand met behulp van NMI-aanroepen](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt bij het Toep assen van beleid voor lokale gebruikers en groepen, [is de VM niet meer reageert bij het Toep assen van Groepsbeleid beleid voor lokale gebruikers en groepen](unresponsive-vm-apply-group-policy.md)