---
title: 'Snelstartgids: overzicht van beveiligings agenten'
description: In deze Quick Start leert u hoe u de architectuur van de beveiligings agent begrijpt voor de agents die worden gebruikt in de Azure Defender voor IoT-service.
ms.topic: quickstart
ms.date: 4/4/2021
ms.openlocfilehash: 0937cccb0335f4eee16ca3590babe9574320b89f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384972"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Snelstartgids: referentie architectuur voor beveiligings agent

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

## <a name="prerequisites"></a>Vereisten

- Geen

## <a name="agent-supported-platforms"></a>Door agents ondersteunde platforms

Defender voor IoT biedt verschillende installatie agenten voor 32 bits-en 64-bits Windows en dezelfde voor 32 bits en 64-bits Linux. Zorg ervoor dat u het juiste installatie programma voor de agent hebt voor elk van uw apparaten volgens de volgende tabel:

| Architectuur | Linux | Windows | Details |
|--|--|--|--|
| 32-bits | C | C# |  |
| 64 bits | C# of C | C# | We raden u aan om de C-agent te gebruiken voor apparaten met meer beperkte of minimale bronnen van apparaten. |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een overzicht op hoog niveau over Defender voor de architectuur van IoT Defender-IoT-micro-agent en de beschik bare installatie Programma's.
Als u aan de slag wilt gaan met Defender voor IoT-implementatie, 

> [!div class="nextstepaction"]
> [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)
