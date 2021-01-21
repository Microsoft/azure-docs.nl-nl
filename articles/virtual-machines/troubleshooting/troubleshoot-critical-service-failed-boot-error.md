---
title: KRITIEKE SERVICE mislukt tijdens het opstarten van een Azure-VM | Microsoft Docs
description: Meer informatie over het oplossen van de fout ' 0x0000005A-CRITICal SERVICE FAILed ' die optreedt tijdens het opstarten | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: a937528e3bfd8bea16912d614133988763748bab
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632956"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows geeft een ' kritieke SERVICE is mislukt ' weer op het blauwe scherm bij het opstarten van een Azure VM
In dit artikel wordt de fout ' essentiële SERVICE is mislukt ' beschreven die u kunt tegen komen wanneer u een virtuele Windows-machine (VM) opstart in Microsoft Azure. Het bevat probleemoplossings stappen om de problemen op te lossen. 


## <a name="symptom"></a>Symptoom 

Een Windows-VM start niet. Wanneer u de opstart scherm afbeeldingen in [Diagnostische gegevens over opstarten](./boot-diagnostics.md)controleert, wordt een van de volgende fout berichten weer gegeven in een blauw scherm:

- ' Er is een probleem opgetreden in de PC en opnieuw moet worden opgestart. U kunt opnieuw opstarten. Ga naar voor meer informatie over dit probleem en mogelijke oplossingen https://windows.com/stopcode . Als u een ondersteunings medewerker belt, geeft u deze de volgende informatie: stop code: kritieke SERVICE mislukt ' 
- ' Er is een probleem opgetreden in de PC en opnieuw moet worden opgestart. Er worden alleen fout gegevens verzameld en vervolgens wordt de computer opnieuw opgestart. Als u meer wilt weten, kunt u later online zoeken naar deze fout: CRITICAL_SERVICE_FAILED "

## <a name="cause"></a>Oorzaak

Er zijn verschillende oorzaken voor Stop-fouten. De meest voorkomende oorzaken zijn:
- Probleem met een stuur programma
- Beschadigd systeem bestand of geheugen
- De toepassing heeft toegang tot een niet-toegestane sector van het geheugen

## <a name="solution"></a>Oplossing 

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Als u dit probleem wilt oplossen, [neemt u contact op met de ondersteuning en verzendt u een dump bestand](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file). Dit helpt ons het probleem sneller op te sporen of de volgende oplossing voor zelf ondersteuning te gebruiken.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteem schijf koppelen aan een herstel-VM

1. Maak een moment opname van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.
2. [Koppel de besturingssysteem schijf aan een herstel-VM](./troubleshoot-recovery-disks-portal-windows.md). 
3. Stel een verbinding met een extern bureau blad met de herstel-VM in.

### <a name="enable-dump-logs-and-serial-console"></a>Dump logboeken en seriële console inschakelen

Het dump logboek en de [seriële console](./serial-console-windows.md) helpen ons bij het oplossen van problemen.

Voer het volgende script uit om dump logboeken en seriële console in te scha kelen.

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).
2. Voer het volgende script uit:

    In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf F is. U moet deze vervangen door de juiste waarde voor uw VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>De niet-ondertekende Stuur Programma's vervangen

1. Voer op de virtuele machine voor herstel de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheden. Met deze opdracht wordt de betrokken besturingssysteem schijf ingesteld om te starten in de veilige modus bij de volgende keer opstarten:

    ```console
    bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal
    ```

    Als de besturingssysteem schijf die u hebt gekoppeld bijvoorbeeld station F is, voert u de volgende opdracht uit:

    ```console
    bcdedit /store F: boot\bcd /set {default} safeboot minimal
    ```

2. [Ontkoppel de besturingssysteem schijf en koppel de besturingssysteem schijf opnieuw aan de betreffende VM](troubleshoot-recovery-disks-portal-windows.md). De virtuele machine wordt opgestart in de veilige modus. Als de fout zich blijft voordoen, gaat u naar de optionele stap.
3. Open het vak **uitvoeren** en voer **Verifier** uit om het hulp programma Driver Verifier Manager te starten.
4. Selecteer **automatisch niet-ondertekende Stuur Programma's selecteren** en klik vervolgens op **volgende**.
5. U krijgt de lijst met de stuurprogrammabestanden die niet zijn ondertekend. Onthoud de bestands namen.
6. Kopieer dezelfde versies van deze bestanden vanaf een werkende VM en vervang deze niet-ondertekende bestanden. 

7. De instellingen voor veilig opstarten verwijderen:

    ```console
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
    ```

8.  Start de VM opnieuw op. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Optioneel: de dump Logboeken in de dump crash modus analyseren

Voer de volgende stappen uit om de dump logboeken zelf te analyseren:

1. Koppel de besturingssysteemschijf aan een virtuele machine voor herstel.
2. Blader op de besturingssysteem schijf die u hebt toegevoegd naar **\Windows\System32\Config**. Kopieer alle bestanden als back-up in geval van een terugdraai actie vereist is.
3. Start de **REGI ster-editor** (regedit.exe).
4. Selecteer de **HKEY_LOCAL_MACHINE** sleutel. Selecteer in het menu de optie **bestand**  >  **laden**.
5. Blader naar de map **\windows\system32\config\SYSTEM** op de besturingssysteem schijf die u hebt toegevoegd. Voer **BROKENSYSTEM** in voor de naam van de component. De nieuwe register component wordt weer gegeven onder de sleutel **HKEY_LOCAL_MACHINE** .
6. Blader naar **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** en breng de volgende wijzigingen aan:

    AutoReboot = 0

    CrashDumpEnabled = 2
7.  Selecteer **BROKENSYSTEM**. Selecteer in het menu het   >  **onderdeel bestand verwijderen**.
8.  Wijzig de BCD-instellingen om op te starten in de foutopsporingsmodus. Voer vanaf een opdrachtprompt met verhoogde bevoegdheid de volgende opdracht uit:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Ontkoppel de besturingssysteem schijf en koppel de besturingssysteem schijf opnieuw aan de betreffende VM](troubleshoot-recovery-disks-portal-windows.md).
10. Start de virtuele machine op om te controleren of de dump analyse wordt weer gegeven. Zoek het bestand dat niet kan worden geladen. U moet dit bestand vervangen door een bestand van de werkende VM. 

    Hieronder ziet u een voor beeld van een dump analyse. U kunt zien dat de **fout** zich voordeed filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Als de virtuele machine eenmaal werkt en normaal wordt opgestart, verwijdert u de dump crash-instellingen:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
