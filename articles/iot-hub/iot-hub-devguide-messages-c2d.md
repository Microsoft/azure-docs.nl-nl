---
title: Meer Azure IoT Hub van cloud-naar-apparaat-berichten | Microsoft Docs
description: In deze ontwikkelaarshandleiding wordt besproken hoe u cloud-naar-apparaat-berichten gebruikt met uw IoT-hub. Het bevat informatie over de levenscyclus van berichten en configuratieopties.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 7bb3ca2b31eaef5c0639f30e0f2a329a37dfe7e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761777"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Cloud-naar-apparaat-berichten verzenden vanuit een IoT-hub

Als u meldingen in één keer naar een apparaat-app wilt verzenden vanuit de back-end van uw oplossing, verzendt u cloud-naar-apparaat-berichten van uw IoT-hub naar uw apparaat. Zie Richtlijnen voor cloud-naar-apparaat-communicatie voor een bespreking van andere cloud-naar-apparaat-opties die door Azure IoT Hub [worden ondersteund.](iot-hub-devguide-c2d-guidance.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

U verzendt cloud-naar-apparaat-berichten via een service-gericht eindpunt, */messages/devicebound.* Een apparaat ontvangt de berichten vervolgens via een apparaatsspecifieke eindpunt, */devices/{deviceId}/messages/devicebound.*

Als u elk cloud-naar-apparaat-bericht op één apparaat wilt  richten, stelt uw IoT-hub de eigenschap in op */devices/{deviceId}/messages/devicebound.*

Elke apparaatwachtrij bevat niet meer dan 50 cloud-naar-apparaat-berichten. Als u meer berichten naar hetzelfde apparaat wilt verzenden, resulteert dit in een fout.

## <a name="the-cloud-to-device-message-life-cycle"></a>De levenscyclus van berichten van cloud naar apparaat

Om te garanderen dat berichten ten minste één keer worden bezorgd, worden cloud-naar-apparaat-berichten door uw IoT-hub in wachtrijen per apparaat opgeslagen. Als de IoT-hub de berichten uit de wachtrij wil verwijderen, moeten de apparaten expliciet bevestigen dat ze *zijn voltooid.* Deze aanpak garandeert tolerantie tegen connectiviteits- en apparaatfouten.

De levenscyclustoestandgrafiek wordt weergegeven in het volgende diagram:

![Levenscyclus van berichten van cloud naar apparaat](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Wanneer de IoT Hub-service een bericht naar een apparaat verzendt, stelt de service de berichttoestand in *op Enqueued.* Wanneer een apparaat een *bericht* wil ontvangen, vergrendelt de IoT-hub  het bericht door de status in te stellen op *Onzichtbaar.* Met deze status kunnen andere threads op het apparaat andere berichten ontvangen. Wanneer een apparaatthread de verwerking van een bericht heeft voltooid, wordt de IoT-hub op de melding gezonden door *het bericht in* te vullen. De IoT-hub stelt vervolgens de status in op *Voltooid.*

Een apparaat kan ook:

* *Weiger* het bericht, waardoor de IoT-hub het in de status *Dead lettered in stelt.* Apparaten die verbinding maken via het Message Queuing Telemetry Transport (MQTT) Protocol kunnen cloud-naar-apparaat-berichten niet afwijzen.

* *Verlaat* het bericht, waardoor de IoT-hub het bericht weer in de wachtrij zet, waarbij de status is ingesteld *op Enqueued*. Apparaten die verbinding maken via het MQTT-protocol, kunnen cloud-naar-apparaat-berichten niet verlaten.

Een thread kan een bericht niet verwerken zonder de IoT-hub hiervan op de hoogte te stellen. In dit geval worden berichten  na een time-out van zichtbaarheid  (of time-out voor vergrendelen) automatisch van de status Onzichtbaar terug naar de status *Enqueued.*  De waarde van deze time-out is één minuut en kan niet worden gewijzigd.

De **eigenschap max delivery count** van de IoT-hub bepaalt het maximum aantal keren dat een bericht kan worden overgeleverd tussen de *enqueued-* en *de invisible-staten.* Na dat aantal overgangen stelt de IoT-hub de status van het bericht in op *Dead lettered*. Op dezelfde manier stelt de IoT-hub de status van een bericht in op *Dead lettered* na de verlooptijd. Zie Time [to Live voor meer informatie.](#message-expiration-time-to-live)

In [het artikel Cloud-naar-apparaat-berichten](iot-hub-csharp-csharp-c2d.md) verzenden met IoT Hub wordt beschreven hoe u cloud-naar-apparaat-berichten verzendt vanuit de cloud en deze op een apparaat ontvangt.

Normaal gesproken voltooit een apparaat een cloud-naar-apparaat-bericht wanneer het verlies van het bericht geen invloed heeft op de toepassingslogica. Een voorbeeld hiervan kan zijn wanneer het apparaat de berichtinhoud lokaal heeft opgeslagen of een bewerking heeft uitgevoerd. Het bericht kan ook tijdelijke informatie bevatten, waarvan het verlies geen invloed heeft op de functionaliteit van de toepassing. Soms kunt u voor langlopende taken het volgende doen:

* Voltooi het cloud-naar-apparaat-bericht nadat de taakbeschrijving van het apparaat in de lokale opslag is opgeslagen.

* Breng de back-end van de oplossing op de hoogte met een of meer apparaat-naar-cloud-berichten in verschillende fasen van de voortgang van de taak.

## <a name="message-expiration-time-to-live"></a>Verlopen van berichten (time to live)

Elk cloud-naar-apparaat-bericht heeft een verlooptijd. Deze tijd wordt ingesteld door een van de volgende opties:

* De **eigenschap ExpiryTimeUtc** in de service
* De IoT-hub, met behulp van de standaard *time to live* die is opgegeven als een eigenschap van een IoT-hub

Zie [Configuratieopties voor cloud-naar-apparaat.](#cloud-to-device-configuration-options)

Een veelgebruikte manier om te profiteren van een verlopen bericht en om te voorkomen dat berichten naar niet-verbonden apparaten worden verzonden, is door korte tijd in te stellen *op livewaarden.* Deze aanpak levert hetzelfde resultaat op als het onderhouden van de verbindingstoestand van het apparaat, maar is efficiënter. Wanneer u berichtbevestigingen aanvraagt, krijgt u een melding van de IoT-hub over de apparaten:

* Kan berichten ontvangen.
* Zijn niet online of zijn mislukt.

## <a name="message-feedback"></a>Feedback op berichten

Wanneer u een cloud-naar-apparaat-bericht verzendt, kan de service de levering van feedback per bericht aanvragen over de uiteindelijke status van dat bericht. U doet dit door de **toepassings-eigenschap iothub-ack** in te stellen in het cloud-naar-apparaat-bericht dat wordt verzonden naar een van de volgende vier waarden:

| ACK-eigenschapswaarde | Gedrag |
| ------------ | -------- |
| geen     | De IoT-hub genereert geen feedbackbericht (standaardgedrag). |
| positief | Als het cloud-naar-apparaat-bericht de status *Voltooid* heeft, genereert de IoT-hub een feedbackbericht. |
| negatief | Als het cloud-naar-apparaat-bericht de status Dead *lettered* bereikt, genereert de IoT-hub een feedbackbericht. |
| Volledige     | In beide gevallen genereert de IoT-hub een feedbackbericht. |

Als de **Ack-waarde** *vol* is en u geen feedbackbericht ontvangt, betekent dit dat het feedbackbericht is verlopen. De service kan niet weten wat er met het oorspronkelijke bericht is gebeurd. In de praktijk moet een service ervoor zorgen dat deze de feedback kan verwerken voordat deze verloopt. De maximale verlooptijd is twee dagen, waardoor er nog tijd over is om de service opnieuw te laten draaien als er een fout optreedt.

Zoals uitgelegd in [Eindpunten,](iot-hub-devguide-endpoints.md)levert de IoT-hub feedback via een service-gericht eindpunt, */messages/servicebound/feedback,* als berichten. De semantiek voor het ontvangen van feedback is hetzelfde als voor cloud-naar-apparaat-berichten. Indien mogelijk wordt berichtfeedback in één batch in één bericht weergegeven, met de volgende indeling:

| Eigenschap     | Beschrijving |
| ------------ | ----------- |
| EnqueuedTime | Een tijdstempel die aangeeft wanneer het feedbackbericht is ontvangen door de hub |
| UserId       | `{iot hub name}` |
| Contenttype  | `application/vnd.microsoft.iothub.feedback.json` |

Het systeem verzendt de feedback wanneer de batch 64 berichten bereikt, of binnen 15 seconden na de laatste verzonden, wat het eerst komt. 

De body is een door JSON geser serialiseerde matrix van records, elk met de volgende eigenschappen:

| Eigenschap           | Beschrijving |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Een tijdstempel die aangeeft wanneer het resultaat van het bericht is gebeurd (de hub heeft bijvoorbeeld het feedbackbericht ontvangen of het oorspronkelijke bericht is verlopen) |
| OriginalMessageId  | De *MessageId* van het cloud-naar-apparaat-bericht waarop deze feedback-informatie betrekking heeft |
| StatusCode         | Een vereiste tekenreeks die wordt gebruikt in feedbackberichten die worden gegenereerd door de IoT-hub: <br/> *Geslaagd* <br/> *Verlopen* <br/> *DeliveryCountExceeded* <br/> *Afgewezen* <br/> *Verwijderd* |
| Description        | Tekenreekswaarden voor *StatusCode* |
| DeviceId           | De *DeviceId* van het doelapparaat van het cloud-naar-apparaat-bericht waaraan dit feedback-gegeven is gerelateerd |
| DeviceGenerationId | De *DeviceGenerationId* van het doelapparaat van het cloud-naar-apparaat-bericht waaraan dit feedback-gegeven is gerelateerd |

De service moet een MessageId opgeven om de feedback van het cloud-naar-apparaat-bericht te correleren met het *oorspronkelijke bericht.*

De body van een feedbackbericht wordt weergegeven in de volgende code:

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

**Feedback in behandeling voor verwijderde apparaten**

Wanneer een apparaat wordt verwijderd, worden eventuele feedback in behandeling ook verwijderd. Apparaatfeedback wordt in batches verzonden. Als een apparaat wordt verwijderd in het smalle venster (vaak minder dan 1 seconde) tussen het moment waarop het apparaat de ontvangst van het bericht bevestigt en wanneer de volgende feedbackbatch wordt voorbereid, vindt de feedback niet plaats.

U kunt dit gedrag aanpakken door een bepaalde tijd te wachten totdat feedback in behandeling is voordat u uw apparaat kunt verwijderen. Gerelateerde berichtfeedback moet verloren gaan zodra een apparaat wordt verwijderd.

## <a name="cloud-to-device-configuration-options"></a>Configuratieopties voor cloud-naar-apparaat

Elke IoT-hub biedt de volgende configuratieopties voor cloud-naar-apparaat-berichten:

| Eigenschap                  | Beschrijving | Bereik en standaard |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standaard-TTL voor cloud-naar-apparaat-berichten | ISO_8601 interval van maximaal 2 dagen (minimaal 1 minuut); standaardinstelling: 1 uur |
| maxDeliveryCount          | Maximum aantal leveringen voor cloud-naar-apparaat-wachtrijen per apparaat | 1 tot 100; standaardinstelling: 10 |
| feedback.ttlAsIso8601     | Retentie voor servicegebonden feedbackberichten | ISO_8601 interval van maximaal 2 dagen (minimaal 1 minuut); standaardinstelling: 1 uur |
| feedback.maxDeliveryCount | Maximum aantal leveringen voor de feedbackwachtrij | 1 tot 100; standaardinstelling: 10 |
| feedback.lockDurationAsIso8601 | Maximum aantal leveringen voor de feedbackwachtrij | ISO_8601 interval tussen 5 en 300 seconden (minimaal 5 seconden); standaardinstelling: 60 seconden. |

U kunt de configuratieopties op een van de volgende manieren instellen:

* **Azure Portal:** selecteer onder **Instellingen** op uw IoT-hub **ingebouwde** eindpunten en vouw **Cloud naar apparaatberichten uit.** (Het instellen van de eigenschappen **feedback.maxDeliveryCount** en **feedback.lockDurationAsIso8601** wordt momenteel niet ondersteund in Azure Portal.)

    ![Configuratieopties instellen voor cloud-naar-apparaat-berichten in de portal](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI:** gebruik de [opdracht az iot hub update:](/cli/azure/iot/hub#az_iot_hub_update)

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

Zie Azure [IoT SDK's](iot-hub-devguide-sdks.md)voor informatie over de SDK's die u kunt gebruiken om cloud-naar-apparaat-berichten te ontvangen.

Zie de zelfstudie Cloud-naar-apparaat verzenden om te proberen cloud-naar-apparaat-berichten [te](iot-hub-csharp-csharp-c2d.md) ontvangen.
