---
title: Quick Start-Azure IoT Hub Device streams C# Quick start voor SSH en RDP
description: In deze quickstart voert u twee C#-voorbeeldtoepassingen uit waarmee SSH- en RDP-scenario's via een IoT Hub-apparaatstream mogelijk zijn.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: references_regions
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 12e26818f86fc4abdc1873d031182fd994c04687
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624368"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snelstart: Schakel SSH en RDP in via een IoT Hub-apparaatstream, met behulp van een C#-proxy-toepassing (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub ondersteunt momenteel apparaatstreams als een [preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Deze quickstart bevat twee C#-toepassingen waarmee verkeer van een client-servertoepassing (zoals Secure Shell [SSH] en Remote Desktop Protocol [RDP]) kan worden verzonden via een apparaatstream die tot stand is gebracht met een IoT-hub. Zie [Voorbeeld van een lokale proxytoepassing voor SSH of RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) voor een overzicht van de installatie.

In dit artikel wordt eerst de installatie van SSH beschreven (met behulp van poort 22), en wordt vervolgens beschreven hoe u de installatiepoort voor RDP wijzigt. Omdat apparaatstreams toepassings- en protocolneutraal zijn, kan hetzelfde voorbeeld zodanig worden bewerkt dat andere typen toepassingsverkeer mogelijk zijn. Voor deze aanpassing hoeft meestal alleen de communicatiepoort te worden gewijzigd in de poort die wordt gebruikt voor de beoogde toepassing.

## <a name="prerequisites"></a>Vereisten

* De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * VS - centraal EUAP
  * Azië - zuidoost
  * Europa - noord

* De twee voorbeeldtoepassingen die u uitvoert in deze quickstart, zijn geschreven in C#. Op de ontwikkelcomputer moet .NET Core SDK 2.1.0 of hoger zijn geïnstalleerd.

    U kunt de [.NET Core SDK voor meerdere platforms downloaden van .NET](https://www.microsoft.com/net/download/all).

    Controleer de huidige versie van C# op de ontwikkelcomputer met behulp van de volgende opdracht:

    ```
    dotnet --version
    ```

* [Download de Azure IoT-voorbeelden in C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) en pak het ZIP-archief uit.

* Een geldig gebruikersaccount en geldige referenties op het apparaat (Windows of Linux) dat is gebruikt om de gebruiker te verifiëren.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Uitleg

In de volgende afbeelding ziet u hoe in dit voorbeeld de proxytoepassingen op het apparaat en in de service end-to-end-connectiviteit mogelijk maken tussen de processen voor de SSH-client en SSH-daemon. We gaan ervanuit dat de daemon wordt uitgevoerd op hetzelfde apparaat als de proxytoepassing op het apparaat.

![Installatie van lokale proxytoepassing](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. De proxytoepassing in de service maakt verbinding met de IoT-hubs en initieert een apparaatstream naar het doelapparaat.

1. De proxytoepassing op het apparaat voltooit de stream-handshake en zet een end-to-end-streaming-tunnel op van het streaming-eindpunt van de IoT-hub naar de servicezijde.

1. De proxytoepassing op het apparaat maakt verbinding met de SSH-daemon die luistert bij poort 22 op het apparaat. Deze instelling kan worden geconfigureerd, zoals beschreven in de sectie "de toepassing proxy in het apparaat uitvoeren".

1. De proxytoepassing in de service wacht op nieuwe SSH-verbindingen van een gebruiker door te luisteren bij een opgegeven poort, in dit geval poort 2222. Deze instelling kan worden geconfigureerd, zoals beschreven in de sectie De proxytoepassing in de service uitvoeren. Wanneer de gebruiker verbinding maakt via de SSH-client, zorgt de tunnel ervoor dat SSH-toepassingsverkeer kan worden overgebracht tussen de SSH-client en servertoepassing.

> [!NOTE]
> SSH-verkeer dat via een apparaatstream wordt verstuurd, gaat via een tunnel van het streaming-eindpunt van de IoT-hub in plaats van dat het rechtstreeks tussen de service en het apparaat wordt verzonden. Zie de [voordelen van het gebruik van IOT Hub-apparaten](iot-hub-device-streams-overview.md#benefits) voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze quickstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

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

1. U hebt ook de *serviceverbindingsreeks* van de IoT-hub nodig, zodat de toepassing aan de servicezijde verbinding kan maken met de IoT-hub en een apparaatstream tot stand kan brengen. Met de volgende opdracht haalt u deze waarde voor uw IoT-hub op:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Noteer de geretourneerde serviceverbindingsreeks voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In deze sectie maakt u een end-to-end-stream voor tunneling van SSH-verkeer.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

Navigeer in een lokaal terminalvenster naar de map `device-streams-proxy/device` in uw uitgepakte projectmap. Houd de volgende informatie bij de hand:

| Argumentnaam | Argumentwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | De eerder gemaakte apparaatverbindingsreeks van het apparaat. |
| `targetServiceHostName` | Het IP-adres waar de SSH-server luistert. Het adres is `localhost`, als het hetzelfde IP-adres is waar de proxytoepassing op het apparaat wordt uitgevoerd. |
| `targetServicePort` | De poort die wordt gebruikt voor het toepassingsprotocol (voor SSH is dit standaard poort 22).  |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

Navigeer in een ander lokaal terminalvenster naar `iot-hub/quickstarts/device-streams-proxy/service` in uw uitgepakte projectmap. Houd de volgende informatie bij de hand:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De serviceverbindingsreeks van uw IoT Hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |
| `localPortNumber` | Een lokale poort waarmee de SSH-client verbinding maakt. In dit voorbeeld gebruiken we poort 2222, maar u kunt willekeurige andere getallen gebruiken. |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>De SSH-client uitvoeren

Gebruik nu de SSH-clienttoepassing en maak verbinding met de proxytoepassing in de service via poort 2222 (in plaats van rechtstreeks met de SSH-daemon).

```
ssh {username}@localhost -p 2222
```

Op dit moment vraagt het SSH-aanmeldingsvenster u om uw referenties in te voeren.

Console-uitvoer aan de servicezijde (de proxytoepassing in de service luistert bij poort 2222):

![Uitvoer van de proxytoepassing in de service](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Console-uitvoer in de proxytoepassing op het apparaat, die verbinding maakt met de SSH-daemon via *IP_address:22*:

![Uitvoer van de proxytoepassing op het apparaat](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Console-uitvoer van de SSH-clienttoepassing. De SSH-client communiceert met de SSH-daemon door verbinding te maken met poort 22, waar de proxytoepassing in de service bij luistert:

![Uitvoer van SSH-clienttoepassing](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP naar een apparaat via apparaatstreams

De installatie voor RDP is vergelijkbaar met SSH (hierboven beschreven). U gebruikt in plaats hiervan het doel-IP-adres van de RDP en poort 3389, en u gebruikt de RDP-client (in plaats van de SSH-client).

### <a name="run-the-device-local-proxy-application-rdp"></a>De proxytoepassing op het apparaat uitvoeren (RDP)

Navigeer in een lokaal terminalvenster naar de map `device-streams-proxy/device` in uw uitgepakte projectmap. Houd de volgende informatie bij de hand:

| Argumentnaam | Argumentwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | De eerder gemaakte apparaatverbindingsreeks van het apparaat. |
| `targetServiceHostName` | De hostnaam of het IP-adres waar RDP-server worden uitgevoerd. Het adres is `localhost`, als het hetzelfde IP-adres is waar de proxytoepassing op het apparaat wordt uitgevoerd. |
| `targetServicePort` | De poort die wordt gebruikt voor de toepassingsprotocol (voor RDP is dit standaard poort 3389).  |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>De proxytoepassing in de service uitvoeren (RDP)

Navigeer in een ander lokaal terminalvenster naar `device-streams-proxy/service` in uw uitgepakte projectmap. Houd de volgende informatie bij de hand:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De serviceverbindingsreeks van uw IoT Hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |
| `localPortNumber` | Een lokale poort waarmee de SSH-client verbinding maakt. In dit voorbeeld gebruiken we poort 2222, maar u kunt dat wijzigen in willekeurige andere getallen. |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>RDP-client uitvoeren

Gebruik nu de RDP-clienttoepassing en maak verbinding met de proxytoepassing in de service via poort 2222 (dit is een willekeurige beschikbare poort die u eerder hebt gekozen).

![RDP maakt verbinding met de proxytoepassing in de service](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een IoT Hub ingesteld, een apparaat geregistreerd, en proxytoepassingen op een apparaat en in een service geïmplementeerd om een apparaatstream tot stand te brengen via de IoT-hub. Ook hebt u de proxytoepassingen gebruikt om SSH- of RDP-verkeer te verzenden via een tunnel. Hetzelfde paradigma kan worden gebruikt voor andere client-serverprotocollen, waar de server wordt uitgevoerd op het apparaat (bijvoorbeeld de SSH-daemon).

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)
