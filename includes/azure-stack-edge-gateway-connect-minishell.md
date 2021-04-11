---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081135"
---
Afhankelijk van het besturings systeem van de client zijn de procedures om een externe verbinding met het apparaat te maken verschillend.

### <a name="remotely-connect-from-a-windows-client"></a>Extern verbinding maken vanaf een Windows-client


#### <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

- Windows Power shell 5,0 of hoger wordt uitgevoerd op uw Windows-client.
- Uw Windows-client heeft de ondertekening keten (basis certificaat) die overeenkomt met het knooppunt certificaat dat op het apparaat is geïnstalleerd. Zie voor gedetailleerde instructies [certificaat installeren op uw Windows-client](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- Het `hosts` bestand dat zich op de Windows-client bevindt, `C:\Windows\System32\drivers\etc` heeft een vermelding die overeenkomt met het knooppunt certificaat in de volgende indeling:

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Hier volgt een voor beeld van een vermelding voor het `hosts` bestand:
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Gedetailleerde stappen

Volg deze stappen om vanaf een Windows-client extern verbinding te maken.

1. Een Windows Power shell-sessie uitvoeren als beheerder.
2. Zorg ervoor dat de Windows Remote Management-service wordt uitgevoerd op de client. Typ in de opdrachtprompt:

    `winrm quickconfig`

    Zie [installatie en configuratie voor Windows Remote Management](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration)voor meer informatie.

3. Wijs een variabele toe aan het IP-adres van het apparaat.

    $ip = "<device_ip>"

    Vervang door `<device_ip>` het IP-adres van uw apparaat.

4. Typ de volgende opdracht om het IP-adres van uw apparaat toe te voegen aan de lijst met vertrouwde hosts van de client:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Start een Windows Power shell-sessie op het apparaat:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Als er een fout met betrekking tot de vertrouwens relatie wordt weer geven, controleert u of de ondertekening keten van het knooppunt certificaat dat is geüpload naar uw apparaat ook is geïnstalleerd op de client die toegang heeft tot uw apparaat.

    Als u de certificaten niet gebruikt (we raden u aan de certificaten uit te voeren), kunt u deze controle overs laan met behulp van de sessie-opties: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` .

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > Wanneer u de `-UseSSL` optie gebruikt, hebt u externe toegang via Power shell via *https*. U kunt het beste altijd *https* gebruiken om extern verbinding te maken via Power shell. 

6. Geef het wacht woord op wanneer u hierom wordt gevraagd. Gebruik hetzelfde wacht woord dat wordt gebruikt om u aan te melden bij de lokale webgebruikersinterface. Het standaard wacht woord voor de lokale web-UI is *Wachtwoord1*. Wanneer u verbinding met het apparaat met behulp van externe Power shell hebt gemaakt, ziet u de volgende voorbeeld uitvoer:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> In de huidige versie kunt u via een Windows-client verbinding maken met de Power shell-interface van het apparaat. De `-UseSSL` optie werkt niet met de Linux-clients.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->