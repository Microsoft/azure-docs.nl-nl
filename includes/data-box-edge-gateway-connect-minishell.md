---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 6e603442bdecf9a627172e24390b06cef573f475
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556800"
---
Afhankelijk van het besturings systeem van de client zijn de procedures om een externe verbinding met het apparaat te maken verschillend.

### <a name="remotely-connect-from-a-windows-client"></a>Extern verbinding maken vanaf een Windows-client

Voordat u begint, moet u ervoor zorgen dat Windows Power shell 5,0 of hoger wordt uitgevoerd op de Windows-client.

Volg deze stappen om vanaf een Windows-client extern verbinding te maken.

1. Een Windows Power shell-sessie uitvoeren als beheerder.
2. Zorg ervoor dat de Windows Remote Management-service wordt uitgevoerd op de client. Typ in de opdrachtprompt:

    `winrm quickconfig`

3. Wijs een variabele toe aan het IP-adres van het apparaat.

    $ip = "<device_ip>"

    Vervang door `<device_ip>` het IP-adres van uw apparaat.

4. Typ de volgende opdracht om het IP-adres van uw apparaat toe te voegen aan de lijst met vertrouwde hosts van de client:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Start een Windows Power shell-sessie op het apparaat:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Geef het wacht woord op wanneer u hierom wordt gevraagd. Gebruik hetzelfde wacht woord dat wordt gebruikt om u aan te melden bij de lokale webgebruikersinterface. Het standaard wacht woord voor de lokale web-UI is *Wachtwoord1*. Wanneer u verbinding met het apparaat met behulp van externe Power shell hebt gemaakt, ziet u de volgende voorbeeld uitvoer:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Extern verbinding maken vanaf een Linux-client

Op de Linux-client die u gebruikt om verbinding te maken:

- [Installeer de meest recente Power shell-kern voor Linux](/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) van github om de functie SSH Remoting op te halen. 
- [Alleen het `gss-ntlmssp` pakket installeren vanuit de NTLM-module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Gebruik de volgende opdracht voor Ubuntu-clients:
    - `sudo apt-get install gss-ntlmssp`

Ga voor meer informatie naar [externe toegang tot Power shell via SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Volg deze stappen om vanaf een NFS-client extern verbinding te maken.

1. Als u Power shell-sessie wilt openen, typt u:

    `sudo pwsh`
 
2. Als u verbinding wilt maken via de externe client, typt u:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Wanneer u hierom wordt gevraagd, geeft u het wacht woord op waarmee u zich aanmeldt bij uw apparaat.
 
> [!NOTE]
> Deze procedure werkt niet op Mac OS.