---
title: Problemen met Azure IoT Hub-fout 400027 ConnectionForcefullyClosedOnNewConnection
description: Meer informatie over het oplossen van fout 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94506325"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

In dit artikel worden de oorzaken en oplossingen voor **400027 ConnectionForcefullyClosedOnNewConnection** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Het apparaat wordt niet meer verbonden met **Communication_Error** als **ConnectionStatusChangeReason** met behulp van de .NET-SDK en het transport type MQTT.

Uw apparaat-naar-Cloud-dubbele bewerking (zoals gerapporteerde eigenschappen voor lezen of patches) of directe methode aanroep mislukt met de fout code **400027**.

## <a name="cause"></a>Oorzaak

Een andere client heeft een nieuwe verbinding gemaakt met IoT Hub met dezelfde referenties, waardoor de vorige verbinding IoT Hub gesloten. IoT Hub mogen niet meer dan één client verbinding maken met behulp van dezelfde set referenties.

## <a name="solution"></a>Oplossing

Zorg ervoor dat elke client verbinding maakt met IoT Hub met behulp van een eigen identiteit.