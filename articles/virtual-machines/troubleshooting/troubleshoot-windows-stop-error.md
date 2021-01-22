---
title: Windows-stopfout - Status geen geheugen
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij Windows niet kan worden gestart en de status ' status geen geheugen ' wordt weer gegeven.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d6e738152e542617046834980d3e7c58e497093
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664678"
---
# <a name="windows-stop-error---status-no-memory"></a>Windows-stopfout - Status geen geheugen

Dit artikel bevat stappen voor het oplossen van problemen waarbij Windows niet kan worden gestart en de status of fout code #0xC0000017, ook wel ' status geen geheugen ' genoemd.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt voor het weer geven van de scherm opname van de virtuele machine (VM), ziet u dat de volgende fout code wordt weer gegeven in de scherm opname: `0xC0000017` . Afhankelijk van de versie van Windows die u gebruikt, ziet u mogelijk deze code die wordt weer gegeven in **Windows Boot Manager** of in het **herstel scherm**.

   **Windows-opstart beheer**

   ![Windows-opstart beheer met de mede deling dat Windows niet kan worden gestart. Een recente wijziging in de hardware of software kan de oorzaak zijn. Als u naar beneden schuift, ziet u de status code ' 0xC0000017 ' en informatie over ' de toepassing of het besturings systeem kan niet worden geladen omdat een vereist bestand ontbreekt of fouten bevat '.](./media/troubleshoot-windows-stop-error/1.png)

   **Herstel scherm**
 
   ![Het herstel scherm met de melding dat de PC/het apparaat moet worden hersteld. Er is onvoldoende geheugen beschikbaar voor het maken van een ramdisk-apparaat. U ziet ook de fout code ' 0xC0000017 '.](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>Oorzaak

De schijf van het besturings systeem is vol, te gefragmenteerd of het besturings systeem (OS) heeft geen toegang tot het geheugen of wissel bestand of beide.

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces:

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

1. Een herstel-VM maken en openen
1. Ruimte vrijmaken op de schijf
1. Onjuist geheugen uit het BCD-archief verwijderen
1. Het wissel bestand herstellen naar de standaard locatie
1. Seriële console-en geheugen dump verzameling inschakelen
1. De virtuele machine opnieuw bouwen

> [!NOTE]
> Als deze fout optreedt, is het gast besturingssysteem niet operationeel. U kunt problemen oplossen in de offline modus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik [stap 1-3 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
1. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>Wijs voor virtuele machines van de 2e generatie een letter toe aan de EFI-partitie (Extensible Firmware Interface):

Als u een virtuele machine van de tweede generatie gebruikt, is er mogelijk geen letter toegewezen aan de EFI-partitie van de gekoppelde schijf. U moet de volgende stappen volgen om een letter aan de partitie toe te wijzen voordat u doorgaat met deze probleemoplossings handleiding.

1. In Windows Search voert u `diskmgmt` de **schijf beheer console** in en opent u deze.
1. Identificeer de defecte schijf die is gekoppeld aan de reparatie-VM. Deze schijf wordt doorgaans als laatste weer gegeven in de-console en heeft de hoogste numerieke waarde.
1. Opmerking Als er op die schijf een partitie is met de **EFI-systeem partitie**, waaraan ook geen letter waarde is toegewezen (zoals station *F:*). Als alle partities zijn toegewezen, kunt u overs Laan om ruimte vrij te maken op de schijf. Als dat niet het geval is, gaat u verder met het toewijzen van een letter aan deze schijf.

   ![De schijf beheer-console, met de gekoppelde schijf "schijf 2", en de niet-toegewezen partitie van 100 MB en is de EFI-systeem partitie.](./media/troubleshoot-windows-stop-error/3.png)

1. Open een opdracht prompt met verhoogde bevoegdheden als beheerder en voer `diskpart` in om het hulp programma **Disk Part** te starten.
1. Voer de volgende opdrachten in:

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - Vervang in de opdracht door `<NUMBER OF THE ATTACHED DISK>` het schijf nummer dat u in stap 2 hebt opgegeven.
   - Vervang in de opdracht door `<NUMBER OF THE SYSTEM PARTION>` het partitie nummer van de EFI-systeem partitie. Aan deze partitie is nog geen letter toegewezen, maar deze moet van het type **systeem** zijn en moet ongeveer 100 MB groot zijn.

   > [!NOTE]
   > Het vergelijken van de partities in de schijf beheer console met die in het hulp programma Disk part, kan nuttig zijn bij het bepalen welk partitie nummer overeenkomt met de EFI-systeem partitie in de gekoppelde schijf.

1. Sluit het opdrachtpromptvenster.

### <a name="free-up-space-on-the-disk"></a>Ruimte vrijmaken op de schijf

Nu de defecte schijf is gekoppeld aan de herstel-VM, moet u controleren of het besturings systeem op die schijf voldoende ruimte heeft om goed te werken. 

1. Controleer of de schijf vol is door met de rechter muisknop op het station van de aangesloten schijf te klikken en **Eigenschappen** te selecteren.
1. Als de schijf **minder dan 300 MB beschik bare ruimte** heeft, kunt u [deze uitbreiden tot Maxi maal 1 TB met behulp van Power shell](../windows/expand-os-disk.md).
1. Zodra de schijf grootte **1 TB** is, moet u een schijf opruiming uitvoeren. U kunt het [hulp programma schijf opruiming](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) gebruiken om ruimte vrij te maken.
1. Open een opdracht prompt met verhoogde bevoegdheid (als administrator uitvoeren) en voer een defragmentatie uit op het station:

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - Afhankelijk van het niveau van de fragmentatie, kan de gefragmentering uren in beslag nemen.
   - Vervang in de opdracht door `<LETTER ASSIGNED TO THE OS DISK>` de stationsletter van de besturingssysteem schijf (zoals station *F:*).

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>Onjuist geheugen uit het archief van de Boot Configuration Data (BCD) verwijderen

1. Open een opdracht prompt met verhoogde bevoegdheid (als administrator uitvoeren).
1. Een query uitvoeren op het opstart configuratie bestand voor ongeldige geheugen markeringen met de volgende opdracht:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - Vervang in de opdracht door `<LETTER ASSIGNED TO THE OS DISK>` de stationsletter van de besturingssysteem schijf (zoals station *F:*).

1. Als in de query geen beschadigde geheugen blokken worden weer gegeven, gaat u verder met de volgende taak. Ga anders verder met stap 4.
1. Als er beschadigde geheugen blokken worden geïdentificeerd, kunnen deze blokken het maken van een ramdisk verhinderen en moeten ze worden verwijderd met de volgende opdracht:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - Vervang in de opdracht door `<LETTER ASSIGNED TO THE OS DISK>` de stationsletter van de besturingssysteem schijf (zoals station *F:*).

### <a name="restore-the-page-file-to-its-default-location"></a>Het wissel bestand herstellen naar de standaard locatie

In het wissel bestand worden gegevens opgeslagen die niet kunnen worden ondergebracht door het RAM-geheugen (Random-Access Memory) van uw computer als een vorm van overloop/back-up. Het is mogelijk dat het bestand wordt gehost in een VHD in plaats van het tijdelijke station. Dit is de standaard locatie voor Azure. Als dit het geval is, is het bestand mogelijk niet toegankelijk en moet het worden teruggezet naar de standaard locatie.

Voordat u een van de stappen neemt, moet u een kopie van de map **\Windows\System32\Config** op een goede schijf maken. Met deze stap zorgt u ervoor dat ongewenste wijzigingen ongedaan kunnen worden gemaakt. U gaat aan de slag met belang rijke systeem bestanden. deze voorzorgsmaatregel wordt daarom ten zeerste aanbevolen.

1. In Windows Search voert u **regedit** in en opent u de REGI ster-editor toepassing.
1. In de REGI ster-editor markeert u de sleutel **HKEY_LOCAL_MACHINE** en selecteert u in het menu **File > load Hive...** .

   ![Het Hive-menu van de REGI ster-editor.](./media/troubleshoot-windows-stop-error/4.png)

1. Selecteer **\windows\system32\config\SYSTEM** in het dialoog venster Hive laden en klik op openen.
   1. U wordt gevraagd om een naam op te geven. u moet **BROKENSYSTEM** invoeren. Met deze naam kunnen de betrokken componenten worden onderscheiden tijdens het oplossen van problemen.
   1. Vouw **HKEY_LOCAL_MACHINE** uit om de nieuwe BROKENSYSTEM-sleutel te zien die u hebt toegevoegd.
1. Bepaal in de REGI ster-editor op welke Beheerset de computer wordt opgestart.
   1. Navigeer naar **HKEY_LOCAL_MACHINE >> BROKENSYSTEM >> Select**.
   1. In de vermelde sleutels noteert u de gegevens waarde actueel. Als deze waarde bijvoorbeeld **1** of **0x00000001 (1)** is, wordt het besturings element ingesteld op ControlSet001.
1. Controleer de locatie waar het wissel bestand is gemaakt.
   1. Vouw in HKEY_LOCAL_MACHINE\BROKENSYSTEM de Directory uit die overeenkomt met het ControlSet-nummer dat u in stap 4 hebt opgegeven, zoals **ControlSet001**.
   1. Ga naar **beheer >> Session Manager >> geheugen beheer** en noteer de locatie van de **ExistingPageFiles** -sleutel.
   1. Deze sleutel moet zich in de standaard locatie van Azure van het tijdelijke station bezien. Als dit niet het geval is en zich op een VHD op een andere locatie bevindt, zoals het gegevens schijf station of het station van het besturings systeem, moet het worden verwijderd.
   1. Blader naar die locatie in Verkenner en verwijder vervolgens het **pagefile.sys** bestand.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>De seriële console en geheugen dump verzameling inschakelen

**Aanbevolen**: voordat u de virtuele machine opnieuw bouwt, moet u de seriële console en geheugen dump verzameling inschakelen door het volgende script uit te voeren:

Voer het volgende script uit om geheugen dump verzameling en seriële console in te scha kelen:

1. Open een opdracht prompt sessie met verhoogde bevoegdheden als beheerder.
1. Vermeld de BCD-opslag gegevens en bepaal de id van de opstart lader, die u in de volgende stap gaat gebruiken.

   1. Voor een virtuele machine van de eerste generatie voert u de volgende opdracht in en noteert u de id die wordt weer gegeven:
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - Vervang in de opdracht door `<BOOT PARTITON>` de letter van de partitie in de gekoppelde schijf die de opstartmap bevat.

      ![De uitvoer van de vermelding van het BCD-archief in een virtuele machine van de eerste generatie, waarin het id-nummer wordt vermeld onder Windows boot loader.](./media/troubleshoot-windows-stop-error/5.png)

   1. Voor een VM van de tweede generatie voert u de volgende opdracht in en noteert u de id die wordt weer gegeven:
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - Vervang in de opdracht door `<LETTER OF THE EFI SYSTEM PARTITION>` de letter van de EFI-systeem partitie.
   - Het kan handig zijn om de schijf beheer console te starten om de juiste systeem partitie te identificeren die is aangeduid als **EFI-systeem partitie**.
   - De id mag een unieke GUID zijn of de standaard- **BOOTMGR** zijn.

1. Voer de volgende opdrachten uit om seriële console in te scha kelen:

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - Vervang in de opdracht door `<VOLUME LETTER WHERE THE BCD FOLDER IS>` de letter van de map BCD.
   - Vervang in de opdracht door `<BOOT LOADER IDENTIFIER>` de id die u hebt gevonden in de vorige stap.

1. Controleer of de beschik bare ruimte op de besturingssysteem schijf groter is dan de geheugen grootte (RAM) op de virtuele machine.

   Als er onvoldoende ruimte beschikbaar is op de besturingssysteem schijf, wijzigt u de locatie waar het geheugen dump bestand wordt gemaakt en verwijst u naar die locatie naar een gegevens schijf die is gekoppeld aan de VM met voldoende beschik bare ruimte. Als u de locatie wilt wijzigen, vervangt u **% System root%** door de stationsletter van de gegevens schijf, zoals station **F:**, in de volgende opdrachten.

   Voorgestelde configuratie voor het inschakelen van de OS-dump:

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
   
### <a name="rebuild-the-vm"></a>De virtuele machine opnieuw bouwen

Gebruik [stap 5 van de opdrachten voor het herstellen van de virtuele machine](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) om de virtuele machine opnieuw samen te stellen.
