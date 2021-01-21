---
title: Het opstarten van de VM is vastgelopen op het ophalen van Windows Ready. Uw computer niet uitschakelen in azure | Microsoft Docs
description: Voer de stappen uit om het probleem op te lossen waarbij het opstarten van de VM vastloopt op ' Windows voorbereiden '. Schakel de computer niet uit.'
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: f3e4d51b4d41fa0dc23e9b12ac0251c14215de5c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633007"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Het opstarten van de VM is vastgelopen op het ophalen van Windows Ready. Uw computer niet uitschakelen in azure

In dit artikel vindt u een beschrijving van de schermen ' Getting Started ' en ' getting Starting Windows Ready ' die kunnen optreden wanneer u een virtuele Windows-machine (VM) opstart in Microsoft Azure. Het bevat stappen om u te helpen bij het verzamelen van gegevens voor een ondersteunings ticket.

 

## <a name="symptoms"></a>Symptomen

Een Windows-VM wordt niet opgestart. Wanneer u **Diagnostische gegevens over opstarten** gebruikt om de scherm opname van de virtuele machine op te halen, ziet u mogelijk dat de VM het bericht ' getting readyed ' of ' getting Windows Ready ' weergeeft.

![Voorbeeld bericht voor Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Voorbeeld bericht](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Oorzaak

Dit probleem treedt meestal op wanneer de server de laatste keer opnieuw wordt opgestart nadat de configuratie is gewijzigd. De configuratie wijziging kan worden geïnitialiseerd door Windows-updates of door de wijzigingen in de functies/functie van de server. Als de grootte van de updates groot is, heeft het besturings systeem meer tijd nodig om de wijzigingen opnieuw te configureren. Windows Update

## <a name="collect-an-os-memory-dump"></a>Een dump van het besturings systeem verzamelen

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Als het probleem niet wordt opgelost nadat u hebt gewacht tot de wijzigingen zijn verwerkt, moet u een geheugen dump bestand verzamelen en contact opnemen met de ondersteuning. Voer de volgende stappen uit om het dump bestand te verzamelen:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteem schijf koppelen aan een herstel-VM

1. Maak een moment opname van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.
2. [Koppel de besturingssysteem schijf aan een herstel-VM](./troubleshoot-recovery-disks-portal-windows.md).
3. Extern bureau blad naar de herstel-VM. 
4. Als de besturingssysteem schijf is versleuteld, moet u de versleuteling uitschakelen voordat u verdergaat met de volgende stap. Zie [de versleutelde besturingssysteem schijf ontsleutelen in de virtuele machine die niet kan worden opgestart](troubleshoot-bitlocker-boot-error.md#solution)voor meer informatie.

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
    
        ```console
        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

        reg unload HKLM\BROKENSYSTEM
        ```

3. [Ontkoppel de besturingssysteem schijf en koppel de besturingssysteem schijf opnieuw aan de betreffende VM](./troubleshoot-recovery-disks-portal-windows.md).
4. Start de VM en open de seriële console.
5. Selecteer **niet-maskeer bare interrupt (NMI) verzenden** om de geheugen dump te activeren.
    ![de afbeelding over waar de niet-maskeer bare interrupt moet worden verzonden](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Koppel de besturingssysteem schijf weer aan een herstel-VM en probeer het dump bestand te verzamelen.

## <a name="contact-microsoft-support"></a>Contact opnemen met Microsoft Ondersteuning

Nadat u het dump bestand hebt verzameld, neemt u contact op met [micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om de hoofd oorzaak te analyseren.
