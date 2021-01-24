---
title: Een adapter bouwen voor de IoT Plug en Play-brug | Microsoft Docs
description: Identificeer de onderdelen van de IoT Plug en Play Bridge-adapter. Meer informatie over het uitbreiden van de Bridge door uw eigen adapter te schrijven.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746823"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>De IoT Plug en Play-brug uitbreiden
Met de [iot Plug en Play-brug](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) kunt u de bestaande apparaten die zijn gekoppeld aan een gateway, verbinden met uw IOT-hub. U gebruikt de Bridge om IoT Plug en Play-interfaces toe te wijzen aan de gekoppelde apparaten. Een IoT Plug en Play-interface definieert de telemetrie die een apparaat verzendt, de eigenschappen gesynchroniseerd tussen het apparaat en de Cloud en de opdrachten waarop het apparaat reageert. U kunt de open-source Bridge-toepassing installeren en configureren op Windows-of Linux-gateways. Daarnaast kan de Bridge worden uitgevoerd als een Azure IoT Edge runtime-module.

In dit artikel wordt uitgelegd hoe u het volgende kunt doen:

- Breid de IoT Plug en Play-brug uit met een adapter.
- Implementeer algemene retour aanroepen voor een brug adapter.

Zie voor een eenvoudig voor beeld waarin wordt getoond hoe u de Bridge kunt gebruiken [hoe u verbinding maakt met het IoT Plug en Play Bridge-voor beeld dat op Linux of Windows wordt uitgevoerd om te IOT hub](howto-use-iot-pnp-bridge.md).

In de richt lijnen en voor beelden in dit artikel wordt ervan uitgegaan dat u bekend bent met de basis kennis van [Azure Digital apparaatdubbels](../digital-twins/overview.md) en [IOT Plug en Play](overview-iot-plug-and-play.md). Daarnaast wordt er in dit artikel van uitgegaan dat u bekend bent met [het bouwen en implementeren van de IoT Plug en Play-brug](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Ontwerp handleiding voor het uitbreiden van de IoT Plug en Play-brug met een adapter

Als u de mogelijkheden van de Bridge wilt uitbreiden, kunt u uw eigen brug adapters ontwerpen.

De Bridge gebruikt adapters voor het volgende:

- Een verbinding tot stand brengen tussen een apparaat en de Cloud.
- Schakel de gegevens stroom tussen een apparaat en de cloud in.
- Schakel Apparaatbeheer in vanuit de Cloud.

Elke brug adapter moet:

- Een Digital apparaatdubbels-interface maken.
- Gebruik de interface om de functionaliteit van het apparaat te binden aan Cloud mogelijkheden zoals telemetrie, eigenschappen en opdrachten.
- Bepaal de controle-en gegevens communicatie met de hardware of firmware van het apparaat.

Elke brug adapter communiceert met een specifiek type apparaat op basis van de manier waarop de adapter verbinding maakt en communiceert met het apparaat. Zelfs als communicatie met een apparaat een shaking-protocol gebruikt, kan een brug adapter meerdere manieren hebben om de gegevens van het apparaat te interpreteren. In dit scenario gebruikt de brug adapter informatie voor de adapter in het configuratie bestand om de *interface configuratie* te bepalen die de adapter moet gebruiken voor het parseren van de gegevens.

Voor de interactie met het apparaat gebruikt een brug adapter een communicatie protocol dat wordt ondersteund door het apparaat en Api's die worden geboden door het onderliggende besturings systeem of de leverancier van het apparaat.

Voor de interactie met de Cloud gebruikt een brug adapter Api's die worden geleverd door de Azure IoT Device C SDK voor het verzenden van telemetrie, het maken van digitale dubbele interfaces, het verzenden van eigenschappen updates en het maken van call back-functies voor eigenschaps updates en opdrachten.

### <a name="create-a-bridge-adapter"></a>Een brug adapter maken

De Bridge verwacht een brug adapter voor het implementeren van de Api's die zijn gedefinieerd in de [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) -Interface:

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

In deze interface:

- `PNPBRIDGE_ADAPTER_CREATE` Hiermee maakt u de adapter en worden de bronnen voor interface beheer ingesteld. Een adapter kan ook gebruikmaken van globale adapter parameters voor het maken van adapters. Deze functie wordt eenmaal voor één adapter aangeroepen.
- `PNPBRIDGE_COMPONENT_CREATE` maakt de digitale twee-client interfaces en koppelt de call back-functies. De adapter initieert het communicatie kanaal op het apparaat. De adapter kan de resources zo instellen dat de telemetrische stroom wordt ingeschakeld, maar er wordt geen telemetrie gerapporteerd totdat deze `PNPBRIDGE_COMPONENT_START` wordt aangeroepen. Deze functie wordt eenmaal aangeroepen voor elk interface onderdeel in het configuratie bestand.
- `PNPBRIDGE_COMPONENT_START` wordt aangeroepen zodat de brug adapter telemetrie van het apparaat naar de digitale dubbele client kan sturen. Deze functie wordt eenmaal aangeroepen voor elk interface onderdeel in het configuratie bestand.
- `PNPBRIDGE_COMPONENT_STOP` Hiermee wordt de telemetrie stroom gestopt.
- `PNPBRIDGE_COMPONENT_DESTROY` de digitale dubbele client en de bijbehorende interface bronnen worden vernietigd. Deze functie wordt eenmaal aangeroepen voor elk interface onderdeel in het configuratie bestand wanneer de Bridge wordt gescheurd of wanneer er een fatale fout optreedt.
- `PNPBRIDGE_ADAPTER_DESTROY` de resources van de brug adapter opruimen.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Kern interactie van Bridge met brug adapters

In de volgende lijst wordt beschreven wat er gebeurt wanneer de Bridge wordt gestart:

1. Wanneer de Bridge wordt gestart, zoekt de brug adapter Manager elk interface onderdeel dat is gedefinieerd in het configuratie bestand en aanroepen `PNPBRIDGE_ADAPTER_CREATE` op de juiste adapter. De adapter kan globale adapter configuratie parameters gebruiken om bronnen in te stellen voor de ondersteuning van de verschillende *Interface configuraties*.
1. Voor elk apparaat in het configuratie bestand initieert Bridge Manager het maken van de interface door de `PNPBRIDGE_COMPONENT_CREATE` juiste brug adapter aan te roepen.
1. De adapter ontvangt alle optionele adapter configuratie-instellingen voor het interface onderdeel en gebruikt deze informatie om verbindingen met het apparaat in te stellen.
1. De adapter maakt de digitale twee client interfaces en koppelt de call back-functies voor eigenschaps updates en opdrachten. Het tot stand brengen van apparaat-verbindingen mag het retour neren van de aanroepen niet blok keren nadat de digitale dubbele interface is gemaakt. De verbinding met het actieve apparaat is onafhankelijk van de actieve interface client die door de Bridge wordt gemaakt. Als een verbinding mislukt, wordt ervan uitgegaan dat het apparaat niet actief is. De brug adapter kan ervoor kiezen om deze verbinding opnieuw tot stand te brengen.
1. Nadat het beheer van de brug adapter alle interface onderdelen heeft gemaakt die in het configuratie bestand zijn opgegeven, registreert alle interfaces met Azure IoT Hub. Registratie is een blokkerende, asynchrone aanroep. Wanneer de oproep is voltooid, wordt een call back in de brug adapter geactiveerd, waarmee de verwerking van eigenschappen en opdrachten in de cloud kan worden gestart.
1. De brug adapter manager roept vervolgens `PNPBRIDGE_INTERFACE_START` op elk onderdeel en de brug adapter begint met het rapporteren van telemetrie naar de digitale dubbele client.

### <a name="design-guidelines"></a>Ontwerprichtlijnen

Volg deze richt lijnen wanneer u een nieuwe brug adapter ontwikkelt:

- Bepaal welke apparaatfuncties worden ondersteund en wat de interface definitie van de onderdelen die deze adapter gebruiken eruitziet als.
- Bepaal in welke interface en globale para meters uw adapter moet worden gedefinieerd in het configuratie bestand.
- Bepaal de communicatie op laag niveau die vereist is voor de ondersteuning van de onderdeel eigenschappen en-opdrachten.
- Bepaal hoe de adapter de onbewerkte gegevens van het apparaat moet parseren en converteer deze naar de telemetrie-typen die door de IoT Plug en Play interface definitie is opgegeven.
- Implementeer de eerder beschreven brug adapter-interface.
- Voeg de nieuwe adapter toe aan het adapter manifest en bouw de Bridge.

### <a name="enable-a-new-bridge-adapter"></a>Een nieuwe brug adapter inschakelen

U schakelt adapters in de Bridge in door een verwijzing toe te voegen aan [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Retour aanroepen van Bridge-adapters worden sequentieel aangeroepen. Een adapter mag geen call back blok keren, omdat hiermee wordt voor komen dat de Bridge-kern voortgang kan maken.

### <a name="sample-camera-adapter"></a>Voor beeld camera adapter

In het [Leesmij-bestand](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) van de camera wordt een voor beeld van een camera adapter beschreven die u kunt inschakelen.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Code voorbeelden voor algemene adapter scenario's/retour aanroepen

In de volgende sectie vindt u informatie over hoe een adapter voor de brug retour aanroepen implementeert voor een aantal algemene scenario's en gebruik in deze sectie worden de volgende call backs behandeld:
- [Update van eigenschap ontvangen (Cloud naar apparaat)](#receive-property-update-cloud-to-device)
- [Een update van een eigenschap melden (apparaat naar Cloud)](#report-a-property-update-device-to-cloud)
- [Telemetrie verzenden (apparaat naar de Cloud)](#send-telemetry-device-to-cloud)
- [Retour aanroep van de opdracht update ontvangen vanuit de Cloud en verwerken op het apparaat (Cloud naar apparaat)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Reageren op de opdracht update aan de kant van het apparaat (apparaat naar Cloud)](#respond-to-command-update-on-the-device-side-device-to-cloud)

De onderstaande voor beelden zijn gebaseerd op de voor beeld-adapter van de [omgevings sensor](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor).

### <a name="receive-property-update-cloud-to-device"></a>Update van eigenschap ontvangen (Cloud naar apparaat)
De eerste stap is het registreren van een call back-functie:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
De volgende stap is het implementeren van de call back functie om de eigenschaps update op het apparaat te lezen:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Een update van een eigenschap melden (apparaat naar Cloud)
Op elk moment nadat het onderdeel is gemaakt, kan uw apparaat eigenschappen rapporteren aan de Cloud met de status: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Telemetrie verzenden (apparaat naar de Cloud)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Retour aanroep van de opdracht update ontvangen vanuit de Cloud en verwerken op het apparaat (Cloud naar apparaat)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Reageren op de opdracht update aan de kant van het apparaat (apparaat naar Cloud)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over de IoT Plug en Play-brug naar de [iot Plug en Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) github-opslag plaats.
