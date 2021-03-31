---
title: Problemen met Azure IoT Hub-fout 409001 DeviceAlreadyExists
description: Meer informatie over het oplossen van fout 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76960787"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

In dit artikel worden de oorzaken en oplossingen voor **409001 DeviceAlreadyExists** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Wanneer u probeert een apparaat te registreren in IoT Hub, mislukt de aanvraag met de fout **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Oorzaak

Er is al een apparaat met dezelfde apparaat-ID in de IoT-hub. 

## <a name="solution"></a>Oplossing

Gebruik een andere apparaat-ID en probeer het opnieuw.