---
title: C#-agent installeren op Windows-apparaat
description: Meer informatie over het installeren van Defender voor IoT-agent op 32-bits of 64-bits Windows-apparaten.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: e7c7fdd5874dbde5ca304309d0840724cb3872df
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494526"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Een Defender implementeren voor IoT C#-gebaseerde beveiligings agent voor Windows

In deze hand leiding wordt uitgelegd hoe u de Defender voor IoT C#-beveiligings agent op Windows installeert.

In deze handleiding leert u het volgende:

- Installeren
- Implementatie controleren
- Agent verwijderen
- Problemen oplossen

## <a name="prerequisites"></a>Vereisten

Zie [de juiste beveiligings agent kiezen](how-to-deploy-agent.md)voor andere platforms en agents.

1. Lokale beheerders rechten op de computer waarop u wilt installeren.

1. [Maak een Defender-IOT-micro-agent](quickstart-create-security-twin.md) voor het apparaat.

## <a name="installation"></a>Installatie

Als u de beveiligings agent wilt installeren, gebruikt u de volgende werk stroom:

1. Installeer de Defender voor IoT Windows C#-agent op het apparaat. Down load de meest recente versie naar uw computer vanuit de Defender voor IoT [github-opslag plaats](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Pak de inhoud van het pakket uit en navigeer naar de map/install.

1. Open Windows Power shell als Administrator.
1. Voer de volgende handelingen uit om actieve machtigingen toe te voegen aan het InstallSecurityAgent-script:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    Voer vervolgens de volgende handelingen uit:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Bijvoorbeeld:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Zie [verificatie configureren](concept-security-agent-authentication-methods.md)voor meer informatie over verificatie parameters.

Met dit script worden de volgende acties uitgevoerd:

* Hiermee worden vereisten geïnstalleerd.
* Hiermee voegt u een service gebruiker toe (waarbij interactieve aanmelding is uitgeschakeld).
* Installeert de agent als een **systeem service**.
* Hiermee configureert u de agent met de opgegeven verificatie parameters.

Gebruik de opdracht Get-Help in Power shell voor extra hulp.

Get-Help voor beeld:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Implementatie status controleren

Controleer de implementatie status van de agent door uit te voeren:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Agent verwijderen

De agent verwijderen:

1. Voer het volgende Power shell-script uit met de para meter **-mode** ingesteld op **verwijderen**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Problemen oplossen

Als de agent niet kan worden gestart, schakelt u logboek registratie in (logboek registratie is standaard *uitgeschakeld* ) om meer informatie te krijgen.

Logboek registratie inschakelen:

1. Open het configuratie bestand (General.config) voor bewerking met een standaard bestands editor.

1. Bewerk de volgende waarden:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Het is raadzaam om de logboek registratie **uit** te scha kelen nadat de probleem oplossing is voltooid. Wanneer de logboek registratie is ingeschakeld, neemt de grootte **van** het logboek bestand en het gegevens gebruik toe.

1. Start de agent opnieuw door de volgende Power shell of opdracht regel uit te voeren:

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   of

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Raadpleeg het logboek bestand voor meer informatie over de fout. Het logboek bestand is aanwezig in de werkmap waarin het script wordt uitgevoerd. 

   Locatie van logboek bestand: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Volgende stappen

* Lees het [overzicht](overview.md) van de Defender voor IOT-service
* Meer informatie over de [architectuur](architecture.md) van Defender voor IOT
* De [service](quickstart-onboard-iot-hub.md) inschakelen
* Lees de [Veelgestelde vragen](resources-frequently-asked-questions.md)
* Meer informatie over [waarschuwingen](concept-security-alerts.md)
