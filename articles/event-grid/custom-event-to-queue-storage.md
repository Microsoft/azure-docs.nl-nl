---
title: 'Quickstart: Aangepaste gebeurtenissen verzenden naar opslagwachtrij - Event Grid, Azure CLI'
description: 'Quickstart: Gebruik Azure Event Grid en Azure CLI om een onderwerp te publiceren en u te abonneren op deze gebeurtenis. Er wordt een opslagwachtrij gebruikt voor het eindpunt.'
ms.date: 02/02/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00808e7eca13824833673ef820d39b70bf618dd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493257"
---
# <a name="quickstart-route-custom-events-to-azure-queue-storage-with-azure-cli-and-event-grid"></a>Quickstart: Aangepaste gebeurtenissen naar Azure Queue Storage routeren met behulp van Azure CLI en Event Grid

Azure Event Grid is een gebeurtenisservice voor de cloud. Azure Queue Storage is een van de ondersteunde gebeurtenis-handlers. In dit artikel gebruikt u de Azure CLI om een aangepast onderwerp te maken, u op het aangepaste onderwerp te abonneren en de gebeurtenis te activeren om het resultaat weer te geven. U verstuurt de gebeurtenissen naar de opslagwachtrij.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.56 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

- Als u Azure PowerShell op de lokale computer gebruikt in plaats van Cloud Shell in Azure Portal, zorgt u ervoor dat u beschikt over Azure PowerShell-versie 1.1.0 of hoger. Download de nieuwste versie van Azure PowerShell op uw Windows-computer vanuit [Azure-downloads - opdrachtregelprogramma's](https://azure.microsoft.com/downloads/). 

Dit artikel bevat opdrachten voor het gebruik van Azure CLI. 

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Event Grid-onderwerpen zijn Azure-resources en moeten in een Azure-resourcegroep worden geplaatst. De resourcegroep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). 

In het volgende voorbeeld wordt een resourcegroep met de naam *gridResourceGroup* gemaakt op de locatie *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Een aangepast onderwerp maken

Een Event Grid-onderwerp biedt een door de gebruiker gedefinieerd eindpunt waarop u de gebeurtenissen kunt posten. In het volgende voorbeeld wordt het aangepaste onderwerp in uw resourcegroep gemaakt. Vervang `<topic_name>` door een unieke naam voor uw aangepaste onderwerp. De naam van het Event Grid-onderwerp moet uniek zijn omdat deze wordt vertegenwoordigd door een DNS-vermelding.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-queue-storage"></a>Opslagwachtrij maken

Voordat u zich abonneert op het aangepaste onderwerp, gaan we het eindpunt voor het gebeurtenisbericht maken. U maakt een opslagwachtrij voor het verzamelen van de gebeurtenissen.

```azurecli-interactive
storagename="<unique-storage-name>"
queuename="eventqueue"

az storage account create -n $storagename -g gridResourceGroup -l westus2 --sku Standard_LRS
az storage queue create --name $queuename --account-name $storagename
```

## <a name="subscribe-to-a-custom-topic"></a>Abonneren op een aangepast onderwerp

U abonneert u op een aangepast onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren. In het volgende voorbeeld ziet u hoe u zich abonneert op het aangepaste onderwerp dat u hebt gemaakt, en hoe de resource-id van de opslagwachtrij wordt doorgegeven voor het eindpunt. Met Azure CLI geeft u de opslag-id van Queue door als het eindpunt. Het eindpunt heeft deze indeling:

`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/queueservices/default/queues/<queue-name>`

Met het volgende script wordt de resource-id van het opslagaccount voor de wachtrij opgehaald. Het script maakt de id voor de opslagwachtrij en neemt vervolgens een abonnement op een onderwerp van Event Grid. Het type eindpunt wordt ingesteld op `storagequeue` en de wachtrij-id wordt gebruikt voor het eindpunt.

```azurecli-interactive
storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)
queueid="$storageid/queueservices/default/queues/$queuename"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type storagequeue \
  --endpoint $queueid \
  --expiration-date "<yyyy-mm-dd>"
```

Het account dat wordt gemaakt van het gebeurtenisabonnement, moet schrijftoegang tot de opslagwachtrij hebben. U ziet dat er een [vervaldatum](concepts.md#event-subscription-expiration) is ingesteld voor het abonnement.

Als u de REST-API gebruikt om het abonnement te maken, geeft u de id van het opslagaccount en de naam van de wachtrij als een afzonderlijke parameter door.

```json
"destination": {
  "endpointType": "storagequeue",
  "properties": {
    "queueName":"eventqueue",
    "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>"
  }
  ...
```

## <a name="send-an-event-to-your-custom-topic"></a>Een gebeurtenis verzenden naar het aangepaste onderwerp

We activeren een gebeurtenis om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. Eerst gaan we de URL en de sleutel voor het aangepaste onderwerp ophalen. Gebruik opnieuw de naam van het aangepaste onderwerp voor `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Om dit artikel zo eenvoudig mogelijk te maken, gebruikt u voorbeeldgebeurtenisgegevens om naar het aangepaste onderwerp te verzenden. Meestal worden de gebeurtenisgegevens verzonden via een toepassing of Azure-service. CURL is een hulpprogramma waarmee HTTP-aanvragen worden verzonden. In dit artikel gebruiken we CURL om de gebeurtenis naar het aangepaste onderwerp te verzenden.  In het volgende voorbeeld worden drie gebeurtenissen verstuurd naar het onderwerp van Event Grid:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

Navigeer naar de opslagwachtrij in de portal en u ziet dat Event Grid deze drie gebeurtenissen naar de wachtrij heeft verzonden.

![Berichten weergeven](./media/custom-event-to-queue-storage/messages.png)

> [!NOTE]
> Als u een [Azure-wachtrij opslag trigger gebruikt voor Azure functions](../azure-functions/functions-bindings-storage-queue-trigger.md) voor een wachtrij die berichten van Event grid ontvangt, wordt het volgende fout bericht weer gegeven bij de uitvoering van de functie: `The input is not a valid Base-64 string as it contains a non-base 64 character, more than two padding characters, or an illegal character among the padding characters.`
> 
> De reden hiervoor is dat wanneer u een [Azure Queue-opslag trigger](../azure-functions/functions-bindings-storage-queue-trigger.md)gebruikt, Azure functions een **Base64-gecodeerde teken reeks** verwacht, maar Event grid berichten naar een opslag wachtrij verzendt in een tekst zonder opmaak. Het is momenteel niet mogelijk om de wachtrij trigger voor Azure Functions te configureren voor het accepteren van tekst zonder opmaak. 


## <a name="clean-up-resources"></a>Resources opschonen
Als u verder wilt werken met deze gebeurtenis, schoon dan de resources die u in dit artikel hebt gemaakt, niet op. Gebruik anders de volgende opdracht om de resources te verwijderen die u in dit artikel hebt gemaakt.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u onderwerpen maakt en hoe u zich abonneert op een gebeurtenis. Kijk waar Event Grid u nog meer bij kan helpen:

- [Over Event Grid](overview.md)
- [Blob Storage-gebeurtenissen naar een aangepast eindpunt op het web routeren](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Wijzigingen in virtuele machines bewaken met Azure Event Grid en Logic Apps)
- [Big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md)
