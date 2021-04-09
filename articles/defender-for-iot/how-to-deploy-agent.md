---
title: Beveiligings agenten selecteren en implementeren
description: Meer informatie over hoe u Defender voor IoT-beveiligings agenten selecteert en implementeert op IoT-apparaten.
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: c71c92ffa79c844f3529265320b46eadd0c158cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778845"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Een beveiligings agent op uw IoT-apparaat selecteren en implementeren

Defender voor IoT bevat referentie architecturen voor beveiligings agenten waarmee gegevens van IoT-apparaten worden gecontroleerd en verzameld.
Zie [referentie architectuur beveiligings agent](security-agent-architecture.md)voor meer informatie.

Agents zijn ontwikkeld als open-source projecten en zijn verkrijgbaar in twee soorten: <br> [C](https://aka.ms/iot-security-github-c)en [C#](https://aka.ms/iot-security-github-cs).

In dit artikel leert u het volgende:
- De versies van de beveiligings agent vergelijken
- Ondersteunde agent platforms detecteren
- Kies de juiste agent smaak voor uw oplossing

## <a name="understand-security-agent-options"></a>Meer informatie over de beveiligings agent opties

Elke Defender voor IoT-beveiligings agent biedt dezelfde set functies en ondersteunt vergelijk bare configuratie opties.

De op C gebaseerde beveiligings agent heeft een lagere geheugen capaciteit en is de ideale keuze voor apparaten met minder beschik bare bronnen.

|     | Op C gebaseerde beveiligings agent | Beveiligings agent op basis van C# |
| --- | ----------- | --------- |
| **Opensource** | Beschikbaar onder een [MIT-licentie](https://en.wikipedia.org/wiki/MIT_License) in [github](https://aka.ms/iot-security-github-c) | Beschikbaar onder een [MIT-licentie](https://en.wikipedia.org/wiki/MIT_License) in [github](https://aka.ms/iot-security-github-cs) |
| **Ontwikkelingstaal**    | C | C# |
| **Ondersteunde Windows-platforms?** | Nee | Ja |
| **Windows-vereisten** | --- | [WMI](/windows/desktop/wmisdk/) |
| **Ondersteunde Linux-platforms?** | Ja, x64 en x86 | Ja, alleen x64 |
| **Vereisten voor Linux** | libunwind8, libcurl3, uuid-runtime, gecontroleerde, audispd-invoeg toepassingen | libunwind8, libcurl3, uuid-runtime, gecontroleerde, audispd-plugins, sudo, netstat, iptables |
| **Schijf ruimte** | 10,5 MB | 90 MB |
| **Geheugen capaciteit (gemiddeld)** | 5,5 MB | 33 MB |
| **[Verificatie](concept-security-agent-authentication-methods.md) voor IOT hub** | Ja | Ja |
| **[Verzameling](how-to-agent-configuration.md#supported-security-events) van beveiligings gegevens** | Ja | Ja |
| **Aggregatie van gebeurtenissen** | Ja | Ja |
| **Externe configuratie via [Defender-IOT-micro agent dubbele](concept-security-module.md)** | Ja | Ja |

## <a name="security-agent-installation-guidelines"></a>Richt lijnen voor installatie van beveiligings agent

Voor **Windows**: het Installeer SecurityAgent.ps1 script moet worden uitgevoerd vanuit een Power shell-venster van de beheerder.

Voor **Linux**: de InstallSecurityAgent.sh moet worden uitgevoerd als super gebruiker. U wordt aangeraden de installatie opdracht voor te stellen met ' sudo '.

## <a name="choose-an-agent-flavor"></a>Een agent selecteren

Beantwoord de volgende vragen over uw IoT-apparaten om de juiste agent te selecteren:

- Gebruikt u _Windows Server_ of _Windows IOT core_?

    [Implementeer een op C# gebaseerde beveiligings agent voor Windows](how-to-deploy-windows-cs.md).

- Gebruikt u een Linux-distributie met een x86-architectuur?

    [Implementeer een op C gebaseerde beveiligings agent voor Linux](how-to-deploy-linux-c.md).

- Gebruikt u een Linux-distributie met een x64-architectuur?

    Beide versies van de agent kunnen worden gebruikt. <br>
    [Implementeer een C-gebaseerde beveiligings agent voor Linux](how-to-deploy-linux-c.md) en/of [Implementeer een op C# gebaseerde beveiligings agent voor Linux](how-to-deploy-linux-cs.md).

Beide versies van de agent bieden dezelfde set functies en bieden ondersteuning voor vergelijk bare configuratie opties.
Zie [vergelijking van beveiligings agent](how-to-deploy-agent.md#understand-security-agent-options) voor meer informatie.

## <a name="supported-platforms"></a>Ondersteunde platforms

De volgende lijst bevat alle momenteel ondersteunde platforms.

|Defender voor IoT-agent |Besturingssysteem |Architectuur |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT core, build 17763    |x64|
|

## <a name="next-steps"></a>Volgende stappen

Ga naar de hand leiding voor agent configuratie voor meer informatie over configuratie opties.
> [!div class="nextstepaction"]
> [Hand leiding voor agent configuratie](./how-to-agent-configuration.md)