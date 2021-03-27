---
title: Gebeurtenis levering, beheerde service-identiteit en persoonlijke koppeling
description: In dit artikel wordt beschreven hoe u de beheerde service-identiteit voor een Azure Event grid-onderwerp inschakelt. Gebruik dit om gebeurtenissen door te sturen naar ondersteunde bestemmingen.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625269"
---
# <a name="event-delivery-with-a-managed-identity"></a>Gebeurtenis levering met een beheerde identiteit
In dit artikel wordt beschreven hoe u een [beheerde service-identiteit](../active-directory/managed-identities-azure-resources/overview.md) gebruikt voor een Azure Event grid-systeem onderwerp, een aangepast onderwerp of een domein. Gebruik dit om gebeurtenissen door te sturen naar ondersteunde bestemmingen, zoals Service Bus-wacht rijen en-onderwerpen, Event hubs en opslag accounts.



## <a name="prerequisites"></a>Vereisten
1. Wijs een door het systeem toegewezen identiteit toe aan een systeem onderwerp, een aangepast onderwerp of een domein. 
    - Zie [Managed Identity inschakelen voor aangepaste onderwerpen](enable-identity-custom-topics-domains.md)en domeinen voor aangepaste onderwerpen en domeinen. 
    - Zie [Managed Identity inschakelen voor systeem onderwerpen](enable-identity-system-topics.md) voor systeem onderwerpen
1. Voeg de identiteit toe aan een geschikte rol (bijvoorbeeld Service Bus verzender van gegevens) op de bestemming (bijvoorbeeld een Service Bus wachtrij). Zie [identiteit toevoegen aan Azure-rollen op bestemmingen](add-identity-roles.md) voor gedetailleerde stappen.

    > [!NOTE]
    > Het is momenteel niet mogelijk om gebeurtenissen te leveren met behulp van [persoonlijke eind punten](../private-link/private-endpoint-overview.md). Zie de sectie [Private endpoints](#private-endpoints) aan het einde van dit artikel voor meer informatie. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Gebeurtenis abonnementen maken die gebruikmaken van een identiteit
Nadat u een aangepast Event grid-onderwerp of systeem onderwerp of-domein hebt met een door het systeem beheerde identiteit en de identiteit hebt toegevoegd aan de juiste rol op de bestemming, kunt u abonnementen maken die gebruikmaken van de identiteit. 

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken
Wanneer u een gebeurtenis abonnement maakt, ziet u een optie om het gebruik van een door het systeem toegewezen identiteit voor een eind punt in de sectie **EINDPUNT Details** in te scha kelen. 

![Identiteit inschakelen tijdens het maken van een gebeurtenis abonnement voor een Service Bus wachtrij](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

U kunt ook inschakelen met behulp van een door het systeem toegewezen identiteit voor onbestelbare berichten op het tabblad **extra functies** . 

![Door het systeem toegewezen identiteit inschakelen voor onbestelbare berichten](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>De Azure CLI-Service Bus-wachtrij gebruiken 
In deze sectie leert u hoe u de Azure CLI gebruikt om het gebruik van een door het systeem toegewezen identiteit in te scha kelen voor het leveren van gebeurtenissen in een Service Bus wachtrij. De identiteit moet lid zijn van de rol **Azure Service Bus gegevens afzender** . Het moet ook lid zijn van de rol **Storage BLOB data Inzender** op het opslag account dat wordt gebruikt voor het onbestelen van berichten. 

#### <a name="define-variables"></a>Variabelen definiëren
Geef eerst waarden op voor de volgende variabelen die moeten worden gebruikt in de CLI-opdracht. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een aangepast Event grid-onderwerp waarvoor een eindpunt type is ingesteld op **Service Bus wachtrij**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering en onbestelbare berichten
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een aangepast Event grid-onderwerp waarvoor een eindpunt type is ingesteld op **Service Bus wachtrij**. Er wordt ook aangegeven dat de door het systeem beheerde identiteit moet worden gebruikt voor onbestelbare berichten. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Azure CLI-Event Hubs gebruiken 
In deze sectie leert u hoe u de Azure CLI gebruikt om het gebruik van een door het systeem toegewezen identiteit in te scha kelen voor het leveren van gebeurtenissen aan een Event Hub. De identiteit moet lid zijn van de rol **Azure Event hubs data Sender** . Het moet ook lid zijn van de rol **Storage BLOB data Inzender** op het opslag account dat wordt gebruikt voor het onbestelen van berichten. 

#### <a name="define-variables"></a>Variabelen definiëren
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een aangepast Event grid-onderwerp waarvoor een eindpunt type is ingesteld op **Event hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering en deadletter 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een aangepast Event grid-onderwerp waarvoor een eindpunt type is ingesteld op **Event hubs**. Er wordt ook aangegeven dat de door het systeem beheerde identiteit moet worden gebruikt voor onbestelbare berichten. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>De Azure CLI-Azure Storage-wachtrij gebruiken 
In deze sectie leert u hoe u de Azure CLI gebruikt om het gebruik van een door het systeem toegewezen identiteit in te scha kelen voor het leveren van gebeurtenissen in een Azure Storage wachtrij. De identiteit moet lid zijn van de rol **afzender gegevens bericht van de opslag wachtrij** op het opslag account. Het moet ook lid zijn van de rol **Storage BLOB data Inzender** op het opslag account dat wordt gebruikt voor het onbestelen van berichten.

#### <a name="define-variables"></a>Variabelen definiëren  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering en deadletter 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Privé-eindpunten
Het is momenteel niet mogelijk om gebeurtenissen te leveren met behulp van [persoonlijke eind punten](../private-link/private-endpoint-overview.md). Dat wil zeggen dat er geen ondersteuning is als u strikte vereisten voor netwerk isolatie hebt waarbij het verkeer van de geleverde gebeurtenissen de privé-IP-ruimte niet mag verlaten. 

Als uw vereisten echter een veilige manier hebben om gebeurtenissen te verzenden met behulp van een versleuteld kanaal en een bekende identiteit van de afzender (in dit geval Event Grid) met behulp van open bare IP-ruimte, kunt u gebeurtenissen leveren aan Event Hubs-, Service Bus-of Azure Storage-service met behulp van een aangepast Azure Event grid-onderwerp of een domein met door het systeem beheerde identiteit die is Vervolgens kunt u een persoonlijke koppeling gebruiken die is geconfigureerd in Azure Functions of uw webhook die is geïmplementeerd in uw virtuele netwerk om gebeurtenissen op te halen. Zie het voor beeld: [verbinding maken met privé-eind punten met Azure functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Onder deze configuratie gaat het verkeer via het open bare IP/Internet van Event Grid naar Event Hubs, Service Bus of Azure Storage, maar kan het kanaal worden versleuteld en wordt een beheerde identiteit van Event Grid gebruikt. Als u uw Azure Functions of webhook die is geïmplementeerd in uw virtuele netwerk configureert om gebruik te maken van een Event Hubs, Service Bus of Azure Storage via een persoonlijke koppeling, blijft die sectie van het verkeer duidelijk binnen Azure.


## <a name="next-steps"></a>Volgende stappen
Zie [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.
