---
title: Problemen met Azure IoT Hub-fout 412002 DeviceMessageLockLost
description: Meer informatie over het oplossen van fout 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 53364009f9b9c041c39728e438c3e24eacfd1665
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435474"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

In dit artikel worden de oorzaken en oplossingen voor **412002 DeviceMessageLockLost** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Wanneer u een Cloud-naar-apparaat-bericht probeert te verzenden, mislukt de aanvraag met de fout **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Oorzaak

Wanneer een apparaat een Cloud-naar-apparaat-bericht ontvangt uit de wachtrij (bijvoorbeeld door gebruik te maken van [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) ), wordt het bericht vergrendeld door IOT hub voor een time-out voor een vergren deling van één minuut. Als het apparaat het bericht probeert te volt ooien nadat de time-out voor vergren deling is verlopen, wordt deze uitzonde ring door IoT Hub gegenereerd.

## <a name="solution"></a>Oplossing

Als IoT Hub de melding niet binnen de time-outperiode van één minuut ontvangt, wordt het bericht weer in de status in *wachtrij* gezet. Het apparaat kan proberen het bericht opnieuw te ontvangen. Om te voor komen dat de fout zich in de toekomst voordoet, implementeert u de logica van het apparaat om het bericht binnen één minuut na ontvangst van het bericht te volt ooien. Deze time-out voor een enkele minuut kan niet worden gewijzigd.