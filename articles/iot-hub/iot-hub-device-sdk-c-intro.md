---
title: De Azure IoT Device SDK voor C | Microsoft Docs
description: Aan de slag met de Azure IoT Device SDK voor C en meer informatie over het maken van apps voor apparaten die communiceren met een IoT-hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 17b31e365e311b97e322828927827f40733313a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588825"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT Device SDK voor C

De **Azure IOT Device SDK** is een set bibliotheken die is ontworpen om het proces van het verzenden van berichten naar en het ontvangen van berichten van de **Azure IOT hub** -service te vereenvoudigen. Er zijn verschillende variaties van de SDK, die elk gericht zijn op een specifiek platform, maar in dit artikel wordt de **Azure IOT Device SDK voor C** beschreven.

> [!NOTE]
> De embedded C SDK is een alternatief voor beperkte apparaten die ondersteuning bieden voor de aanpak van uw eigen netwerk (BYON). IoT-ontwikkel aars hebben de vrijheid om de MQTT-client, TLS en socket van hun keuze te maken om een oplossing voor een apparaat te creëren. Meer [informatie over de embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

De Azure IoT Device SDK voor C is geschreven in ANSI C (C99) om de draag baarheid te maximaliseren. Met deze functie kunnen de bibliotheken goed worden toegepast op meerdere platforms en apparaten, met name waarbij het minimaliseren van schijf-en geheugen capaciteit een prioriteit is.

Er is een breed scala aan platforms waarop de SDK is getest (Zie de [Azure Certified voor IOT-Apparaatbeheer](https://catalog.azureiotsolutions.com/) voor meer informatie). Hoewel dit artikel stapsgewijze instructies bevat voor voorbeeld code die wordt uitgevoerd op het Windows-platform, is de code die in dit artikel wordt beschreven, identiek in het bereik van ondersteunde platforms.

De volgende video biedt een overzicht van de Azure IoT SDK voor C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

In dit artikel vindt u een inleiding tot de architectuur van de Azure IoT Device SDK voor C. Hierin ziet u hoe u de Apparaatbeheer initialiseert, gegevens naar IoT Hub verzendt en er berichten van ontvangt. De informatie in dit artikel moet voldoende zijn om aan de slag te gaan met de SDK, maar biedt ook verwijzingen naar aanvullende informatie over de bibliotheken.

## <a name="sdk-architecture"></a>SDK-architectuur

U vindt de [**Azure IOT Device SDK voor c**](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats en Raadpleeg de details van de API in de [C API-referentie](/azure/iot-hub/iot-c-sdk-ref/).

De meest recente versie van de bibliotheken vindt u in de **hoofd** vertakking van de opslag plaats:

  ![Scherm afbeelding van de hoofd vertakking van de opslag plaats](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* De kern implementatie van de SDK bevindt zich in de **iothub- \_ client** map die de implementatie van de laagste API-laag in de SDK bevat: de **IoTHubClient** -bibliotheek. De **IoTHubClient** -bibliotheek bevat api's die RAW Messa ging implementeren voor het verzenden van berichten naar IOT hub en het ontvangen van berichten van IOT hub. Wanneer u deze bibliotheek gebruikt, bent u verantwoordelijk voor het implementeren van bericht serialisatie, maar andere details van het communiceren met IoT Hub worden voor u afgehandeld.

* De map **serializer** bevat hulp functies en voor beelden die laten zien hoe u gegevens kunt serialiseren voordat u deze naar Azure IOT hub verzendt met de-client bibliotheek. Het gebruik van de serialisatiefunctie is niet verplicht en is als gebruiks gemak opgegeven. Als u de **serialisatiefunctie** -bibliotheek wilt gebruiken, definieert u een model dat de gegevens bevat die moeten worden verzonden naar IOT hub en de berichten die u verwacht te ontvangen. Zodra het model is gedefinieerd, biedt de SDK u een API-Opper vlak waarmee u eenvoudig kunt werken met apparaat-naar-Cloud-en Cloud-naar-apparaat-berichten zonder dat u zich zorgen hoeft te maken over de details van de serialisatie. De bibliotheek is afhankelijk van andere open-source bibliotheken die Trans Port implementeren met behulp van protocollen zoals MQTT en AMQP.

* De **IoTHubClient** -bibliotheek is afhankelijk van andere open-source-bibliotheken:

  * De bibliotheek met [gedeelde Azure c-hulpprogram ma's](https://github.com/Azure/azure-c-shared-utility) , waarmee algemene taken kunnen worden uitgevoerd (zoals teken reeksen, lijst bewerkingen en i/o) die nodig zijn voor meerdere aan Azure gerelateerde C-sdk's.

  * De [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) -bibliotheek, een implementatie aan de client zijde van AMQP die is geoptimaliseerd voor apparaten die zijn beperkt tot een resource.

  * De [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) -bibliotheek, een bibliotheek voor algemeen gebruik die het MQTT-protocol implementeert en geoptimaliseerd is voor apparaten die zijn beperkt.

Het gebruik van deze bibliotheken is gemakkelijker te begrijpen door voorbeeld code te bekijken. In de volgende secties vindt u een overzicht van de voorbeeld toepassingen die in de SDK zijn opgenomen. In dit overzicht vindt u een goede indruk van de verschillende mogelijkheden van de architectuur lagen van de SDK en een inleiding tot de werking van de Api's.

## <a name="before-you-run-the-samples"></a>Voordat u de voor beelden uitvoert

Voordat u de voor beelden in de Azure IoT Device SDK voor C kunt uitvoeren, moet u [een exemplaar van de IOT hub-service](iot-hub-create-through-portal.md) in uw Azure-abonnement maken. Voer vervolgens de volgende taken uit:

* Uw ontwikkelomgeving voorbereiden
* De referenties van het apparaat ophalen.

### <a name="prepare-your-development-environment"></a>Uw ontwikkelomgeving voorbereiden

Pakketten worden weer gegeven voor algemene platformen (zoals NuGet voor Windows of apt_get voor Debian en Ubuntu) en de voor beelden gebruiken deze pakketten wanneer deze beschikbaar zijn. In sommige gevallen moet u de SDK voor of op uw apparaat compileren. Als u de SDK wilt compileren, raadpleegt u [uw ontwikkel omgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de GitHub-opslag plaats.

Down load een exemplaar van de SDK via GitHub om de voorbeeld toepassings code te verkrijgen. Haal uw kopie van de bron op uit de **hoofd** vertakking van de [github-opslag plaats](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>De referenties van het apparaat ophalen

Nu u de voor beeld-bron code hebt, is het volgende te doen om een set apparaatgegevens te verkrijgen. Als een apparaat een IoT-hub kan openen, moet u het apparaat eerst toevoegen aan het IoT Hub-identiteits register. Wanneer u uw apparaat toevoegt, krijgt u een set apparaatspecifieke referenties die nodig zijn voor het apparaat om verbinding te kunnen maken met de IoT-hub. De voorbeeld toepassingen die in de volgende sectie worden beschreven, verwachten deze referenties in de vorm van een **apparaat Connection String**.

Er zijn verschillende open source-hulpprogram ma's die u helpen bij het beheren van uw IoT-hub.

* Een Windows-toepassing met de naam [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer).

* Een platformoverschrijdende Visual Studio-code-uitbrei ding met de naam [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Een platformoverschrijdende python-CLI met [de naam IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

Deze zelf studie maakt gebruik van het hulp programma voor grafische *apparaten* . U kunt de *Azure IOT-Hulpprogram ma's voor VS code* gebruiken als u in VS code ontwikkelt. U kunt ook de *IOT-extensie voor Azure CLI 2,0* -hulp programma gebruiken als u liever een CLI-hulp programma gebruikt.

Het hulp programma Device Explorer maakt gebruik van de Azure IoT-service bibliotheken voor het uitvoeren van verschillende functies op IoT Hub, waaronder het toevoegen van apparaten. Als u het hulp programma Device Explorer gebruikt om een apparaat toe te voegen, krijgt u een connection string voor uw apparaat. U hebt deze connection string nodig om de voorbeeld toepassingen uit te voeren.

Als u niet bekend bent met het hulp programma Device Explorer, wordt in de volgende procedure beschreven hoe u het kunt gebruiken om een apparaat toe te voegen en een apparaat connection string te verkrijgen.

1. Zie [de Device Explorer voor IOT Hub apparaten gebruiken](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/)om het hulp programma Device Explorer te installeren.

1. Wanneer u het programma uitvoert, ziet u deze interface:

   ![Dubbele scherm afbeelding van Device Explorer](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Voer uw **IOT hub verbindings reeks** in het eerste veld in en klik op **bijwerken**. Met deze stap configureert u het hulp programma zodat het kan communiceren met IoT Hub. 

De **verbindings reeks** vindt u onder **IOT hub service**-  >  **instellingen**  >  **beleid voor gedeelde toegang**  >  **iothubowner**.

1. Wanneer de IoT Hub connection string is geconfigureerd, klikt u op het tabblad **beheer** :

   ![Scherm afbeelding voor dubbele/beheer Device Explorer](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Op dit tabblad kunt u de apparaten beheren die zijn geregistreerd in uw IoT-hub.

1. U maakt een apparaat door te klikken op de knop **maken** . Er wordt een dialoog venster weer gegeven met een set vooraf ingevulde sleutels (primair en secundair). Voer een **apparaat-id** in en klik vervolgens op **maken**.

   ![Scherm opname van apparaat maken](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Wanneer het apparaat wordt gemaakt, worden de apparaten weer geven met alle geregistreerde apparaten, met inbegrip van het item dat u zojuist hebt gemaakt. Als u met de rechter muisknop op het nieuwe apparaat klikt, ziet u dit menu:

   ![Het dubbele rechter klikken Device Explorer resultaat](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Als u **Connection String kopiëren kiest voor het geselecteerde apparaat**, wordt het apparaat Connection String gekopieerd naar het klem bord. Bewaar een kopie van de connection string van het apparaat. U hebt deze nodig bij het uitvoeren van de voorbeeld toepassingen die in de volgende secties worden beschreven.

Wanneer u de bovenstaande stappen hebt voltooid, kunt u beginnen met het uitvoeren van code. De meeste voor beelden hebben een constante boven aan het hoofd bron bestand waarmee u een connection string kunt invoeren. De bijbehorende regel van de **iothub_client voor \_ beelden \_ iothub_convenience_sample** toepassing wordt bijvoorbeeld als volgt weer gegeven.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>De IoTHubClient-bibliotheek gebruiken

In de **map iothub- \_ client** in de [Azure-IOT-SDK-c-](https://github.com/azure/azure-iot-sdk-c) opslag plaats bevindt zich een map met voor **beelden** die een toepassing bevat met de naam **iothub \_ client \_ sample \_ mqtt**.

De Windows-versie van de iothub_client-voor **\_ beelden \_ iothub_convenience_sample** toepassing bevat de volgende Visual Studio-oplossing:

  ![Solution Explorer van Visual Studio](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Als u in Visual Studio wordt gevraagd om het project opnieuw te richten op de meest recente versie, accepteert u de prompt.

Deze oplossing bevat één project. Er zijn vier NuGet-pakketten geïnstalleerd in deze oplossing:

* Micro soft. Azure. C. SharedUtility
* Micro soft. Azure. IoTHub. MqttTransport
* Micro soft. Azure. IoTHub. IoTHubClient
* Micro soft. Azure. umqtt

U hebt altijd het pakket **micro soft. Azure. C. SharedUtility** nodig wanneer u met de SDK werkt. In dit voor beeld wordt het MQTT-protocol gebruikt. Daarom moet u de pakketten **micro soft. Azure. umqtt** en **micro soft. Azure. IoTHub. MqttTransport** gebruiken (er zijn equivalente pakketten voor AMQP en https). Omdat in het voor beeld de **IoTHubClient** -bibliotheek wordt gebruikt, moet u ook het pakket **micro soft. Azure. IoTHub. IoTHubClient** in uw oplossing toevoegen.

U kunt de implementatie voor de voorbeeld toepassing vinden in het bron bestand iothub_client-voor **\_ beelden \_ iothub_convenience_sample** .

In de volgende stappen wordt deze voorbeeld toepassing gebruikt om u te helpen bij wat u nodig hebt om de **IoTHubClient** -bibliotheek te gebruiken.

### <a name="initialize-the-library"></a>De bibliotheek initialiseren

> [!NOTE]
> Voordat u aan de slag gaat met de Bibliotheken, moet u mogelijk bepaalde platformspecifieke initialisaties uitvoeren. Als u bijvoorbeeld van plan bent AMQP in Linux te gebruiken, moet u de OpenSSL-bibliotheek initialiseren. De voor beelden in de [github-opslag plaats](https://github.com/Azure/azure-iot-sdk-c) roepen de functie **platform \_ init** van het hulp programma aan wanneer de client wordt gestart en roept de functie voor het **\_ deinit** van het platform aan voordat deze wordt afgesloten. Deze functies worden gedeclareerd in het bestand platform. h header. Bekijk de definities van deze functies voor uw doel platform in de [opslag plaats](https://github.com/Azure/azure-iot-sdk-c) om te bepalen of u een platformspecifieke initialisatie code moet gebruiken in uw-client.

Wijs eerst een IoT Hub client ingang toe om te gaan werken met de bibliotheken:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

U geeft een kopie van het apparaat door connection string u hebt verkregen van het hulp programma Device Explorer naar deze functie. U wijst ook het communicatie protocol aan dat moet worden gebruikt. In dit voor beeld wordt MQTT gebruikt, maar AMQP en HTTPS zijn ook opties.

Wanneer u een geldige **IOTHUB- \_ client \_ ingang** hebt, kunt u beginnen met het aanroepen van de api's voor het verzenden en ontvangen van berichten van en naar IOT hub.

### <a name="send-messages"></a>Berichten verzenden

De voorbeeld toepassing stelt een lus in voor het verzenden van berichten naar uw IoT-hub. Het volgende code fragment:

- Hiermee maakt u een bericht.
- Hiermee wordt een eigenschap toegevoegd aan het bericht.
- Hiermee wordt een bericht verzonden.

Maak eerst een bericht:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Telkens wanneer u een bericht verzendt, geeft u een verwijzing op naar een call back-functie die wordt aangeroepen wanneer de gegevens worden verzonden. In dit voor beeld wordt de call back-functie **SendConfirmationCallback** genoemd. In het volgende code fragment wordt deze call back-functie weer gegeven:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Let op de aanroep van de functie **IoTHubMessage \_ Destroy** wanneer u klaar bent met het bericht. Met deze functie worden de resources vrijgegeven die zijn toegewezen tijdens het maken van het bericht.

### <a name="receive-messages"></a>Berichten ontvangen

Het ontvangen van een bericht is een asynchrone bewerking. Eerst registreert u de retour aanroep om aan te roepen wanneer het apparaat een bericht ontvangt:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

De laatste para meter is een ongeldige pointer naar de gewenste naam. In het voor beeld is het een verwijzing naar een geheel getal, maar dit kan een verwijzing naar een complexere gegevens structuur zijn. Met deze para meter kan de call back functie worden uitgevoerd in de gedeelde status met de aanroeper van deze functie.

Wanneer het apparaat een bericht ontvangt, wordt de geregistreerde call back-functie aangeroepen. Deze call back-functie haalt het volgende op:

* De bericht-ID en correlatie-ID van het bericht.
* De inhoud van het bericht.
* Alle aangepaste eigenschappen van het bericht.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Gebruik de functie **IoTHubMessage \_ GetByteArray** om het bericht op te halen dat in dit voor beeld een teken reeks is.

### <a name="uninitialize-the-library"></a>De initialisatie van de bibliotheek ongedaan te doen

Wanneer u klaar bent met het verzenden van gebeurtenissen en het ontvangen van berichten, kunt u de initialisatie van de IoT-bibliotheek ongedaan maken. U doet dit door de volgende functie aanroep uit te voeren:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Met deze aanroep worden de resources vrijgemaakt die eerder zijn toegewezen door de **IoTHubClient \_ API createfromconnectionstring** -functie.

Zoals u kunt zien, is het eenvoudig om berichten te verzenden en ontvangen met de **IoTHubClient** -bibliotheek. De bibliotheek verwerkt de details van de communicatie met IoT Hub, met inbegrip van het protocol dat moet worden gebruikt (vanuit het oogpunt van de ontwikkelaar is dit een eenvoudige configuratie optie).

De **IoTHubClient** -bibliotheek biedt ook een nauw keurige controle over het serialiseren van de gegevens die uw apparaat naar IOT hub stuurt. In sommige gevallen is dit controle niveau een voor deel, maar in andere situaties is het een implementatie details die u niet wilt gebruiken. Als dat het geval is, kunt u overwegen de **serialisatiefunctie** -bibliotheek te gebruiken, die in de volgende sectie wordt beschreven.

## <a name="use-the-serializer-library"></a>De serialisatiefunctie-bibliotheek gebruiken

De **serializer** -bibliotheek bevindt zich boven aan de **IoTHubClient** -bibliotheek in de SDK. Er wordt gebruikgemaakt van de **IoTHubClient** -bibliotheek voor de onderliggende communicatie met IOT hub, maar er worden model functies toegevoegd waarmee de belasting van de verwerking van de bericht serialisatie van de ontwikkelaar wordt verwijderd. Hoe deze tape wisselaar het beste kan worden gedemonstreerd door een voor beeld.

In de map **serializer** in de [Azure-IOT-SDK-c-opslag plaats](https://github.com/Azure/azure-iot-sdk-c)is een map met voor **beelden** die een toepassing bevat met de naam **simple sample \_ mqtt**. De Windows-versie van dit voor beeld bevat de volgende Visual Studio-oplossing:

  ![Visual Studio-oplossing voor mqtt-voor beeld](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Als u in Visual Studio wordt gevraagd om het project opnieuw te richten op de meest recente versie, accepteert u de prompt.

Net als bij het vorige voor beeld bevat dit een aantal NuGet-pakketten:

* Micro soft. Azure. C. SharedUtility
* Micro soft. Azure. IoTHub. MqttTransport
* Micro soft. Azure. IoTHub. IoTHubClient
* Micro soft. Azure. IoTHub. serializer
* Micro soft. Azure. umqtt

U hebt de meeste van deze pakketten in het vorige voor beeld gezien, maar **micro soft. Azure. IoTHub. serializer** is nieuw. Dit pakket is vereist wanneer u de **serialisatiefunctie** -bibliotheek gebruikt.

U kunt de implementatie van de voorbeeld toepassing vinden in het bestand iothub_client-voor **\_ beelden \_ iothub_convenience_sample** .

In de volgende secties vindt u een overzicht van de belangrijkste onderdelen van dit voor beeld.

### <a name="initialize-the-library"></a>De bibliotheek initialiseren

Roep de initialisatie-Api's aan om te gaan werken met de **serializer** -bibliotheek:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

De aanroep van de **serialisatiefunctie serializer \_ init** -functie is een eenmalige aanroep en initialiseert de onderliggende bibliotheek. Vervolgens roept u de **IoTHubClient \_ ll \_ API createfromconnectionstring** -functie aan. Dit is dezelfde API als in het **IoTHubClient** -voor beeld. Met deze aanroep wordt het apparaat ingesteld connection string (deze aanroep is ook het protocol dat u wilt gebruiken). In dit voor beeld wordt MQTT gebruikt als het Trans Port, maar kan AMQP of HTTPS gebruiken.

Roep tot slot de **functie \_ model \_ exemplaar maken** aan. **WeatherStation** is de naam ruimte van het model en de **ContosoAnemometer** is de name van het model. Zodra het model exemplaar is gemaakt, kunt u het gebruiken om berichten te verzenden en te ontvangen. Het is echter belang rijk om te begrijpen wat een model is.

### <a name="define-the-model"></a>Het model definiëren

Een model in de **serialisatiefunctie** -bibliotheek definieert de berichten die uw apparaat naar IOT hub kan verzenden en de berichten, *acties* genoemd in de model taal, die kunnen worden ontvangen. U definieert een model met behulp van een set C-macro's, zoals in de voorbeeld toepassing **iothub_client \_ samples \_ iothub_convenience_sample** :

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

De **begin \_ naam ruimte** -en **End- \_ naam ruimte** -macro's nemen de naam ruimte van het model als een argument. Er wordt naar verwachting alles tussen deze macro's is de definitie van uw model of modellen en de gegevens structuren die de modellen gebruiken.

In dit voor beeld is er één model met de naam **ContosoAnemometer**. Dit model definieert twee stukjes gegevens die uw apparaat kan verzenden naar IoT Hub: **DeviceID** en **WindSpeed**. Er worden ook drie acties (berichten) gedefinieerd die uw apparaat kan ontvangen: **TurnFanOn**, **TurnFanOff** en **SetAirResistance**. Elk gegevens element heeft een type en elke actie heeft een naam (en optioneel een set para meters).

De gegevens en acties die in het model zijn gedefinieerd, definiëren een API-Opper vlak dat u kunt gebruiken om berichten te verzenden naar IoT Hub en te reageren op berichten die naar het apparaat worden verzonden. Het gebruik van dit model wordt het best geïnterpreteerd door een voor beeld.

### <a name="send-messages"></a>Berichten verzenden

Het model definieert de gegevens die u naar IoT Hub kunt verzenden. In dit voor beeld betekent dit een van de twee gegevens items die zijn gedefinieerd met behulp van de **WITH_DATA** macro. Er zijn verschillende stappen vereist voor het verzenden van **DeviceID** -en **WindSpeed** -waarden naar een IOT-hub. De eerste is het instellen van de gegevens die u wilt verzenden:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Met het model dat u eerder hebt gedefinieerd, kunt u de waarden instellen door leden van een **struct** in te stellen. Serialiseren vervolgens het bericht dat u wilt verzenden:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Met deze code wordt de apparaat-naar-Cloud naar een buffer geserialiseerd (waarnaar wordt verwezen door de **bestemming**). De code roept vervolgens de **SendMessage** -functie aan om het bericht naar IOT hub te verzenden:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

De tweede tot laatste para meter van **IoTHubClient \_ ll \_ SendEventAsync** is een verwijzing naar een call back-functie die wordt aangeroepen wanneer de gegevens zijn verzonden. Hier volgt de call back-functie in het voor beeld:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

De tweede para meter is een verwijzing naar gebruikers context. dezelfde pointer is door gegeven aan **IoTHubClient \_ ll \_ SendEventAsync**. In dit geval is de context een eenvoudig item, maar dit kan alles wat u wilt.

Dat is alles wat u kunt doen om apparaat-naar-Cloud-berichten te verzenden. Het enige wat u kunt dekken, is het ontvangen van berichten.

### <a name="receive-messages"></a>Berichten ontvangen

Het ontvangen van een bericht werkt op een vergelijk bare manier als berichten in de **IoTHubClient** -bibliotheek. Eerst registreert u een call back-functie:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Vervolgens schrijft u de call back-functie die wordt aangeroepen wanneer een bericht wordt ontvangen:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
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

Deze code is standaard, maar is hetzelfde voor alle oplossingen. Deze functie ontvangt het bericht en zorgt ervoor dat het wordt doorgestuurd naar de juiste functie via de aanroep naar **Execute- \_ opdracht**. De functie die op dit punt wordt aangeroepen, is afhankelijk van de definitie van de acties in uw model.

Wanneer u een actie in uw model definieert, bent u verplicht een functie te implementeren die wordt aangeroepen wanneer het bijbehorende bericht wordt ontvangen op het apparaat. Als uw model bijvoorbeeld deze actie definieert:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Definieer een functie met deze hand tekening:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

U ziet hoe de naam van de functie overeenkomt met de naam van de actie in het model en dat de para meters van de functie overeenkomen met de para meters die zijn opgegeven voor de actie. De eerste para meter is altijd vereist en bevat een verwijzing naar het exemplaar van uw model.

Wanneer het apparaat een bericht ontvangt dat overeenkomt met deze hand tekening, wordt de bijbehorende functie aangeroepen. Daarom hoeft u niet alleen de standaard code van **IoTHubMessage** op te nemen, maar het ontvangen van berichten is slechts een kwestie van het definiëren van een eenvoudige functie voor elke actie die in uw model is gedefinieerd.

### <a name="uninitialize-the-library"></a>De initialisatie van de bibliotheek ongedaan te doen

Wanneer u klaar bent met het verzenden van gegevens en het ontvangen van berichten, kunt u de initialisatie van de IoT-bibliotheek ongedaan maken:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Elk van deze drie functies wordt uitgelijnd met de drie initialisatie functies die eerder zijn beschreven. Door deze Api's aan te roepen, zorgt u ervoor dat u eerder toegewezen resources vrijmaakt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de basis beginselen besproken van het gebruik van de bibliotheken in de **Azure IOT Device SDK voor C**. U krijgt voldoende informatie om te begrijpen wat er is opgenomen in de SDK, de architectuur ervan en hoe u kunt werken met de Windows-voor beelden. In het volgende artikel wordt de beschrijving van de SDK voortgezet door meer te weten te komen [over de IoTHubClient-bibliotheek](iot-hub-device-sdk-c-iothubclient.md).

Zie de [Azure IOT sdk's](iot-hub-devguide-sdks.md)voor meer informatie over het ontwikkelen van IOT hub.

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)
