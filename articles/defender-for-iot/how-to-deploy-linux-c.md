---
title: Een Linux C-agent installeren & implementeren
description: Meer informatie over het installeren en implementeren van de Defender voor IoT C-beveiligings agent op Linux
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6f59db7ff24412c66a6a4898b14272ea9540fdd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778811"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Defender implementeren voor IoT C-gebaseerde beveiligings agent voor Linux

In deze hand leiding wordt uitgelegd hoe u de Defender voor IoT C-beveiligings agent op Linux installeert en implementeert.

- Installeren
- Implementatie controleren
- Agent verwijderen
- Problemen oplossen

## <a name="prerequisites"></a>Vereisten

Zie [de juiste beveiligings agent kiezen](how-to-deploy-agent.md)voor andere platforms en agents.

1. Voor het implementeren van de beveiligings agent zijn lokale beheerders rechten vereist op de computer waarop u wilt installeren (sudo).

1. [Maak een Defender-IOT-micro-agent](quickstart-create-security-twin.md) voor het apparaat.

## <a name="installation"></a>Installatie

Als u de beveiligings agent wilt installeren en implementeren, gebruikt u de volgende werk stroom:

1. Down load de meest recente versie van [github](https://aka.ms/iot-security-github-c)naar uw computer.

1. Pak de inhoud van het pakket uit en navigeer naar de map _/src/Installation_ .

1. Voeg actieve machtigingen toe aan het **script InstallSecurityAgent** door de volgende opdracht uit te voeren:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Voer vervolgens de volgende handelingen uit:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Zie [verificatie configureren](concept-security-agent-authentication-methods.md) voor meer informatie over verificatie parameters.

Met dit script wordt de volgende functie uitgevoerd:

1. Hiermee worden vereisten geïnstalleerd.

1. Hiermee wordt een service gebruiker (met interactieve aanmelding uitgeschakeld) toegevoegd.

1. Installeert de agent als een **daemon** : veronderstelt dat het apparaat **wordt gebruikt voor** Service beheer.

1. Hiermee configureert u de agent met de opgegeven verificatie parameters.

Voer het script uit met de para meter – Help voor meer informatie.

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Agent verwijderen

Als u de agent wilt verwijderen, voert u het script uit met de para meter –-Uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Problemen oplossen

De implementatie status controleren door uit te voeren:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van de Defender voor IOT-service
- Meer informatie over de [architectuur](architecture.md) van Defender voor IOT
- De [service](quickstart-onboard-iot-hub.md) inschakelen
- Lees de [Veelgestelde vragen](resources-frequently-asked-questions.md)
- [Beveiligings waarschuwingen](concept-security-alerts.md) begrijpen
