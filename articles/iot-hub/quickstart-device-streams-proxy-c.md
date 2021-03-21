---
title: Snelstartgids-Azure IoT Hub Device streams C Quick start voor SSH en RDP
description: In deze quickstart voert u een voorbeeld van een C#-toepassing uit die als een proxy fungeert om scenario's met SSH en RDP via IoT Hub-apparaatstreams mogelijk te maken.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: a6e3407d9ddb799ab0ee5846acc149048f593563
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202005"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Quickstart: Schakel SSH en RDP in via een IoT Hub-apparaatstroom in met behulp van een C-proxy-toepassing (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub ondersteunt momenteel apparaatstreams als een [preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](./iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Zie [De voorbeeldpagina van de lokale proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) voor een overzicht van de installatie.

In deze quickstart wordt beschreven hoe u tunneling van Secure Shell (SSH)-verkeer instelt (met behulp van poort 22) via apparaatstreams. De configuratie voor Remote Desktop Protocol (RDP)-verkeer is vergelijkbaar en vereist een eenvoudige wijziging in de configuratie. Omdat apparaatstreams toepassings- en protocolneutraal zijn, kan u deze quickstart zodanig bewerken dat er andere soorten toepassingsverkeer mogelijk zijn.

## <a name="prerequisites"></a>Vereisten

* De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * VS - centraal EUAP
  * Europa - noord
  * Azië - zuidoost

* [Visual Studio 2019](https://www.visualstudio.com/vs/) installeren met de workload [Desktopontwikkeling met C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ingeschakeld.
* Installeer de meest recente versie van [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Hoe werkt het?

In de volgende afbeelding ziet u hoe het apparaat en de proxyprogramma's in de service end-to-end-connectiviteit mogelijk maken tussen de SSH-client- en SSH-daemonprocessen. Gedurende de openbare preview biedt de C SDK alleen ondersteuning voor apparaatstreams aan de apparaatzijde. Daarom bevat deze quickstart alleen instructies voor het uitvoeren van de proxytoepassing in het apparaat. Volg de instructies in een van de volgende quickstarts om de bijbehorende service-side toepassing te bouwen en uit te voeren:

* [SSH/RDP via IoT Hub-apparaatstreams met behulp van C#-proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP via IoT Hub-apparaatstreams met behulp van Node.JS-proxy](./quickstart-device-streams-proxy-nodejs.md).

![Lokale proxy-instellingen](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. De proxy in de service maakt verbinding met de IoT Hub en begint een apparaatstream naar het doelapparaat.

2. De proxy in het apparaat voltooit de stream-handshake en zet een end-to-end-streaming-tunnel op van het streaming-eindpunt van de IoT-hub naar de servicezijde.

3. De proxy in het apparaat maakt verbinding met de SSH-daemon die luistert op poort 22 op het apparaat. Deze instelling kan worden geconfigureerd, zoals beschreven in de sectie "de toepassing proxy in het apparaat uitvoeren".

4. De proxy in de service wacht op nieuwe SSH-verbindingen van een gebruiker door te luisteren naar een opgegeven poort, in dit geval poort 2222. Deze instelling kan worden geconfigureerd, zoals beschreven in de sectie "de toepassing proxy in het apparaat uitvoeren". Wanneer de gebruiker verbinding maakt via een SSH-client, zorgt de tunnel ervoor dat SSH-toepassingsverkeer kan worden overgebracht tussen de SSH-client en serverprogramma's.

> [!NOTE]
> SSH-verkeer dat via een apparaatstream wordt verstuurd, gaat via een tunnel van het streaming-eindpunt van de IoT-hub in plaats van dat het rechtstreeks tussen de service en het apparaat wordt verzonden. Zie de [voordelen van het gebruik van IOT Hub-apparaten](iot-hub-device-streams-overview.md#benefits) voor meer informatie. In de afbeelding ziet u bovendien hoe de SSH-daemon die wordt uitgevoerd op hetzelfde apparaat (of machine) als de proxy in het apparaat. In deze quickstart zorgt de verstrekking van het IP-adres van de SSH-daemon ervoor dat de proxy in het apparaat en de daemon ook op verschillende computers worden uitgevoerd.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Voor deze quickstart gebruikt u de [Azure IoT device-SDK voor C](iot-hub-device-sdk-c-intro.md). U bereidt een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) van GitHub te klonen en te bouwen. De SDK op GitHub bevat de voorbeeldcode die wordt gebruikt in deze quickstart.

1. Download het [CMake-buildsysteem](https://cmake.org/download/).

    Het is belangrijk dat de vereisten voor Visual Studio met (Visual Studio en de workload *Desktopontwikkeling met C++* ) op uw computer zijn geïnstalleerd *voordat* u de CMake-installatie start. Nadat er aan de vereisten is voldaan en de download is geverifieerd, kunt u het CMake-bouwsysteem installeren.

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

   * Voer in Windows de volgende opdrachten uit in Developer Command Prompt voor Visual Studio 2015- of 2017. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de map *cmake*.

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

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell met de [IoT-extensie](/cli/azure/ext/azure-iot/iot) om een gesimuleerd apparaat te registreren.

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

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In deze sectie maakt u een end-to-end-stream voor tunneling van SSH-verkeer.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

1. Bewerk het bronbestand **iothub_client_c2d_streaming_proxy_sample. c** in de map `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` en geef uw apparaat de verbindingsreeks, het IP-adres of de hostnaam van het doelapparaat en de SSH-poort 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Compileer het voorbeeld:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Voer het gecompileerde programma uit op het apparaat:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

Zoals besproken in de sectie "Hoe het werkt", is voor het tot stand brengen van een end-to-end-stream voor tunneling van SSH-verkeer een lokale proxy aan elk zijde nodig (zowel bij de service en het apparaat). Gedurende de openbare preview biedt de IoT Hub C SDK alleen ondersteuning voor apparaatstreams aan de apparaatzijde. Volg de instructies in een van de volgende quickstarts om de service-lokale proxy te bouwen en uit te voeren:

   * [SSH/RDP via IoT Hub-apparaatstreams met behulp van C#-proxy-apps](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP via IoT Hub-apparaatstreams met behulp van Node.js-proxy-apps](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Een SSH-sessie tot stand brengen

Nadat de proxy's zowel in het apparaat als in de service worden uitgevoerd, gebruikt u uw SSH-clientprogramma en maakt u verbinding met de proxy in de service op poort 2222 (in plaats van rechtstreeks verbinding te maken met de SSH-daemon).

```cmd/sh
ssh {username}@localhost -p 2222
```

Op dit moment vraagt het SSH-aanmeldingsvenster u om uw referenties in te voeren.

De volgende afbeelding toont de console-uitvoer in de proxy in het apparaat die verbinding maakt met de SSH-daemon via `IP_address:22`:

![Uitvoer van de proxy in het apparaat](./media/quickstart-device-streams-proxy-c/device-console-output.png)

De volgende afbeelding toont de console-uitvoer van het SSH-clientprogramma. De SSH-client communiceert met de SSH-daemon door verbinding te maken met poort 22, waar de proxy in de service naar luistert:

![SSH-clientuitvoer](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een IoT Hub ingesteld, een apparaat geregistreerd, een apparaat en een proxyprogramma in een service geïmplementeerd om een apparaatstream via IoT Hub vast te stellen. U hebt eveneens de proxy's gebruikt om SSH-verkeer via een tunnel te versturen.

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
