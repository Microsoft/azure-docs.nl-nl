---
title: Meer informatie over Azure IoT Hub Cloud-naar-apparaat-berichten | Microsoft Docs
description: In deze ontwikkelaars handleiding wordt beschreven hoe u Cloud-naar-apparaat-berichten kunt gebruiken met uw IoT-hub. Het bevat informatie over de levens cyclus en configuratie opties voor het bericht.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 154b496a6c14d307c09ddcd1b42bf4ba568cb315
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607888"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Cloud-naar-apparaat-berichten verzenden vanuit een IoT-hub

Als u eenrichtings meldingen naar een apparaat-app wilt verzenden vanuit de back-end van uw oplossing, verzendt u Cloud-naar-apparaat-berichten van uw IoT-hub naar uw apparaat. Zie [Cloud-naar-apparaat-communicatie richtlijnen](iot-hub-devguide-c2d-guidance.md)voor een bespreking van andere Cloud-naar-apparaat-opties die door Azure IOT hub worden ondersteund.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

U verzendt Cloud-naar-apparaat-berichten via een service gericht eind punt, */Messages/devicebound*. Een apparaat ontvangt vervolgens de berichten via een apparaat-specifiek eind punt, */devices/{deviceId}/messages/devicebound*.

Om elk Cloud-naar-apparaat-bericht op één apparaat te richten, stelt uw IoT-hub de eigenschap **in** op */devices/{deviceId}/messages/devicebound*.

Elke wachtrij van een apparaat bevat Maxi maal 50 Cloud-naar-apparaat-berichten. Als u wilt proberen om meer berichten te verzenden naar hetzelfde apparaat, resulteert dit in een fout.

## <a name="the-cloud-to-device-message-life-cycle"></a>De levens cyclus van het Cloud-naar-apparaat-bericht

Om ten minste één keer per e-mail te zorgen, blijven de Cloud-naar-apparaat-berichten in wacht rijen van het apparaat. Voor de IoT-hub om de berichten uit de wachtrij te verwijderen, moeten de apparaten de *voltooiing* expliciet bevestigen. Deze aanpak garandeert de flexibiliteit tegen connectiviteit en storingen in het apparaat.

De grafiek levens cyclus status wordt weer gegeven in het volgende diagram:

![Levens cyclus van Cloud-naar-apparaat-berichten](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Wanneer de IoT hub-service een bericht naar een apparaat verzendt, stelt de service de bericht status in in *wachtrij*. Wanneer een apparaat een bericht wil *ontvangen* , wordt het bericht door de IOT-hub *vergrendeld* door de status op *onzichtbaar* in te stellen. Met deze status kunnen andere threads op het apparaat andere berichten ontvangen. Wanneer een apparaat-thread de verwerking van een bericht voltooit, wordt de IoT hub geïnformeerd door het bericht te *volt ooien* . De IoT hub stelt vervolgens de status in op *voltooid*.

Een apparaat kan ook het volgende doen:

* Het bericht *afwijzen* , wat ervoor zorgt dat de IOT-hub deze instelt op de status voor *onbestelbare berichten* . Apparaten die verbinding maken via het MQTT-Protocol (Message Queuing telemetrie Trans Port), kunnen geen Cloud-naar-apparaat-berichten afwijzen.

* Het bericht *afbreken* , wat ervoor zorgt dat de IOT-hub het bericht weer in de wachtrij plaatst, waarbij de status is ingesteld op in *wachtrij*. Apparaten die via het MQTT-protocol verbinding maken, kunnen geen Cloud-naar-apparaat-berichten afbreken.

Een thread kan een bericht niet verwerken zonder de IoT-hub op de hoogte te stellen. In dit geval worden berichten automatisch overgezet van de *onzichtbare* status *na* een time-out van de *zicht baarheid* (of *vergrendelings* time-out). De waarde van deze time-out is één minuut en kan niet worden gewijzigd.

De eigenschap **Max Delivery Count** van de IOT hub bepaalt het maximum aantal keren dat een bericht kan worden overgezet tussen de status in *wachtrij* en *onzichtbaar* . Na dat aantal overgangen stelt de IoT-hub de status van het bericht in op *onbestelbare* berichten. Op dezelfde manier wordt de status van een bericht in de IoT-hub ingesteld op *onbestelbare* berichten na verloop tijd. Zie [time to Live (TTL](#message-expiration-time-to-live)) voor meer informatie.

Het [verzenden van Cloud-naar-apparaat-berichten met IOT hub](iot-hub-csharp-csharp-c2d.md) artikel laat zien hoe u Cloud-naar-apparaat-berichten vanuit de Cloud verzendt en op een apparaat ontvangt.

Een apparaat voert normaal gesp roken een Cloud-naar-apparaat-bericht uit wanneer het verlies van het bericht niet van invloed is op de toepassings logica. Een voor beeld hiervan is dat wanneer het apparaat de inhoud van het bericht lokaal persistent heeft of een bewerking heeft uitgevoerd. Het bericht kan ook tijdelijke informatie bevatten, waarvan het verlies geen invloed heeft op de functionaliteit van de toepassing. Soms kunt u voor langlopende taken het volgende doen:

* Voltooi het Cloud-naar-apparaat-bericht nadat het apparaat de taak beschrijving in de lokale opslag heeft behouden.

* Waarschuw de back-end van de oplossing met een of meer apparaat-naar-Cloud-berichten in verschillende stadia van de voortgang van de taak.

## <a name="message-expiration-time-to-live"></a>Verlopen van berichten (time to Live)

Elk Cloud-naar-apparaat-bericht heeft een verloop tijd. Deze tijd wordt ingesteld op een van de volgende manieren:

* De eigenschap **ExpiryTimeUtc** in de service
* De IoT-hub, met behulp van de standaard *time to Live* die is opgegeven als een IOT hub-eigenschap

Zie [Opties voor Cloud-naar-apparaat-configuratie](#cloud-to-device-configuration-options).

Een veelgebruikte manier om te profiteren van het verlopen van een bericht en om te voor komen dat er berichten worden verzonden naar niet-verbonden apparaten, is door een korte TTL-waarde *in te* stellen. Deze aanpak bereikt hetzelfde resultaat als het onderhouden van de verbindings status van het apparaat, maar het is efficiënter. Wanneer u bericht bevestigingen aanvraagt, wordt de IoT-hub op de hoogte gesteld van de volgende apparaten:

* Kan berichten ontvangen.
* Zijn niet online of zijn mislukt.

## <a name="message-feedback"></a>Feedback over berichten

Wanneer u een Cloud-naar-apparaat-bericht verzendt, kan de service de levering van feedback per bericht over de uiteindelijke status van het bericht aanvragen. U doet dit door de eigenschap **iothub-ACK-** toepassing in te stellen op het Cloud-naar-apparaat-bericht dat wordt verzonden naar een van de volgende vier waarden:

| Waarde van de eigenschap ACK | Gedrag |
| ------------ | -------- |
| geen     | De IoT-hub genereert geen feedback bericht (standaard gedrag). |
| positief | Als het Cloud-naar-apparaat-bericht de status *voltooid* heeft bereikt, genereert IOT hub een feedback bericht. |
| negatief | Als het Cloud-naar-apparaat-bericht de status voor *onbestelbare berichten* bereikt, genereert de IOT-hub een feedback bericht. |
| waard     | In de IoT-hub wordt in beide gevallen een feedback bericht gegenereerd. |

Als de **ACK** -waarde *vol* is en er geen feedback bericht wordt weer gegeven, betekent dit dat het feedback bericht is verlopen. De service kan niet weten wat er met het oorspronkelijke bericht is gebeurd. In de praktijk moet een service ervoor zorgen dat de feedback kan worden verwerkt voordat deze verloopt. De maximale verloop tijd is twee dagen, waardoor de service opnieuw wordt uitgevoerd als er een fout optreedt.

Zoals uitgelegd in [eind punten](iot-hub-devguide-endpoints.md), levert de IOT-hub feedback via een service gericht eind punt, */Messages/servicebound/feedback*, als berichten. De semantiek voor het ontvangen van feedback is hetzelfde als voor Cloud-naar-apparaat-berichten. Indien mogelijk wordt feedback over het bericht in één bericht batch gegeven, met de volgende indeling:

| Eigenschap     | Beschrijving |
| ------------ | ----------- |
| EnqueuedTime | Een tijds tempel die aangeeft wanneer het feedback bericht is ontvangen door de hub |
| UserId       | `{iot hub name}` |
| Invoer  | `application/vnd.microsoft.iothub.feedback.json` |

Het systeem verzendt de feedback wanneer de batch tot 64 berichten heeft of in 15 seconden van de laatste verzen ding, afhankelijk van wat het eerste komt. 

De hoofd tekst is een JSON-serialisatie matrix met records, elk met de volgende eigenschappen:

| Eigenschap           | Beschrijving |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Een tijds tempel die aangeeft wanneer de uitkomst van het bericht is opgetreden (bijvoorbeeld omdat de hub het feedback bericht heeft ontvangen of het oorspronkelijke bericht is verlopen) |
| OriginalMessageId  | De *MessageId* van het Cloud-naar-apparaat-bericht waarop deze feedback informatie betrekking heeft |
| Status code         | Een vereiste teken reeks die wordt gebruikt in feedback berichten die door de IoT hub worden gegenereerd: <br/> *Geslaagd* <br/> *Verlopen* <br/> *DeliveryCountExceeded* <br/> *Afgewezen* <br/> *Opgeschoond* |
| Description        | Teken reeks waarden voor *status* code |
| DeviceId           | De *DeviceID* van het doel apparaat van het Cloud-naar-apparaat-bericht waarop dit feedback-item betrekking heeft |
| DeviceGenerationId | De *DeviceGenerationId* van het doel apparaat van het Cloud-naar-apparaat-bericht waarop dit feedback-item betrekking heeft |

Voor het Cloud-naar-apparaat-bericht om de feedback te correleren met het oorspronkelijke bericht, moet de service een *MessageId* opgeven.

De hoofd tekst van een feedback bericht wordt weer gegeven in de volgende code:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**Feedback voor verwijderde apparaten in behandeling**

Wanneer een apparaat wordt verwijderd, worden ook alle openstaande feedback verwijderd. Feedback van het apparaat wordt in batches verzonden. Als een apparaat wordt verwijderd in het smalle venster (vaak minder dan 1 seconde) tussen het moment dat het apparaat de ontvangst van het bericht bevestigt en wanneer de volgende feedback batch wordt voor bereid, wordt de feedback niet weer gegeven.

U kunt dit gedrag aanpakken door gedurende een periode te wachten op feedback die in behandeling is voordat u het apparaat verwijdert. Bij het verwijderen van een apparaat moet de feedback van het gerelateerde bericht verloren gaan.

## <a name="cloud-to-device-configuration-options"></a>Opties voor Cloud-naar-apparaat-configuratie

Elke IoT-hub biedt de volgende configuratie opties voor het verzenden van Cloud-naar-apparaat-berichten:

| Eigenschap                  | Beschrijving | Bereik en standaard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standaard-TTL voor Cloud-naar-apparaat-berichten | ISO_8601 interval tot 2 dagen (mini maal 1 minuut); standaard: 1 uur |
| maxDeliveryCount          | Maximum aantal leveringen voor wacht rijen van Cloud naar apparaat per apparaat | 1 tot 100; standaard: 10 |
| feedback. ttlAsIso8601     | Retentie voor service-gebonden feedback berichten | ISO_8601 interval tot 2 dagen (mini maal 1 minuut); standaard: 1 uur |
| feedback. maxDeliveryCount | Maximum aantal bezorgingen voor de feedback wachtrij | 1 tot 100; standaard: 10 |
| feedback. lockDurationAsIso8601 | Maximum aantal bezorgingen voor de feedback wachtrij | ISO_8601 interval van 5 tot 300 seconden (mini maal 5 seconden); standaard: 60 seconden. |

U kunt de configuratie opties op een van de volgende manieren instellen:

* **Azure Portal**: Selecteer onder **instellingen** op uw IOT-hub **ingebouwde eind punten** en vouw Cloud uit **voor het verzenden van apparaten**. (Het instellen van de eigenschappen **feedback. maxDeliveryCount** en **feedback. lockDurationAsIso8601** wordt momenteel niet ondersteund in azure Portal.)

    ![Configuratie opties voor Cloud-naar-apparaat-berichten in de Portal instellen](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure cli**: gebruik de opdracht [AZ IOT hub update](/cli/azure/iot/hub#az-iot-hub-update) :

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure IOT sdk's](iot-hub-devguide-sdks.md)(Engelstalig) voor meer informatie over de sdk's die u kunt gebruiken om Cloud-naar-apparaat-berichten te ontvangen.

Als u Cloud-naar-apparaat-berichten wilt ontvangen, raadpleegt u de zelf studie over het verzenden van de [Cloud naar het apparaat](iot-hub-csharp-csharp-c2d.md) .
