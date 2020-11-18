---
title: 'Snelstartgids: Een apparaat beheren vanuit Azure IoT Hub (.NET) | Microsoft Docs'
description: In deze snelstartgids gaan we twee voorbeeldtoepassingen geschreven in C# uitvoeren. De ene toepassing is een back-endtoepassing waarmee u op afstand apparaten kunt beheren die zijn verbonden met uw hub. De andere toepassing simuleert een apparaat dat is verbonden met uw hub en dat op afstand kan worden beheerd.
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
ms.date: 03/04/2020
ms.openlocfilehash: aac03cad9dc6b83e7831b35ac2873ddaae6eda75
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843108"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Quickstart: Een apparaat beheren dat is verbonden met een IoT-hub (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub is een Azure-service waarmee u uw IoT-apparaten kunt beheren vanuit de cloud en grote hoeveelheden apparaattelemetrie kunt opnemen in de cloud voor opslag of bewerking. In deze snelstartgids gebruikt u een *directe methode* om een gesimuleerd apparaat te beheren dat met uw IoT-hub is verbonden. U kunt directe methoden gebruiken om het gedrag van een apparaat dat is verbonden met uw IoT-hub, op afstand te wijzigen.

In de snelstartgids worden twee vooraf geschreven .NET-toepassingen gebruikt:

* Een toepassing voor een gesimuleerd apparaat die reageert op de directe methoden die worden aangeroepen vanuit een back-endtoepassing. Om de aanroepen van de directe methoden te kunnen ontvangen, maakt deze toepassing verbinding met een apparaatspecifiek eindpunt op uw IoT-hub.

* Een back-endtoepassing die de directe methoden op het gesimuleerde apparaat aanroept. Als u een directe methode op een apparaat wilt aanroepen, maakt u met deze toepassing verbinding met een eindpunt aan de servicezijde van uw IoT-hub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* De twee voorbeeldtoepassingen die u uitvoert in deze snelstartgids zijn geschreven in C#. .NET Core SDK 2.1.0 of hoger moet zijn geïnstalleerd op uw ontwikkelcomputer.

    U kunt de .NET Core SDK voor meerdere platforms downloaden van [.NET](https://www.microsoft.com/net/download/all).

    Gebruik de volgende opdracht om de huidige versie van C# op uw ontwikkelcomputer te controleren:

    ```cmd/sh
    dotnet --version
    ```
* Als u dit nog niet hebt gedaan, downloadt u de Azure IoT C#-voorbeelden van https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip en pakt u het ZIP-archief uit.

* Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in deze quickstart wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Als u [Snelstart: Als u telemetrie vanaf een apparaat wilt verzenden naar een IoT-hub](quickstart-send-telemetry-dotnet.md), kunt u deze stap overslaan.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Als u [Snelstart: Als u telemetrie vanaf een apparaat wilt verzenden naar een IoT-hub](quickstart-send-telemetry-dotnet.md), kunt u deze stap overslaan.

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdrachten uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyDotnetDevice**: dit is de naam van het apparaat dat u gaat registreren. Het is raadzaam om **MyDotnetDevice** te gebruiken zoals wordt weergegeven. Als u een andere naam voor het apparaat kiest, moet u deze naam mogelijk ook in de rest van dit artikel gebruiken, en moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Voer de volgende opdrachten uit in Azure Cloud Shell om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

## <a name="retrieve-the-service-connection-string"></a>De verbindingsreeks voor de service ophalen

U hebt ook de _serviceverbindingsreeks_ voor de IoT-hub nodig, zodat de back-endtoepassing verbinding kan maken met de hub en de berichten kan ophalen. Met de volgende opdracht haalt u de serviceverbindingsreeks voor uw IoT-hub op:

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Noteer de serviceverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

U gebruikt deze waarde verderop in de snelstartgids. De verbindingsreeks voor de service is anders dan de verbindingsreeks voor het apparaat die u in de vorige stap hebt genoteerd.

## <a name="listen-for-direct-method-calls"></a>Luisteren naar aanroepen van directe methoden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub, verstuurt gesimuleerde telemetrie en luistert naar aanroepen van directe methoden vanuit de hub. In deze snelstartgids geeft de aanroep van de directe methode vanuit de hub het apparaat opdracht om het interval voor het verzenden van telemetrie te wijzigen. Het gesimuleerde apparaat stuurt een bevestiging terug naar de hub nadat de directe methode is uitgevoerd.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in C#. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device-2**.

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

    ![Het gesimuleerde apparaat uitvoeren](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>De directe methode aanroepen

De back-endtoepassing maakt verbinding met een eindpunt aan de servicezijde van uw IoT-hub. De toepassing verzendt via uw IoT-hub aanroepen naar directe methoden op een apparaat en luistert naar bevestigingen. Een back-endtoepassing van IoT Hub wordt meestal in de cloud uitgevoerd.

1. Navigeer in een ander lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in C#. Navigeer vervolgens naar de map **iot-hub\Quickstarts\back-end-application**.

2. Open het bestand **BackEndApplication.cs** in een teksteditor van uw keuze.

    Vervang de waarde van de variabele `s_connectionString` door de serviceverbindingsreeks die u eerder hebt genoteerd. Sla de wijzigingen vervolgens op in **BackEndApplication.cs**.

3. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken voor de back-endtoepassing te installeren:

    ```cmd/sh
    dotnet restore
    ```

4. Voer in het lokale terminalvenster de volgende opdrachten uit om de back-endtoepassing te bouwen en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing een directe methode op het apparaat aanroept en een bevestiging ontvangt:

    ![De back-endtoepassing uitvoeren](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Nadat u de back-endtoepassing hebt uitgevoerd, ziet u een bericht in het consolevenster dat het gesimuleerde apparaat wordt uitgevoerd, en dat het interval voor het verzenden van berichten is gewijzigd:

    ![Wijziging in gesimuleerde client](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u vanuit een back-endtoepassing een directe methode op een apparaat aangeroepen en op de aanroep van de directe methode gereageerd in een toepassing voor een gesimuleerd apparaat.

Ga verder met de volgende zelfstudie als u wilt leren hoe u berichten van een apparaat naar andere bestemmingen in de cloud kunt routeren.

> [!div class="nextstepaction"]
> [Zelfstudie: Routeren van telemetriegegevens naar verschillende eindpunten voor verwerking](tutorial-routing.md)
