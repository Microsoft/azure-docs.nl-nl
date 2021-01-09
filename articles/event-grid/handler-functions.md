---
title: Een functie in azure gebruiken als gebeurtenis-handler voor Azure Event Grid gebeurtenissen
description: Hierin wordt beschreven hoe u functies kunt gebruiken die zijn gemaakt in en worden gehost door Azure Functions als gebeurtenis-handlers voor Event Grid-gebeurtenissen.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 5a1ec575b58829a422e4d263ae0324e0343d5ad3
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034965"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>Een functie gebruiken als gebeurtenis-handler voor Event Grid gebeurtenissen

Een gebeurtenis-handler is de plaats waar de gebeurtenis wordt verzonden. De handler heeft een actie nodig om de gebeurtenis te verwerken. Verschillende Azure-Services worden automatisch geconfigureerd voor het afhandelen van gebeurtenissen en **Azure functions** is een hiervan. 


Als u een functie in azure als een handler voor gebeurtenissen wilt gebruiken, volgt u een van de volgende benaderingen: 

-   Gebruik [Event grid trigger](../azure-functions/functions-bindings-event-grid-trigger.md).  Geef een **Azure-functie** op als het **type eind punt**. Geef vervolgens de functie-app en de functie op waarmee gebeurtenissen worden verwerkt. 
-   [Http-trigger](../azure-functions/functions-bindings-http-webhook.md)gebruiken.  Geef een **Webhook** op als het **type eind punt**. Geef vervolgens de URL op voor de functie waarmee gebeurtenissen worden verwerkt. 

U wordt aangeraden de eerste benadering (Event Grid trigger) te gebruiken, omdat deze de volgende voor delen heeft ten opzichte van de tweede benadering:
-   Event Grid triggers worden door Event Grid automatisch gevalideerd. Bij algemene HTTP-triggers moet u het [validatie antwoord](webhook-event-delivery.md) zelf implementeren.
-   Event Grid past automatisch de snelheid aan waarmee gebeurtenissen worden afgeleverd aan een functie die wordt geactiveerd door een Event Grid gebeurtenis op basis van de waargenomen snelheid waarmee de functie gebeurtenissen kan verwerken. Met deze functie voor de prijs overeenkomst worden bezorgings fouten van de functie voor het verwerken van gebeurtenissen in de loop van de tijd van de functie van de gebeurtenis verwerkings frequentie in Avert. Schakel batch verwerking in voor het gebeurtenis abonnement om de efficiëntie bij hoge door voer te verbeteren. Zie [batching inschakelen](#enable-batching)voor meer informatie.

    > [!NOTE]
    > Op dit moment kunt u geen Event Grid trigger gebruiken voor een functie-app wanneer de gebeurtenis wordt geleverd in het **CloudEvents** -schema. Gebruik in plaats daarvan een HTTP-trigger.

## <a name="tutorials"></a>Zelfstudies

|Titel  |Beschrijving  |
|---------|---------|
| [Quick Start: gebeurtenissen met functie afhandelen](custom-event-to-function.md) | Hiermee wordt een aangepaste gebeurtenis verzonden naar een functie voor verwerking. |
| [Zelf studie: het formaat van geüploade afbeeldingen automatisch wijzigen met behulp van Event Grid](resize-images-on-storage-blob-upload-event.md) | Gebruikers uploaden afbeeldingen via web-app naar het opslag account. Wanneer een opslag-BLOB wordt gemaakt, verzendt Event Grid een gebeurtenis naar de functie-app, waarmee het formaat van de geüploade afbeelding wordt gewijzigd. |
| [Zelf studie: stream big data naar een Data Warehouse](event-grid-event-hubs-integration.md) | Wanneer Event Hubs een opname bestand maakt, wordt Event Grid een gebeurtenis naar een functie-app verzonden. De app haalt het opname bestand op en migreert gegevens naar een Data Warehouse. |
| [Zelf studie: Azure Service Bus voor voor beelden van Azure Event Grid-integratie](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid verzendt berichten van Service Bus onderwerp naar een functie-app en een logische app. |

## <a name="rest-example-for-put"></a>REST-voor beeld (voor PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Batchverwerking inschakelen
Schakel voor een hogere door Voer batch verwerking in voor het abonnement. Als u de Azure Portal gebruikt, kunt u het maximum aantal gebeurtenissen per batch en de gewenste Batch grootte instellen op kilo bytes op het moment dat u een abonnement maakt of na het maken. 

U kunt batch-instellingen configureren met behulp van de sjabloon Azure Portal, Power shell, CLI of Resource Manager. 

### <a name="azure-portal"></a>Azure Portal
Op het moment dat u een abonnement maakt in de gebruikers interface, gaat u op de pagina **gebeurtenis abonnement maken** naar het tabblad **geavanceerde functies** en stelt u waarden in voor het **maximum aantal gebeurtenissen per batch** en de **voor Keurs-Batch grootte in KB**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Batch verwerking inschakelen op het moment van het maken van een abonnement":::

U kunt deze waarden voor een bestaand abonnement bijwerken op het tabblad **functies** van de pagina **Event grid onderwerp** . 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Batch verwerking inschakelen na het maken":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
U kunt **maxEventsPerBatch** en **preferredBatchSizeInKilobytes** instellen in een Azure Resource Manager sjabloon. Zie voor meer informatie [micro soft. EventGrid eventSubscriptions-sjabloon verwijzing](/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>Azure CLI
U kunt de opdracht [AZ eventgrid Event-Subscription Create](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) of [AZ eventgrid Event-Subscription update](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) gebruiken om batch-gerelateerde instellingen te configureren met de volgende para meters: `--max-events-per-batch` of `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
U kunt de cmdlet [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) of [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) gebruiken om batch instellingen te configureren met behulp van de volgende para meters: `-MaxEventsPerBatch` of `-PreferredBatchSizeInKiloBytes` .

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over [gebeurtenis-handlers](event-handlers.md) voor een lijst met ondersteunde gebeurtenis-handlers.
