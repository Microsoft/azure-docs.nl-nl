---
title: Overzicht van beveiligings agenten
description: Inzicht in de architectuur van de beveiligings agent voor de agents die worden gebruikt in de Azure Defender voor IoT-service.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: ff837fe88f878c522366b2b6bc19a1ef3954b667
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820650"
---
# <a name="security-agent-reference-architecture"></a>Referentie architectuur beveiligings agent

Azure Defender voor IoT biedt referentie architectuur voor beveiligings agenten waarmee beveiligings gegevens via IoT Hub worden geregistreerd, verwerkt, geaggregeerd en verzonden.

Beveiligings agenten zijn ontworpen om te werken in een beperkte IoT-omgeving en zijn zeer aanpasbaar in termen van waarden die ze bieden in vergelijking met de resources die ze gebruiken.

Beveiligings agenten bieden ondersteuning voor de volgende functies:

- Verifieer met de bestaande apparaat-id of een toegewezen module-identiteit. Zie [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)voor meer informatie.

- Onbewerkte beveiligings gebeurtenissen van het onderliggende besturings systeem (Linux, Windows) verzamelen. Zie voor meer informatie over beschik bare beveiligings gegevens verzamelaars de [Defender voor IOT-agent configuratie](how-to-agent-configuration.md).

- Verdeel onbewerkte beveiligings gebeurtenissen in berichten die via IoT Hub worden verzonden.

- Configureer op afstand via het gebruik van de **azureiotsecurity** -module dubbele. Zie [Configure a Defender for IOT agent](how-to-agent-configuration.md)(Engelstalig) voor meer informatie.

Defender voor IoT-beveiligings agenten is ontwikkeld als open-source projecten en zijn verkrijgbaar via GitHub:

- [Defender voor IoT C-gebaseerde agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender voor IoT C#-gebaseerde agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Door agents ondersteunde platforms

Defender voor IoT biedt verschillende installatie agenten voor 32 bits-en 64-bits Windows en dezelfde voor 32 bits en 64-bits Linux. Zorg ervoor dat u het juiste installatie programma voor de agent hebt voor elk van uw apparaten volgens de volgende tabel:

| Architectuur | Linux | Windows | Details |
|--|--|--|--|
| 32-bits | C | C# |  |
| 64 bits | C# of C | C# | We raden u aan om de C-agent te gebruiken voor apparaten met meer beperkte of minimale bronnen van apparaten. |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een overzicht op hoog niveau over de architectuur van Defender voor IoT-beveiligings modules en de beschik bare installatie Programma's.

Als u aan de slag wilt gaan met Defender voor IoT-implementatie, gebruikt u de volgende artikelen:

- Meer informatie over [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)
- Een [beveiligings agent](how-to-deploy-agent.md) selecteren en implementeren
- Raadpleeg de vereisten voor het Defender- [systeem](quickstart-system-prerequisites.md) voor IOT
- Meer informatie over het [inschakelen van Defender voor IOT-service in uw IOT hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service van de [Veelgestelde vragen over Defender voor IOT](resources-frequently-asked-questions.md)
