---
title: Durable Functions publiceren naar Azure Event Grid (preview-versie)
description: Meer informatie over het configureren van automatische Azure Event Grid publicatie voor Durable Functions.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 44df100a5c794abf918a09dea0f94d30ddf916d3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175954"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publiceren naar Azure Event Grid (preview-versie)

In dit artikel wordt beschreven hoe u Durable Functions instelt voor het publiceren van Orchestrator-levenscyclus gebeurtenissen (zoals gemaakte, voltooide en mislukte) naar een aangepast [Azure Event grid onderwerp](../../event-grid/overview.md).

Hier volgen enkele scenario's waarin deze functie nuttig is:

* **DevOps-scenario's zoals Blue/groen-implementaties**: u wilt mogelijk weten of er taken worden uitgevoerd voordat u de [implementatie strategie naast](durable-functions-versioning.md#side-by-side-deployments)elkaar implementeert.

* **Ondersteuning voor geavanceerde bewakings-en diagnostische** gegevens: u kunt de informatie over de Orchestration-status bijhouden in een extern archief dat is geoptimaliseerd voor query's, zoals Azure SQL Database of Azure Cosmos db.

* **Langlopende achtergrond activiteit**: als u Durable functions gebruikt voor een langlopende achtergrond activiteit, kunt u met deze functie de huidige status weten.

## <a name="prerequisites"></a>Vereisten

* Installeer [micro soft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in uw Durable functions-project.
* Installeer [Azure Storage-emulator](../../storage/common/storage-use-emulator.md) (alleen Windows) of gebruik een bestaand Azure Storage-account.
* [Azure cli](/cli/azure/) installeren of [Azure Cloud shell](../../cloud-shell/overview.md) gebruiken

## <a name="create-a-custom-event-grid-topic"></a>Een aangepast Event Grid onderwerp maken

Maak een Event Grid onderwerp voor het verzenden van gebeurtenissen vanuit Durable Functions. De volgende instructies laten zien hoe u een onderwerp maakt met behulp van Azure CLI. U kunt het onderwerp ook maken met behulp van [Power shell](../../event-grid/custom-event-quickstart-powershell.md) of via [de Azure Portal](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht `az group create`. Azure Event Grid biedt momenteel geen ondersteuning voor alle regio's. Zie [Azure Event grid-overzicht](../../event-grid/overview.md)voor meer informatie over welke regio's worden ondersteund.

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Een aangepast onderwerp maken

Een Event Grid onderwerp bevat een door de gebruiker gedefinieerd eind punt waarop u uw gebeurtenis plaatst. Vervang `<topic_name>` door een unieke naam voor het onderwerp. De naam van het onderwerp moet uniek zijn omdat het een DNS-vermelding wordt.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Het eind punt en de sleutel ophalen

Het eind punt van het onderwerp ophalen. Vervang door `<topic_name>` de naam die u hebt gekozen.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Haal de sleutel van het onderwerp op. Vervang door `<topic_name>` de naam die u hebt gekozen.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

U kunt nu gebeurtenissen verzenden naar het onderwerp.

## <a name="configure-event-grid-publishing"></a>Event Grid publicatie configureren

Zoek het bestand in uw Durable Functions-project `host.json` .

### <a name="durable-functions-1x"></a>Durable Functions 1.x

`eventGridTopicEndpoint` `eventGridKeySettingName` Een `durableTask` eigenschap toevoegen.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2.x

Een `notifications` sectie toevoegen aan de `durableTask` eigenschap van het bestand, vervangen `<topic_name>` door de naam die u hebt gekozen. Als de `durableTask` of `extensions` -Eigenschappen niet bestaan, maakt u deze zoals in dit voor beeld:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

De mogelijke Azure Event Grid configuratie-eigenschappen vindt u in de [host.jsop de documentatie](../functions-host-json.md#durabletask). Nadat u het bestand hebt geconfigureerd `host.json` , verzendt uw functie-app levenscyclus gebeurtenissen naar het event grid onderwerp. Deze actie wordt gestart wanneer u de functie-app zowel lokaal als in azure uitvoert.

Stel de app-instelling in voor de sleutel van het onderwerp in de functie-app en `local.settings.json` . De volgende JSON is een voor beeld van de `local.settings.json` voor lokale fout opsporing. Vervang door `<topic_key>` de sleutel van het onderwerp.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Als u de [opslag emulator](../../storage/common/storage-use-emulator.md) gebruikt (alleen Windows), controleert u of deze werkt. Het is een goed idee om de opdracht uit te voeren voordat u het programma `AzureStorageEmulator.exe clear all` uitvoert.

Als u een bestaand Azure Storage-account gebruikt, vervangt `UseDevelopmentStorage=true` u `local.settings.json` het door de Connection String.

## <a name="create-functions-that-listen-for-events"></a>Functies maken die Luis teren naar gebeurtenissen

Maak met behulp van de Azure Portal een andere functie-app om te Luis teren naar gebeurtenissen die door uw Durable Functions-app worden gepubliceerd. U kunt deze het beste vinden in dezelfde regio als het onderwerp Event Grid.

### <a name="create-an-event-grid-trigger-function"></a>Een Event Grid-trigger functie maken

1. Selecteer in de functie-app **functies** en selecteer vervolgens **+ toevoegen** 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Een functie toevoegen in de Azure Portal." border="true":::

1. Zoek naar **Event grid** en selecteer vervolgens de **trigger sjabloon Azure Event grid** . 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Selecteer de trigger sjabloon voor gebeurtenis raster in het Azure Portal." border="true":::

1. Geef de nieuwe trigger een naam en selecteer vervolgens **functie maken**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Geef de trigger Event grid een naam in de Azure Portal." border="true":::


    Er wordt een functie gemaakt met de volgende code:

    # <a name="c-script"></a>[C# Script](#tab/csharp-script)

    ```csharp
    #r "Newtonsoft.Json"
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(JObject eventGridEvent, ILogger log)
    {
        log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
    }
    ```

   # <a name="javascript"></a>[JavaScript](#tab/javascript)

   ```javascript
   module.exports = async function(context, eventGridEvent) {
       context.log(typeof eventGridEvent);
       context.log(eventGridEvent);
   }
   ```

---

### <a name="add-an-event-grid-subscription"></a>Een Event Grid-abonnement toevoegen

U kunt nu een Event Grid-abonnement toevoegen voor het Event Grid onderwerp dat u hebt gemaakt. Zie [concepten in azure Event grid](../../event-grid/concepts.md)voor meer informatie.

1. Selecteer in de nieuwe functie **integratie** en selecteer vervolgens **Event grid trigger (eventGridEvent)**. 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Selecteer de koppeling Trigger Event Grid." border="true":::

1. Selecteer **Beschrijving van Event Grid maken**.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Maak het Event Grid-abonnement." border="true":::

1. Geef uw gebeurtenis abonnement een naam en selecteer het onderwerp type **Event grid topics** . 

1. Selecteer het abonnement. Selecteer vervolgens de resource groep en de resource die u hebt gemaakt voor het Event Grid onderwerp. 

1. Selecteer **Maken**.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Een Event Grid-abonnement maken." border="true":::

Nu bent u klaar om levenscyclus gebeurtenissen te ontvangen.

## <a name="run-durable-functions-app-to-send-the-events"></a>Durable Functions-app uitvoeren om de gebeurtenissen te verzenden

Start in het Durable Functions project dat u eerder hebt geconfigureerd, de fout opsporing op uw lokale computer en start een indeling. De app publiceert Durable Functions levenscyclus gebeurtenissen naar Event Grid. Controleer of Event Grid de listenerfunctie activeert die u hebt gemaakt door de logboeken in de Azure Portal te controleren.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Gebeurtenisschema

In de volgende lijst wordt het schema voor levenscyclus gebeurtenissen uitgelegd:

* **`id`**: De unieke id voor de Event Grid gebeurtenis.
* **`subject`**: Pad naar het gebeurtenis onderwerp. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` is `Running` ,, `Completed` en `Failed` `Terminated` .  
* **`data`**: Durable Functions specifieke para meters.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) -naam.
  * **`functionName`**: Functie naam van Orchestrator.
  * **`instanceId`**: Durable Functions instanceId.
  * **`reason`**: Aanvullende gegevens die zijn gekoppeld aan de tracerings gebeurtenis. Zie [Diagnostische gegevens in Durable functions (Azure functions)](durable-functions-diagnostics.md) voor meer informatie.
  * **`runtimeStatus`**: Status van Orchestration runtime. Uitvoeren, voltooid, mislukt, geannuleerd.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: De tijd van de gebeurtenis (UTC).
* **`dataVersion`**: Versie van het levenscyclus gebeurtenis schema.
* **`metadataVersion`**: De versie van de meta gegevens.
* **`topic`**: Resource grid-onderwerpnaam.

## <a name="how-to-test-locally"></a>Lokaal testen

Als u lokaal wilt testen, leest u [Azure Function Event grid de lokale fout opsporing te activeren](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Instantie beheer leren in Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Versie beheer leren in Durable Functions](durable-functions-versioning.md)
