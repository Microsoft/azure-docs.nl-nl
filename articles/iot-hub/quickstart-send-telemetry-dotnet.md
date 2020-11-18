---
title: 'Snelstartgids: Telemetrie verzenden naar Azure IoT Hub (C#) | Microsoft Docs'
description: In deze snelstart voert u twee C#-voorbeeldtoepassingen uit om gesimuleerde telemetrie te verzenden naar een IoT-hub en telemetrie van de IoT-hub te lezen voor verwerking in de cloud.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: 21410f7137a76b43f57ca7a1e037908410eae365
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844519"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Quickstart: Telemetrie vanaf een apparaat verzenden naar een IoT-hub en lezen met een back-endtoepassing (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. In deze snelstartgids verzendt u telemetrie vanuit een toepassing voor een gesimuleerd apparaat via IoT Hub naar een back-endtoepassing voor verwerking.

In de snelstartgids worden twee vooraf geschreven C#-toepassingen gebruikt, één voor het verzenden van de telemetrie en één voor het lezen van de telemetrie van de hub. Voordat u deze twee toepassingen kunt uitvoeren, moet u een IoT-hub maken en een apparaat registreren bij de hub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* De twee voorbeeldtoepassingen die u uitvoert in deze snelstartgids zijn geschreven in C#. .NET Core SDK 3.0 of hoger moet zijn geïnstalleerd op uw ontwikkelcomputer.

    U kunt de .NET Core SDK voor meerdere platforms downloaden van [.NET](https://www.microsoft.com/net/download/all).

    Gebruik de volgende opdracht om de huidige versie van C# op uw ontwikkelcomputer te controleren:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > .NET Core SDK 3.0 of hoger wordt aanbevolen voor het compileren van de Event Hubs-servicecode die wordt gebruikt voor het lezen van telemetrie in deze quickstart. U kunt .NET Core SDK 2.1 gebruiken als u de taalversie voor de servicecode instelt op preview, zoals vermeld in de sectie [De telemetrie van uw hub lezen](#read-the-telemetry-from-your-hub).


* Download de Azure IoT-voorbeelden van [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) en pak het ZIP-archief uit.

* Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in deze quickstart wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdrachten uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyDotnetDevice**: dit is de naam van het apparaat dat u gaat registreren. Het is raadzaam om **MyDotnetDevice** te gebruiken zoals wordt weergegeven. Als u een andere naam voor het apparaat kiest, moet u deze naam ook in de rest van dit artikel gebruiken, en moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u deze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Voer de volgende opdracht uit in Azure Cloud Shell om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de quickstart.

3. U hebt ook het _Event Hubs-compatibele eindpunt_, het _Event Hubs-compatibele pad_ en de _primaire sleutel service_ van uw IoT-hub nodig om de back-endtoepassing in staat te stellen verbinding te maken met uw IoT-hub en de berichten op te halen. Met de volgende opdrachten worden deze waarden opgehaald voor uw IoT-hub:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Noteer deze drie waarden die u later in deze quickstart gaat gebruiken.

## <a name="send-simulated-telemetry"></a>Gesimuleerde telemetrie verzenden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verstuurt gesimuleerde telemetrie over temperatuur en luchtvochtigheid.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in C#. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device**.

2. Open het bestand **SimulatedDevice.cs** in een teksteditor van uw keuze.

    Vervang de waarde van de variabele `s_connectionString` door de apparaatverbindingsreeks die u eerder hebt genoteerd. Sla daarna de wijzigingen op in **SimulatedDevice.cs**.

3. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste pakketten te installeren voor de toepassing voor het gesimuleerde apparaat:

    ```cmd/sh
    dotnet restore
    ```

4. Voer in het lokale terminalvenster de volgende opdracht uit om de toepassing voor het gesimuleerde apparaat te compileren en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Het gesimuleerde apparaat uitvoeren](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

De back-endtoepassing maakt verbinding met het eindpunt **Events** aan de servicezijde van uw IoT-hub. De toepassing ontvangt de berichten die van het gesimuleerde apparaat naar de cloud worden verzonden. Een back-endtoepassing van IoT Hub wordt meestal uitgevoerd in de cloud om berichten van apparaat naar cloud te ontvangen en verwerken.

1. Navigeer in een ander lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in C#. Navigeer vervolgens naar de map **iot-hub\Quickstarts\read-d2c-messages**.

2. Open het bestand **ReadDeviceToCloudMessages.cs** in een teksteditor van uw keuze. Werk de volgende variabelen bij en sla de wijzigingen in het bestand op.

    | Variabele | Waarde |
    | -------- | ----------- |
    | `EventHubsCompatibleEndpoint` | Vervang de waarde van de variabele door het met Event Hubs compatibele eindpunt dat u eerder hebt genoteerd. |
    | `EventHubName`                | Vervang de waarde van de variabele door het met Event Hubs compatibele pad dat u eerder hebt genoteerd. |
    | `IotHubSasKey`                | Vervang de waarde van de variabele door de primaire sleutel service die u eerder hebt genoteerd. |

    > [!NOTE]
    > Als u .NET Core SDK 2.1 gebruikt, moet u de taalversie instellen op preview om de code te compileren. Open hiervoor het bestand **read-d2c-messages.csproj** en stel de waarde van het element `<LangVersion>` in op `preview`.

3. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken voor de back-endtoepassing te installeren:

    ```cmd/sh
    dotnet restore
    ```

4. Voer in het lokale terminalvenster de volgende opdrachten uit om de back-endtoepassing te bouwen en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de back-endtoepassing telemetriegegevens ontvangt die door het gesimuleerde apparaat naar de hub zijn verzonden:

    ![De back-endtoepassing uitvoeren](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een IoT-hub geconfigureerd, een apparaat geregistreerd, gesimuleerde telemetrie verzonden naar de hub met behulp van een C#-toepassing en de telemetrie gelezen van de hub met behulp van een eenvoudige back-endtoepassing.

Ga verder met de volgende snelstartgids als u wilt weten hoe u een gesimuleerd apparaat beheert vanuit een back-endtoepassing.

> [!div class="nextstepaction"]
> [Snelstart: Een apparaat beheren dat is verbonden met een IoT-hub](quickstart-control-device-dotnet.md)
