---
title: Quick Start-streams van Azure IoT Hub-apparaten Node.js Quick start voor SSH en RDP
description: In deze quickstart voert u een voorbeeld van een Node.js-app uit die als een proxy fungeert om scenario's met SSH en RDP via IoT Hub-apparaatstreams mogelijk te maken.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: ef45a6277adeff09a34fe22b7abeb21d3e603167
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624351"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Quickstart: SSH en RDP inschakelen via een IoT Hub-apparaatstream met behulp van een Node.js-proxy-app (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

In deze quickstart schakelt u het SSH- (Secure Shell) en RDP-verkeer (Remote Desktop Protocol) in voor verzending naar het apparaat via een apparaatstream. Azure IoT Hub-apparaatstreams zorgen ervoor dat service- en apparaat-apps kunnen communiceren op een beveiligde manier die de firewall toestaat. In deze quickstart wordt de uitvoering van een Node.js-proxy-app beschreven die aan servicezijde wordt uitgevoerd. Gedurende de openbare preview ondersteunt de Node.js SDK alleen apparaatstreams aan servicezijde. Daarom bevat deze quickstart alleen de instructies voor het uitvoeren van de proxy-app voor de service.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [SSH en RDP inschakelen via een IoT Hub-apparaatstream met behulp van een C-proxy-app](./quickstart-device-streams-proxy-c.md) of [SSH en RDP inschakelen via een IoT Hub-apparaatstream met behulp van een C#-proxy-app](./quickstart-device-streams-proxy-csharp.md).

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

    Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

    ```cmd/sh
    node --version
    ```

* [Een voorbeeld van een Node.js-project](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub ondersteunt momenteel apparaatstreams als een [preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:
>
> * VS - centraal
> * VS - centraal EUAP
> * Europa - noord
> * Azië - zuidoost

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voeg de Azure IoT-extensie voor Azure CLI toe aan uw Cloud Shell-instantie door de volgende opdracht uit te voeren. Met de IoT-extensie worden IoT Hub-, IoT Edge- en IoT Device Provisioning Service-specifieke opdrachten (DPS) toegevoegd aan de Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Als u [Snelstart: Als u telemetrie vanaf een apparaat wilt verzenden naar een IoT-hub](quickstart-send-telemetry-node.md), kunt u deze stap overslaan.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Als u klaar bent met [Quickstart: Als u telemetrie vanaf een apparaat wilt verzenden naar een IoT-hub](quickstart-send-telemetry-node.md), kunt u deze stap overslaan.

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In dit gedeelte gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdrachten uit in Cloud Shell om de apparaat-id te maken:

   > [!NOTE]
   > * Vervang de tijdelijke aanduiding *YourIoTHubName* door een door u gekozen naam voor de IoT-hub.
   > * Het is raadzaam om *MyDevice* te gebruiken zoals wordt weergegeven voor de naam van het apparaat dat u registreert. Als u een andere naam voor het apparaat kiest, moet u deze naam in de rest van dit artikel gebruiken, en moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. U hebt ook een *serviceverbindingsreeks* nodig, zodat de back-end-app verbinding kan maken met uw IoT-hub en de berichten kan ophalen. Met de volgende opdracht haalt u de tekenreeks voor uw IoT-hub op:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Noteer de geretourneerde serviceverbindingsreeks voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In deze sectie maakt u een end-to-end-stream voor tunneling van SSH-verkeer.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

Zoals eerder vermeld, ondersteunt de IoT Hub Node.js SDK alleen apparaatstreams aan servicezijde. Gebruik voor de app op het apparaat een apparaatproxy-app die beschikbaar is in een van de volgende quickstarts:

   * [SSH en RDP inschakelen via een IoT Hub-apparaatstream met behulp van een C-proxy-app](./quickstart-device-streams-proxy-c.md)
   * [SSH en RDP inschakelen via een IoT Hub-apparaatstream met behulp van een C#-proxy-app](./quickstart-device-streams-proxy-csharp.md) 

Voordat u verdergaat met de volgende stap, moet u controleren of de proxy-app op het apparaat wordt uitgevoerd. Zie [Voorbeeld van een lokale proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) voor een overzicht van de installatie.

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

In dit artikel wordt de instelling van SSH (met behulp van poort 22) beschreven en wordt vervolgens uiteengezet hoe u de instelling voor RDP (die gebruikmaakt van poort 3389) wijzigt. Omdat apparaatstreams app- en protocolneutraal zijn, kunt u hetzelfde voorbeeld aanpassen, zodat andere soorten client/server-appverkeer mogelijk worden gemaakt, doorgaans door de communicatiepoort te wijzigen.

Zorg ervoor dat de proxy-app op het apparaat actief is en voer de proxy-app voor de service uit die is geschreven in Node.js door het volgende te doen in een lokaal terminalvenster:

1. Geef voor de omgevingsvariabelen uw servicereferenties, de id van het doelapparaat waarop de SSH-daemon wordt uitgevoerd en het poortnummer voor de proxy die op het apparaat wordt uitgevoerd.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Wijzig de tijdelijke aanduiding ServiceConnectionString zodat deze overeenkomt met uw serviceverbindingsreeks en zodat **MyDevice** overeenkomt met uw apparaat-id als u die een andere naam hebt gegeven.

1. Navigeer naar de map `Quickstarts/device-streams-service` in uw uitgepakte projectmap. Gebruik de volgende code om de proxy-app van de service uit te voeren:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH naar uw apparaat via apparaatstreams

In Linux voert u met behulp van `ssh $USER@localhost -p 2222` SSH uit op een terminal. In Windows gebruikt u uw favoriete SSH-client (bijvoorbeeld PuTTY).

Console-uitvoer in de service nadat de SSH-sessie tot stand is gebracht (de proxy-app van de service luistert aan poort 2222):

![SSH-terminaluitvoer](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Console-uitvoer van de SSH-client-app (SSH-client communiceert met de SSH-daemon door verbinding te maken met poort 22, waar de proxy-app voor de service luistert):

![SSH-clientuitvoer](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP naar uw apparaat via apparaatstreams

Gebruik nu uw RDP-client-app en maak verbinding met de serviceproxy via poort 2222. Dit is een willekeurige poort die u eerder hebt gekozen.

> [!NOTE]
> Zorg ervoor dat uw apparaat-proxy correct is geconfigureerd voor RDP met poort 3389.

![De RDP-client maakt verbinding met de proxy-app voor de service](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een IoT-hub ingesteld, een apparaat geregistreerd en een proxy-app voor een service geïmplementeerd om RDP- en SSH-verkeer op een IoT-apparaat mogelijk te maken. Het RDP- en SSH-verkeer wordt via een apparaatstream door een tunnel door de IoT-hub geleid. Hierdoor is er geen directe verbinding met het apparaat nodig.

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
