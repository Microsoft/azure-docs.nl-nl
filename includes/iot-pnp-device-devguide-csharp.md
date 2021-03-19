---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 38a29aa765bde4adb390423a9d180132b57d4fee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582761"
---
## <a name="model-id-announcement"></a>Aankondiging van model-ID

Als u de model-ID wilt aankondigen, moet het apparaat deze in de verbindings informatie bevatten:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

De nieuwe `ClientOptions` overbelasting is beschikbaar in alle `DeviceClient` methoden die worden gebruikt voor het initialiseren van een verbinding.

> [!TIP]
> Voor modules en IoT Edge, gebruikt u `ModuleClient` in plaats van `DeviceClient` .

> [!TIP]
> Dit is de enige keer dat een apparaat model-ID kan instellen. Dit kan niet worden bijgewerkt nadat het apparaat verbinding maakt.

## <a name="dps-payload"></a>DPS-nettolading

Apparaten met behulp van de [Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) kunnen de gebruiken die `modelId` tijdens het inrichtings proces moet worden gebruikt met behulp van de volgende JSON-nettolading.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Telemetrie, eigenschappen en opdrachten implementeren

Zoals beschreven in [inzicht in onderdelen in IoT Plug en Play-modellen](../articles/iot-pnp/concepts-modeling-guide.md), moeten apparaten kunnen bepalen of ze onderdelen willen gebruiken om hun apparaten te beschrijven. Bij het gebruik van onderdelen moeten apparaten voldoen aan de regels die in deze sectie worden beschreven.

### <a name="telemetry"></a>Telemetrie

Voor een standaard onderdeel is geen speciale eigenschap vereist.

Bij het gebruik van geneste onderdelen moeten apparaten een bericht eigenschap met de naam van het onderdeel instellen:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Alleen-lezen eigenschappen

Voor het melden van een eigenschap vanuit het standaard onderdeel is geen speciale construct vereist:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Bij het gebruik van geneste onderdelen moeten eigenschappen worden gemaakt binnen de naam van het onderdeel:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Beschrijf bare eigenschappen

Deze eigenschappen kunnen worden ingesteld door het apparaat of worden bijgewerkt door de oplossing. Als met de oplossing een eigenschap wordt bijgewerkt, ontvangt de client een melding als een retour aanroep in de `DeviceClient` of `ModuleClient` . Als u de IoT Plug en Play-conventies wilt volgen, moet het apparaat de service op de hoogte stellen dat de eigenschap is ontvangen.

#### <a name="report-a-writable-property"></a>Een schrijf bare eigenschap rapporteren

Wanneer een apparaat een Beschrijf bare eigenschap rapporteert, moet dit de waarden bevatten die zijn `ack` gedefinieerd in de conventies.

Een Beschrijf bare eigenschap van het standaard onderdeel melden:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Als u een Beschrijf bare eigenschap van een genest onderdeel wilt melden, moet de dubbele markering het volgende bevatten:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Abonneren op de gewenste eigenschaps updates

Services kunnen de gewenste eigenschappen bijwerken waarmee een melding op de verbonden apparaten wordt geactiveerd. Deze melding bevat de bijgewerkte gewenste eigenschappen, inclusief het versie nummer waarmee de update wordt geïdentificeerd. Apparaten moeten reageren met hetzelfde `ack` bericht als gerapporteerde eigenschappen.

Een standaard onderdeel ziet de eigenschap single en maakt de gerapporteerde `ack` met de ontvangen versie:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Het apparaat dubbele toont de eigenschap in de gewenste en gerapporteerde secties:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Een geneste component ontvangt de gewenste eigenschappen met de naam van het onderdeel en moet de gerapporteerde eigenschap weer geven `ack` :

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Het apparaat dubbele voor een genest onderdeel toont de gewenste en gerapporteerde secties als volgt:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Opdracht

Een standaard onderdeel ontvangt de opdracht naam zoals deze is aangeroepen door de service.

Een genest onderdeel ontvangt de opdracht naam voorafgegaan door de naam van het onderdeel en het `*` scheidings teken.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Nettoladingen voor aanvragen en antwoorden

Opdrachten gebruiken typen om hun aanvraag-en reactie-nettoladingen te definiëren. Een apparaat moet de binnenkomende invoer parameter deserialiseren en het antwoord serialiseren. In het volgende voor beeld ziet u hoe u een opdracht implementeert met complexe typen die zijn gedefinieerd in de payloads:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

De volgende code fragmenten laten zien hoe een apparaat deze opdracht definitie implementeert, met inbegrip van de typen die worden gebruikt voor het inschakelen van serialisatie en deserialisatie:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> De namen van de aanvraag en de reactie zijn niet aanwezig in de geserialiseerde nettoladingen die via de kabel worden verzonden.
