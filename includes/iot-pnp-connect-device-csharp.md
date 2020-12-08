---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 13d0bdf82052ff2c61c5b2c6010956c8fb27574d
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122459"
---
In deze quickstart ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de telemetrie weer te geven die wordt verzonden. De voorbeeldtoepassing wordt geschreven in C# en is inbegrepen in de Azure IoT Samples for C#. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Als u deze quickstart in Windows wilt uitvoeren, moet de volgende software op uw ontwikkelcomputer zijn geïnstalleerd:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>De code downloaden

In deze quickstart bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de opslagplaats Azure IoT Samples for C#.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) te klonen naar deze locatie:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>De code bouwen

U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

1. Open het projectbestand *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* in Visual Studio 2019.

1. Ga in Visual Studio naar **Project > Thermostaateigenschappen > Fouten opsporen**. Voeg vervolgens de volgende omgevingsvariabelen toe aan het project:

    | Naam | Waarde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | De waarde die u hebt genoteerd toen u [Uw omgeving instellen](../articles/iot-pnp/set-up-environment.md) hebt uitgevoerd |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | De waarde die u hebt genoteerd toen u [Uw omgeving instellen](../articles/iot-pnp/set-up-environment.md) hebt uitgevoerd |

U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

## <a name="run-the-device-sample"></a>Het apparaatvoorbeeld uitvoeren

Om de code-uitvoering in Visual Studio in Windows bij te houden, voegt u een onderbrekingspunt toe aan de functie `main` in het bestand program.cs.

Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen, en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een eenvoudig IoT Plug and Play-thermostaatapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt geen gebruik van IoT Plug and Play-[onderdelen](../articles/iot-pnp/concepts-components.md). Het [Digital Twins Definition Language-modelbestand (DTDL) voor het thermostaatapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

De apparaatcode maakt verbinding met uw IoT-hub met behulp van de standaardmethode `CreateFromConnectionString`. Het apparaat verzendt de model-id van het DTDL-model dat het in de verbindingsaanvraag implementeert. Een apparaat dat een model-id verzendt, is een IoT Plug and Play-apparaat:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

De model-id wordt in de code opgeslagen, zoals weergegeven in het volgende fragment:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

De code die eigenschappen bijwerkt, opdrachten verwerkt en telemetrie verzendt, is identiek aan de code voor een apparaat die de IoT Plug and Play-conventies niet gebruikt.

Het voorbeeld maakt gebruik van een JSON-bibliotheek om JSON-objecten in de nettoladingen te parseren die worden verzonden vanuit uw IoT-hub:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
