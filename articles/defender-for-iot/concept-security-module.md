---
title: Defender-IoT-micro agent en apparaatdubbels voor apparaten
description: Meer informatie over het concept van Defender-IoT-micro agent apparaatdubbels en hoe deze worden gebruikt in Defender voor IoT.
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
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 552da329b90b102a13ef53158ec81be87684c1fc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493183"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-micro agent

In dit artikel wordt uitgelegd hoe Defender voor IoT gebruikmaakt van apparaatdubbels en modules van het apparaat.

## <a name="device-twins"></a>Apparaat apparaatdubbels

Apparaatdubbels spelen een belangrijke rol voor zowel apparaatbeheer als procesautomatisering voor IoT-oplossingen die in Azure zijn ingebouwd.

Defender for IoT biedt volledige integratie met uw bestaande IoT-beheerplatform, zodat u de beveiligingsstatus van uw apparaten kunt beheren en ook de bestaande apparaten kunt gebruiken. Integratie wordt bereikt door gebruik te maken van de IoT Hub dubbele methode.

Meer informatie over het concept van [device apparaatdubbels](../iot-hub/iot-hub-devguide-device-twins.md) in azure IOT hub.

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-micro-agent apparaatdubbels

Defender voor IoT onderhoudt een Defender-IoT-micro agent voor elk apparaat in de service.
De Defender-IoT-micro agent bevat alle informatie die relevant is voor de beveiliging van apparaten voor elk specifiek apparaat in uw oplossing.
De beveiligings eigenschappen van het apparaat worden onderhouden in een specifieke Defender-IoT-micro agent-twee voor veiliger communicatie en voor het inschakelen van updates en onderhoud waarvoor minder resources nodig zijn.

Zie voor het maken van een dubbele [agent](quickstart-create-security-twin.md) en het [configureren van beveiligings agenten](how-to-agent-configuration.md) voor meer informatie over het maken, aanpassen en configureren van de dubbele. Zie [Wat is module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md) ? voor meer informatie over het concept van module apparaatdubbels in IOT hub.

## <a name="see-also"></a>Zie ook

- [Overzicht van Defender voor IoT](overview.md)
- [Beveiligingsagents implementeren](how-to-deploy-agent.md)
- [Verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)