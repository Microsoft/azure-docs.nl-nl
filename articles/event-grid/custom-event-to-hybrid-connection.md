---
title: 'Zelfstudie: Aangepaste gebeurtenissen verzenden naar hybride verbinding - Event Grid'
description: 'Zelfstudie: Gebruik Azure Event Grid en Azure CLI om een onderwerp te publiceren en u te abonneren op deze gebeurtenis. Een hybride verbinding wordt gebruikt voor het eindpunt.'
ms.date: 07/07/2020
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: c709d77827551860cc917c3c84c5a849d9fdc512
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566823"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Zelfstudie: Aangepaste gebeurtenissen naar Azure Relay Hybrid Connections routeren met behulp van Azure CLI en Event Grid

Azure Event Grid is een gebeurtenisservice voor de cloud. Azure Relay Hybrid Connections is een van de ondersteunde gebeurtenis-handlers. U gebruikt hybride verbindingen als gebeurtenis-handler wanneer u gebeurtenissen uit toepassingen moet verwerken die geen openbaar eindpunt hebben. Deze toepassingen bevinden zich mogelijk in uw bedrijfsnetwerk. In dit artikel gebruikt u de Azure CLI om een aangepast onderwerp te maken, u op het aangepaste onderwerp te abonneren en de gebeurtenis te activeren om het resultaat weer te geven. U verstuurt de gebeurtenissen naar de hybride verbinding.

## <a name="prerequisites"></a>Vereisten

- In dit artikel wordt ervan uitgegaan dat u al een hybride verbinding en een listener-toepassing hebt. Zie [Aan de slag met Relay Hybrid Connections - .NET](../azure-relay/relay-hybrid-connections-dotnet-get-started.md) of [Aan de slag met Relay Hybrid Connections - knooppunt](../azure-relay/relay-hybrid-connections-node-get-started.md) als u wilt beginnen met hybride verbindingen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0.56 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Event Grid-onderwerpen zijn Azure-resources en moeten in een Azure-resourcegroep worden geplaatst. De resourcegroep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). 

In het volgende voorbeeld wordt een resourcegroep met de naam *gridResourceGroup* gemaakt op de locatie *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Een aangepast onderwerp maken

Een Event Grid-onderwerp biedt een door de gebruiker gedefinieerd eindpunt waarop u de gebeurtenissen kunt posten. In het volgende voorbeeld wordt het aangepaste onderwerp in uw resourcegroep gemaakt. Vervang `<topic_name>` door een unieke naam voor uw aangepaste onderwerp. De naam van het Event Grid-onderwerp moet uniek zijn omdat deze wordt vertegenwoordigd door een DNS-vermelding.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Abonneren op een aangepast onderwerp

U abonneert u op een Event Grid-onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren. In het volgende voorbeeld ziet u hoe u zich abonneert op het aangepaste onderwerp dat u hebt gemaakt, en hoe de resource-id van de hybride verbinding wordt doorgegeven voor het eindpunt. De id van hybride verbinding heeft deze indeling:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Met het volgende script wordt de resource-id van de relay-naamruimte opgehaald. Het script maakt de id voor de hybride verbinding en neemt vervolgens een abonnement op een onderwerp van Event Grid. Het script stelt het type eindpunt in op `hybridconnection` en de id van de hybride verbinding wordt gebruikt voor het eindpunt.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

U ziet dat er een [vervaldatum](concepts.md#event-subscription-expiration) is ingesteld voor het abonnement.

## <a name="create-application-to-process-events"></a>Toepassing maken om gebeurtenissen te verwerken

U hebt een toepassing nodig die gebeurtenissen uit de hybride verbinding kan ophalen. Het [voorbeeld van een hybride verbindingsconsumer voor C# van Microsoft Azure Event Grid](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination) voert die bewerking uit. U hebt de vereiste stappen al uitgevoerd.

1. Zorg ervoor dat u Visual Studio 2019 of hoger hebt.

1. Kloon de opslagplaats naar uw lokale computer.

1. Laad het project HybridConnectionConsumer in Visual Studio.

1. Vervang in Program.cs `<relayConnectionString>` en `<hybridConnectionName>` door de relaytekenreeks en de naam van de hybride verbinding die u hebt gemaakt.

1. Compileer de toepassing en voer deze uit vanuit Visual Studio.

## <a name="send-an-event-to-your-topic"></a>Een gebeurtenis verzenden naar het onderwerp

We activeren een gebeurtenis om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. In dit artikel wordt beschreven hoe u Azure CLI kunt gebruiken om de gebeurtenis te activeren. U kunt ook [de uitgeverstoepassing van Event Grid](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher) gebruiken.

Eerst gaan we de URL en de sleutel voor het aangepaste onderwerp ophalen. Gebruik opnieuw de naam van het aangepaste onderwerp voor `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Om dit artikel zo eenvoudig mogelijk te maken, gebruikt u voorbeeldgebeurtenisgegevens om naar het aangepaste onderwerp te verzenden. Meestal worden de gebeurtenisgegevens verzonden via een toepassing of Azure-service. CURL is een hulpprogramma waarmee HTTP-aanvragen worden verzonden. In dit artikel gebruiken we CURL om de gebeurtenis naar het aangepaste onderwerp te verzenden.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Uw listener-toepassing moet het gebeurtenisbericht ontvangen.

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