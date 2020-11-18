---
title: Communiceren met de apparaattoepassing in C met Azure IoT Hub-apparaatstreams
description: In deze quickstart voert u een C-toepassing aan de servicezijde uit die via een apparaatstream communiceert met een IoT-apparaat.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: de310846ad0449a0dac7eccd60d82d4c68ef519b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832201"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Quickstart: communiceren met een apparaattoepassing in C via IoT Hub-apparaatstreams (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub ondersteunt momenteel apparaatstreams als een [preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Gedurende de openbare preview biedt de C SDK alleen ondersteuning voor apparaatstreams aan de apparaatzijde. Daarom bevat deze quickstart alleen maar instructies voor het uitvoeren van de toepassing aan de apparaatzijde. Als u een bijbehorende toepassing aan de servicezijde wilt uitvoeren, raadpleegt u de volgende artikelen:

* [Communiceren met apparaattoepassingen in C# via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-csharp.md)

* [Communiceren met een apparaattoepassing in Node.js via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-nodejs.md)

De C-toepassing aan de apparaatzijde in deze quickstart heeft de volgende functionaliteit:

* Een apparaatstream naar een IoT-apparaat tot stand brengen.

* Gegevens ontvangen die zijn verzonden vanaf de servicezijde van de toepassing en deze terug echoën.

De code demonstreert het initiatieproces van een apparaatstream en laat ook zien hoe gegevens worden verzonden en ontvangen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De volgende vereisten zijn nodig:

* [Visual Studio 2019](https://www.visualstudio.com/vs/) installeren met de workload **Desktopontwikkeling met C++** ingeschakeld.

* Installeer de meest recente versie van [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * VS - centraal EUAP
  * Europa - noord
  * Azië - zuidoost

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Voor deze quickstart gebruikt u de [Azure IoT device-SDK voor C](iot-hub-device-sdk-c-intro.md). U bereidt een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) van GitHub te klonen en te bouwen. De SDK op GitHub bevat de voorbeeldcode die wordt gebruikt in deze quickstart.

   > [!NOTE]
   > Voordat u met deze procedure begint, moet u ervoor zorgen dat Visual Studio is geïnstalleerd met de workload **Desktop-ontwikkeling met C++** .

1. Installeer het [CMake-bouwsysteem](https://cmake.org/download/) zoals beschreven op de downloadpagina.

1. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit voor het klonen van de GitHub-opslagplaats voor de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking duurt enkele minuten.

1. Maak de submap *cmake* in de hoofdmap van de Git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten uit vanuit de map *azure-iot-sdk-c*:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdracht uit vanuit de map *cmake* om een versie van de SDK te bouwen die specifiek is voor uw clientplatform voor ontwikkeling.

   * In Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Open een [Opdrachtprompt voor ontwikkelaars voor Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) in Windows. Voer de opdracht uit voor uw versie van Visual Studio. Deze quickstart gebruikt Visual Studio 2019. Deze opdrachten maken een Visual Studio-oplossing voor het gesimuleerde apparaat in de map *cmake*.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

U moet een apparaat moet zijn registreren bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell met de [IoT-extensie](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) om een gesimuleerd apparaat te registreren.

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
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Bekijk de geretourneerde verbindingsreeks van het apparaat voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Communicatie tussen het apparaat en de service via apparaatstreams

In deze sectie voert u zowel de toepassing op het apparaat als de toepassing aan de servicezijde uit en communiceert u tussen de twee toepassingen.

### <a name="run-the-device-side-application"></a>De toepassing aan de apparaatzijde uitvoeren

Volg deze stappen om deze toepassing aan de apparaatzijde uit te voeren:

1. Geef de referenties van uw apparaat op door het bronbestand **iothub_client_c2d_streaming_sample. c** te bewerken in de map `iothub_client/samples/iothub_client_c2d_streaming_sample` en de verbindingsreeks van uw apparaat toe te voegen.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Compileer de code met de volgende opdrachten:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Voer het gecompileerde programma uit:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>De toepassing aan de servicezijde uitvoeren

Zoals eerder genoemd, biedt de IoT Hub C SDK alleen ondersteuning voor apparaatstreams aan de apparaatzijde. Volg de instructies in een van de volgende quickstarts om de bijbehorende service-side toepassing te bouwen en uit te voeren:

* [Communiceren met een apparaattoepassing in C# via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-csharp.md)

* [Communiceren met een apparaattoepassing in Node.js via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, een apparaat geregistreerd, een apparaatstroom tussen een C-toepassing aan de apparaatzijde en een andere toepassing aan de servicezijde tot stand gebracht en de stroom gebruikt gegevens heen en weer tussen de toepassingen te verzenden.

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
