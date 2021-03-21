---
title: Problemen met Azure IoT Hub-fout 404104 DeviceConnectionClosedRemotely
description: Meer informatie over het oplossen van fout 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 673a76417739fa59a91979cca7c6807a584868f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92538252"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

In dit artikel worden de oorzaken en oplossingen voor **404104 DeviceConnectionClosedRemotely** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

### <a name="symptom-1"></a>Symptoom 1

Apparaten verbreekt de verbinding met een regel matig interval (bijvoorbeeld elke 65 minuten) en u ziet **404104 DeviceConnectionClosedRemotely** in IOT hub bron Logboeken. Soms ziet u ook **401003 IoTHubUnauthorized** en een geslaagde apparaatfout van minder dan een minuut later.

### <a name="symptom-2"></a>Symptoom 2

Apparaten worden wille keurig losgekoppeld en u ziet **404104 DeviceConnectionClosedRemotely** in IOT hub bron Logboeken.

### <a name="symptom-3"></a>Symptoom 3

Veel apparaten worden in één keer losgekoppeld, maar er wordt een dip in de [connectedDeviceCount (Connected devices)](monitor-iot-hub-reference.md)weer gegeven en er zijn meer **404104 DeviceConnectionClosedRemotely** en [500Xxx interne fouten](iot-hub-troubleshoot-error-500xxx-internal-errors.md) in azure monitor logboeken dan gebruikelijk.

## <a name="causes"></a>Oorzaken

### <a name="cause-1"></a>Oorzaak 1

Het [SAS-token dat wordt gebruikt om verbinding te maken met IOT hub](iot-hub-devguide-security.md#security-tokens) is verlopen. Dit zorgt ervoor dat IOT hub de verbinding met het apparaat verbreekt. De verbinding wordt hersteld wanneer het token wordt vernieuwd door het apparaat. Bijvoorbeeld: [het SAS-token verloopt elk uur standaard voor C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), wat kan leiden tot regel matige verbreken van de verbinding.

Zie [401003 IoTHubUnauthorized oorzaak](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)voor meer informatie.

### <a name="cause-2"></a>Oorzaak 2

Enkele mogelijkheden zijn:

- Het apparaat heeft het onderliggende netwerk connectiviteit dat langer is dan de [MQTT Keep-Alive](iot-hub-mqtt-support.md#default-keep-alive-timeout), wat resulteert in een time-out voor inactiviteit op afstand. De MQTT Keep-Alive-instelling kan per apparaat verschillen.

- Het apparaat heeft een reset op TCP/IP-niveau verzonden, maar er is geen toepassings niveau verzonden `MQTT DISCONNECT` . Het apparaat heeft in principe de onderliggende socket verbinding plotseling gesloten. Dit probleem wordt soms veroorzaakt door fouten in oudere versies van de Azure IoT SDK.

- De toepassing aan het apparaat is vastgelopen.

### <a name="cause-3"></a>Oorzaak 3

Mogelijk ondervindt IoT Hub een tijdelijk probleem. Zie [IOT hub interne server fout oorzaak](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Oplossingen

### <a name="solution-1"></a>Oplossing 1

Zie [401003 IoTHubUnauthorized-oplossing 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Oplossing 2

- Controleer of het apparaat een goede verbinding heeft met IoT Hub door [de verbinding te testen](tutorial-connectivity.md). Als het netwerk onbetrouwbaar of onverwacht is, raden we u aan de Keep-Alive-waarde niet te verhogen, omdat dit ertoe kan leiden dat er meer detectie plaatsvindt (bijvoorbeeld via Azure Monitor-waarschuwingen). 

- Gebruik de nieuwste versies van de [IOT-sdk's](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Oplossing 3

Zie [oplossingen voor het IOT hub van interne server fouten](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Volgende stappen

U kunt het beste Azure IoT-apparaat-Sdk's gebruiken om verbindingen betrouwbaar te beheren. Zie [connectiviteit en betrouw bare berichten beheren met Azure IOT hub apparaat-sdk's](iot-hub-reliability-features-in-sdks.md) voor meer informatie.