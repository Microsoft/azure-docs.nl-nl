---
title: Een firewall regel in-of uitschakelen op een gast besturingssysteem in azure VM | Microsoft Docs
description: Meer informatie over het gebruik van online of offline externe hulpprogram ma's of register instellingen voor het in-of uitschakelen van firewall regels voor het gast besturingssysteem op een externe virtuele machine van Azure.
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
ms.openlocfilehash: 9ee27f429dbfd1e550a45bbc26413a1c259c4fbe
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092365"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Een firewallregel in een gastbesturingssysteem in- of uitschakelen

Dit artikel bevat een Naslag informatie voor het oplossen van een situatie waarin u vermoedt dat de firewall van het gast besturingssysteem gedeeltelijk verkeer op een virtuele machine (VM) filtert. Dit kan handig zijn om de volgende redenen:

*   Als er een wijziging is doorgevoerd in de firewall waardoor RDP-verbindingen zijn mislukt, kunt u het probleem oplossen met behulp van de functie voor aangepaste script extensies.

*   Uitschakelen van alle Firewall profielen is een Foolproof manier om problemen op te lossen dan het instellen van de RDP-specifieke firewall regel.

## <a name="solution"></a>Oplossing

Hoe u de firewall regels configureert, is afhankelijk van het niveau van toegang tot de vereiste VM. De volgende voor beelden gebruiken RDP-regels. Dezelfde methoden kunnen echter worden toegepast op elk ander type verkeer door de juiste register sleutel te wijzen.

### <a name="online-troubleshooting"></a>Online problemen oplossen 

#### <a name="mitigation-1-custom-script-extension"></a>Beperking 1: aangepaste script extensie

1.  Maak uw script met behulp van de volgende sjabloon.

    *   Een regel inschakelen:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Een regel uitschakelen:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Upload dit script in de Azure Portal met de functie [aangepaste script extensie](../extensions/custom-script-windows.md) . 

#### <a name="mitigation-2-remote-powershell"></a>Beperking 2: externe Power shell

Als de virtuele machine online is en toegankelijk is op een andere VM in hetzelfde virtuele netwerk, kunt u de Volg oplossingen volgen met behulp van de andere VM.

1.  Open een Power shell-console venster op de virtuele machine voor probleem oplossing.

2.  Voer de volgende opdrachten uit, indien van toepassing.

    *   Een regel inschakelen:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Een regel uitschakelen:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Risico beperking 3: PSTools-opdrachten

Als de virtuele machine online is en toegankelijk is op een andere VM in hetzelfde virtuele netwerk, kunt u de Volg oplossingen volgen met behulp van de andere VM.

1.  Down load [PSTools](/sysinternals/downloads/pstools)op de virtuele machine voor probleem oplossing.

2.  Open een CMD-exemplaar en toegang tot de virtuele machine via het interne IP-adres (DIP). 

    * Een regel inschakelen:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Een regel uitschakelen:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Risico beperking 4: extern REGI ster

Als de virtuele machine online is en toegankelijk is op een andere VM in hetzelfde virtuele netwerk, kunt u [extern REGI ster](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/314837) gebruiken op de andere VM.

1.  Open de REGI ster-editor (regedit.exe) op de virtuele machine voor probleem oplossing en selecteer vervolgens **bestand**  >  **Connect netwerk register**.

2.  Open de *doel machine*\System vertakking en geef vervolgens de volgende waarden op:

    * Als u een regel wilt inschakelen, opent u de volgende register waarde:
    
        \SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-usermode-in-TCP *doel computer*
    
        Wijzig vervolgens **actief = False** in **actief = True** in de teken reeks:

        `v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`
    
    * Als u een regel wilt uitschakelen, opent u de volgende register waarde:
    
        \SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-usermode-in-TCP *doel computer*

        Wijzig vervolgens **actief = True** in **actief = onwaar**:
        
        `v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

3.  Start de virtuele machine opnieuw op om de wijzigingen toe te passen.

### <a name="offline-troubleshooting"></a>Offline problemen oplossen 

Als u geen toegang hebt tot de virtuele machine met behulp van een wille keurige methode, mislukt de aangepaste script extensie en moet u in de OFFLINE modus werken door rechtstreeks via de systeem schijf te werken.

Voordat u deze stappen volgt, moet u een moment opname maken van de systeem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

1.  [Koppel de systeem schijf aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een Extern bureaublad verbinding met de virtuele machine voor herstel.

3.  Zorg ervoor dat de schijf is gemarkeerd als **online** in de schijf beheer-console. Houd er rekening mee dat de stationsletter die is toegewezen aan de gekoppelde systeem schijf.

4.  Voordat u wijzigingen aanbrengt, maakt u een kopie van de map \Windows\System32\Config als u de wijzigingen ongedaan wilt maken.

5.  Start de REGI ster-editor (regedit.exe) op de virtuele machine voor probleem oplossing.

6.  Markeer de **HKEY_LOCAL_MACHINE** sleutel en selecteer vervolgens component **bestand**  >  **laden** in het menu.

    ![Bregedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Zoek en open het \windows\system32\config\SYSTEM-bestand. 

    > [!Note]
    > U wordt gevraagd een naam op te vragen. Voer **BROKENSYSTEM** in en vouw vervolgens **HKEY_LOCAL_MACHINE** uit. Er wordt nu een extra sleutel met de naam **BROKENSYSTEM** weer geven. Voor deze probleem oplossing worden deze probleem componenten als **BROKENSYSTEM** gekoppeld.

8.  Breng de volgende wijzigingen aan op de BROKENSYSTEM-vertakking:

    1.  Controleer de register sleutel van de **ControlSet** waarvan de VM begint. Het sleutel nummer wordt weer geven in HKLM\BROKENSYSTEM\Select\Current.

    2.  Als u een regel wilt inschakelen, opent u de volgende register waarde:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Wijzig vervolgens **actief = False** in **actief = True**.
        
        `v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

    3.  Als u een regel wilt uitschakelen, opent u de volgende register sleutel:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Wijzig vervolgens **actief = True** in **actief = onwaar**.
        
        `v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

9.  Markeer **BROKENSYSTEM** en selecteer vervolgens component **bestand**  >  **verwijderen** in het menu.

10. [Ontkoppel de systeem schijf en maak de virtuele machine opnieuw](troubleshoot-recovery-disks-portal-windows.md).

11. Controleer of het probleem is opgelost.
