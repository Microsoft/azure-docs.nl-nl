---
title: Azure IoT Device SDK voor C-serializer | Microsoft Docs
description: Het gebruik van de Serialisatiefunctie-bibliotheek in de Azure IoT Device SDK voor C om apparaat-apps te maken die communiceren met een IoT-hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f52d1d1c5f264550076688d5e25e110de230eff4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152231"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT Device SDK voor C: meer informatie over serialisatiefunctie

In het eerste artikel in deze reeks is de [Inleiding tot Azure IOT Device SDK voor C](iot-hub-device-sdk-c-intro.md)geïntroduceerd. In het volgende artikel vindt u een gedetailleerde beschrijving van de [Azure IOT Device SDK voor C--IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). In dit artikel wordt de dekking van de SDK voltooid door een gedetailleerde beschrijving van het resterende onderdeel te bieden: de **serialisatiefunctie** -bibliotheek.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

In dit artikel wordt beschreven hoe u de **serialisatiefunctie** -bibliotheek gebruikt om gebeurtenissen te verzenden naar en berichten te ontvangen van IOT hub. In dit artikel wordt die discussie uitgebreid door een gedetailleerde uitleg te geven over het model leren van uw gegevens met de taal van de **serialisatiefunctie** -macro. Het artikel bevat ook meer informatie over hoe de bibliotheek berichten opserialeert (en in sommige gevallen hoe u het serialisatie-gedrag kunt beheren). Daarnaast worden enkele para meters beschreven die u kunt aanpassen om de grootte te bepalen van de modellen die u maakt.

Ten slotte worden in het artikel enkele onderwerpen die in eerdere artikelen worden behandeld, zoals de verwerking van berichten en eigenschappen, opnieuw bezocht. Zoals u ziet, werken deze functies op dezelfde manier als de **serialisatiefunctie** -bibliotheek, net als bij de **IoTHubClient** -bibliotheek.

Alles wat in dit artikel wordt beschreven, is gebaseerd op de voor beelden van de **serialisatiefunctie** -SDK. Als u de volg orde wilt volgen, raadpleegt u de **\_ http-** toepassingen **simple sample \_ AMQP** en simple sample die zijn opgenomen in de Azure IOT Device SDK voor C.

U vindt de [**Azure IOT Device SDK voor c**](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats en Raadpleeg de details van de API in de [C API-referentie](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>De modelleer taal

De [Azure IOT Device SDK voor c](iot-hub-device-sdk-c-intro.md) -artikel in deze reeks heeft de **Azure IOT Device SDK voor** de model taal c geïntroduceerd via het voor beeld in de toepassing **simple sample \_ AMQP** :

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Zoals u kunt zien, is de model taal gebaseerd op C-macro's. U begint altijd met de definitie met **begin \_ naam ruimte** en eindigt altijd met **End \_ naam ruimte**. Het is gebruikelijk om de naam ruimte voor uw bedrijf te noemen of, zoals in dit voor beeld, het project waaraan u werkt.

Wat er binnen de naam ruimte komt, zijn model definities. In dit geval is er één model voor een anemometer. Het model kan opnieuw worden benoemd, maar het model krijgt meestal een naam voor het apparaat of type gegevens dat u wilt uitwisselen met IoT Hub.  

Modellen bevatten een definitie van de gebeurtenissen die u kunt inhalen voor IoT Hub (de *gegevens*) en de berichten die u kunt ontvangen van IOT hub (de *acties*). Zoals u kunt zien in het voor beeld, hebben gebeurtenissen een type en een naam; acties hebben een naam en optionele para meters (elk met een type).

Wat niet wordt beschreven in dit voor beeld zijn extra gegevens typen die door de SDK worden ondersteund. We behandelen dat volgende.

> [!NOTE]
> IoT Hub verwijst naar de gegevens die een apparaat verzendt als *gebeurtenissen*, terwijl de model taal ernaar verwijst als *gegevens* (gedefinieerd met behulp van **WITH_DATA**). IoT Hub verwijst ook naar de gegevens die u naar apparaten verzendt als *berichten*, terwijl de model taal ernaar verwijst als *acties* (gedefinieerd met behulp van **WITH_ACTION**). Houd er rekening mee dat deze voor waarden door elkaar kunnen worden gebruikt in dit artikel.
> 
> 

## <a name="supported-data-types"></a>Ondersteunde gegevenstypen

De volgende gegevens typen worden ondersteund in modellen die zijn gemaakt met de **serialisatiefunctie** -bibliotheek:

| Type | Beschrijving |
| --- | --- |
| double |drijvende-komma getal met dubbele precisie |
| int |32-bits geheel getal |
| float |drijvende-komma getal met enkele precisie |
| long |lange integer |
| int8 \_ t |8-bits geheel getal |
| INT16 \_ t |16-bits geheel getal |
| Int32 \_ t |32-bits geheel getal |
| int64 \_ t |64-bits geheel getal |
| booleaans |booleaans |
| ASCII- \_ teken \_ PTR |ASCII-teken reeks |
| datum en tijd van EDM- \_ \_ \_ Offset |datum en tijd verschuiving |
| EDM- \_ GUID |GUID |
| binair van EDM \_ |binair |
| STRUCT DECLAReren \_ |complex gegevens type |

Laten we beginnen met het laatste gegevens type. Met de **Declare \_ STRUCT** kunt u complexe gegevens typen definiëren. Dit zijn groeperingen van de andere primitieve typen. Met deze groeperingen kunnen we een model definiëren dat er als volgt uitziet:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Ons model bevat één gegevens gebeurtenis van het type **TestType**. **TestType** is een complex type dat meerdere leden bevat, waarin gezamenlijk de primitieve typen worden getoond die worden ondersteund door de **serialisatiefunctie** -model taal.

Met een dergelijk model kunnen we code schrijven om gegevens te verzenden naar IoT Hub die er als volgt uitziet:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

In principe wijst u een waarde toe aan elk lid van de **test** structuur en roept u vervolgens **SendAsync** aan om de gebeurtenis **test** gegevens naar de cloud te verzenden. **SendAsync** is een hulp functie waarmee één gegevens gebeurtenis naar IOT hub wordt verzonden:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Deze functie serializeert de gegeven gegevens gebeurtenis en verzendt deze naar IoT Hub met behulp van **IoTHubClient \_ SendEventAsync**. Dit is dezelfde code die wordt besproken in eerdere artikelen (**SendAsync** bevat de logica in een handige functie).

Een andere Help-functie die wordt gebruikt in de vorige code is **GetDateTimeOffset**. Met deze functie wordt de opgegeven tijd getransformeerd naar een waarde van het type **EDM \_ Date \_ time \_ Offset**:

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Als u deze code uitvoert, wordt het volgende bericht verzonden naar IoT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Houd er rekening mee dat de serialisatie wordt gejsont. Dit is de indeling die wordt gegenereerd door de **serialisatiefunctie** -bibliotheek. Houd er ook rekening mee dat elk lid van het geserialiseerde JSON-object overeenkomt met de leden van de **TestType** die we in ons model hebben gedefinieerd. De waarden zijn ook exact hetzelfde als die in de code. Houd er echter rekening mee dat de binaire gegevens base64-gecodeerd zijn: "AQID" is de base64-code ring van {0x01, 0x02, 0x03}.

In dit voor beeld ziet u het voor deel van het gebruik van de **serialisatiefunctie** -bibliotheek. Hiermee kunnen we JSON naar de Cloud verzenden, zonder dat u expliciet met serialisatie in onze toepassing hoeft te omgaan. U hoeft zich geen zorgen te maken over het instellen van de waarden van de gegevens gebeurtenissen in ons model en vervolgens het aanroepen van eenvoudige Api's om die gebeurtenissen naar de cloud te verzenden.

Met deze informatie kunnen we modellen definiëren die het bereik van ondersteunde gegevens typen bevatten, waaronder complexe typen (we kunnen zelfs complexe typen opnemen in andere complexe typen). De geserialiseerde JSON die door bovenstaand voor beeld wordt gegenereerd, brengt echter een belang rijk punt met zich mee. *Hoe* we gegevens verzenden met de **serialisatiefunctie** -bibliotheek, bepalen precies hoe de JSON wordt gevormd. Het betreffende punt is wat we nu gaan behandelen.

## <a name="more-about-serialization"></a>Meer informatie over serialisatie

In de vorige sectie ziet u een voor beeld van de uitvoer die wordt gegenereerd door de **serialisatiefunctie** -bibliotheek. In deze sectie wordt uitgelegd hoe de tape wisselaar gegevens opmaakt en hoe u dat gedrag kunt beheren met behulp van de serialisatie-Api's.

We werken met een nieuw model op basis van een thermo staat om de discussie over serialisatie te voors Laan. Laten we eerst een achtergrond geven over het scenario dat we proberen aan te pakken.

We willen een thermo staat model leren die de Tempe ratuur en lucht vochtigheid meet. Elk stukje gegevens wordt naar IoT Hub anders verzonden. Standaard ingresses de Thermo staat een temperatuur gebeurtenis eenmaal per 2 minuten. een vochtigheids gebeurtenis wordt één keer per 15 minuten binnenkomen. Wanneer een van beide gebeurtenissen is ontstaan, moet deze een tijds tempel bevatten waarin de tijd wordt weer gegeven waarop de bijbehorende Tempe ratuur of vochtigheid is gemeten.

Op basis van dit scenario zien we twee verschillende manieren voor het model leren van de gegevens. we bespreken het effect dat modelleert op de geserialiseerde uitvoer.

### <a name="model-1"></a>Model 1

Dit is de eerste versie van een model dat het vorige scenario ondersteunt:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Houd er rekening mee dat het model twee gegevens gebeurtenissen bevat: **Tempe ratuur** en **vochtigheid**. In tegens telling tot eerdere voor beelden is het type van elke gebeurtenis een structuur die is gedefinieerd met behulp van **\_ STRUCT declareren**. **TemperatureEvent** bevat een meting van de Tempe ratuur en een tijds tempel; **HumidityEvent** bevat een meting van vochtigheid en een tijds tempel. Dit model biedt ons een natuurlijke manier om de gegevens te model leren voor het hierboven beschreven scenario. Wanneer we een gebeurtenis naar de Cloud verzenden, verzenden we een Tempe ratuur/tijds tempel of een paar van vochtigheid/tijds tempel.

We kunnen een temperatuur gebeurtenis naar de Cloud verzenden met behulp van de volgende code:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

We gebruiken hardcoded waarden voor de Tempe ratuur en vochtigheid in de voorbeeld code, maar stel dat we deze waarden daad werkelijk ophalen door de overeenkomstige Sens oren op de Thermo staat te bemonsteren.

De bovenstaande code maakt gebruik van het **GetDateTimeOffset** -hulp programma dat eerder is geïntroduceerd. Met deze code wordt de taak voor het serialiseren en verzenden van de gebeurtenis expliciet gescheiden. Met de vorige code wordt de temperatuur gebeurtenis in een buffer geserialiseerd. Vervolgens is **SendMessage** een hulp functie (opgenomen in **simple sample \_ AMQP**) waarmee de gebeurtenis wordt verzonden naar IOT hub:

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Deze code is een subset van het **SendAsync** -hulp programma dat in de vorige sectie is beschreven. dit wordt hier niet weer gegeven.

Wanneer we de vorige code voor het verzenden van de temperatuur gebeurtenis uitvoeren, wordt deze geserialiseerde vorm van de gebeurtenis verzonden naar IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Er wordt een Tempe ratuur verzonden die van het type **TemperatureEvent** is en die struct bevat een **Tempe ratuur** en **tijd** lid. Dit wordt rechtstreeks weer gegeven in de geserialiseerde gegevens.

Op dezelfde manier kunnen we een vochtigheids gebeurtenis met deze code verzenden:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Het geserialiseerde formulier dat naar IoT Hub wordt verzonden, ziet er als volgt uit:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Opnieuw, dit is zoals verwacht.

Met dit model kunt u zien hoe extra gebeurtenissen eenvoudig kunnen worden toegevoegd. U definieert meer structuren met **declareren van \_ STRUCT** en bevatten de bijbehorende gebeurtenis in het model met behulp **van \_ gegevens**.

Nu gaan we het model wijzigen zodat het dezelfde gegevens bevat, maar met een andere structuur.

### <a name="model-2"></a>Model 2

Bekijk dit alternatieve model op de bovenstaande manier:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

In dit geval hebben we de STRUCT-macro's voor het **declareren \_** verwijderd en worden de gegevens items uit ons scenario door middel van eenvoudige typen uit de model taal gedefinieerd.

U kunt de **tijd** gebeurtenis gewoon negeren. Dit is de code voor ingangs **temperatuur**:

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Met deze code wordt de volgende geserialiseerde gebeurtenis verzonden naar IoT Hub:

```C
{"Temperature":75}
```

En de code voor het verzenden van de vochtigheids gebeurtenis wordt als volgt weer gegeven:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Met deze code wordt dit verzonden naar IoT Hub:

```C
{"Humidity":45}
```

Tot nu toe zijn er nog steeds geen verrassingen. We gaan nu wijzigen hoe we de SERIALisatie-macro gebruiken.

De **serialisatie** macro kan meerdere gegevens gebeurtenissen als argumenten hebben. Hierdoor kunnen we de gebeurtenis voor de **Tempe ratuur** en de **lucht vochtigheid** op elkaar afzetten en deze naar IOT hub in één gesprek verzenden:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Het resultaat van deze code is dat er twee gegevens gebeurtenissen naar IoT Hub worden verzonden:

[{"Tempe ratuur": 75}, {"vochtigheids graad": 45}]

Met andere woorden, u zult kunnen verwachten dat deze code hetzelfde is als het verzenden van de **Tempe ratuur** en **vochtigheid** afzonderlijk. Het is slechts een gemak om beide gebeurtenissen door te geven om in dezelfde oproep te **serialiseren** . Dat is echter niet het geval. In plaats daarvan verzendt de bovenstaande code deze enkele gegevens gebeurtenis naar IoT Hub:

{"Tempe ratuur": 75, "vochtigheids graad": 45}

Dit lijkt vreemd omdat in ons model de **Tempe ratuur** en **vochtigheid** als twee *afzonderlijke* gebeurtenissen worden gedefinieerd:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Meer aan het punt hebben we deze gebeurtenissen niet kunnen model leren wanneer de **Tempe ratuur** en **vochtigheid** zich in dezelfde structuur bevinden:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Als we dit model hebben gebruikt, is het eenvoudiger om te begrijpen hoe de **Tempe ratuur** en **vochtigheids graad** in hetzelfde geserialiseerde bericht zouden worden verzonden. Het kan echter niet duidelijk zijn waarom het werkt op die manier wanneer u beide gegevens gebeurtenissen doorgeeft aan een **serialisatie** met behulp van model 2.

Dit gedrag is gemakkelijker te begrijpen als u weet wat de veronderstellingen zijn die de **serializer** -bibliotheek maakt. Om dit te maken, gaan we terug naar ons model:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Denk aan dit model in object-georiënteerde voor waarden. In dit geval wordt een fysiek apparaat (een thermo staat) gemodelleerd en dat apparaat bevat kenmerken als **Tempe ratuur** en **vochtigheid**.

We kunnen de volledige status van ons model verzenden met de volgende code:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ervan uitgaande dat de waarden van de Tempe ratuur, vochtigheid en tijd zijn ingesteld, zien we een gebeurtenis zoals deze wordt verzonden naar IoT Hub:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Soms wilt u mogelijk slechts *enkele* eigenschappen van het model naar de Cloud verzenden (dit is vooral waar als uw model een groot aantal gegevens gebeurtenissen bevat). Het is handig alleen een subset van gegevens gebeurtenissen te verzenden, zoals in het vorige voor beeld:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Hiermee wordt exact dezelfde geserialiseerde gebeurtenis gegenereerd alsof er een **TemperatureEvent** is gedefinieerd met een **Tempe ratuur** en **tijd** lid, net zoals bij model 1. In dit geval kunnen we exact dezelfde geserialiseerde gebeurtenis genereren met behulp van een ander model (model 2) omdat we **serialisatie** op een andere manier hebben genoemd.

Het belang rijk punt is dat als u meerdere gegevens gebeurtenissen doorgeeft om te **serialiseren,** ervan wordt uitgegaan dat elke gebeurtenis een eigenschap in één JSON-object is.

De beste benadering is afhankelijk van u en hoe u uw model vindt. Als u gebeurtenissen naar de Cloud verzendt en elke gebeurtenis een gedefinieerde set eigenschappen bevat, is de eerste benadering veel zinvol. In dat geval kunt u **\_ STRUCT declareren** gebruiken om de structuur van elke gebeurtenis te definiëren en deze vervolgens opnemen in uw model met de macro **with \_ Data** . Vervolgens verzendt u elke gebeurtenis als in het eerste voor beeld hierboven. In deze benadering geeft u slechts één gegevens gebeurtenis door aan **serializer**.

Als u uw model op een objectgeoriënteerd manier wilt bedenken, kan de tweede benadering bij u aansluiten. In dit geval zijn de elementen die zijn gedefinieerd met behulp van **\_ gegevens** de ' Eigenschappen ' van uw object. U geeft een subset van de gebeurtenissen die u wilt **serialiseren** , afhankelijk van de hoeveelheid van de status van het object dat u naar de Cloud wilt verzenden.

De Nether-benadering is correct of onjuist. U hoeft alleen maar op de hoogte te zijn van de werking van de **serialisatiefunctie** -bibliotheek en de model benadering te kiezen die het beste bij uw behoeften past.

## <a name="message-handling"></a>Afhandeling van berichten

Tot nu toe heeft dit artikel alleen betrekking op het verzenden van gebeurtenissen naar IoT Hub en heeft geen berichten ontvangen. De reden hiervoor is dat we moeten weten over de ontvangst van berichten grotendeels zijn behandeld in het artikel [Azure IOT Device SDK voor C](iot-hub-device-sdk-c-intro.md). Intrekken van dat artikel waarin u berichten verwerkt door een bericht retour aanroep-functie te registreren:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Vervolgens schrijft u de call back-functie die wordt aangeroepen wanneer een bericht wordt ontvangen:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Deze implementatie van **IoTHubMessage** roept de specifieke functie aan voor elke actie in uw model. Als uw model bijvoorbeeld deze actie definieert:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

U moet een functie definiëren met deze hand tekening:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** wordt vervolgens aangeroepen wanneer dat bericht naar uw apparaat wordt verzonden.

Wat we nog niet hebben uitgelegd, is de seriële versie van het bericht. Met andere woorden, als u een **SetAirResistance** -bericht wilt verzenden naar uw apparaat, wat is er hetzelfde?

Als u een bericht naar een apparaat verzendt, kunt u dit doen via de Azure IoT Service SDK. U moet nog steeds weten welke teken reeks moet worden verzonden om een bepaalde actie aan te roepen. De algemene indeling voor het verzenden van een bericht ziet er als volgt uit:

```C
{"Name" : "", "Parameters" : "" }
```

U verzendt een geserialiseerd JSON-object met twee eigenschappen: **name** is de naam van de actie (bericht) en de **para meters** bevatten de para meters van die actie.

Als u bijvoorbeeld **SetAirResistance** aanroept, kunt u dit bericht verzenden naar een apparaat:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

De naam van de actie moet exact overeenkomen met een actie die in het model is gedefinieerd. De parameter namen moeten ook overeenkomen. Let ook op hoofdletter gevoeligheid. **Naam** en **para meters** zijn altijd hoofd letters. Zorg ervoor dat het hoofdletter gebruik overeenkomt met de naam en para meters van uw actie in uw model. In dit voor beeld is de naam van de actie ' SetAirResistance ' en niet ' SetAirResistance '.

De twee andere acties **TurnFanOn** en **TurnFanOff** kunnen worden opgeroepen door deze berichten naar een apparaat te verzenden:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

In deze sectie wordt beschreven wat u moet weten wanneer u gebeurtenissen verzendt en berichten ontvangt met de **serialisatiefunctie** -bibliotheek. Voordat u doorgaat, gaan we enkele para meters voor u configureren die bepalen hoe groot uw model is.

## <a name="macro-configuration"></a>Macro configuratie

Als u de **serialisatiefunctie** -bibliotheek gebruikt, is er een belang rijk onderdeel van de SDK waarmee u rekening moet houden, is te vinden in de bibliotheek van Azure-c-Shared-Utility.

Als u de Azure-IOT-SDK-c-opslag plaats hebt gekloond van GitHub en de `git submodule update --init` opdracht hebt uitgegeven, dan vindt u deze gedeelde hulpprogramma bibliotheek hier:

```C
.\\c-utility
```

Als u de bibliotheek niet hebt gekloond, kunt u deze [hier](https://github.com/Azure/azure-c-shared-utility)vinden.

In de bibliotheek van het gedeelde hulp programma vindt u de volgende map:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Deze map bevat een Visual Studio-oplossing met de naam **macro \_ hulppra \_ \_ generator. SLN**:

  ![Scherm afbeelding van de Visual Studio-oplossing maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Het programma in deze oplossing genereert het bestand **macro \_ hulppr. h** . Er is een standaard macro van \_ het bestand hulppr. h dat is opgenomen in de SDK. Met deze oplossing kunt u enkele para meters wijzigen en vervolgens het header-bestand opnieuw maken op basis van deze para meters.

De twee belang rijke para meters die u wilt uitvoeren, zijn **nArithmetic** en **nMacroParameters**, die worden gedefinieerd in de volgende twee regels in macro \_ utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Deze waarden zijn de standaard parameters die in de SDK zijn opgenomen. Elke para meter heeft de volgende betekenis:

* nMacroParameters: bepaalt het aantal para meters dat u kunt hebben in één DECLAReer \_ model macro definitie.
* nArithmetic: bepaalt het totale aantal leden dat is toegestaan in een model.

De reden hiervoor is dat deze para meters belang rijk zijn omdat ze bepalen hoe groot uw model kan zijn. Denk bijvoorbeeld aan deze model definitie:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Zoals eerder vermeld, is **Declare \_ model** gewoon a C macro. De namen van het model en de instructie **with \_ Data** (nog een andere macro) zijn para meters van het **Declare- \_ model**. **nMacroParameters** definieert hoeveel para meters in het **Declare- \_ model** kunnen worden opgenomen. Dit bepaalt effectief hoeveel gegevens gebeurtenissen en actie declaraties u kunt hebben. Als zodanig, met de standaard limiet van 124, betekent dit dat u een model kunt definiëren met een combi natie van ongeveer 60 acties en gegevens gebeurtenissen. Als u deze limiet overschrijdt, ontvangt u compilatie fouten die er ongeveer als volgt uitzien:

  ![Scherm afbeelding van de compiler fouten van macro parameters](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

De para meter **nArithmetic** is meer informatie over de interne werking van de macro taal dan uw toepassing.  Hiermee bepaalt u het totale aantal leden dat in uw model kan worden opgenomen, met inbegrip van **DECLARE_STRUCT** -macro's. Als u Compileer fouten zoals deze wilt bekijken, moet u proberen om **nArithmetic** te verg Roten:

   ![Scherm opname van reken kundige Compileer fouten](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Als u deze para meters wilt wijzigen, wijzigt u de waarden in het \_ utils.tt-bestand van de macro, compileert u de macro \_ hulppr \_ & \_ generator. SLN en voert u het gecompileerde programma uit. Wanneer u dit doet, wordt een nieuwe macro \_ van het bestand hulppr. h gegenereerd en in de geplaatst. \\ algemene \\ Inc-Directory.

Als u de nieuwe versie van macro \_ hulppr. h wilt gebruiken, verwijdert u het **serializer** NuGet-pakket uit uw oplossing en op de plaats daarvan voegt u het **serializer** Visual Studio-project toe. Hierdoor kan uw code worden gecompileerd op basis van de bron code van de serialisatiefunctie-bibliotheek. Dit omvat de bijgewerkte macro \_ hulppr. h. Als u dit wilt doen voor **simple sample \_ AMQP**, moet u eerst het NuGet-pakket voor de serialisatiefunctie-bibliotheek verwijderen uit de oplossing:

   ![Scherm opname van het verwijderen van het NuGet-pakket voor de serialisatiefunctie-bibliotheek](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Voeg dit project vervolgens toe aan uw Visual Studio-oplossing:

> .\\ c \\ serializer \\ bouwt \\ Windows \\ serializer. vcxproj
> 
> 

Wanneer u klaar bent, moet uw oplossing er als volgt uitzien:

   ![Scherm afbeelding van de simplesample_amqp Visual Studio-oplossing](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Wanneer u de oplossing nu compileert, wordt de bijgewerkte macro \_ hulppr. h opgenomen in uw binaire bestand.

Houd er rekening mee dat het verhogen van deze waarden groot genoeg kan zijn voor de compilatie limieten. Op dit punt is de **nMacroParameters** de belangrijkste para meter waarmee de taak moet worden betrokken. De C99 SPEC geeft aan dat er mini maal 127 para meters zijn toegestaan in een macro definitie. De micro soft compiler volgt precies de specificatie (en heeft een limiet van 127), zodat u de **nMacroParameters** niet meer kunt verhogen dan de standaard waarde. Met andere compilers kunt u dit doen (bijvoorbeeld omdat de GNU-compiler een hogere limiet ondersteunt).

Tot nu toe hebben we precies uitgelegd wat u nodig hebt om te weten te komen over het schrijven van code met de **serialisatiefunctie** -bibliotheek. Voordat u een sluiting maakt, gaan we enkele onderwerpen opnieuw bezoeken uit eerdere artikelen die u mogelijk vraagt.

## <a name="the-lower-level-apis"></a>De lagere Api's
De voorbeeld toepassing waarop dit artikel is gericht, is **simple sample \_ AMQP**. In dit voor beeld wordt gebruikgemaakt van de api's op hoger niveau (de niet-**ll**) om gebeurtenissen te verzenden en berichten te ontvangen. Als u deze Api's gebruikt, wordt er een achtergrond thread uitgevoerd die zorgt voor zowel het verzenden van gebeurtenissen als het ontvangen van berichten. U kunt echter de Api's van het lagere niveau (LL) gebruiken om deze achtergrond thread te elimineren en expliciet controle te krijgen over wanneer u gebeurtenissen verzendt of berichten ontvangt van de Cloud.

Zoals beschreven in een voor [gaande artikel](iot-hub-device-sdk-c-iothubclient.md), is er een set functies die bestaat uit de api's van het hogere niveau:

* IoTHubClient \_ API createfromconnectionstring
* IoTHubClient \_ SendEventAsync
* IoTHubClient \_ SetMessageCallback
* IoTHubClient \_ vernietigen

Deze Api's worden in **simple sample \_ AMQP** gedemonstreerd.

Er is ook een analoge set Api's met een lager niveau.

* IoTHubClient \_ ll \_ API createfromconnectionstring
* IoTHubClient \_ ll \_ SendEventAsync
* IoTHubClient \_ ll \_ SetMessageCallback
* IoTHubClient \_ ll \_ vernietigen

Houd er rekening mee dat de Api's van het lagere niveau precies dezelfde manier werken als in de vorige artikelen. U kunt de eerste set Api's gebruiken als u wilt dat een achtergrond thread het verzenden van gebeurtenissen en het ontvangen van berichten verwerkt. U gebruikt de tweede set Api's als u expliciete controle wilt over wanneer u gegevens van IoT Hub verzendt en ontvangt. Een set Api's werkt gelijk goed met de **serialisatiefunctie** -bibliotheek.

Voor een voor beeld van hoe de Api's met een lager niveau worden gebruikt met de **serialisatiefunctie** -bibliotheek, raadpleegt u de **\_ http-toepassing simple sample** .

## <a name="additional-topics"></a>Extra onderwerpen
Enkele andere onderwerpen over het opnieuw vermelden van eigenschappen zijn het afhandelen van een eigenschap, het gebruik van alternatieve referenties voor apparaten en configuratie opties. Dit zijn alle onderwerpen die in een [vorig artikel](iot-hub-device-sdk-c-iothubclient.md)worden behandeld. Het belangrijkste punt is dat al deze functies op dezelfde manier werken als de **serialisatiefunctie** -bibliotheek, net zoals met de **IoTHubClient** -bibliotheek. Als u bijvoorbeeld eigenschappen aan een gebeurtenis van uw model wilt koppelen, gebruikt u **IoTHubMessage- \_ Eigenschappen** en wijst u  \_ **AddorUpdate**, op dezelfde manier zoals eerder beschreven:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Of de gebeurtenis is gegenereerd op basis van de **serialisatiefunctie** -bibliotheek of hand matig is gemaakt met behulp van de **IoTHubClient** -bibliotheek.

Voor de referenties van het alternatieve apparaat kunt u met **IoTHubClient \_ ll \_ maken** werken, net als **IoTHubClient \_ API createfromconnectionstring** voor het toewijzen van een **IOTHUB- \_ client \_ ingang**.

Ten slotte kunt u, als u de **serialisatiefunctie** -bibliotheek gebruikt, configuratie opties instellen met **IoTHubClient \_ ll \_ SetOption** , net zoals u dat hebt gedaan bij het gebruik van de **IoTHubClient** -bibliotheek.

Een functie die uniek is voor de **serialisatiefunctie** -bibliotheek, zijn de initialisatie-api's. Voordat u met de bibliotheek kunt gaan werken, moet u **serializer \_ init** aanroepen:

```C
serializer_init(NULL);
```

Dit doet u net voordat u **IoTHubClient \_ API createfromconnectionstring** aanroept.

Wanneer u klaar bent met het werken met de bibliotheek, is de laatste aanroep die u maakt, de **serialisatie \_ deinit**:

```C
serializer_deinit();
```

Als dat niet het geval is, worden alle andere functies die hierboven worden genoemd, op dezelfde manier uitgevoerd als **in de** **IoTHubClient** -bibliotheek. Zie het [vorige artikel](iot-hub-device-sdk-c-iothubclient.md) in deze serie voor meer informatie over deze onderwerpen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de unieke aspecten beschreven van de **serialisatiefunctie** -bibliotheek in de **Azure IOT Device SDK voor C**. Met de verstrekte informatie moet u een goed idee hebben van het gebruik van modellen om gebeurtenissen te verzenden en berichten van IoT Hub te ontvangen.

Hiermee wordt ook de reeks met drie gedeelten afgesloten voor het ontwikkelen van toepassingen met de **Azure IOT Device SDK voor C**. Dit moet voldoende informatie zijn om niet alleen aan de slag te kunnen, maar u kunt een grondige uitleg geven over de werking van de Api's. Voor aanvullende informatie zijn er enkele voor beelden in de SDK die hier niet worden besproken. Anders is de [Azure IOT SDK-documentatie](https://github.com/Azure/azure-iot-sdk-c) een goede bron voor aanvullende informatie.

Zie de [Azure IOT sdk's](iot-hub-devguide-sdks.md)voor meer informatie over het ontwikkelen van IOT hub.

Zie [AI implementeren op edge-apparaten met Azure IOT Edge](../iot-edge/quickstart-linux.md)om de mogelijkheden van IOT hub verder te verkennen.