---
title: Webhooks maken voor regels in azure IoT Central | Microsoft Docs
description: Webhooks maken in azure IoT Central om andere toepassingen automatisch te waarschuwen wanneer regels worden gestart.
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87337225"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-acties maken voor regels in Azure IoT Central

*Dit onderwerp is van toepassing op bouwers en beheerders.*

Met webhooks kunt u uw IoT Central-app verbinden met andere toepassingen en services voor externe controle en meldingen. Webhooks melden automatisch andere toepassingen en services die u maakt wanneer een regel wordt geactiveerd in uw IoT Central-app. De IoT Central-app verzendt een POST-aanvraag naar het HTTP-eind punt van de andere toepassing wanneer een regel wordt geactiveerd. De payload bevat details over het apparaat en de regel trigger.

## <a name="set-up-the-webhook"></a>De webhook instellen

In dit voor beeld maakt u verbinding met RequestBin om een melding te ontvangen wanneer regels worden gestart met webhooks.

1. Open [RequestBin](https://requestbin.net/).

1. Maak een nieuwe RequestBin en kopieer de **URL van de opslag locatie**.

1. Maak een [telemetrie-regel](tutorial-create-telemetry-rules.md). Sla de regel op en voeg een nieuwe actie toe.

    ![Scherm webhook maken](media/howto-create-webhooks/webhookcreate.png)

1. Kies de actie webhook en geef een weergave naam op en plak de URL van de opslag plaats als de URL voor terugbellen.

1. Sla de regel op.

Wanneer de regel wordt geactiveerd, ziet u nu een nieuwe aanvraag wordt weer gegeven in RequestBin.

## <a name="payload"></a>Nettolading

Wanneer een regel wordt geactiveerd, wordt er een HTTP POST-aanvraag verzonden naar de call back-URL met een JSON-nettolading met de telemetrie, het apparaat, de regel en de toepassings gegevens. De payload kan er als volgt uitzien:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
Als de regel de geaggregeerde telemetrie in een bepaalde periode bewaakt, bevat de payload een andere telemetrie-sectie.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>Wijzigings waarschuwing voor gegevens indeling

Als u een of meer webhooks hebt gemaakt en opgeslagen vóór **3 April 2020**, moet u de webhook verwijderen en een nieuwe webhook maken. Dit komt doordat oudere webhooks een oudere Payload-indeling gebruiken die in de toekomst zal worden afgeschaft.

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>Payload van webhook (indeling afgeschaft vanaf 3 april 2020)

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Bekende beperkingen

Er is momenteel geen programmatische manier om u te abonneren op of afmelden bij deze webhooks via een API.

Als u ideeën hebt voor het verbeteren van deze functie, plaatst u uw suggesties op het [forum voor gebruikers spraak](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u webhooks kunt instellen en gebruiken, is de voorgestelde volgende stap het configureren van [Azure monitor actie groepen](howto-use-action-groups.md)verkennen.
