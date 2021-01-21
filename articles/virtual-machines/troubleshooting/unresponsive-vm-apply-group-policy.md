---
title: De virtuele machine van Azure reageert niet wanneer het beleid wordt toegepast
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij het laad scherm niet reageert wanneer een beleid wordt toegepast tijdens het opstarten van een virtuele machine van Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 7160ec9564ede21eab0a205b2d66a7d566639506
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632653"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>De VM reageert niet bij het Toep assen van groepsbeleid beleid voor lokale gebruikers en groepen

Dit artikel bevat stappen voor het oplossen van problemen waarbij het laad scherm niet reageert wanneer een beleid wordt toegepast op een virtuele machine van Azure (VM) tijdens het opstarten.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om een scherm opname van de virtuele machine weer te geven, wordt het scherm vastgeladen met het bericht: ' toep assen Groepsbeleid lokale gebruikers en groeps beleid '.

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Scherm opname van het Toep assen van groepsbeleid voor het laden van lokale gebruikers en groepen (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Scherm opname van het Toep assen van groepsbeleid voor het laden van lokale gebruikers en groepen (Windows Server 2012).":::

## <a name="cause"></a>Oorzaak

Er zijn conflicterende vergren delingen wanneer het beleid probeert oude gebruikers profielen op te schonen.

> [!NOTE]
> Dit geldt alleen voor Windows Server 2012 en Windows Server 2012 R2.

Dit is het problematische beleid:

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. [Een herstel-VM maken en openen](#step-1-create-and-access-a-repair-vm)
1. [Het beleid uitschakelen](#step-2-disable-the-policy)
1. [Seriële console-en geheugen dump verzameling inschakelen](#step-3-enable-serial-console-and-memory-dump-collection)
1. [De virtuele machine opnieuw bouwen](#step-4-rebuild-the-vm)

> [!NOTE]
> Als deze opstart fout optreedt, is het gast besturingssysteem niet operationeel. U moet problemen oplossen in de offline modus.

### <a name="step-1-create-and-access-a-repair-vm"></a>Stap 1: een herstel-VM maken en openen

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om een herstel-VM voor te bereiden.
2. Gebruik Verbinding met extern bureaublad om verbinding te maken met de herstel-VM.

### <a name="step-2-disable-the-policy"></a>Stap 2: het beleid uitschakelen

1. Open de REGI ster-editor op de virtuele machine herstellen.
1. Zoek de sleutel **HKEY_LOCAL_MACHINE** en selecteer   >  **component bestand laden** in het menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="Scherm opname toont de gemarkeerde HKEY_LOCAL_MACHINE en het menu met de Component Load.":::

    - U kunt component laden gebruiken om register sleutels van een offline systeem te laden. In dit geval is het systeem de defecte schijf die is gekoppeld aan de reparatie-VM.
    - De instellingen voor het hele systeem worden opgeslagen op `HKEY_LOCAL_MACHINE` en kunnen worden afgekort tot ' HKLM '.
1. Ga op de gekoppelde schijf naar het `\windows\system32\config\SOFTWARE` bestand en open het.

    1. Wanneer u wordt gevraagd een naam op te geven, voert u BROKENSOFTWARE in.
    1. Als u wilt controleren of BROKENSOFTWARE is geladen, vouwt u **HKEY_LOCAL_MACHINE** uit en zoekt u naar de toegevoegde BROKENSOFTWARE-sleutel.
1. Ga naar BROKENSOFTWARE en controleer of de sleutel CleanupProfile bestaat in de geladen component.

    1. Als de sleutel bestaat, wordt het CleanupProfile-beleid ingesteld. De waarde vertegenwoordigt het Bewaar beleid dat wordt gemeten in dagen. Ga door met het verwijderen van de sleutel.
    1. Als de sleutel niet bestaat, wordt het CleanupProfile-beleid niet ingesteld. [Dien een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)in, met inbegrip van het Memory. dmp-bestand dat zich bevindt in de Windows-map van de gekoppelde besturingssysteem schijf.

1. Verwijder de CleanupProfiles-sleutel met behulp van deze opdracht:

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
1.  Verwijder de BROKENSOFTWARE-component met behulp van deze opdracht:

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Stap 3: seriële console-en geheugen dump verzameling inschakelen

Voer dit script uit om geheugen dump verzameling en de seriële console in te scha kelen:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden. (Als administrator uitvoeren.)
1. Voer deze opdrachten uit om de seriële console in te scha kelen:
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. Controleer of de beschik bare ruimte op de besturingssysteem schijf ten minste gelijk is aan de geheugen grootte (RAM) van de virtuele machine.

    Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie van de geheugen dump en verwijst u deze naar een gekoppelde gegevens schijf met voldoende beschik bare ruimte. Als u de locatie wilt wijzigen, vervangt u '% System root% ' door de stationsletter (bijvoorbeeld ' F: ') van de gegevens schijf in de volgende opdrachten.

    **Voorgestelde configuratie voor het inschakelen van de dump van het besturings systeem**

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

### <a name="step-4-rebuild-the-vm"></a>Stap 4: de virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.

Als het probleem is opgelost, wordt het beleid nu lokaal uitgeschakeld. Gebruik voor een permanente oplossing niet het CleanupProfiles-beleid op Vm's. Gebruik een andere methode om profiel opruimingen uit te voeren.

Dit beleid niet gebruiken:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt bij het Toep assen van Windows Update, raadpleegt u de [virtuele machine reageert niet met de fout ' C01A001D ' bij het Toep assen van Windows Update](./unresponsive-vm-apply-windows-update.md).
