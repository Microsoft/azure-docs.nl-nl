---
title: 'Snelstartgids: overzicht van beveiligings agenten'
description: In deze Quick Start leert u hoe u de architectuur van de beveiligings agent begrijpt voor de agents die in de Azure Defender voor IoT-service worden gebruikt.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: de8c52372b2ef92dcf2abe357a40ef8947439a8a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493954"
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

Geen

## <a name="agent-supported-platforms"></a>Door agents ondersteunde platforms

Defender voor IoT biedt verschillende installatie agenten voor 32 bits-en 64-bits Windows en dezelfde voor 32 bits en 64-bits Linux. Zorg ervoor dat u het juiste installatie programma voor de agent hebt voor elk van uw apparaten volgens de volgende tabel:

| Architectuur | Linux | Windows | Details |
|--|--|--|--|
| 32-bits | C | C# |  |
| 64 bits | C# of C | C# | We raden u aan om de C-agent te gebruiken voor apparaten met meer beperkte of minimale bronnen van apparaten. |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een overzicht op hoog niveau over Defender voor de architectuur van IoT Defender-IoT-micro-agent en de beschik bare installatie Programma's.

Als u aan de slag wilt gaan met Defender voor IoT-implementatie, gebruikt u de volgende artikelen:

> [!div class="nextstepaction"]
> [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)
