---
title: 'Quick Start: communiceren met device-app in C# met Azure IoT Hub Device streams'
description: In deze quickstart voert u twee voorbeeldtoepassingen in C# uit die communiceren via een apparaatstream die tot stand is gebracht via IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3eb65db27e5b96f4b12973154bc860a2ab3df020
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624605"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snelstart: Communiceren met een apparaattoepassing in C# via IoT Hub-apparaatstreams (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub ondersteunt momenteel apparaatstreams als een [preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](./iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. In deze quickstart worden twee C#-toepassingen gebruikt die apparaatstreams benutten om gegevens heen en weer (echo) te verzenden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:
  * VS - centraal
  * VS - centraal EUAP
  * Europa - noord
  * Azië - zuidoost

* De twee voorbeeldtoepassingen die u uitvoert in deze quickstart, zijn geschreven in C#. Op de ontwikkelcomputer moet .NET Core SDK 2.1.0 of hoger zijn geïnstalleerd.

    Download de [.NET Core SDK voor meerdere platforms downloaden van .NET](https://www.microsoft.com/net/download/all).

    Controleer de huidige versie van C# op de ontwikkelcomputer met behulp van de volgende opdracht:

    ```
    dotnet --version
    ```

* [Download de Azure IoT-voorbeelden in C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) en pak het ZIP-archief uit. U hebt deze zowel aan de zijde van het apparaat als aan de zijde van de service nodig.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In dit gedeelte gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdrachten uit in Cloud Shell om de apparaat-id te maken:

   > [!NOTE]
   > * Vervang de tijdelijke aanduiding *YourIoTHubName* door een door u gekozen naam voor de IoT-hub.
   > * Het is raadzaam om *MyDevice* te gebruiken zoals wordt weergegeven voor de naam van het apparaat dat u registreert. Als u een andere naam voor het apparaat kiest, moet u deze naam in de rest van dit artikel gebruiken, en moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Voer de volgende opdrachten uit in Cloud Shell om de *apparaatverbindingsreeks* op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Bekijk de geretourneerde verbindingsreeks van het apparaat voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. U hebt ook de *serviceverbindingsreeks* van uw IoT Hub nodig, zodat de toepassing aan de servicezijde verbinding kan maken met uw IoT Hub en een apparaatstream kan opzetten. Met de volgende opdracht haalt u deze waarde voor uw IoT-hub op:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Noteer de geretourneerde serviceverbindingsreeks voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Communicatie tussen het apparaat en de service via apparaatstreams

In deze sectie voert u zowel de toepassing op het apparaat als de toepassing aan de servicezijde uit en communiceert u tussen de twee toepassingen.

### <a name="run-the-service-side-application"></a>De toepassing aan de servicezijde uitvoeren

Navigeer in een lokaal terminalvenster naar de map `iot-hub/Quickstarts/device-streams-echo/service` in uw uitgepakte projectmap. Houd de volgende informatie bij de hand:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De serviceverbindingsreeks van uw IoT-hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
De toepassing wacht totdat de apparaattoepassing beschikbaar wordt.

> [!NOTE]
> Er treedt een time-out op als de toepassing aan de apparaatzijde niet op tijd reageert.

### <a name="run-the-device-side-application"></a>De toepassing aan de apparaatzijde uitvoeren

Navigeer in een ander lokaal terminalvenster naar de map `iot-hub/Quickstarts/device-streams-echo/device` in uw uitgepakte projectmap. Houd de volgende informatie bij de hand:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | De apparaatverbindingsreeks van uw IoT Hub. |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

Aan het einde van de laatste stap initieert de toepassing aan de servicezijde een stream naar uw apparaat. Nadat de stroom tot stand is gebracht, verzendt de toepassing een tekenreeksbuffer naar de service via de stroom. In dit voorbeeld doet de toepassing aan de servicezijde niets meer dan dezelfde gegevens weer naar het apparaat echoën, wat een geslaagde bidirectionele communicatie tussen de twee toepassingen aantoont.

Console-uitvoer aan de apparaatzijde:

![Console-uitvoer aan de apparaatzijde](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Console-uitvoer aan de servicezijde:

![Console-uitvoer aan de servicezijde](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Het verkeer dat via de stream wordt verstuurd, wordt via IoT-hub gesluisd en niet rechtstreeks verzonden. De beschikbare voordelen worden beschreven in [Voordelen van apparaatstreams](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een IoT-hub ingesteld, een apparaat geregistreerd, een apparaatstream tussen C#-toepassingen aan de apparaat- en servicezijde tot stand gebracht en de stream gebruikt om gegevens tussen de toepassingen heen en weer te verzenden.

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
