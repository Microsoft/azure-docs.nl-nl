---
title: Beveiligings module en apparaat apparaatdubbels
description: Meer informatie over het concept van de Security module apparaatdubbels en hoe deze worden gebruikt in Defender voor IoT.
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
ms.openlocfilehash: b33c456d47426a3721e8582f24ffd603db0429c9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340030"
---
# <a name="security-module"></a>Beveiligingsmodule

In dit artikel wordt uitgelegd hoe Defender voor IoT gebruikmaakt van apparaatdubbels en modules van het apparaat.

## <a name="device-twins"></a>Apparaat apparaatdubbels

Apparaatdubbels spelen een belangrijke rol voor zowel apparaatbeheer als procesautomatisering voor IoT-oplossingen die in Azure zijn ingebouwd.

Defender for IoT biedt volledige integratie met uw bestaande IoT-beheerplatform, zodat u de beveiligingsstatus van uw apparaten kunt beheren en ook de bestaande apparaten kunt gebruiken. Integratie wordt bereikt door gebruik te maken van de IoT Hub dubbele methode.

Meer informatie over het concept van [device apparaatdubbels](../iot-hub/iot-hub-devguide-device-twins.md) in azure IOT hub.

## <a name="security-module-twins"></a>Beveiligings module apparaatdubbels

Defender voor IoT onderhoudt een beveiligings module voor elk apparaat in de service.
De beveiligings module bevat alle informatie die relevant is voor de beveiliging van apparaten voor elk specifiek apparaat in uw oplossing.
Beveiligings eigenschappen van apparaten worden onderhouden in een speciale beveiligings module, met als gevolg dat er een veiliger communicatie is en voor het inschakelen van updates en onderhoud waarvoor minder resources nodig zijn.

Zie [Security-module](quickstart-create-security-twin.md) configureren, en [Configureer beveiligings agenten](how-to-agent-configuration.md) voor meer informatie over het maken, aanpassen en configureren van de dubbele. Zie [Wat is module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md) ? voor meer informatie over het concept van module apparaatdubbels in IOT hub.

## <a name="see-also"></a>Zie ook

- [Overzicht van Defender voor IoT](overview.md)
- [Beveiligingsagents implementeren](how-to-deploy-agent.md)
- [Verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)