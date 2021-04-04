---
title: Azure IoT Device SDK voor C-IoTHubClient | Microsoft Docs
description: Het gebruik van de IoTHubClient-bibliotheek in de Azure IoT Device SDK voor C om apparaat-apps te maken die communiceren met een IoT-hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f33521dd9110d7ba6ee84650345b38c8c6a4950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149135"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT Device SDK voor C: meer informatie over IoTHubClient

[Azure IOT Device SDK voor c](iot-hub-device-sdk-c-intro.md) is het eerste artikel in deze reeks introductie **van de Azure IOT Device SDK voor c**. In dit artikel wordt uitgelegd dat er twee architectuur lagen zijn in de SDK. De basis is de **IoTHubClient** -bibliotheek die de communicatie met IOT hub rechtstreeks beheert. Er is ook de **serialisatiefunctie** -bibliotheek die boven op die gebaseerd is om serialisatie services te bieden. In dit artikel vindt u meer informatie over de **IoTHubClient** -bibliotheek.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

In het vorige artikel wordt beschreven hoe u de **IoTHubClient** -bibliotheek gebruikt om gebeurtenissen te verzenden naar IOT hub en berichten te ontvangen. In dit artikel wordt uitgelegd hoe u deze discussie uitbreidt door uit te leggen *hoe u de* gegevens die u verzendt en ontvangt, nauw keuriger kunt beheren door te kennismaken met de **lagere api's**. Er wordt ook uitgelegd hoe u eigenschappen koppelt aan gebeurtenissen (en ze ophaalt uit berichten) met behulp van de functies voor het afhandelen van eigenschappen in de **IoTHubClient** -bibliotheek. Ten slotte bieden we extra uitleg over verschillende manieren om berichten te verwerken die zijn ontvangen van IoT Hub.

Het artikel wordt afgesloten met een aantal verschillende onderwerpen, met inbegrip van meer informatie over de referenties van het apparaat en het wijzigen van het gedrag van de **IoTHubClient** via configuratie opties.

We gebruiken de **IoTHubClient** SDK-voor beelden om deze onderwerpen uit te leggen. Als u wilt volgen, raadpleegt u de voor beelden van **iothub \_ \_ -client \_ -http-** en **iothub- \_ client \_ \_ AMQP** -toepassingen die zijn opgenomen in de Azure IOT Device SDK voor C. alles wat wordt beschreven in de volgende secties, wordt in deze voor beelden toegelicht.

U vindt de [**Azure IOT Device SDK voor c**](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats en Raadpleeg de details van de API in de [C API-referentie](/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>De lagere Api's

In het vorige artikel wordt de basis bewerking van de **IotHubClient** in de context van de **iothub client-voor \_ \_ beeld \_ AMQP** -toepassing beschreven. Het is bijvoorbeeld uitgelegd hoe u de bibliotheek initialiseert met behulp van deze code.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Ook wordt beschreven hoe u gebeurtenissen verzendt met behulp van deze functie aanroep.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

In dit artikel wordt ook beschreven hoe u berichten kunt ontvangen door een call back-functie te registreren.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Het artikel heeft ook geleerd hoe u resources kunt vrijmaken met behulp van code zoals het volgende.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Er zijn aanvullende functies voor elk van deze Api's:

* IoTHubClient \_ ll \_ API createfromconnectionstring
* IoTHubClient \_ ll \_ SendEventAsync
* IoTHubClient \_ ll \_ SetMessageCallback
* IoTHubClient \_ ll \_ vernietigen

Deze functies bevatten allemaal **ll** in de API-naam. Andere het **ll** deel van de naam, de para meters van elk van deze functies zijn identiek aan de niet-ll equivalenten. Het gedrag van deze functies verschilt echter op een belang rijke manier.

Wanneer u **IoTHubClient \_ API createfromconnectionstring** aanroept, maken de onderliggende bibliotheken een nieuwe thread die op de achtergrond wordt uitgevoerd. Deze thread verzendt gebeurtenissen naar en ontvangt berichten van IoT Hub. Er wordt geen thread gemaakt wanneer u met de **ll** -api's werkt. Het maken van de achtergrond thread is een gemak voor de ontwikkelaar. U hoeft zich geen zorgen te maken over het expliciet verzenden van gebeurtenissen en het ontvangen van berichten van IoT Hub. dit gebeurt automatisch op de achtergrond. De **ll** -api's bieden daarentegen expliciet controle over de communicatie met IOT hub, indien nodig.

Voor een beter begrip van dit concept bekijken we een voor beeld:

Wanneer u **IoTHubClient \_ SendEventAsync** aanroept, wordt de gebeurtenis in een buffer geplaatst. De achtergrond thread die wordt gemaakt wanneer u **IoTHubClient \_ API createfromconnectionstring** aanroept, bewaakt deze buffer voortdurend en stuurt alle gegevens die deze bevat naar IOT hub. Dit gebeurt op de achtergrond op hetzelfde moment dat de hoofd thread andere werkzaamheden uitvoert.

En wanneer u een call back-functie voor berichten registreert met **IoTHubClient \_ SetMessageCallback**, geeft u de SDK de opdracht de call back-functie aan te roepen wanneer een bericht wordt ontvangen, onafhankelijk van de hoofd thread.

De **ll** -api's maken geen achtergrond thread. In plaats daarvan moet een nieuwe API worden aangeroepen voor het expliciet verzenden en ontvangen van gegevens van IoT Hub. Dit wordt in het volgende voor beeld gedemonstreerd.

De **iothub \_ client \_ \_ -http-** toepassing die in de SDK is opgenomen, demonstreert de api's van het lagere niveau. In dat voor beeld sturen we gebeurtenissen naar IoT Hub met de volgende code:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

De eerste drie regels maken het bericht en de laatste regel verzendt de gebeurtenis. Zoals eerder vermeld, betekent het verzenden van de gebeurtenis echter dat de gegevens eenvoudigweg in een buffer worden geplaatst. Er wordt niets verzonden op het netwerk wanneer **IoTHubClient \_ ll \_ SendEventAsync** wordt aangeroepen. Als u de gegevens die u wilt IoT Hub daad werkelijk wilt intrekken, moet u **IoTHubClient \_ ll \_ DoWork** aanroepen, zoals in dit voor beeld:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Deze code (van de **voor \_ \_ beeld \_ -http** -toepassing van de iothub-client) roept herhaaldelijk **IoTHubClient \_ ll \_ DoWork**. Elke keer dat **IoTHubClient \_ ll \_ DoWork** wordt aangeroepen, verzendt het een aantal gebeurtenissen uit de buffer naar IOT hub en haalt het een bericht in de wachtrij op dat naar het apparaat wordt verzonden. Dit laatste geval betekent dat als er een call back functie voor berichten is geregistreerd, de call back wordt aangeroepen (ervan uitgaande dat er berichten in de wachtrij zijn geplaatst). We hebben een dergelijke call back-functie met de volgende code geregistreerd:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

De reden dat **IoTHubClient \_ ll \_ DoWork** vaak in een lus wordt genoemd, is dat elke keer dat deze wordt aangeroepen, een *aantal* gebufferde gebeurtenissen naar IOT hub worden verzonden en *het volgende* bericht wordt opgehaald dat voor het apparaat in de wachtrij is geplaatst. Elke aanroep is niet gegarandeerd om alle gebufferde gebeurtenissen te verzenden of om alle berichten in de wachtrij op te halen. Als u alle gebeurtenissen in de buffer wilt verzenden en vervolgens wilt door gaan met andere verwerking, kunt u deze lus vervangen door de volgende code:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Met deze code wordt **IoTHubClient \_ ll \_ DoWork** aangeroepen totdat alle gebeurtenissen in de buffer naar IOT hub zijn verzonden. Opmerking: Dit betekent niet dat alle berichten in de wachtrij zijn ontvangen. Daarom is het controleren op ' alle ' berichten niet als deterministisch een actie. Wat gebeurt er als u ' alle ' van de berichten ophaalt, maar er wordt direct na een andere verzen ding naar het apparaat verzonden. Een betere manier om dat te doen met een geprogrammeerde time-out. De functie voor het terugbellen van berichten kan bijvoorbeeld elke keer dat deze wordt aangeroepen een timer opnieuw instellen. U kunt vervolgens logica schrijven om de verwerking voort te zetten als er bijvoorbeeld geen berichten in de afgelopen *X* seconden zijn ontvangen.

Wanneer u klaar bent met het uitvallen van gebeurtenissen en het ontvangen van berichten, moet u de bijbehorende functie aanroepen om de resources op te schonen.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

In principe is er slechts één set Api's voor het verzenden en ontvangen van gegevens met een achtergrond thread en een andere set Api's die hetzelfde hetzelfde doen als de achtergrond thread. Een groot aantal ontwikkel aars kan de voor keur geven aan de niet-LL Api's, maar de Api's van het lagere niveau zijn handig wanneer de ontwikkelaar expliciete controle over netwerk overdrachten wil. Sommige apparaten verzamelen bijvoorbeeld gegevens gedurende een bepaalde periode en alleen ingangs gebeurtenissen met opgegeven intervallen (bijvoorbeeld eenmaal per uur of eenmaal per dag). De Api's op lagere niveaus bieden u de mogelijkheid om expliciet te bepalen wanneer u gegevens van IoT Hub verzendt en ontvangt. Anderen hebben gewoon de voor keur aan de eenvoud die de Api's van het lagere niveau bieden. Alles gebeurt op de hoofd thread in plaats van op de achtergrond.

Welk model u kiest, zorg ervoor dat u consistent bent met de Api's die u gebruikt. Als u begint met het aanroepen van **IoTHubClient \_ ll \_ API createfromconnectionstring**, moet u ervoor zorgen dat u alleen de bijbehorende api's met een lager niveau gebruikt voor eventuele opvolgings werkzaamheden:

* IoTHubClient \_ ll \_ SendEventAsync
* IoTHubClient \_ ll \_ SetMessageCallback
* IoTHubClient \_ ll \_ vernietigen
* IoTHubClient \_ ll \_ DoWork

Het tegenovergestelde is ook waar. Als u begint met **IoTHubClient \_ API createfromconnectionstring**, gebruikt u de niet-ll api's voor aanvullende verwerking.

In de Azure IoT Device SDK voor C raadpleegt u **de \_ iothub \_ client \_ -voorbeeld-http-** toepassing voor een volledig voor beeld van de api's van het lagere niveau. Er kan worden verwezen naar de **\_ \_ \_ AMQP** -toepassing van de iothub-client voor een volledig voor beeld van de niet-ll api's.

## <a name="property-handling"></a>Afhandeling van eigenschappen

Tot nu toe is het verzenden van gegevens naar de hoofd tekst van het bericht verwezen. Neem bijvoorbeeld de volgende code:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

In dit voor beeld wordt een bericht verzonden naar IoT Hub met de tekst ' Hallo wereld '. IoT Hub echter ook toestaan dat eigenschappen aan elk bericht worden gekoppeld. Eigenschappen zijn naam/waarde-paren die aan het bericht kunnen worden toegevoegd. We kunnen bijvoorbeeld de vorige code wijzigen om een eigenschap aan het bericht toe te voegen:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

We beginnen met het aanroepen van **IoTHubMessage- \_ Eigenschappen** en geven deze de ingang van ons bericht door. We gaan terug naar een verwijzing naar de **kaart \_ koppeling** waarmee we eigenschappen kunnen toevoegen. Deze laatste wordt bereikt door het aanroepen van de **kaart \_ AddOrUpdate**, die een verwijzing naar een koppelings \_ ingang, de naam van de eigenschap en de waarde van de eigenschap. Met deze API kunnen we net zoveel eigenschappen toevoegen als we.

Wanneer de gebeurtenis wordt gelezen uit **Event hubs**, kan de ontvanger de eigenschappen opsommen en de bijbehorende waarden ophalen. In .NET wordt dit bijvoorbeeld gedaan door de [verzameling eigenschappen van het object Event Data](/dotnet/api/microsoft.servicebus.messaging.eventdata)te openen.

In het vorige voor beeld voegen we eigenschappen toe aan een gebeurtenis die wordt verzonden naar IoT Hub. Eigenschappen kunnen ook worden toegevoegd aan berichten die zijn ontvangen van IoT Hub. Als we eigenschappen van een bericht willen ophalen, kunnen we code gebruiken zoals het volgende in onze bericht retour aanroep functie:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

De aanroep van **IoTHubMessage- \_ Eigenschappen** retourneert de verwijzing naar de **kaart \_ koppeling** . Vervolgens wordt deze verwijzing door gegeven **om \_ GetInternals** toe te wijzen om een verwijzing te verkrijgen naar een matrix van de naam/waarde-paren (evenals een telling van de eigenschappen). Op dat moment is het een eenvoudige kwestie van het opsommen van de eigenschappen om naar de gewenste waarden te gaan.

U hoeft geen eigenschappen in uw toepassing te gebruiken. Als u ze echter wilt instellen op gebeurtenissen of ze wilt ophalen uit berichten, maakt de **IoTHubClient** -bibliotheek het eenvoudig.

## <a name="message-handling"></a>Afhandeling van berichten

Zoals eerder is aangegeven, reageert het aanroepen van een geregistreerde call back-functie wanneer berichten binnenkomen van IoT Hub de **IoTHubClient** -bibliotheek. Er is een retour parameter van deze functie die een extra uitleg verdient. Hier volgt een fragment van de call back-functie in de voor **\_ \_ beeld \_ -http-voorbeeld toepassing iothub client** :

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Houd er rekening mee dat het retour type **IOTHUBMESSAGE \_ \_** is en in dit specifieke geval retour neren **IOTHUBMESSAGE \_ geaccepteerd**. Er zijn andere waarden die we kunnen retour neren van deze functie die wijzigen hoe de **IoTHubClient** -bibliotheek op de bericht terugroeping reageert. Dit zijn de opties.

* **IOTHUBMESSAGE \_ GEACCEPTEERD** : het bericht is verwerkt. De **IoTHubClient** -bibliotheek roept de call back-functie niet opnieuw aan met hetzelfde bericht.

* **IOTHUBMESSAGE \_ AFGEWEZEN** : het bericht is niet verwerkt en kan niet in de toekomst worden gedaan. De **IoTHubClient** -bibliotheek moet de call back functie niet opnieuw aanroepen met hetzelfde bericht.

* **IOTHUBMESSAGE \_ Afgebroken** : het bericht is niet verwerkt, maar de **IoTHubClient** -bibliotheek moet de call back-functie opnieuw met hetzelfde bericht aanroepen.

Voor de eerste twee retour codes stuurt de **IoTHubClient** -bibliotheek een bericht naar IOT hub om aan te geven dat het bericht moet worden verwijderd uit de wachtrij van het apparaat en niet opnieuw wordt bezorgd. Het net-effect is hetzelfde (het bericht wordt uit de wachtrij verwijderd), maar of het bericht wel of niet is geaccepteerd, wordt nog steeds vastgelegd.  Het vastleggen van dit onderscheid is handig voor afzenders van het bericht dat kan worden geluisterd naar feedback en te achterhalen of een apparaat een bepaald bericht heeft geaccepteerd of geweigerd.

In het laatste geval wordt er ook een bericht verzonden naar IoT Hub, maar dit geeft aan dat het bericht opnieuw moet worden bezorgd. Normaal gesp roken wordt er een bericht weer gegeven als er een fout optreedt, maar u wilt proberen het bericht opnieuw te verwerken. Het afwijzen van een bericht is daarentegen geschikt wanneer u een onherstelbare fout tegen komt (of als u het bericht alleen wilt verwerken).

Let in elk geval op de verschillende retour codes, zodat u het gewenste gedrag uit de **IoTHubClient** -bibliotheek kunt ontzeggen.

## <a name="alternate-device-credentials"></a>Alternatieve referenties voor het apparaat

Zoals eerder is uitgelegd, is het eerste wat u moet doen bij het werken met de **IoTHubClient** -bibliotheek om een **IOTHUB- \_ client \_ ingang** te verkrijgen met een aanroep zoals de volgende:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

De argumenten voor **IoTHubClient \_ API createfromconnectionstring** zijn de Connection String van het apparaat en een para meter die het protocol aangeeft dat we gebruiken om te communiceren met IOT hub. Het connection string van het apparaat heeft een indeling die er als volgt uitziet:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Er zijn vier stukjes gegevens in deze teken reeks: IoT Hub naam, IoT Hub achtervoegsel, apparaat-ID en gedeelde toegangs sleutel. U haalt de Fully Qualified Domain Name (FQDN) van een IoT-hub op wanneer u uw IoT hub-instantie in de Azure Portal maakt. Dit geeft u de naam van de IoT-hub (het eerste deel van de FQDN) en het achtervoegsel van de IoT-hub (de rest van de FQDN). U krijgt de apparaat-ID en de gedeelde toegangs sleutel wanneer u uw apparaat registreert bij IoT Hub (zoals beschreven in het [vorige artikel](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient \_ API createfromconnectionstring** biedt u één manier om de bibliotheek te initialiseren. U kunt desgewenst een nieuwe **IOTHUB- \_ client \_ ingang** maken met behulp van deze afzonderlijke para meters in plaats van de Connection String van het apparaat. Dit wordt bereikt met de volgende code:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Dit heeft hetzelfde effect als **IoTHubClient \_ API createfromconnectionstring**.

Het lijkt erop dat u **IoTHubClient \_ API createfromconnectionstring** wilt gebruiken in plaats van deze uitgebreide initialisatie methode. Houd er echter wel van uit dat wanneer u een apparaat registreert in IoT Hub wat u krijgt, een apparaat-ID en een apparaatcode (niet een connection string). In het hulp programma *device Explorer* SDK dat in het [vorige artikel](iot-hub-device-sdk-c-intro.md) is geïntroduceerd, worden de bibliotheken in de **Azure IOT Service SDK** gebruikt voor het maken van het apparaat Connection String van de apparaat-id, de apparaatcode en de naam van de IOT hub-host. Daarom is het aanroepen van **IoTHubClient \_ ll \_** mogelijk voor keur omdat u de stap voor het genereren van een Connection String opslaat. Gebruik de methode die u wilt gebruiken.

## <a name="configuration-options"></a>Configuratie-opties

Tot nu toe is alles wat wordt beschreven in de manier waarop de **IoTHubClient** -bibliotheek het standaard gedrag weerspiegelt. Er zijn echter enkele opties die u kunt instellen om de werking van de bibliotheek te wijzigen. Dit wordt bereikt door gebruik te maken van de **IoTHubClient \_ ll \_ SetOption** API. Kijk eens naar dit voorbeeld:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Er zijn een aantal opties die vaak worden gebruikt:

* **SetBatching** (BOOL): als deze **waarde True** is, worden gegevens die naar IOT hub worden verzonden, in batches verzonden. Indien **Onwaar**, worden berichten afzonderlijk verzonden. De standaard waarde is **False**. Batch verwerking via AMQP/AMQP-WS, en het toevoegen van systeem eigenschappen op D2C-berichten wordt ondersteund.

* **Time-out** (niet-ondertekende int): deze waarde wordt weer gegeven in milliseconden. Als het verzenden van een HTTPS-aanvraag of het ontvangen van een reactie langer duurt dan deze tijd, wordt de verbinding verbroken.

De batch optie is belang rijk. Standaard zijn de bibliotheek ingresses-gebeurtenissen afzonderlijk (één gebeurtenis is datgene wat u doorgeeft aan **IoTHubClient \_ ll \_ SendEventAsync**). Als de batch optie **waar** is, verzamelt de bibliotheek zoveel gebeurtenissen als dat kan van de buffer (Maxi maal de maximale bericht grootte die IOT hub accepteert).  De gebeurtenis batch wordt verzonden naar IoT Hub in één HTTPS-aanroep (de afzonderlijke gebeurtenissen worden gebundeld in een JSON-matrix). Het inschakelen van batch verwerking leidt doorgaans tot grote prestatie verbeteringen omdat u de netwerk round-trips reduceert. Ook wordt de band breedte aanzienlijk verminderd omdat u één set HTTPS-headers verzendt met een gebeurtenis batch in plaats van een set kopteksten voor elke afzonderlijke gebeurtenis. Tenzij u een specifieke reden hebt om anders te doen, is het doorgaans verstandig om batch verwerking in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt gedetailleerde informatie gegeven over het gedrag van de **IoTHubClient** -bibliotheek die in de **Azure IOT Device SDK voor C** is gevonden. Met deze informatie moet u een goed idee hebben van de mogelijkheden van de **IoTHubClient** -bibliotheek. Het tweede artikel in deze reeks is [Azure IOT Device SDK voor C-serializer](iot-hub-device-sdk-c-serializer.md), waarmee vergelijk bare Details worden geboden in de **serialisatiefunctie** -bibliotheek.

Zie de [Azure IOT sdk's](iot-hub-devguide-sdks.md)voor meer informatie over het ontwikkelen van IOT hub.

Zie [AI implementeren op edge-apparaten met Azure IOT Edge](../iot-edge/quickstart-linux.md)om de mogelijkheden van IOT hub verder te verkennen.