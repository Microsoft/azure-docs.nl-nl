---
title: Azure Event Grid systeem onderwerpen maken, weer geven en beheren met CLI
description: In dit artikel wordt beschreven hoe u Azure CLI gebruikt om systeem onderwerpen te maken, weer te geven en te verwijderen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c847c7f25e3a656b798e186a408e560b9ee9e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633219"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Event Grid systeem onderwerpen maken, weer geven en beheren met behulp van Azure CLI
In dit artikel wordt beschreven hoe u systeem onderwerpen maakt en beheert met behulp van Azure CLI. Zie [systeem onderwerpen](system-topics.md)voor een overzicht van systeem onderwerpen.

## <a name="install-extension-for-azure-cli"></a>Extensie voor Azure CLI installeren
Voor Azure CLI hebt u de [extensie Event grid](/cli/azure/azure-cli-extensions-list)nodig.

In Cloud Shell:

- Als u de extensie eerder hebt geïnstalleerd, werkt u deze bij: `az extension update -n eventgrid`
- Als u de extensie nog niet eerder hebt geïnstalleerd, installeert u deze:  `az extension add -n eventgrid`

Voor een lokale installatie:

1. [Installeer de Azure CLI](/cli/azure/install-azure-cli). Zorg ervoor dat u de nieuwste versie hebt door te controleren met `az --version` .
2. Eerdere versies van de uitbrei ding verwijderen: `az extension remove -n eventgrid`
3. Installeer de eventgrid-extensie met `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Een systeem onderwerp maken

- Als u eerst een systeem onderwerp wilt maken in een Azure-bron en vervolgens een gebeurtenis abonnement voor dat onderwerp wilt maken, raadpleegt u de volgende onderwerpen:
    - [AZ eventgrid System-topic Create](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-create)

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

        `topic-type`Voer de volgende opdracht uit voor een lijst met waarden die u kunt gebruiken om een systeem onderwerp te maken. De waarden van het type topic vertegenwoordigen de gebeurtenis bronnen die ondersteuning bieden voor het maken van systeem onderwerpen. Negeer `Microsoft.EventGrid.Topics` en `Microsoft.EventGrid.Domains` in de lijst. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [AZ eventgrid System-onderwerp Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Als u een systeem onderwerp (impliciet) wilt maken bij het maken van een gebeurtenis abonnement voor een Azure-bron, gebruikt u de methode [AZ eventgrid Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) . Hier volgt een voorbeeld:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Zie [Abonneren op een opslag account](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)voor een zelf studie met stapsgewijze instructies.

## <a name="view-all-system-topics"></a>Alle systeem onderwerpen weer geven
Als u alle systeem onderwerpen en Details van een geselecteerd systeem onderwerp wilt weer geven, gebruikt u de volgende opdrachten:

- [AZ eventgrid System-topic List](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [AZ eventgrid System-topic show](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Een systeem onderwerp verwijderen
Als u een systeem onderwerp wilt verwijderen, gebruikt u de volgende opdracht: 

- [AZ eventgrid System-onderwerp Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Volgende stappen
Zie de sectie [systeem onderwerpen in azure Event grid](system-topics.md) voor meer informatie over systeem onderwerpen en onderwerp typen die door Azure Event grid worden ondersteund. 