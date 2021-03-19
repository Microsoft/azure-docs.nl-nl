---
title: Webhooks als gebeurtenis-handlers voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u webhooks kunt gebruiken als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen. Azure Automation runbooks en Logic apps worden ondersteund als gebeurtenis-handlers via webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: aa20c75789a18b93f787134dffe165e60ff1ab6f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91875798"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhooks, Automation-runbooks Logic Apps als gebeurtenis-handlers voor Azure Event Grid-gebeurtenissen
Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een aantal verdere acties nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het verwerken van gebeurtenissen. U kunt ook een webhook gebruiken voor het verwerken van gebeurtenissen. De webhook hoeft niet te worden gehost in azure voor het verwerken van gebeurtenissen. Event Grid ondersteunt alleen HTTPS-webhook-eind punten.

> [!NOTE]
> - Azure Automation runbooks en Logic apps worden ondersteund als gebeurtenis-handlers via webhooks. 
> - Hoewel u **webhook** kunt gebruiken als een **eindpunt type** om een Azure-functie als een gebeurtenis-handler te configureren, gebruikt u **Azure function** als een eindpunt type. Zie [Azure function as a event handler](handler-functions.md)(Engelstalig) voor meer informatie.

## <a name="webhooks"></a>Webhooks
Raadpleeg de volgende artikelen voor een overzicht en voor beelden van het gebruik van webhooks als gebeurtenis-handlers. 

|Titel  |Beschrijving  |
|---------|---------|
| Snelstartgids: aangepaste gebeurtenissen maken en routeren met- [Azure cli](custom-event-quickstart.md), [Power shell](custom-event-quickstart-powershell.md)en [Portal](custom-event-quickstart-portal.md). | Laat zien hoe u aangepaste gebeurtenissen naar een webhook verzendt. |
| Snelstartgids: Blob Storage-gebeurtenissen door sturen naar een aangepast webeindpunt met- [Azure cli](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [Power shell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)en [Portal](blob-event-quickstart-portal.md). | Toont hoe Blob Storage-gebeurtenissen naar een webhook worden verzonden. |
| [Snelstartgids: container register gebeurtenissen verzenden](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Laat zien hoe u met Azure CLI Container Registry-gebeurtenissen kunt verzenden. |
| [Overzicht: gebeurtenissen ontvangen naar een HTTP-eind punt](receive-events.md) | Hierin wordt beschreven hoe u een HTTP-eind punt kunt valideren om gebeurtenissen van een gebeurtenis abonnement te ontvangen en gebeurtenissen te ontvangen en te deserialiseren. |


## <a name="azure-automation"></a>Azure Automation
U kunt gebeurtenissen verwerken door Azure Automation runbooks te gebruiken. Het verwerken van gebeurtenissen met behulp van geautomatiseerde runbooks wordt ondersteund via webhooks. U maakt een webhook voor het runbook en gebruikt vervolgens de webhook-handler. Raadpleeg de volgende zelf studie voor een voor beeld: 

|Titel  |Beschrijving  |
|---------|---------|
|[Zelf studie: Azure Automation met Event Grid en micro soft teams](ensure-tags-exists-on-new-virtual-machines.md) |Maak een virtuele machine die een gebeurtenis verzendt. De gebeurtenis activeert een Automation-runbook waarmee de virtuele machine wordt gecodeerd en een bericht wordt geactiveerd dat naar een micro soft teams-kanaal wordt verzonden. |


## <a name="logic-apps"></a>Logic Apps
Gebruik **Logic apps** om bedrijfs processen te implementeren om event grid-gebeurtenissen te verwerken. U maakt geen webhook expliciet in dit scenario. De webhook wordt automatisch voor u gemaakt wanneer u de logische app configureert voor het afhandelen van gebeurtenissen van Event Grid. Raadpleeg de volgende zelf studies voor voor beelden: 

|Titel  |Beschrijving  |
|---------|---------|
| [Zelf studie: wijzigingen van virtuele machines bewaken met Azure Event Grid en Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Een logische app controleert wijzigingen aan een virtuele machine en stuurt e-mails over die wijzigingen. |
| [Zelf studie: e-mail meldingen over Azure IoT Hub-gebeurtenissen verzenden met Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Een logische app verzendt een e-mail melding wanneer een apparaat wordt toegevoegd aan uw IoT-hub. |
| [Zelfstudie: Reageren op Azure Service Bus-gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Functions en Azure Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid verzendt berichten van Service Bus onderwerp naar de functie app en de logische app. |

## <a name="rest-example-for-put"></a>REST-voor beeld (voor PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers. 
