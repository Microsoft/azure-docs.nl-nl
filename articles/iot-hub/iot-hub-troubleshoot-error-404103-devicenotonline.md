---
title: Problemen met Azure IoT Hub-fout 404103 DeviceNotOnline
description: Meer informatie over het oplossen van fout 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: ed4341c1c8df588bf735bdc9c531c2165514d610
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061261"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

In dit artikel worden de oorzaken en oplossingen voor **404103 DeviceNotOnline** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Een directe methode op een apparaat mislukt met de fout **404103 DeviceNotOnline** zelfs als het apparaat online is. 

## <a name="cause"></a>Oorzaak

Als u weet dat het apparaat online is en de fout nog steeds krijgt, is het waarschijnlijk dat de call back van de methode direct wordt niet geregistreerd op het apparaat.

## <a name="solution"></a>Oplossing

Zie [een directe methode op een apparaat afhandelen](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)om uw apparaat op de juiste wijze te configureren voor direct-methode-aanroepen.