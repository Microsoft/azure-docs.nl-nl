---
title: Windows-stopfout - Hardwarestoring
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij virtuele Windows Server 2008-machines vastlopen met een fout bericht dat aangeeft dat er een hardwarestoring is opgetreden.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 89faa5b29e0a972f31ad51a7354635a53176541a
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661354"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows-stopfout - Hardwarestoring

Dit artikel bevat stappen voor het oplossen van problemen waarbij virtuele Windows Server 2008-machines vastlopen met een fout bericht dat aangeeft dat er een hardwarestoring is opgetreden.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname een blauw scherm met het bericht bevat:

**\*\*\* Hardwarestoring**

**Neem contact op met de leverancier voor ondersteuning**

**\*\*\* Het systeem is gestopt \*\*\***

#### <a name="blue-screen"></a>Blauw scherm

![In de scherm afbeelding ziet u een blauwe scherm fout bij het vastlopen van hardware.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Seriële console

![In de scherm afbeelding ziet u het bericht ' hardwarestoring ' op de seriële console-functie als de seriële console is ingeschakeld.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Oorzaak

Dit scherm wordt weer gegeven wanneer het gast besturingssysteem niet correct is ingesteld en er een niet-maskeer bare interrupt (NMI) is verzonden. Het fout bericht geeft aan dat er een uitzonde ring is gegenereerd door een programma in kernelmodus, dat niet is onderschept door de handler. U kunt identificeren welke uitzonde ring is gegenereerd door een geheugen dump te verzamelen.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces 

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. De register sleutel voor niet-maskeer bare interrupt (NMI) instellen 
2. Een herstel-VM maken en openen 
3. Seriële console-en geheugen dump verzameling inschakelen 
4. De virtuele machine opnieuw bouwen 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>De register sleutel voor niet-maskeer bare interrupt (NMI) instellen

1. Start met behulp van de Azure Portal de virtuele machine opnieuw op zodat het gast besturingssysteem normaal wordt opgestart. 
2. Nadat de toegang tot de virtuele machine is hersteld, opent u een opdracht prompt met verhoogde bevoegdheden (als administrator uitvoeren). 
3. Stel de register sleutel NMI in met de volgende opdracht:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [Meer informatie over de opdracht REG ADD weer geven](/windows-server/administration/windows-commands/reg-add)
4. *(Optioneel)* Verzameling van installatie geheugen dump:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Optioneel)* Toegang tot seriële console instellen:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [Meer informatie over de opdracht BCDEDIT weer geven](/windows-server/administration/windows-commands/bcdedit)
6. Start de VM opnieuw met de volgende opdracht:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [Meer informatie over de AFSLUIT opdracht weer geven](/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> Het probleem zou nu moeten worden opgelost.

> [!NOTE]
> Test uw VM na het opnieuw opstarten om te controleren of deze werkt zoals normaal. Als u nog steeds problemen ondervindt, kunt u door gaan naar de volgende sectie voor verdere instructies.

> [!TIP]
> Het is raadzaam om de niet-maskeer bare interrupt (NMI)-register sleutel in de bovenstaande sectie in te stellen, maar als uw virtuele machine daarna niet normaal is opgestart, is het mogelijk dat er geen wijzigingen zijn aangebracht in het REGI ster van het gast besturingssysteem. Als dat het geval is, kunt u de onderstaande instructies volgen om de Register instellingen hand matig toe te voegen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
2. Maak verbinding met de herstel-VM met behulp van Verbinding met extern bureaublad.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Seriële console-en geheugen dump verzameling inschakelen

Voordat u de virtuele machine opnieuw bouwt, wordt het aanbevolen om geheugen dump verzameling en seriële console in te scha kelen. Voer hiervoor het volgende script uit: 

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren). 
2. Vermeld de BCD-opslag gegevens en bepaal de id van de opstart lader, die u in de volgende stap gaat gebruiken. 
    1. Voor een virtuele machine van de eerste generatie voert u de volgende opdracht in en noteert u de id die wordt weer gegeven: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        Vervang in de opdracht door `<BOOT PARTITON>` de letter van de partitie in de gekoppelde schijf die de opstartmap bevat. 

        ![In de scherm afbeelding ziet u de uitvoer van de vermelding van het BCD-archief in een virtuele machine van de eerste generatie, die wordt vermeld onder Windows boot loader, het id-nummer.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. Voor een VM van de tweede generatie voert u de volgende opdracht in en noteert u de id die wordt weer gegeven:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * Vervang in de opdracht door `<LETTER OF THE EFI SYSTEM PARTITION>` de letter van de EFI-systeem partitie.
        * Het kan handig zijn om de schijf beheer console te starten om de juiste systeem partitie te identificeren die is aangeduid als *EFI-systeem partitie*.
        * De id mag een unieke GUID zijn of de standaard- *BOOTMGR* zijn.
3. Voer de volgende opdrachten uit om seriële console in te scha kelen:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * Vervang in de opdracht door `<VOLUME LETTER WHERE THE BCD FOLDER IS>` de letter van de map BCD.
    * Vervang in de opdracht door `<BOOT LOADER IDENTIFIER>` de id die u hebt gevonden in de vorige stap.
4. Controleer of de beschik bare ruimte op de besturingssysteem schijf groter is dan de geheugen grootte (RAM) op de virtuele machine. 
    1. Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt. In plaats van het bestand op de besturingssysteem schijf te maken, kunt u dit naar een andere gegevens schijf die is gekoppeld aan de VM met voldoende beschik bare ruimte. Als u de locatie wilt wijzigen, vervangt u **% System root%** door de stationsletter (bijvoorbeeld **F:**) van de gegevens schijf in de onderstaande opdrachten. 
    2. Voer de onderstaande opdrachten in (aanbevolen dump configuratie):

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
### <a name="rebuild-the-virtual-machine"></a>De virtuele machine opnieuw bouwen

* Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Opstart fouten van Azure Virtual Machine oplossen](./boot-error-troubleshoot.md)