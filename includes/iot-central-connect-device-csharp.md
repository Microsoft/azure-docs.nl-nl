---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/25/2020
ms.openlocfilehash: f4536beae18a50d3e1d42fc1593cf826c94418f8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033849"
---
## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt met behulp van de sjabloon **Aangepaste toepassing**. Zie voor meer informatie de [snelstart over het maken van een toepassing](../articles/iot-central/core/quick-deploy-iot-central.md). De toepassing moet op of na 14 juli 2020 zijn gemaakt.
* Een ontwikkelaarsmachine met [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/).
* Een lokale kopie van de [Microsoft Azure IoT-voorbeelden voor C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-opslagplaats die de voorbeeldcode bevat. Gebruik deze koppeling om een kopie van de opslagplaats te downloaden: [ZIP-bestand downloaden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Pak het bestand vervolgens uit op een geschikte locatie op uw lokale computer.

## <a name="review-the-code"></a>De code bekijken

Open in het exemplaar van de Microsoft Azure IoT-voorbeelden voor C#-opslagplaats die u eerder hebt gedownload het projectbestand *azure-iot-samples-csharp-master\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* in Visual Studio. Open in het project **Thermostat** de bestanden *Program.cs* en *ThermostatSample.cs* om de code voor dit voorbeeld weer te geven.

Wanneer u het voorbeeld uitvoert om verbinding te maken met IoT Central, wordt daarvoor gebruikgemaakt van de Device Provisioning Service (DPS) om het apparaat te registreren en een verbindingsreeks te genereren. Het voorbeeld haalt de DPS-verbindingsgegevens die nodig zijn op vanuit de omgeving.

In *Program.cs* roept de methode `main` `SetupDeviceClientAsync` aan:

* Gebruik de model-id `dtmi:com:example:Thermostat;1` wanneer het apparaat wordt ingericht met DPS. IoT Central gebruikt de model-id om de apparaatsjabloon voor dit apparaat te identificeren of te genereren. Zie [Een apparaat koppelen met een apparaatsjabloon](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template) voor meer informatie.
* Maak een exemplaar van **DeviceClient** om verbinding te maken met IoT Central.

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      s_logger.LogDebug($"Initializing via DPS");
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
        // ...
        break;

    default:
        // ...
  }

  return deviceClient;
}
```

De hoofdmethode maakt vervolgens een exemplaar van **ThermostatSample** en roept de methode `PerformOperationsAsync` aan om de interacties met IoT Central te verwerken.

In *ThermostatSample.cs* doet de methode `PerformOperationsAsync` het volgende:

* Het stelt een handler in voor het ontvangen van de gewenste eigenschap-updates van de doeltemperatuur.
* Stelt een handler in voor de opdracht **getMaxMinReport**.
* Verzendt periodiek temperatuurtelemetriegegevens.
* Verzendt de maximale temperatuur sinds de laatste keer opnieuw opstarten wanneer een nieuwe maximum temperatuur wordt bereikt.

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(TargetTemperatureUpdateCallbackAsync, _deviceClient, cancellationToken);

  await _deviceClient.SetMethodHandlerAsync("getMaxMinReport", HandleMaxMinReportCommand, _deviceClient, cancellationToken);

  bool temperatureReset = true;
  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
      _temperature = Math.Round(_random.NextDouble() * 40.0 + 5.0, 1);
      temperatureReset = false;
    }

    await SendTemperatureAsync();
    await Task.Delay(5 * 1000);
  }
}
```

De methode `SendTemperatureAsync` laat zien hoe het apparaat de temperatuurtelemetriegegevens verzendt naar IoT Central:

```csharp
private async Task SendTemperatureAsync()
{
  await SendTemperatureTelemetryAsync();

  double maxTemp = _temperatureReadingsDateTimeOffset.Values.Max<double>();
  if (maxTemp > _maxTemp)
  {
    _maxTemp = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync();
  }
}
```

De methode `UpdateMaxTemperatureSinceLastRebootAsync` verzendt een `maxTempSinceLastReboot`-eigenschapupdate naar IoT Central:

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync()
{
  const string propertyName = "maxTempSinceLastReboot";

  var reportedProperties = new TwinCollection();
  reportedProperties[propertyName] = _maxTemp;

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties);
}
```

De methode `TargetTemperatureUpdateCallbackAsync` zorgt voor de update van de beschrijfbare eigenschap doeltemperatuur van IoT Central:

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
    const string propertyName = "targetTemperature";

    (bool targetTempUpdateReceived, double targetTemperature) = GetPropertyFromTwin<double>(desiredProperties, propertyName);
    if (targetTempUpdateReceived)
    {
      string jsonPropertyPending = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.InProgress}, " +
          $"\"av\": {desiredProperties.Version} }} }}";
      var reportedPropertyPending = new TwinCollection(jsonPropertyPending);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedPropertyPending);

      // Update Temperature in 2 steps
      double step = (targetTemperature - _temperature) / 2d;
      for (int i = 1; i <= 2; i++)
      {
        _temperature = Math.Round(_temperature + step, 1);
        await Task.Delay(6 * 1000);
      }

      string jsonProperty = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.Completed}, " +
        $"\"av\": {desiredProperties.Version}, \"ad\": \"Successfully updated target temperature\" }} }}";
      var reportedProperty = new TwinCollection(jsonProperty);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedProperty);
  }
  else
  {
    // ...
  }
}
```

De methode `HandleMaxMinReportCommand` verwerkt de opdracht die wordt aangeroepen vanuit IoT Central:

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
  try
  {
    DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
    var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

    Dictionary<DateTimeOffset, double> filteredReadings = _temperatureReadingsDateTimeOffset
      .Where(i => i.Key > sinceInDateTimeOffset)
      .ToDictionary(i => i.Key, i => i.Value);

    if (filteredReadings != null && filteredReadings.Any())
    {
      var report = new
      {
        maxTemp = filteredReadings.Values.Max<double>(),
        minTemp = filteredReadings.Values.Min<double>(),
        avgTemp = filteredReadings.Values.Average(),
        startTime = filteredReadings.Keys.Min(),
        endTime = filteredReadings.Keys.Max(),
      };

      byte[] responsePayload = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(report));
      return Task.FromResult(new MethodResponse(responsePayload, (int)StatusCode.Completed));
    }

    return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
  }
  catch (JsonReaderException ex)
  {
    // ...
  }
}
```

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>De code uitvoeren

Om de voorbeeldtoepassing uit te voeren:

1. Open het projectbestand *azure-iot-samples-csharp-master/iot-hub/Samples/device/PnpDeviceSamples/Thermostat/Thermostat.csproj* in Visual Studio.

1. Ga in Visual Studio naar **Project > Thermostaateigenschappen > Fouten opsporen**. Voeg vervolgens de volgende omgevingsvariabelen toe aan het project:

    | Naam | Waarde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | De waarde van het id-bereik dat u eerder hebt genoteerd. |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | sample-device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | De gegenereerde waarde van de apparaatsleutel die u eerder hebt genoteerd. |

U kunt het voorbeeld nu uitvoeren en fouten opsporen in Visual Studio.

In de volgende uitvoer ziet u hoe het apparaat zich registreert bij IoT Central en er verbinding mee maakt. Het voorbeeld begint met het verzenden van telemetriegegevens:

```output
[11/25/2020 11:07:58]info: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Press Control+C to quit the sample.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set up the device client.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Initializing via DPS
[11/25/2020 11:08:11]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler to receive "targetTemperature" updates.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler for "getMaxMinReport" command.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 36.5°C } is Completed.
[11/25/2020 11:08:18]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:23]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:29]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

U kunt zien hoe het apparaat reageert op opdrachten en updates van eigenschappen:

```output
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 19/11/2020 06:30:00.
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: MaxMinReport since 19/11/2020 06:30:00: maxTemp=36.5, minTemp=36.5, avgTemp=36.5, startTime=25/11/2020 11:08:13, endTime=25/11/2020 11:09:51

...

[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Received - { "targetTemperature": 56°C }.
[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is InProgress.
[11/25/2020 11:14:40]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 56°C } is Completed.
[11/25/2020 11:14:43]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is Completed.
```
