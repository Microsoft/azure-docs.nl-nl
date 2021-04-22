---
title: Systeemonderwerpen maken, weergeven Azure Event Grid beheren met cli
description: In dit artikel wordt beschreven hoe u Azure CLI gebruikt voor het maken, weergeven en verwijderen van systeemonderwerpen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34a098406762fd57dc9dc4b58fc375286f5d5b13
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874293"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Systeemonderwerpen voor een Event Grid maken, weergeven en beheren met behulp van Azure CLI
In dit artikel wordt beschreven hoe u systeemonderwerpen maakt en beheert met behulp van Azure CLI. Zie Systeemonderwerpen voor een overzicht [van systeemonderwerpen.](system-topics.md)

## <a name="install-extension-for-azure-cli"></a>Extensie voor Azure CLI installeren
Voor Azure CLI hebt u de extensie [Event Grid nodig.](/cli/azure/azure-cli-extensions-list)

In Cloud Shell:

- Als u de extensie eerder hebt geïnstalleerd, moet u deze bijwerken: `az extension update -n eventgrid`
- Als u de extensie nog niet eerder hebt geïnstalleerd, installeert u deze:  `az extension add -n eventgrid`

Voor een lokale installatie:

1. [Installeer de Azure CLI](/cli/azure/install-azure-cli). Zorg ervoor dat u de nieuwste versie hebt door te controleren met `az --version` .
2. Verwijder eerdere versies van de extensie: `az extension remove -n eventgrid`
3. Installeer de eventgrid-extensie met `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Een systeemonderwerp maken

- Zie de volgende naslagonderwerpen om eerst een systeemonderwerp te maken in een Azure-bron en vervolgens een gebeurtenisabonnement voor dat onderwerp te maken:
    - [az eventgrid system-topic create](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        Voer de volgende opdracht uit voor een lijst met waarden die u kunt gebruiken om `topic-type` een systeemonderwerp te maken. Deze onderwerptypewaarden vertegenwoordigen de gebeurtenisbronnen die ondersteuning bieden voor het maken van systeemonderwerpen. Negeer en `Microsoft.EventGrid.Topics` `Microsoft.EventGrid.Domains` in de lijst. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-topic event-subscription create](/cli/azure/eventgrid/system-topic/event-subscription#az_eventgrid_system_topic_event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Als u een systeemonderwerp (impliciet) wilt maken bij het maken van een gebeurtenisabonnement voor een Azure-bron, gebruikt u [de methode az eventgrid event-subscription create.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) Hier volgt een voorbeeld:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Zie Abonneren op opslagaccount voor een zelfstudie met [stapsgewijs instructies.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)

## <a name="view-all-system-topics"></a>Alles weergeven systeemonderwerpen
Als u alle systeemonderwerpen en details van een geselecteerd systeemonderwerp wilt weergeven, gebruikt u de volgende opdrachten:

- [az eventgrid system-topic list](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-topic show](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Een systeemonderwerp verwijderen
Gebruik de volgende opdracht om een systeemonderwerp te verwijderen: 

- [az eventgrid system-topic delete](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Volgende stappen
Zie de [sectie Systeemonderwerpen in Azure Event Grid](system-topics.md) voor meer informatie over systeemonderwerpen en onderwerptypen die worden ondersteund door Azure Event Grid. 