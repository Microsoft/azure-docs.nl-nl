---
title: Meer informatie over het uploaden van Azure IoT Hub bestand | Microsoft Docs
description: 'Ontwikkelaars handleiding: gebruik de functie voor het uploaden van bestanden van IoT Hub voor het beheren van het uploaden van bestand van een apparaat naar een Azure Storage-BLOB-container.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 3286b464051b8fea88d2797d4f82b20fe432b4b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90019526"
---
# <a name="upload-files-with-iot-hub"></a>Bestanden uploaden met IoT Hub

Zoals beschreven in het artikel over [IOT hub-eind punten](iot-hub-devguide-endpoints.md) kan een bestand worden geüpload door een melding te verzenden via een eind punt op basis van een apparaat (**/devices/{deviceId}/files**). Wanneer een apparaat IoT Hub dat een upload is voltooid, verzendt IoT Hub een bericht over het uploaden van een bestand via het service Facing-eind punt van de **/Messages/servicebound/filenotifications** .

In plaats van berichten te brokeren via IoT Hub zichzelf, IoT Hub in plaats daarvan als een verzender naar een gekoppeld Azure Storage-account. Een apparaat vraagt een opslag token op van IoT Hub dat specifiek is voor het bestand dat het apparaat wil uploaden. Het apparaat gebruikt de SAS-URI voor het uploaden van het bestand naar de opslag en wanneer het uploaden is voltooid, stuurt het apparaat een melding van voltooiing naar IoT Hub. IoT Hub controleert of het uploaden van het bestand is voltooid en voegt vervolgens een bericht over het uploaden van een bestand toe aan het eind punt voor bestands meldingen op de service.

Voordat u een bestand uploadt naar IoT Hub van een apparaat, moet u uw hub configureren door [een Azure Storage](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) -account aan het te koppelen.

Uw apparaat kan vervolgens [een upload initialiseren](iot-hub-devguide-file-upload.md#initialize-a-file-upload) en vervolgens [IOT hub waarschuwen](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) wanneer het uploaden is voltooid. Wanneer een apparaat aangeeft IoT Hub dat het uploaden is voltooid, kan de service een [meldings bericht](iot-hub-devguide-file-upload.md#file-upload-notifications)genereren.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>Wanneer gebruikt u dit?

Gebruik bestand uploaden om media bestanden en grote telemetriegegevens te verzenden die worden geüpload door apparaten die op een regel matig zijn aangesloten of die zijn gecomprimeerd om band breedte te besparen.

Raadpleeg de [richt lijnen voor communicatie tussen apparaten](iot-hub-devguide-d2c-guidance.md) in de Cloud als u twijfelt tussen het gebruik van gerapporteerde eigenschappen, apparaat-naar-Cloud-berichten of het uploaden van bestanden.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Een Azure Storage-account koppelen aan IoT Hub

Als u de functie voor het uploaden van bestanden wilt gebruiken, moet u eerst een Azure Storage-account koppelen aan de IoT Hub. U kunt deze taak volt ooien via de Azure Portal, of programmatisch door de [IOT hub resource provider rest-api's](/rest/api/iothub/iothubresource). Zodra u een Azure Storage account hebt gekoppeld aan uw IoT Hub, retourneert de service een SAS-URI naar een apparaat wanneer het apparaat een aanvraag voor het uploaden van bestanden start.

De [Upload bestanden van uw apparaat naar de Cloud met IOT hub](iot-hub-csharp-csharp-file-upload.md) hand leidingen bieden een volledig overzicht van het uploaden van het bestand. In deze hand leidingen wordt uitgelegd hoe u de Azure Portal kunt gebruiken om een opslag account te koppelen aan een IoT-hub.

> [!NOTE]
> De [Azure IOT sdk's](iot-hub-devguide-sdks.md) verwerken automatisch het ophalen van de SAS-URI, het uploaden van het bestand en het melden van IOT hub van een voltooide upload.

## <a name="initialize-a-file-upload"></a>Het uploaden van een bestand initialiseren
IoT Hub heeft een eind punt dat specifiek is voor apparaten om een SAS-URI voor opslag aan te vragen voor het uploaden van een bestand. Om het proces voor het uploaden van bestanden te starten, verzendt het apparaat een POST-aanvraag naar `{iot hub}.azure-devices.net/devices/{deviceId}/files` met de volgende JSON-hoofd tekst:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub retourneert de volgende gegevens, die door het apparaat worden gebruikt voor het uploaden van het bestand:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Afgeschaft: een bestand uploaden met een GET initialiseren

> [!NOTE]
> In deze sectie wordt de afgeschafte functionaliteit voor het ontvangen van een SAS-URI van IoT Hub beschreven. Gebruik de POST-methode die eerder is beschreven.

IoT Hub heeft twee REST-eind punten ter ondersteuning van het uploaden van bestanden, een om de SAS-URI voor opslag te verkrijgen en de andere om de IoT-hub op de hoogte te stellen van een voltooide upload. Het apparaat start het upload proces van het bestand door een GET naar de IoT-hub te verzenden naar `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` . De IoT-hub retourneert:

* Een SAS-URI die specifiek is voor het bestand dat moet worden geüpload.

* Een correlatie-ID die moet worden gebruikt wanneer het uploaden is voltooid.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>IoT Hub informeren over het uploaden van voltooide bestanden

Het apparaat uploadt het bestand naar opslag met behulp van de Azure Storage Sdk's. Wanneer het uploaden is voltooid, verzendt het apparaat een POST-aanvraag naar `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` met de volgende JSON-hoofd tekst:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

De waarde van `isSuccess` is een Boolean die aangeeft of het bestand is geüpload. De status code voor `statusCode` is de status van het uploaden van het bestand naar Storage en het `statusDescription` komt overeen met de `statusCode` .

## <a name="reference-topics"></a>Naslag onderwerpen:

In de volgende onderwerpen vindt u meer informatie over het uploaden van bestanden vanaf een apparaat.

## <a name="file-upload-notifications"></a>Meldingen over het uploaden van bestanden

Wanneer een apparaat aangeeft IoT Hub dat een upload is voltooid, wordt in IoT Hub een meldings bericht gegenereerd. Dit bericht bevat de naam en de opslag locatie van het bestand.

Zoals in [eind punten](iot-hub-devguide-endpoints.md)wordt uitgelegd, levert IOT hub meldingen over het uploaden van bestanden via een service Facing endpoint (**/Messages/servicebound/fileuploadnotifications**) als berichten. De ontvangst semantiek voor meldingen over het uploaden van bestanden zijn hetzelfde als voor Cloud-naar-apparaat-berichten en hebben dezelfde [levens cyclus](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)van het bericht. Elk bericht dat wordt opgehaald uit het eind punt voor het uploaden van berichten, is een JSON-record met de volgende eigenschappen:

| Eigenschap | Beschrijving |
| --- | --- |
| EnqueuedTimeUtc |De tijds tempel die aangeeft wanneer de melding is gemaakt. |
| DeviceId |**DeviceID** van het apparaat dat het bestand heeft geüpload. |
| BlobUri |De URI van het geüploade bestand. |
| BlobName |De naam van het geüploade bestand. |
| LastUpdatedTime |Tijds tempel die aangeeft wanneer het bestand voor het laatst is bijgewerkt. |
| BlobSizeInBytes |Grootte van het geüploade bestand. |

**Voor beeld**. Dit voor beeld toont de hoofd tekst van een bericht over het uploaden van bestanden.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Configuratie opties voor meldingen over het uploaden van bestanden

Elke IoT-hub beschikt over de volgende configuratie opties voor meldingen over het uploaden van bestanden:

| Eigenschap | Beschrijving | Bereik en standaard |
| --- | --- | --- |
| **enableFileUploadNotifications** |Hiermee wordt bepaald of het uploaden van meldingen naar het eind punt van bestands meldingen wordt geschreven. |BOOL. Standaard: waar. |
| **fileNotifications.ttlAsIso8601** |Standaard-TTL voor meldingen over het uploaden van bestanden. |ISO_8601 interval tot 48H (mini maal 1 minuut). Standaard: 1 uur. |
| **fileNotifications.lockDuration** |Vergrendelings duur voor de wachtrij met berichten voor het uploaden van bestanden. |5 tot 300 seconden (mini maal 5 seconden). Standaard: 60 seconden. |
| **fileNotifications.maxDeliveryCount** |Maximum aantal bezorgingen voor de berichten wachtrij voor het uploaden van bestanden. |1 tot en met 100. Standaard: 100. |

U kunt deze eigenschappen instellen voor uw IoT-hub met behulp van de Azure Portal, Azure CLI of Power shell. Zie de onderwerpen onder het [bestand upload configureren](iot-hub-configure-file-upload.md)voor meer informatie.

## <a name="additional-reference-material"></a>Extra referentie materiaal

Andere naslag onderwerpen in de IoT Hub ontwikkelaars handleiding zijn:

* [IOT hub-eind punten](iot-hub-devguide-endpoints.md) beschrijven de verschillende IOT hub-eind punten voor runtime-en beheer bewerkingen.

* Met [beperking en quota's](iot-hub-devguide-quotas-throttling.md) worden de quota's en beperkings gedrag beschreven die van toepassing zijn op de IOT hub-service.

* Met de [sdk's van Azure IOT-apparaat en-service](iot-hub-devguide-sdks.md) worden de diverse sdk's voor de taal weer gegeven die u kunt gebruiken bij het ontwikkelen van zowel apparaat-als service-apps die communiceren met IOT hub.

* [IOT hub query taal](iot-hub-devguide-query-language.md) beschrijft de query taal die u kunt gebruiken om informatie op te halen van IOT hub over de apparaatdubbels en taken van uw apparaat.

* [IOT hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IOT hub ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u bestanden uploadt van apparaten met behulp van IoT Hub, bent u mogelijk geïnteresseerd in de volgende onderwerpen over IoT Hub ontwikkelaars handleiding:

* [Apparaat-id's in IoT Hub beheren](iot-hub-devguide-identity-registry.md)

* [Toegang tot IoT Hub regelen](iot-hub-devguide-security.md)

* [Apparaatdubbels gebruiken om status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md)

* [Een rechtstreekse methode aanroepen op een apparaat](iot-hub-devguide-direct-methods.md)

* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Raadpleeg de volgende IoT Hub zelf studie als u een aantal van de concepten wilt uitproberen die in dit artikel worden beschreven:

* [Bestanden van apparaten uploaden naar de Cloud met IoT Hub](iot-hub-csharp-csharp-file-upload.md)