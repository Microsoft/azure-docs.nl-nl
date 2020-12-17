---
title: Problemen met de Windows-opstart beheer fout oplossen-0xC0000225 ' status niet gevonden '
description: Stappen voor het oplossen van problemen waarbij fout code 0xC0000225 optreedt in een Azure-VM.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: ff7d5a4e1181dccedc3584d958038a1d695c57ca
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657121"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Problemen met de Windows-opstart beheer fout oplossen-0xC0000225 ' status niet gevonden '
 
Dit artikel bevat stappen voor het oplossen van problemen waarbij fout code 0xC0000225 optreedt in een Azure-VM. Deze fout geeft aan dat de status of het object niet is gevonden.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname een Windows-fout bericht kan niet starten met de status code *0xc0000225*.

Het bestand dat aan deze fout code is gekoppeld, laat u weten welke stappen u moet ondernemen om het probleem op te lossen. Zoek het **bestand:** de tekst van de sectie om de juiste gedrags duur te bepalen.

### <a name="drivers-os-related-or-third-party"></a>Stuur Programma's, aan het besturings systeem gerelateerde of externe partij

Als het bestand aanwezig is, maar verwijst naar een stuur programma (zoals wordt weer gegeven) of als het besturings systeem of een derde partij is, volgt u de stappen onder de sectie [herstel van het bestand System](#repair-the-system-file).
 
In de volgende afbeelding, Windows Boot Manager-statussen ' Windows is niet gestart. Een recente wijziging in de hardware of software kan de oorzaak zijn. " De afbeelding toont ook de status ' 0xc0000225 ', **File:** as en `\windows\System32\drivers\atapi.sys` **Info:** ' het besturings systeem kan niet worden geladen omdat een essentieel systeem stuur programma ontbreekt of fouten bevat. '

![Windows-statussen van Windows-opstart beheer kan niet worden gestart. Een recente wijziging in de hardware of software kan de oorzaak zijn. Daarnaast wordt de status weer gegeven als ' 0xc0000225 ', het bestand ' \windows\System32\drivers\atapi.sys ' en in het gedeelte Info: ' het besturings systeem kan niet worden geladen omdat een essentieel systeem stuur programma ontbreekt of fouten bevat. '](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Geen bestand

Als u de status code hebt opgegeven, maar er geen bestand wordt weer gegeven, volgt u de stappen in de sectie [de variabele OSDEVICE toevoegen](#add-the-osdevice-variable).

In de volgende afbeelding, Windows Boot Manager-statussen ' Windows is niet gestart. Een recente wijziging in de hardware of software kan de oorzaak zijn. " De afbeelding toont ook de status "0xc0000225" en **Info:** als "de opstart selectie is mislukt omdat een vereist apparaat niet toegankelijk is."

![Windows-statussen van Windows-opstart beheer kan niet worden gestart. Een recente wijziging in de hardware of software kan de oorzaak zijn. Daarnaast wordt de status weer gegeven als ' 0xc0000225 ' en in het gedeelte Info: ' de opstart selectie is mislukt omdat een vereist apparaat niet toegankelijk is. '](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>Register bestand

Als deze verwijst naar een van de register bestanden, zoals \windows\system32\config\system, volgt u de stappen in de sectie [een ondersteunings ticket maken](#contact-support).
 
In de volgende afbeelding, Windows Boot Manager-statussen ' Windows is niet gestart. Een recente wijziging in de hardware of software kan de oorzaak zijn. " De afbeelding toont ook de status ' 0xc0000225 ', het bestand als en `\windows\System32\config\system` **Info:** als ' het besturings systeem kan niet worden geladen omdat het systeem register bestand ontbreekt of fouten bevat. '

![Windows-statussen van Windows-opstart beheer kan niet worden gestart. Een recente wijziging in de hardware of software kan de oorzaak zijn. Daarnaast wordt de status weer gegeven als ' 0xc0000225 ', het bestand ' \windows\System32\config\system ' en in het gedeelte Info: ' het besturings systeem kan niet worden geladen omdat het systeem register bestand ontbreekt of fouten bevat. '](./media/troubleshoot-boot-error-status-not-found/3.png)

In de volgende afbeelding wordt in het herstel scherm de status ' uw PC/apparaat moet worden hersteld ' weer gegeven. Het besturings systeem kan niet worden geladen omdat het systeem register bestand ontbreekt of fouten bevat. " De afbeelding toont ook de fout code als ' 0xc0000225 ' en het bestand als `\windows\System32\config\system` .

![In het herstel scherm wordt aangegeven dat uw PC/apparaat moet worden hersteld. Het besturings systeem kan niet worden geladen omdat het systeem register bestand ontbreekt of fouten bevat. De fout code wordt ook weer gegeven als ' 0xc0000225 ' en het bestand ' \windows\System32\config\system '.](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Oorzaken

### <a name="missing-binary"></a>Ontbrekend binair bestand

Er wordt mogelijk een ontbrekend of beschadigd binair bestand gevonden op uw systeem **(. sys)** .

### <a name="bcd-corruption-or-improper-vhd-migration"></a>BCD-beschadiging of onjuiste VHD-migratie

In dit geval is de **Boot Configuration Data (BCD)** beschadigd of is de **virtuele harde schijf (VHD)** van on-premises gemigreerd, maar is deze niet op de juiste wijze voor bereid. Het resultaat is dat de variabele **OSDEVICE** ontbreekt en moet worden toegevoegd.

### <a name="registry-hive-corruption"></a>Beschadiging van register component

Een beschadiging van een register component kan de volgende oorzaken hebben:

- De Hive is mislukt
- De Hive koppelt, maar is leeg
- De component is niet juist afgesloten
## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Overzicht van het proces

1. Een herstel-VM maken en openen.
1. Selecteer een oplossing:
   - [Het systeem bestand herstellen](#repair-the-system-file)
   - [De variabele OSDevice toevoegen](#add-the-osdevice-variable)
   - [Een ondersteunings ticket maken](#contact-support)
1. Schakel seriële console-en geheugen dump verzameling in.
1. Bouw de virtuele machine opnieuw op.

### <a name="create-and-access-a-repair-vm"></a>Een herstel-VM maken en openen

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
1. Maak met behulp van Verbinding met extern bureaublad verbinding met de herstel-VM.

### <a name="select-a-solution"></a>Een oplossing selecteren

1. Open een opdracht prompt met verhoogde bevoegdheden.
1. Volg de stappen in de bijbehorende oplossing op basis van het symptoom dat u eerder hebt geïdentificeerd. U kunt de stappen in de andere oplossingen overs Laan, aangezien deze niet van toepassing zijn op uw probleem:

- [Het systeem bestand herstellen](#repair-the-system-file)
- [De variabele OSDevice toevoegen](#add-the-osdevice-variable)
- [Een ondersteunings ticket maken](#contact-support)

### <a name="repair-the-system-file"></a>Het systeem bestand herstellen

1. Ga met de gekoppelde VHD naar de locatie van het binaire bestand dat wordt weer gegeven in de scherm afbeelding van de virtuele machine (VM).
1. Klik met de rechter muisknop op het bestand, selecteer **Eigenschappen** en selecteer vervolgens het tabblad **Details** om informatie over het bestand weer te geven.
   1. Noteer de versie van het bestand, zoals wordt weer gegeven in de onderstaande afbeelding:

      ![Het venster Eigenschappen voor het bestand ' cng.sys ', waarbij de bestands versie is gemarkeerd.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Wijzig de naam van het bestand in **< BINARY.SYS >. old**, waarbij u **< BINARY.SYS** >vervangt door het bestand.

   Voor de afbeelding in de bovenstaande stap wordt de naam van het bestand **cng.sys** gewijzigd in **cng.sys. old**

   > [!NOTE]
   > Als u de naam van het bestand wilt wijzigen en het bericht ' het bestand is beschadigd en onleesbaar ' wilt ontvangen, [neemt u contact op met de ondersteuning voor hulp](https://azure.microsoft.com/support/create-ticket/), omdat deze oplossing niet werkt.

1. Nu het beschadigde bestand is hernoemd, herstelt u het bestand door het te herstellen vanuit de interne opslag plaats.
   1. Een **cmd** -sessie starten.
   1. Navigeer naar **\windows\winsxs**.

   1. Zoek naar de binaire bestanden die aan het begin van deze sectie staan met de volgende opdracht:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      Met deze opdracht worden alle versies van het bestand dat de machine heeft, weer geven, waarmee u de padgegevens van het onderdeel krijgt.

      **Dir cng.sys** zou bijvoorbeeld de naam **dir cng.sys/s**

   1. Kies de meest recente versie van het bestand in de lijst (of een wille keurige voor keur) en kopieer het bestand naar de map **Windows\System32** met het voor gaande pad en de volgende opdracht:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Als het meest recente binaire bestand niet werkt, probeert u een versie vóór dat item, of een van de versies waarvan u weet dat er sprake is van een stabiel, zoals een versie vóór een patch.

      Als bijvoorbeeld het binaire bestand dat u zoekt **cmimcext.sys** is, de defecte schijf is station **F:**, en u een zoek opdracht hebt uitgevoerd naar de nieuwste versie, ziet u de volgende afbeelding, waarbij een query in de opdracht prompt van de `dir cmim* /s` meest recente versie van het cmimcext.sys-bestand zoekt.

      ![Een query in de opdracht prompt van dir cmim */s om de meest recente versie van het cmimcext.sys-bestand te zoeken.](./media/troubleshoot-boot-error-status-not-found/6.png)

      In het bovenstaande voor beeld is de query uitgevoerd op **C:**, terwijl de stationsletter moet zijn van het defecte station, **F:**, de besturingssysteem schijf die is gekoppeld als een gegevens schijf op de herstel-VM.

      De resulterende opdracht voor het kopiëren van het bestand is: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers` .

Als deze taak is voltooid, gaat u door met [het inschakelen van de seriële console en de geheugen dump verzameling](#enable-the-serial-console-and-memory-dump-collection).

### <a name="add-the-osdevice-variable"></a>De variabele OSDEVICE toevoegen

Verzamel de huidige informatie over de opstart installatie en noteer de id op de actieve partitie. Vervolgens gebruikt u deze informatie om de variabele **OSDEVICE** toe te voegen, gevolgd door de instructies voor het genereren van uw VM.

Als deze informatie verzameling een fout retourneert waarin zich geen **\boot\bcd** -bestand bevindt, gebruikt u de instructies in in plaats daarvan [het systeem bestand herstellen](#repair-the-system-file) . 

1. Voor virtuele machines van de eerste generatie opent u een opdracht prompt met verhoogde bevoegdheden als beheerder en voert u de volgende opdracht in:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   In deze installatie kopie wordt het Windows-opstart laad programma weer gegeven in een virtuele machine van de eerste generatie, waarbij het kenmerk identifier is gemarkeerd. Het kenmerk id is gemarkeerd en bevat een unieke alfanumerieke teken reeks.

   ![Windows-opstart laad programma dat wordt weer gegeven in een virtuele machine van de eerste generatie met het kenmerk id gemarkeerd. Het kenmerk id is gemarkeerd en bevat een unieke alfanumerieke teken reeks.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Noteer de id van het Windows-opstart laad programma waarvan het pad is **\windows\system32\winload.exe**.

1. Controleer voor virtuele machines van de 2e generatie of de besturingssysteem schijf online is en of de stationsletters zijn toegewezen. Wanneer dit is geverifieerd, moet u de informatie over de opstart installatie verzamelen.
   1. Typ **schijf beheer** in **Windows Search** en open de schijf beheer console. Gebruik deze console om het schijf nummer te identificeren dat is gekoppeld aan de herstel-VM en de EFI-partitie (Extensible Firmware Interface) die het BCD-archief bevat.

   In de volgende afbeelding is schijf 2 het schijf nummer dat is gekoppeld aan de reparatie-VM. De afbeelding toont ook de EFI-systeem partitie op schijf 2, 100 MB groot en heeft geen toegewezen letter.

   ![Schijf 2 wordt weer gegeven als het schijf nummer dat is gekoppeld aan de reparatie-VM. Ook wordt de EFI-systeem partitie op schijf 2 weer gegeven. Dit is 100 MB en er is geen letter aan toegewezen.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Open een opdracht prompt met verhoogde bevoegdheden als beheerder en voer de volgende opdrachten in:
      1. Open het **hulp programma Disk Part** met behulp van de opdracht `diskpart` .
      1. Alle schijven weer geven en vervolgens de gekoppelde schijf selecteren die in de vorige stap is geïdentificeerd:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         In de volgende afbeelding ziet u de resultaten van het weer geven en selecteren van een schijf. Schijf 0 (127 GB/online), schijf 1 (32 GB/online) en schijf 2 (127 GB/online) worden weer gegeven en schijf 2 wordt geselecteerd met behulp van de opdracht `sel disk 2` .

         ![De resultaten van de vermelding en het selecteren van een schijf. Schijf 0 (127 GB | Online), schijf 1 (32 GB | Online) en schijf 2 (127 GB | Online) worden weer gegeven, waarbij schijf 2 wordt geselecteerd.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Vermeld de partities en selecteer de EFI-systeem partitie die in de vorige stap is geïdentificeerd:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         In de volgende afbeelding ziet u de resultaten van een vermelding en het selecteren van een partitie. Partitie 1 (gereserveerd/16 MB), partitie 2 (systeem/100 MB) en partitie 3 (primair/126 GB) worden weer gegeven, waarbij partitie 2 wordt geselecteerd met behulp van de opdracht `sel part 2` .

         ![De resultaten van de vermelding en het selecteren van een partitie. Partitie 1 (gereserveerd | 16 MB), partitie 2 (systeem | 100 MB) en partitie 3 (primair | 126 GB) worden weer gegeven en partitie 2 wordt geselecteerd.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Wijs een letter aan de EFI-partitie toe met behulp van de opdracht `assign` .

         In de volgende afbeelding zijn de `assign` opdracht en het nieuwe station **systeem (F:)** beide zichtbaar in Verkenner.

         ![De opdracht toewijzen en het nieuwe station systeem (F:) kunnen worden bekeken in Verkenner.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. Vermeld de BCD-opslag gegevens met behulp van de volgende opdracht:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         In de volgende afbeelding bevindt Windows boot loader zich op een virtuele machine van de tweede generatie met het kenmerk id gemarkeerd. Het kenmerk van de gemarkeerde id heeft de waarde **{default}**.

         ![Windows boot loader wordt weer gegeven in een VM van de tweede generatie met het kenmerk id gemarkeerd. Het kenmerk identifier is gemarkeerd als waarde.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Noteer de id van de Windows-opstart lader, waarvan het pad **\windows\system32\winload.efi** is.

1. U ziet dat de variabele OSDEVICE op de actieve partitie ontbreekt:

   ![De kenmerken Windows Boot Manager en Windows boot loader worden weer gegeven in de opdracht prompt, waarbij het kenmerk van het besturings systeem ontbreekt.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   In deze installatie kopie worden de kenmerken Windows Boot Manager en Windows boot loader weer gegeven in de opdracht prompt, maar het kenmerk OSDEVICE ontbreekt.

1. Voeg de variabele OSDEVICE toe op basis van de volgende gegevens:

   Voor schijven met een besturings systeem met één partitie, toevoegen `BOOT` .

   > [!NOTE]
   > De map voor opstarten kan zich op dezelfde partitie bevindt als de Windows-map **\Windows**.
   > - De opstart bare map voor virtuele machines van de eerste generatie is **(\boot\bcd map)**.
   > - De opstart bare map voor virtuele machines van generatie 2 is **EFI\Microsoft\boot\bcd**.

   Voor virtuele machines van de eerste generatie voert u de volgende opdracht in:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Voor virtuele machines van de tweede generatie voert u de volgende opdracht in:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Voor meerdere partitie besturingssysteem schijven toevoegen `PARTITION=<LETTER OF WINDOWS FOLDER>:` .

   > [!NOTE]
   > De map voor opstart toegang is waarschijnlijk op een andere partitie dan de Windows-map **\Windows**.
   > - De opstart bare map voor virtuele machines van de eerste generatie is **(\boot\bcd map)**.
   > - De opstart bare map voor virtuele machines van generatie 2 is **EFI\Microsoft\boot\bcd**.

   Voor virtuele machines van de eerste generatie voert u de volgende opdracht in:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   Voor virtuele machines van de tweede generatie voert u de volgende opdracht in:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

Als deze taak is voltooid, gaat u door met [het inschakelen van de seriële console en de geheugen dump verzameling](#enable-the-serial-console-and-memory-dump-collection).

### <a name="contact-support"></a>Contact met ondersteuning

Er is een oplossing voor het **register bestand** , maar u moet [een ondersteunings ticket maken](https://azure.microsoft.com/support/create-ticket/) voor verdere ondersteuning.

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
