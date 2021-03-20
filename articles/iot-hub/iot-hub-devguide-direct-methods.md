---
title: Meer informatie over Azure IoT Hub directe methoden | Microsoft Docs
description: 'Ontwikkelaars handleiding: gebruik directe methoden om code op uw apparaten aan te roepen vanuit een service-app.'
author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: philmea
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: b75e859fc1237bc88bee464cef423b7289810fa8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147790"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Directe methoden van IoT Hub begrijpen en aanroepen

IoT Hub biedt u de mogelijkheid om direct methoden te activeren op apparaten in de Cloud. Directe methoden vertegenwoordigen een aanvraag/antwoord-interactie met een apparaat dat vergelijkbaar is met een HTTP-aanroep wanneer deze slagen of direct mislukken (na een door de gebruiker opgegeven time-out). Deze aanpak is nuttig voor scenario's waarbij het verloop van de onmiddellijke actie verschillend is, afhankelijk van het feit of het apparaat in staat was om te reageren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Elke device-methode is gericht op één apparaat. [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md) laat zien hoe u direct methoden kunt aanroepen op meerdere apparaten, en hoe u de methode aanroep plant voor niet-verbonden apparaten.

Iedereen met **service Connect** -machtigingen op IOT hub kan een-methode aanroepen op een apparaat.

Directe methoden volgen een aanvraag/antwoord patroon en zijn bedoeld voor communicatie waarvoor onmiddellijke bevestiging van het resultaat is vereist. Bijvoorbeeld interactief beheer van het apparaat, zoals het inschakelen van een ventilator.

Raadpleeg de [communicatie richtlijnen van Cloud naar apparaat](iot-hub-devguide-c2d-guidance.md) als u twijfelt tussen het gebruik van de gewenste eigenschappen, directe methoden of Cloud-naar-apparaat-berichten.

## <a name="method-lifecycle"></a>Methode levenscyclus

Directe methoden worden geïmplementeerd op het apparaat en kunnen nul of meer invoer vereisen in de methode-Payload om op de juiste wijze te instantiëren. U roept een directe methode aan via een service gerichte URI ( `{iot hub}/twins/{device id}/methods/` ). Een apparaat ontvangt directe methoden via een apparaatspecifieke MQTT onderwerp ( `$iothub/methods/POST/{method name}/` ) of via AMQP-koppelingen (de eigenschappen van de `IoThub-methodname` en `IoThub-status` toepassing).

> [!NOTE]
> Wanneer u een rechtstreekse methode aanroept op een apparaat, kunnen eigenschaps namen en-waarden alleen in de volgende gevallen een door ons ASCII afdruk bare alfanumerieke waarde bevatten: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Directe methoden zijn synchroon en slagen of mislukken na de time-outperiode (standaard: 30 seconden, instelbaar tussen 5 en 300 seconden). Directe methoden zijn handig in interactieve scenario's waarin u wilt dat een apparaat reageert als en alleen als het apparaat online is en opdrachten ontvangt. U kunt bijvoorbeeld een lampje van een telefoon inschakelen. In deze scenario's wilt u direct slagen of mislukken, zodat de Cloud service zo snel mogelijk kan reageren op het resultaat. Het apparaat kan een deel van de bericht tekst retour neren als resultaat van de methode, maar dit is niet vereist voor de methode. Er is geen garantie voor de volg orde of enige semantiek van gelijktijdigheids op methode aanroepen.

Directe methoden zijn alleen HTTPS vanuit de Cloud kant en MQTT, AMQP, MQTT over websockets of AMQP over websockets van de kant van het apparaat.

De payload voor methode aanvragen en-antwoorden is een JSON-document van Maxi maal 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Een directe methode vanuit een back-end-app aanroepen

Roep nu een directe methode aan vanuit een back-end-app.

### <a name="method-invocation"></a>Methode aanroep

Directe methode aanroepen op een apparaat zijn HTTPS-aanroepen die bestaan uit de volgende items:

* De *aanvraag-URI* die specifiek is voor het apparaat, samen met de [API-versie](/rest/api/iothub/service/devices/invokemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* De POST- *methode*

* *Headers* die de autorisatie, aanvraag-id, inhouds type en inhouds codering bevatten.

* Een transparante JSON- *hoofd tekst* met de volgende indeling:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

De waarde `responseTimeoutInSeconds` die in de aanvraag wordt gegeven, is de hoeveelheid tijd die IOT hub service moet wachten tot de uitvoering van een directe methode op een apparaat is voltooid. Stel deze time-out in op mini maal zo lang als de verwachte uitvoerings tijd van een directe methode door een apparaat. Als er geen time-out wordt gegeven, wordt de standaard waarde van 30 seconden gebruikt. De minimum-en maximum waarden voor `responseTimeoutInSeconds` zijn respectievelijk 5 en 300 seconden.

De waarde die `connectTimeoutInSeconds` in de aanvraag is gegeven, is de hoeveelheid tijd die nodig is voor het aanroepen van een directe methode die IOT hub service moet wachten totdat een apparaat zonder verbinding online kan worden gezet. De standaard waarde is 0, wat betekent dat apparaten al online moeten zijn bij het aanroepen van een directe methode. De maximum waarde voor `connectTimeoutInSeconds` is 300 seconden.

#### <a name="example"></a>Voorbeeld

Met dit voor beeld kunt u veilig een aanvraag initiëren voor het aanroepen van een directe methode op een IoT-apparaat dat is geregistreerd bij een Azure-IoT Hub.

Als u wilt beginnen, gebruikt u de [Microsoft Azure IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) om een SharedAccessSignature te maken.

```bash
az iot hub generate-sas-token -n <iothubName> -du <duration>
```

Vervang vervolgens de autorisatie-header door de zojuist gegenereerde SharedAccessSignature en wijzig de `iothubName` `deviceId` `methodName` para meters, en en stel deze `payload` in op uw implementatie in de `curl` onderstaande voorbeeld opdracht.  

```bash
curl -X POST \
  https://<iothubName>.azure-devices.net/twins/<deviceId>/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

Voer de gewijzigde opdracht uit om de opgegeven directe methode aan te roepen. Geslaagde aanvragen retour neren een HTTP 200-status code.

> [!NOTE]
> In het bovenstaande voor beeld ziet u hoe u een directe methode aanroept op een apparaat.  Als u een directe methode in een IoT Edge module wilt aanroepen, moet u de URL-aanvraag wijzigen, zoals hieronder wordt weer gegeven:

```bash
https://<iothubName>.azure-devices.net/twins/<deviceId>/modules/<moduleName>/methods?api-version=2018-06-30
```
### <a name="response"></a>Antwoord

De back-end-app ontvangt een antwoord dat bestaat uit de volgende items:

* *HTTP-status code*:
  * 200 geeft een geslaagde uitvoering van de directe methode aan.
  * 404 geeft aan dat de apparaat-ID ongeldig is, of dat het apparaat niet online was bij het aanroepen van een directe methode en voor het volgende `connectTimeoutInSeconds` (gebruik een begeleid fout bericht om de hoofd oorzaak te begrijpen).
  * 504 geeft de time-out van de gateway aan waardoor het apparaat niet reageert op een directe methode aanroep binnen `responseTimeoutInSeconds` .

* *Headers* die de ETAG, aanvraag-id, inhouds type en inhouds codering bevatten.

* Een JSON- *hoofd tekst* met de volgende indeling:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Beide `status` en `body` worden verzorgd door het apparaat en worden gebruikt om te reageren met de eigen status code en/of beschrijving van het apparaat.

### <a name="method-invocation-for-iot-edge-modules"></a>Methode aanroep voor IoT Edge modules

Het aanroepen van directe methoden met een module-ID wordt ondersteund in de C#-SDK van de [IOT-service client](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Voor dit doel gebruikt u de `ServiceClient.InvokeDeviceMethodAsync()` methode en geeft u de `deviceId` `moduleId` para meters en als door.

## <a name="handle-a-direct-method-on-a-device"></a>Een directe methode op een apparaat verwerken

Laten we eens kijken hoe u een directe methode op een IoT-apparaat kunt verwerken.

### <a name="mqtt"></a>MQTT

De volgende sectie is voor het MQTT-protocol.

#### <a name="method-invocation"></a>Methode aanroep

Apparaten ontvangen directe methode aanvragen in het onderwerp MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}` . Het aantal abonnementen per apparaat is beperkt tot 5. Daarom is het raadzaam om niet afzonderlijk te abonneren op elke directe methode. Overweeg in plaats daarvan te abonneren `$iothub/methods/POST/#` en vervolgens de bezorgde berichten te filteren op basis van de namen van de gewenste methoden.

De hoofd tekst die het apparaat ontvangt, heeft de volgende indeling:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Methode aanvragen zijn QoS 0.

#### <a name="response"></a>Antwoord

Het apparaat verzendt reacties naar `$iothub/methods/res/{status}/?$rid={request id}` , waarbij:

* De `status` eigenschap is de door het apparaat opgegeven status van de uitvoering van de methode.

* De `$rid` eigenschap is de aanvraag-id van de aanroep methode die is ontvangen van IOT hub.

De hoofd tekst wordt ingesteld door het apparaat en kan elke status hebben.

### <a name="amqp"></a>AMQP

De volgende sectie is voor het AMQP-protocol.

#### <a name="method-invocation"></a>Methode aanroep

Het apparaat ontvangt directe methode aanvragen door een ontvangst koppeling op adres te maken `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` .

Het AMQP-bericht arriveert op de ontvangst koppeling die de methode aanvraag vertegenwoordigt. Het bevat de volgende secties:

* De eigenschap correlatie-ID, die een aanvraag-ID bevat die opnieuw moet worden door gegeven met de bijbehorende methode reactie.

* Een toepassings eigenschap genaamd `IoThub-methodname` , die de naam bevat van de methode die wordt aangeroepen.

* De bericht tekst van het AMQP met de methode payload als JSON.

#### <a name="response"></a>Antwoord

Het apparaat maakt een verzend koppeling om de methode reactie op adres te retour neren `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` .

De reactie van de methode wordt geretourneerd op de verzendende koppeling en is als volgt gestructureerd:

* De eigenschap correlatie-ID, die de aanvraag-ID bevat die is door gegeven in het aanvraag bericht van de methode.

* Een toepassings eigenschap `IoThub-status` met de naam, die de door de gebruiker opgegeven methode status bevat.

* De AMQP-bericht tekst die de methode respons bevat als JSON.

## <a name="additional-reference-material"></a>Extra referentie materiaal

Andere naslag onderwerpen in de IoT Hub ontwikkelaars handleiding zijn:

* [IOT hub-eind punten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eind punten die elke IOT-hub beschikbaar maakt voor runtime-en beheer bewerkingen.

* Met [beperking en quota](iot-hub-devguide-quotas-throttling.md) worden de quota's beschreven die van toepassing zijn en het beperkings gedrag dat moet worden verwacht wanneer u IOT hub gebruikt.

* Met de [sdk's van Azure IOT-apparaat en-service](iot-hub-devguide-sdks.md) worden de diverse sdk's voor de taal weer gegeven die u kunt gebruiken bij het ontwikkelen van zowel apparaat-als service-apps die communiceren met IOT hub.

* [IOT hub query taal voor apparaatdubbels, taken en bericht routering](iot-hub-devguide-query-language.md) beschrijft de IOT hub query taal die u kunt gebruiken om informatie op te halen van IOT hub over de apparaatdubbels en taken van uw apparaat.

* [IOT hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IOT hub ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u direct-methoden gebruikt, bent u mogelijk geïnteresseerd in het volgende artikel IoT Hub ontwikkelaars handleiding:

* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Als u een aantal van de concepten wilt uitproberen die in dit artikel worden beschreven, bent u mogelijk geïnteresseerd in de volgende IoT Hub zelf studie:

* [Directe methoden gebruiken](quickstart-control-device-node.md)
* [Apparaatbeheer met Azure IoT Tools voor VS Code](iot-hub-device-management-iot-toolkit.md)