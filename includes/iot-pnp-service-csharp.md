---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 4308dd2b63b33604af83b360e5c1c0f02a3dec27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95487791"
---
IoT Plug en Play vereenvoudigt IoT en stelt u in staat om gebruik te maken van de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u C# gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Als u deze quickstart in Windows wilt uitvoeren, moet de volgende software op uw ontwikkelcomputer zijn geïnstalleerd:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Kloon de SDK-opslagplaats met de voorbeeldcode

Als u klaar bent met [Quickstart: Verbind een voorbeeld van een IoT Plug en Play-apparaat-app die in Windows wordt uitgevoerd met IoT Hub (C#)](../articles/iot-pnp/quickstart-connect-device.md). U hebt de opslagplaats al gekloond.

Kloon de voorbeelden uit de GitHub-opslagplaats met Azure IoT-voorbeelden voor C#. Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats voor de [Microsoft Azure IoT-voorbeelden voor .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) te klonen:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u als voorbeeld een thermostaat die in C# is geschreven als het IoT Plug en Play-apparaat. Het voorbeeldapparaat uitvoeren:

1. Open het projectbestand *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* in Visual Studio 2019.

1. Ga in Visual Studio naar **Project > Thermostaateigenschappen > Fouten opsporen**. Voeg vervolgens de volgende omgevingsvariabelen toe aan het project:

    | Naam | Waarde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | De waarde die u hebt genoteerd toen u [Uw omgeving instellen](../articles/iot-pnp/set-up-environment.md) hebt uitgevoerd |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | De waarde die u hebt genoteerd toen u [Uw omgeving instellen](../articles/iot-pnp/set-up-environment.md) hebt uitgevoerd |

1. U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

1. Er worden berichten weergegeven met de melding dat het apparaat een aantal gegevens heeft verzonden en zichzelf online heeft gerapporteerd. Deze berichten geven aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze instantie van Visual Studio niet. U hebt deze nodig om te controleren of het servicevoorbeeld werkt.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In [Quickstarts en zelfstudies voor het instellen van uw omgeving voor IoT Plug en Play](../articles/iot-pnp/set-up-environment.md) hebt u twee omgevingsvariabelen gemaakt om het voorbeeld zo te configureren dat verbinding wordt gemaakt met uw IoT-hub en apparaat:

* **IOTHUB_CONNECTION_STRING**: de verbindingsreeks voor de IoT-hub die u eerder hebt genoteerd.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

In deze quickstart gebruikt u een IoT-voorbeeldoplossing in C# om te werken met het voorbeeldapparaat dat u zojuist hebt ingesteld.

1. Open in een andere Visual Studio-instantie het project *azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. Ga in Visual Studio naar **Project > Thermostaateigenschappen > Fouten opsporen**. Voeg vervolgens de volgende omgevingsvariabelen toe aan het project:

    | Naam | Waarde |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | De waarde die u hebt genoteerd toen u [Uw omgeving instellen](../articles/iot-pnp/set-up-environment.md) hebt uitgevoerd |

1. U kunt het voorbeeld nu in Visual Studio bouwen en uitvoeren in foutopsporingsmodus.

### <a name="get-device-twin"></a>Apparaatdubbel ophalen

Het volgende codefragment laat zien hoe de service-app de apparaatdubbel ophaalt:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> In dit voorbeeld wordt gebruikgemaakt van de naamruimte **Microsoft.Azure.Devices.Client** van de **IoT Hub-serviceclient**. Zie de [handleiding voor serviceontwikkelaars](../articles/iot-pnp/concepts-developer-guide-service.md) voor meer informatie over de API's, waaronder de API's voor digitale dubbels.

Met deze code wordt de volgende uitvoer gegenereerd:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

Het volgende codefragment laat zien hoe u een *patch* gebruikt om eigenschappen bij te werken via de apparaatdubbel:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Met deze code wordt de volgende uitvoer van het apparaat gegenereerd wanneer de service de eigenschap `targetTemperature` bijwerkt:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

Het volgende codefragment laat zien hoe u een opdracht aanroept:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Met deze code wordt de volgende uitvoer van het apparaat gegenereerd wanneer de service de opdracht `getMaxMinReport` aanroept:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```
