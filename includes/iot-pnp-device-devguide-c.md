---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 1485adc1407720343c27e07c2d2f4a13ba037a90
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511471"
---
## <a name="model-id-announcement"></a>Aankondiging van model-ID

Als u de model-ID wilt aankondigen, moet het apparaat deze in de verbindings informatie bevatten:

```c
static const char g_ThermostatModelId[] = "dtmi:com:example:Thermostat;1";
IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceHandle = NULL;
deviceHandle = CreateDeviceClientLLHandle();
iothubResult = IoTHubDeviceClient_LL_SetOption(
    deviceHandle, OPTION_MODEL_ID, g_ThermostatModelId);
```

> [!TIP]
> Voor modules en IoT Edge, gebruikt u `IoTHubModuleClient_LL` in plaats van `IoTHubDeviceClient_LL` .

## <a name="dps-payload"></a>DPS-nettolading

Apparaten met behulp van de [Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) kunnen de gebruiken die `modelId` tijdens het inrichtings proces moet worden gebruikt met behulp van de volgende JSON-nettolading.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Telemetrie, eigenschappen en opdrachten implementeren

Zoals beschreven in [inzicht in onderdelen in IoT Plug en Play-modellen](../articles/iot-pnp/concepts-components.md), moeten apparaten kunnen bepalen of ze onderdelen willen gebruiken om hun apparaten te beschrijven. Bij het gebruik van onderdelen moeten apparaten voldoen aan de regels die in deze sectie worden beschreven.

### <a name="telemetry"></a>Telemetrie

Voor een standaard onderdeel is geen speciale eigenschap vereist.

Bij het gebruik van geneste onderdelen moeten apparaten een bericht eigenschap met de naam van het onderdeel instellen:

```c
void PnP_ThermostatComponent_SendTelemetry(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    IOTHUB_CLIENT_RESULT iothubResult;

    char temperatureStringBuffer[32];

    if (snprintf(
        temperatureStringBuffer,
        sizeof(temperatureStringBuffer),
        g_temperatureTelemetryBodyFormat,
        pnpThermostatComponent->currentTemperature) < 0)
    {
        LogError("snprintf of current temperature telemetry failed");
    }
    else if ((messageHandle = PnP_CreateTelemetryMessageHandle(
        pnpThermostatComponent->componentName, temperatureStringBuffer)) == NULL)
    {
        LogError("Unable to create telemetry message");
    }
    else if ((iothubResult = IoTHubDeviceClient_LL_SendEventAsync(
        deviceClientLL, messageHandle, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send telemetry message, error=%d", iothubResult);
    }

    IoTHubMessage_Destroy(messageHandle);
}

// ...

PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
```

### <a name="read-only-properties"></a>Alleen-lezen eigenschappen

Voor het melden van een eigenschap vanuit het standaard onderdeel is geen speciale construct vereist:

```c
static const char g_maxTemperatureSinceRebootFormat[] = "{\"maxTempSinceLastReboot\":%.2f}";

char maxTemperatureSinceRebootProperty[256];

snprintf(
    maxTemperatureSinceRebootProperty,
    sizeof(maxTemperatureSinceRebootProperty),
    g_maxTemperatureSinceRebootFormat,
    38.7);

IOTHUB_CLIENT_RESULT iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)maxTemperatureSinceRebootProperty,
    strlen(maxTemperatureSinceRebootProperty), NULL, NULL));
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Bij het gebruik van geneste onderdelen moeten eigenschappen worden gemaakt binnen de naam van het onderdeel:

```c
STRING_HANDLE PnP_CreateReportedProperty(
    const char* componentName,
    const char* propertyName,
    const char* propertyValue
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":%s}",
            propertyName, propertyValue);
    }
    else 
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":%s}}",
            componentName, propertyName, propertyValue);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

void PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(
    PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle,
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent =
        (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
    char maximumTemperatureAsString[32];
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;

    if (snprintf(maximumTemperatureAsString, sizeof(maximumTemperatureAsString),
        "%.2f", pnpThermostatComponent->maxTemperature) < 0)
    {
        LogError("Unable to create max temp since last reboot string for reporting result");
    }
    else if ((jsonToSend = PnP_CreateReportedProperty(
                pnpThermostatComponent->componentName,
                g_maxTempSinceLastRebootPropertyName,
                maximumTemperatureAsString)) == NULL)
    {
        LogError("Unable to build max temp since last reboot property");
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
                deviceClientLL,
                (const unsigned char*)jsonToSendStr,
                jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
        {
            LogError("Unable to send reported state, error=%d", iothubClientResult);
        }
        else
        {
            LogInfo("Sending maximumTemperatureSinceLastReboot property to IoTHub for component=%s",
                pnpThermostatComponent->componentName);
        }
    }

    STRING_delete(jsonToSend);
}

// ...

PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
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

```c
IOTHUB_CLIENT_RESULT iothubClientResult;
char targetTemperatureResponseProperty[256];

snprintf(
    targetTemperatureResponseProperty,
    sizeof(targetTemperatureResponseProperty),
    "{\"targetTemperature\":{\"value\":%.2f,\"ac\":%d,\"av\":%d,\"ad\":\"%s\"}}",
    23.2, 200, 3, "Successfully updated target temperature");

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)targetTemperatureResponseProperty,
    strlen(targetTemperatureResponseProperty), NULL, NULL);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Als u een Beschrijf bare eigenschap van een genest onderdeel wilt melden, moet de dubbele markering het volgende bevatten:

```c
STRING_HANDLE PnP_CreateReportedPropertyWithStatus(const char* componentName,
    const char* propertyName, const char* propertyValue,
    int result, const char* description, int ackVersion
)
{
    STRING_HANDLE jsonToSend;

    if (componentName == NULL) 
    {
        jsonToSend = STRING_construct_sprintf(
            "{\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}",
            propertyName, propertyValue,
            result, description, ackVersion);
    }
    else
    {
       jsonToSend = STRING_construct_sprintf(
            "{\"""%s\":{\"__t\":\"c\",\"%s\":{\"value\":%s,\"ac\":%d,\"ad\":\"%s\",\"av\":%d}}}",
            componentName, propertyName, propertyValue,
            result, description, ackVersion);
    }

    if (jsonToSend == NULL)
    {
        LogError("Unable to allocate JSON buffer");
    }

    return jsonToSend;
}

// ...

char targetTemperatureAsString[32];
IOTHUB_CLIENT_RESULT iothubClientResult;
STRING_HANDLE jsonToSend = NULL;

snprintf(targetTemperatureAsString,
    sizeof(targetTemperatureAsString),
    "%.2f",
    23.2);
jsonToSend = PnP_CreateReportedPropertyWithStatus(
    "thermostat1",
    "targetTemperature",
    targetTemperatureAsString,
    200,
    "complete",
    3);

const char* jsonToSendStr = STRING_c_str(jsonToSend);
size_t jsonToSendStrLen = strlen(jsonToSendStr);

iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(
    deviceClientLL,
    (const unsigned char*)jsonToSendStr,
    jsonToSendStrLen, NULL, NULL);

STRING_delete(jsonToSend);
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

```c
static void Thermostat_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback)
{
    // The device handle associated with this request is passed as the context,
    // since we will need to send reported events back.
    IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL =
        (IOTHUB_DEVICE_CLIENT_LL_HANDLE)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    JSON_Value* versionValue = NULL;
    JSON_Value* targetTemperatureValue = NULL;

    jsonStr = CopyTwinPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    targetTemperatureValue = json_object_get_value(desiredObject, "targetTemperature"));
    versionValue = json_object_get_value(desiredObject, "$version"));
    json_value_get_type(versionValue);
    json_value_get_type(targetTemperatureValue);

    double targetTemperature = json_value_get_number(targetTemperatureValue);
    int version = (int)json_value_get_number(versionValue);

    // ...

    // The device needs to let the service know that it has received the targetTemperature desired property.
    SendTargetTemperatureReport(deviceClientLL, targetTemperature, 200, version, "Successfully updated target temperature");

    json_value_free(rootValue);
    free(jsonStr);
}

// ...

IOTHUB_CLIENT_RESULT iothubResult;
iothubResult = IoTHubDeviceClient_LL_SetDeviceTwinCallback(
    deviceHandle, Thermostat_DeviceTwinCallback, (void*)deviceHandle))
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
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Een geneste component ontvangt de gewenste eigenschappen met de naam van het onderdeel en moet de gerapporteerde eigenschap weer geven `ack` :

```c
bool PnP_ProcessTwinData(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size, const char** componentsInModel,
    size_t numComponentsInModel,
    PnP_PropertyCallbackFunction pnpPropertyCallback,
    void* userContextCallback)
{
    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    JSON_Object* desiredObject;
    bool result;

    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    desiredObject = GetDesiredJson(updateState, rootValue));
    
    result = VisitDesiredObject(
        desiredObject, componentsInModel,
        numComponentsInModel, pnpPropertyCallback,
        userContextCallback);


    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...
static const char g_thermostatComponent1Name[] = "thermostat1";
static const size_t g_thermostatComponent1Size = sizeof(g_thermostatComponent1Name) - 1;
static const char g_thermostatComponent2Name[] = "thermostat2";

static const char* g_modeledComponents[] = {g_thermostatComponent1Name, g_thermostatComponent2Name};
static const size_t g_numModeledComponents = sizeof(g_modeledComponents) / sizeof(g_modeledComponents[0]);

static void PnP_TempControlComponent_DeviceTwinCallback(
    DEVICE_TWIN_UPDATE_STATE updateState,
    const unsigned char* payload,
    size_t size,
    void* userContextCallback
)
{
    PnP_ProcessTwinData(
        updateState, payload,
        size, g_modeledComponents,
        g_numModeledComponents,
        PnP_TempControlComponent_ApplicationPropertyCallback,
        userContextCallback);
}
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

### <a name="commands"></a>Opdrachten

Een standaard onderdeel ontvangt de opdracht naam zoals deze is aangeroepen door de service.

Een genest onderdeel ontvangt de opdracht naam voorafgegaan door de naam van het onderdeel en het `*` scheidings teken.

```c
void PnP_ParseCommandName(
    const char* deviceMethodName,
    unsigned const char** componentName,
    size_t* componentNameSize,
    const char** pnpCommandName
)
{
    const char* separator;

    if ((separator = strchr(deviceMethodName, "*")) != NULL)
    {
        *componentName = (unsigned const char*)deviceMethodName;
        *componentNameSize = separator - deviceMethodName;
        *pnpCommandName = separator + 1;
    }
    else
    {
        *componentName = NULL;
        *componentNameSize = 0;
        *pnpCommandName = deviceMethodName;
    }
}

static int PnP_TempControlComponent_DeviceMethodCallback(
    const char* methodName,
    const unsigned char* payload,
    size_t size,
    unsigned char** response,
    size_t* responseSize,
    void* userContextCallback)
{
    (void)userContextCallback;

    char* jsonStr = NULL;
    JSON_Value* rootValue = NULL;
    int result;
    unsigned const char *componentName;
    size_t componentNameSize;
    const char *pnpCommandName;

    *response = NULL;
    *responseSize = 0;

    // Parse the methodName into its componentName and CommandName.
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);

    // Parse the JSON of the payload request.
    jsonStr = PnP_CopyPayloadToString(payload, size));
    rootValue = json_parse_string(jsonStr));
    if (componentName != NULL)
    {
        if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
        {
            result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
        }
        else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
        {
            result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
        }
        else
        {
            LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }
    else
    {
        LogInfo("Received PnP command for TemperatureController component, command=%s", pnpCommandName);
        if (strcmp(pnpCommandName, g_rebootCommand) == 0)
        {
            result = PnP_TempControlComponent_InvokeRebootCommand(rootValue);
        }
        else
        {
            LogError("PnP command=s%s is not supported by TemperatureController", pnpCommandName);
            result = PNP_STATUS_NOT_FOUND;
        }
    }

    if (*response == NULL)
    {
        SetEmptyCommandResponse(response, responseSize, &result);
    }

    json_value_free(rootValue);
    free(jsonStr);

    return result;
}

// ...

PNP_DEVICE_CONFIGURATION g_pnpDeviceConfiguration;
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

#### <a name="request-and-response-payloads"></a>Nettoladingen voor aanvragen en antwoorden

Opdrachten gebruiken typen om hun aanvraag-en reactie-nettoladingen te definiëren. Een apparaat moet de binnenkomende invoer parameter deserialiseren en het antwoord serialiseren. In het volgende voor beeld ziet u hoe u een opdracht implementeert met complexe typen die zijn gedefinieerd in de payloads:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

De volgende code fragmenten laten zien hoe een apparaat deze opdracht definitie implementeert, met inbegrip van de typen die worden gebruikt voor het inschakelen van serialisatie en deserialisatie:

```c
static const char g_maxMinCommandResponseFormat[] = "{\"maxTemp\":%.2f,\"minTemp\":%.2f,\"avgTemp\":%.2f,\"startTime\":\"%s\",\"endTime\":\"%s\"}";

// ...

static bool BuildMaxMinCommandResponse(
    PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent,
    unsigned char** response,
    size_t* responseSize)
{
    int responseBuilderSize = 0;
    unsigned char* responseBuilder = NULL;
    bool result;
    char currentTime[TIME_BUFFER_SIZE];

    BuildUtcTimeFromCurrentTime(currentTime, sizeof(currentTime));
    responseBuilderSize = snprintf(NULL, 0, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures /
        pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime, currentTime));

    responseBuilder = calloc(1, responseBuilderSize + 1));

    responseBuilderSize = snprintf(
        (char*)responseBuilder, responseBuilderSize + 1, g_maxMinCommandResponseFormat,
        pnpThermostatComponent->maxTemperature,
        pnpThermostatComponent->minTemperature,
        pnpThermostatComponent->allTemperatures / pnpThermostatComponent->numTemperatureUpdates,
        g_programStartTime,
        currentTime));

    *response = responseBuilder;
    *responseSize = (size_t)responseBuilderSize;

    return true;
}
```

> [!Tip]
> De namen van de aanvraag en de reactie zijn niet aanwezig in de geserialiseerde nettoladingen die via de kabel worden verzonden.
