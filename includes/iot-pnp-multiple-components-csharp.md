---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: cd87978f9ec34e103ede869360858c5633a8c6ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612677"
---
In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met onderdelen maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing wordt geschreven in C# en is inbegrepen in de Azure IoT device-SDK voor C#. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Download de voorbeeldcode.
> * De voorbeeld code maken.
> * Voer de toepassing voor voorbeeld apparaten uit en controleer of deze verbinding maakt met uw IoT-hub.
> * Controleer de bron code.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Om deze zelfstudie in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Kloon de SDK-opslagplaats met de voorbeeldcode

Als u klaar bent met [Quickstart: Verbind een voorbeeld van een IoT Plug en Play-apparaat-app die in Windows wordt uitgevoerd met IoT Hub (C#)](../articles/iot-pnp/quickstart-connect-device.md). U hebt de opslagplaats al gekloond.

Kloon de voorbeelden uit de GitHub-opslagplaats met Azure IoT-voorbeelden voor C#. Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats voor de [Microsoft Azure IoT-voorbeelden voor .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) te klonen:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u als voorbeeld een temperatuurregelaar die in C# is geschreven als het IoT Plug en Play-apparaat. Het voorbeeldapparaat uitvoeren:

1. Open het projectbestand *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* in Visual Studio 2019.

1. Ga in Visual Studio naar **Project > Eigenschappen temperatuurregelaar > Fouten opsporen**. Voeg vervolgens de volgende omgevingsvariabelen toe aan het project:

    | Naam | Waarde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | De waarde die u hebt genoteerd toen u [Uw omgeving instellen](../articles/iot-pnp/set-up-environment.md) hebt uitgevoerd |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | De waarde die u hebt genoteerd toen u [Uw omgeving instellen](../articles/iot-pnp/set-up-environment.md) hebt uitgevoerd |


1. U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

1. Er worden berichten weergegeven met de melding dat het apparaat een aantal gegevens heeft verzonden en zichzelf online heeft gerapporteerd. Deze berichten geven aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze instantie van Visual Studio niet. U hebt deze nodig om te controleren of het servicevoorbeeld werkt.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een IoT Plug and Play-temperatuurregelingsapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt gebruik van [meerdere onderdelen](../articles/iot-pnp/concepts-modeling-guide.md). Het [Digital Twins Definition Language-modelbestand (DTDL) voor het temperatuurregelingsapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

De apparaatcode maakt verbinding met uw IoT-hub met behulp van de standaardmethode `CreateFromConnectionString`. Het apparaat verzendt de model-id van het DTDL-model dat het in de verbindingsaanvraag implementeert. Een apparaat dat een model-id verzendt, is een IoT Plug and Play-apparaat:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

De model-id wordt in de code opgeslagen, zoals weergegeven in het volgende fragment:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Nadat het apparaat verbinding heeft gemaakt met uw IoT-hub, registreert de code de opdrachthandlers. De opdracht `reboot` wordt gedefinieerd in het standaardonderdeel. De opdracht `getMaxMinReport` wordt gedefinieerd in elk van de twee thermostaatonderdelen:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

Er zijn afzonderlijke handlers voor de gewenste updates voor eigenschappen in de twee thermostaatonderdelen:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

De voorbeeldcode verzendt telemetrie vanuit elk thermostaatonderdeel:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

De `SendTemperatureTelemetryAsync`-methode maakt gebruik van de `PnpHhelper`-klasse om berichten voor elk onderdeel te maken:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

De `PnpHelper`-klasse bevat andere voorbeeldmethoden die u kunt gebruiken met een model met meerdere onderdelen.

Gebruik het hulpprogramma Azure IoT Explorer om de telemetrie en eigenschappen uit de twee thermostaatonderdelen weer te geven:

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Apparaat met meerdere onderdelen in Azure IoT Explorer":::

U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om opdrachten aan te roepen in een van de twee thermostaatonderdelen of in het hoofdonderdeel.
