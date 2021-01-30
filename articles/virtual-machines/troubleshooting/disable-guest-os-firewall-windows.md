---
title: De firewall voor het gast besturingssysteem uitschakelen in azure VM | Microsoft Docs
description: Meer informatie over de tijdelijke oplossings methode voor het oplossen van problemen waarbij een gast besturingssysteem firewall gedeeltelijk filtert of verkeer naar een virtuele machine voltooit.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 74d06d3d4aaa0d76b80257d2148fb62f71c3fdb0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093192"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>De firewall van het gastbesturingssysteem uitschakelen in Azure VM

Dit artikel bevat een verwijzing naar situaties waarin u vermoedt dat de firewall van het gast besturingssysteem gedeeltelijk filtert of verkeer naar een virtuele machine (VM) afrondt. Dit kan gebeuren als er wijzigingen zijn aangebracht aan de firewall waardoor RDP-verbindingen zijn mislukt.

## <a name="solution"></a>Oplossing

Het proces dat in dit artikel wordt beschreven, is bedoeld om te worden gebruikt als tijdelijke oplossing, zodat u zich kunt richten op het oplossen van uw echte probleem. Dit is de manier waarop u de firewall regels correct instelt. Het is een Best Practice van micro soft om het onderdeel Windows Firewall te kunnen inschakelen. Hoe u de firewall regels configureert, is afhankelijk van het niveau van toegang tot de vereiste virtuele machine.

### <a name="online-solutions"></a>Online oplossingen 

Als de virtuele machine online is en toegankelijk is op een andere VM in hetzelfde virtuele netwerk, kunt u deze oplossingen doen met behulp van de andere VM.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Beperking 1: aangepaste script extensie of opdracht uitvoeren

Als u een werkende Azure-agent hebt, kunt u [aangepaste script extensie](../extensions/custom-script-windows.md) of de functie [opdrachten uitvoeren](../windows/run-command.md) (alleen vm's van Resource Manager) gebruiken om de volgende scripts op afstand uit te voeren.

> [!Note]
> * Als de firewall lokaal is ingesteld, voert u het volgende script uit:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Als de firewall is ingesteld met behulp van een Active Directory beleid, kunt u het volgende script uitvoeren voor tijdelijke toegang. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' -name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Zodra het beleid opnieuw wordt toegepast, wordt u echter niet meer op de externe sessie uitgevoerd. De permanente oplossing voor dit probleem is het wijzigen van het beleid dat op deze computer wordt toegepast.

#### <a name="mitigation-2-remote-powershell"></a>Beperking 2: externe Power shell

1.  Maak verbinding met een virtuele machine die zich in hetzelfde virtuele netwerk bevindt als de virtuele machine die u niet kunt bereiken met behulp van een RDP-verbinding.

2.  Open een Power shell-console venster.

3.  Voer de volgende opdrachten uit:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Als de firewall is ingesteld via een groepsbeleid-object, werkt deze methode mogelijk niet omdat met deze opdracht alleen de lokale register vermeldingen worden gewijzigd. Als er een beleid is geïmplementeerd, wordt deze wijziging overschreven. 

#### <a name="mitigation-3-pstools-commands"></a>Risico beperking 3: PSTools-opdrachten

1.  Down load [PSTools](/sysinternals/downloads/pstools)op de virtuele machine voor probleem oplossing.

2.  Open een CMD-exemplaar en ga vervolgens naar de virtuele machine via de DIP.

3.  Voer de volgende opdrachten uit:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Risico beperking 4: extern REGI ster 

Volg deze stappen om [extern REGI ster](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/314837)te gebruiken.

1.  Start de REGI ster-editor op de virtuele machine voor probleem oplossing en ga naar het **bestand**  >  **Connect netwerk Registry**.

2.  Open de *doel machine*\System-vertakking en geef de volgende waarden op:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Start de service opnieuw. Omdat u dit niet kunt doen met behulp van het externe REGI ster, moet u de externe service console gebruiken.

4.  Open een instantie van **Services. msc**.

5.  Klik op **Services (lokaal)**.

6.  Selecteer **verbinding maken met een andere computer**.

7.  Voer het **privé IP-adres (DIP)** van de probleem-vm in.

8.  Start het lokale firewall beleid opnieuw.

9.  Probeer opnieuw verbinding te maken met de virtuele machine via RDP vanaf uw lokale computer.

### <a name="offline-solutions"></a>Offline oplossingen 

Als u een situatie hebt waarin u de virtuele machine niet op een wille keurige manier kunt bereiken, mislukt de aangepaste script extensie en moet u in de OFFLINE modus werken door rechtstreeks via de systeem schijf te werken. Voer hiervoor de volgende stappen uit:

1.  [Koppel de systeem schijf aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een Extern bureaublad verbinding met de virtuele machine voor herstel.

3.  Zorg ervoor dat de schijf is gemarkeerd als online in de schijf beheer-console. Noteer de stationsletter die is toegewezen aan de gekoppelde systeem schijf.

4.  Voordat u wijzigingen aanbrengt, maakt u een kopie van de map \Windows\System32\Config als u de wijzigingen ongedaan wilt maken.

5.  Start de REGI ster-editor (regedit.exe) op de virtuele machine voor probleem oplossing. 

6.  Voor deze probleemoplossings procedure koppelen we de componenten als BROKENSYSTEM en BROKENSOFTWARE.

7.  Markeer de HKEY_LOCAL_MACHINE sleutel en selecteer vervolgens bestand > component laden in het menu.

8.  Zoek het bestand \windows\system32\config\SYSTEM op de gekoppelde systeem schijf.

9.  Open een Power shell-exemplaar met verhoogde bevoegdheden en voer de volgende opdrachten uit:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Ontkoppel de systeem schijf en maak de virtuele machine opnieuw](troubleshoot-recovery-disks-portal-windows.md).

11. Controleer of het probleem is opgelost.
