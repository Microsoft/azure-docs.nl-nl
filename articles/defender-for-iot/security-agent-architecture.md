---
title: Architectuur van beveiligings agent
description: Inzicht in de architectuur van de beveiligings agent voor de agents die worden gebruikt in de Azure Defender voor IoT-service.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 5773f7af3bb065976e8f05d7b54c58b90da2d3d2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835122"
---
# <a name="security-agent-reference-architecture"></a>Referentie architectuur beveiligings agent

Azure Defender voor IoT biedt referentie architectuur voor beveiligings agenten waarmee beveiligings gegevens via IoT Hub worden geregistreerd, verwerkt, geaggregeerd en verzonden.

Beveiligings agenten zijn ontworpen om te werken in een beperkte IoT-omgeving en zijn zeer aanpasbaar in termen van waarden die ze bieden in vergelijking met de resources die ze gebruiken.

Beveiligings agenten bieden ondersteuning voor de volgende functies:

- Onbewerkte beveiligings gebeurtenissen van het onderliggende besturings systeem (Linux, Windows) verzamelen. Zie voor meer informatie over beschik bare beveiligings gegevens verzamelaars de [Defender voor IOT-agent configuratie](how-to-agent-configuration.md).

- Verdeel onbewerkte beveiligings gebeurtenissen in berichten die via IoT Hub worden verzonden.

- Verifieer met de bestaande apparaat-id of een toegewezen module-identiteit. Zie [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md) voor meer informatie.

- Configureer op afstand via het gebruik van de **azureiotsecurity** -module dubbele. Zie [een Defender voor IOT-agent configureren](how-to-agent-configuration.md)voor meer informatie.

Defender voor IoT-beveiligings agenten zijn ontwikkeld als open-source projecten en zijn verkrijgbaar via GitHub:

- [Defender voor IoT C-gebaseerde agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender voor IoT C#-gebaseerde agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Door agents ondersteunde platforms

Defender voor IoT biedt verschillende installatie agenten voor 32-en 64-bits Windows en hetzelfde voor 32-en 64-bits Linux. Zorg ervoor dat u het juiste installatie programma voor de agent hebt voor elk van uw apparaten volgens de volgende tabel:

| Architectuur | Linux | Windows | Details |
|--|--|--|--|
| 32 | C | C# |  |
| 64 | C# of C | C# | We raden u aan om de C-agent te gebruiken voor apparaten met meer beperkte of minimale bronnen van apparaten. |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de architectuur van Defender voor IoT-beveiligings agent en de beschik bare installatie Programma's.

Als u aan de slag wilt gaan met Defender voor IoT-implementatie, gebruikt u de volgende artikelen:

- Meer informatie over [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)
- Een [beveiligings agent](how-to-deploy-agent.md) selecteren en implementeren
- Raadpleeg de vereisten voor het Defender- [systeem](quickstart-system-prerequisites.md) voor IOT
- Meer informatie over het [inschakelen van Defender voor IOT-service in uw IOT hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service van de [Veelgestelde vragen over Defender voor IOT](resources-frequently-asked-questions.md)
