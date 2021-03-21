---
title: Een Linux C#-agent installeren & implementeren
description: Meer informatie over het installeren en implementeren van de Defender voor IoT C#-gebaseerde beveiligings agent op Linux
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
ms.openlocfilehash: f2251ef79f568677d6ef09d918cb61ffeba07888
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494543"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Defender voor IoT C# implementeren op basis van een beveiligings agent voor Linux

In deze hand leiding wordt uitgelegd hoe u de Defender voor IoT C#-beveiligings agent op Linux installeert en implementeert.

In deze handleiding leert u het volgende:

- Installeren
- Implementatie controleren
- Agent verwijderen
- Problemen oplossen

## <a name="prerequisites"></a>Vereisten

Zie [de juiste beveiligings agent kiezen](how-to-deploy-agent.md)voor andere platforms en agents.

1. Voor het implementeren van de beveiligings agent zijn lokale beheerders rechten vereist op de computer waarop u wilt installeren.

1. [Maak een Defender-IOT-micro-agent](quickstart-create-security-twin.md) voor het apparaat.

## <a name="installation"></a>Installatie

Als u de beveiligings agent wilt implementeren, voert u de volgende stappen uit:

1. Down load de meest recente versie van [github](https://aka.ms/iot-security-github-cs)naar uw computer.

1. Pak de inhoud van het pakket uit en navigeer naar de map _/install_ .

1. Actieve machtigingen toevoegen aan het **InstallSecurityAgent-script** door uit te voeren `chmod +x InstallSecurityAgent.sh`

1. Voer vervolgens de volgende opdracht uit met **machtigingen voor het hoofd niveau**:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   Zie [verificatie configureren](concept-security-agent-authentication-methods.md)voor meer informatie over verificatie parameters.

Met dit script worden de volgende acties uitgevoerd:

- Hiermee worden vereisten geïnstalleerd.

- Hiermee wordt een service gebruiker (met interactieve aanmelding uitgeschakeld) toegevoegd.

- Installeert de agent als een **daemon** : er wordt ervan uitgegaan dat het apparaat is **gesystemeerd** voor het klassieke implementatie model.

- Hiermee configureert u **sudo** zodat de agent bepaalde taken kan uitvoeren als basis.

- Hiermee configureert u de agent met de opgegeven verificatie parameters.

Voer het script uit met de para meter – Help voor meer informatie. `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Agent verwijderen

Als u de agent wilt verwijderen, voert u het script uit met de para meter – u: `./InstallSecurityAgent.sh -u` .

> [!NOTE]
> Met verwijderen worden ontbrekende vereiste onderdelen die tijdens de installatie zijn geïnstalleerd, niet verwijderd.

## <a name="troubleshooting"></a>Problemen oplossen

1. De implementatie status controleren door uit te voeren:

    `systemctl status ASCIoTAgent.service`

1. Schakel logboek registratie in.
   Als de agent niet kan worden gestart, schakelt u logboek registratie in om meer informatie te krijgen.

   Schakel logboek registratie in op:

   1. Open het configuratie bestand voor bewerking in een Linux-editor:

        `vi /var/ASCIoTAgent/General.config`

   1. Bewerk de volgende waarden:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       De **logFilePath** -waarde kan worden geconfigureerd.

       > [!NOTE]
       > Het is raadzaam om de logboek registratie **uit** te scha kelen nadat de probleem oplossing is voltooid. Wanneer de logboek registratie is ingeschakeld, neemt de grootte **van** het logboek bestand en het gegevens gebruik toe.

   1. Start de agent opnieuw door het volgende uit te voeren:

       `systemctl restart ASCIoTAgent.service`

   1. Raadpleeg het logboek bestand voor meer informatie over de fout.

       De locatie van het logboek bestand is: `/var/ASCIoTAgent/IotAgentLog.log`

       Wijzig het pad naar de bestands locatie op basis van de naam die u in stap 2 hebt gekozen voor de **logFilePath** .

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van de Defender voor IOT-service
- Meer informatie over de [architectuur](architecture.md) van Defender voor IOT
- De [service](quickstart-onboard-iot-hub.md) inschakelen
- Lees de [Veelgestelde vragen](resources-frequently-asked-questions.md)
- Meer informatie over [waarschuwingen](concept-security-alerts.md)
