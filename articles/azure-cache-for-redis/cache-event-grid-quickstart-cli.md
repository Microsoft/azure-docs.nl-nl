---
title: 'Snelstartgids: Azure-cache voor redis-gebeurtenissen naar een webeindpunt door sturen met Azure CLI'
description: Gebruik Azure Event Grid om u te abonneren op Azure cache voor redis-gebeurtenissen, een gebeurtenis te activeren en de resultaten weer te geven.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 7f33ca0043400962054fabb1aadb1da612fe5426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806423"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>Snelstartgids: Azure-cache voor redis-gebeurtenissen naar een webeindpunt door sturen met Azure CLI

Azure Event Grid is een gebeurtenisservice voor de cloud. In deze Quick Start gebruikt u de Azure CLI om u te abonneren op Azure cache voor redis-gebeurtenissen, een gebeurtenis te activeren en de resultaten weer te geven.

Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Als u deze Snelstartgids echter wilt vereenvoudigen, verzendt u gebeurtenissen naar een web-app waarmee de berichten worden verzameld en weer gegeven. Wanneer u de stappen in deze Snelstartgids hebt voltooid, ziet u dat de gebeurtenis gegevens naar de web-app zijn verzonden.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Snelstartgids gebruikmaken van de nieuwste versie van Azure CLI (2.0.70 of hoger). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Als u Cloud Shell niet gebruikt, moet u zich eerst aanmelden met `az login`.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Event Grid-onderwerpen worden geïmplementeerd als afzonderlijke Azure-resources en moeten worden ingericht onder een Azure-resource groep. Een resource groep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). 

In het volgende voorbeeld wordt een resourcegroep met de naam `<resource_group_name>` gemaakt op de locatie *westcentralus*.  Vervang `<resource_group_name>` door een unieke naam voor uw resourcegroep.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>Een cache-exemplaar maken

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op het onderwerp, gaan we het eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

Vervang `<your-site-name>` door een unieke naam voor de web-app. De naam van de web-app moet uniek zijn omdat deze deel uitmaakt van de DNS-vermelding.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

Op de site zouden momenteel geen berichten moeten wijn weergeven.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>Abonneren op uw Azure-cache voor redis-exemplaar

In deze stap maakt u een abonnement op een onderwerp om te zien Event Grid welke gebeurtenissen u wilt bijhouden en waar u deze gebeurtenissen wilt verzenden. In het volgende voor beeld wordt een abonnement genomen op de Azure-cache voor het redis-exemplaar dat u hebt gemaakt en wordt de URL van uw web-app door gegeven als het eind punt voor gebeurtenis meldingen. Vervang `<event_subscription_name>` door een naam voor het gebeurtenisabonnement. Gebruik voor `<resource_group_name>` en `<cache_name>` de waarden die u eerder hebt gemaakt.

Het eindpunt voor uw web-app moet het achtervoegsel `/api/updates/` bevatten.

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Een gebeurtenis activeren vanuit Azure cache voor redis

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. We gaan de gegevens exporteren die zijn opgeslagen in uw Azure-cache voor redis-exemplaar. Gebruik opnieuw de waarden voor `{cache_name}` en die `{resource_group_name}` u eerder hebt gemaakt.

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Bekijk uw web-app om de gebeurtenis te zien die u zojuist hebt verzonden.


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent om met deze Azure-cache te blijven werken voor redis-exemplaar en gebeurtenis abonnement, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende opdracht om de resources te verwijderen die u in deze Quick Start hebt gemaakt.

Vervang `<resource_group_name>` door de resourcegroep die u eerder hebt gemaakt.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u onderwerpen en gebeurtenis abonnementen maakt, leest u meer over Azure cache voor redis-gebeurtenissen en wat Event Grid u kunt doen:

- [Reageren op Azure cache voor redis-gebeurtenissen](cache-event-grid.md)
- [Over Event Grid](../event-grid/overview.md)
