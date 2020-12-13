---
title: Gebeurtenis levering, beheerde service-identiteit en persoonlijke koppeling
description: In dit artikel wordt beschreven hoe u de beheerde service-identiteit voor een Azure Event grid-onderwerp inschakelt. Gebruik dit om gebeurtenissen door te sturen naar ondersteunde bestemmingen.
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: edb3e5ac8257a29ecd3835e1dfd4c116c3cc7164
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368610"
---
# <a name="event-delivery-with-a-managed-identity"></a>Gebeurtenis levering met een beheerde identiteit
In dit artikel wordt beschreven hoe u een [beheerde service-identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor Azure Event grid-onderwerpen of-domeinen inschakelt. Gebruik dit om gebeurtenissen door te sturen naar ondersteunde bestemmingen, zoals Service Bus-wacht rijen en-onderwerpen, Event hubs en opslag accounts.

Hier volgen de stappen die in dit artikel worden besproken:
1. Maak een onderwerp of domein met een door het systeem toegewezen identiteit of werk een bestaand onderwerp of domein bij om identiteit in te scha kelen. 
1. Voeg de identiteit toe aan een geschikte rol (bijvoorbeeld Service Bus verzender van gegevens) op de bestemming (bijvoorbeeld een Service Bus wachtrij).
1. Wanneer u gebeurtenis abonnementen maakt, moet u het gebruik van de identiteit inschakelen voor het leveren van gebeurtenissen aan de bestemming. 

> [!NOTE]
> Het is momenteel niet mogelijk om gebeurtenissen te leveren met behulp van [persoonlijke eind punten](../private-link/private-endpoint-overview.md). Zie de sectie [Private endpoints](#private-endpoints) aan het einde van dit artikel voor meer informatie. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Een onderwerp of domein maken met een identiteit
Laten we eerst eens kijken hoe u een onderwerp of een domein met een door een systeem beheerde identiteit maakt.

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken
U kunt de door het systeem toegewezen identiteit inschakelen voor een onderwerp of domein terwijl u deze maakt in de Azure Portal. De volgende afbeelding laat zien hoe u een door een systeem beheerde identiteit voor een onderwerp inschakelt. In principe selecteert u de optie **systeem toegewezen identiteit inschakelen** op de pagina **Geavanceerd** van de wizard voor het maken van het onderwerp. U ziet deze optie ook op de pagina **Geavanceerd** van de wizard voor het maken van het domein. 

![Identiteit inschakelen tijdens het maken van een onderwerp](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Azure CLI gebruiken
U kunt ook de Azure CLI gebruiken om een onderwerp of domein te maken met een door het systeem toegewezen identiteit. Gebruik de `az eventgrid topic create` opdracht met de `--identity` para meter ingesteld op `systemassigned` . Als u geen waarde voor deze para meter opgeeft, wordt de standaard waarde `noidentity` gebruikt. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Op dezelfde manier kunt u de `az eventgrid domain create` opdracht gebruiken om een domein te maken met een door het systeem beheerde identiteit.

## <a name="enable-an-identity-for-an-existing-topic-or-domain"></a>Een identiteit voor een bestaand onderwerp of domein inschakelen
In de vorige sectie hebt u geleerd hoe u een door het systeem beheerde identiteit inschakelt tijdens het maken van een onderwerp of een domein. In deze sectie leert u hoe u een door het systeem beheerde identiteit voor een bestaand onderwerp of domein inschakelt. 

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken
De volgende procedure laat zien hoe u een door het systeem beheerde identiteit voor een onderwerp inschakelt. De stappen voor het inschakelen van een identiteit voor een domein zijn vergelijkbaar. 

1. Ga naar [Azure Portal](https://portal.azure.com).
2. Zoek naar **Event grid-onderwerpen** in de zoek balk aan de bovenkant.
3. Selecteer het **onderwerp** waarvoor u de beheerde identiteit wilt inschakelen. 
4. Ga naar het tabblad **identiteit** . 
5. Schakel de switch **in** om de identiteit in te scha kelen. 
1. Selecteer **Opslaan** op de werk balk om de instelling op te slaan. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Identiteits pagina voor een onderwerp"::: 

U kunt soort gelijke stappen gebruiken om een identiteit in te scha kelen voor een event grid-domein.

### <a name="use-the-azure-cli"></a>Azure CLI gebruiken
Gebruik de `az eventgrid topic update` opdracht met `--identity` ingesteld op `systemassigned` om de door het systeem toegewezen identiteit in te scha kelen voor een bestaand onderwerp. Als u de identiteit wilt uitschakelen, geeft u `noidentity` de waarde op. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

De opdracht voor het bijwerken van een bestaand domein is vergelijkbaar ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-azure-roles"></a>Ondersteunde doelen en Azure-rollen
Nadat u de identiteit voor het onderwerp of het domein van het gebeurtenis raster hebt ingeschakeld, maakt Azure automatisch een identiteit in Azure Active Directory. Voeg deze identiteit toe aan de juiste Azure-rollen zodat het onderwerp of het domein gebeurtenissen naar ondersteunde bestemmingen kan door sturen. U kunt bijvoorbeeld de identiteit toevoegen aan de rol **azure Event hubs data Sender** voor een Azure Event hubs-naam ruimte, zodat het onderwerp Event grid gebeurtenissen kan door sturen naar Event hubs in die naam ruimte. 

Op dit moment ondersteunt Azure Event grid onderwerpen of domeinen die zijn geconfigureerd met een door het systeem toegewezen beheerde identiteit voor het door sturen van gebeurtenissen naar de volgende bestemmingen. In deze tabel vindt u ook de rollen waarin de identiteit zich moet bevinden, zodat het onderwerp de gebeurtenissen kan door sturen.

| Doel | Azure-rol | 
| ----------- | --------- | 
| Service Bus-wacht rijen en-onderwerpen | [Afzender van Azure Service Bus gegevens](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Afzender van Azure Event Hubs gegevens](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Inzender voor Storage Blob-gegevens](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Afzender gegevens bericht van opslag wachtrij](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>Een identiteit toevoegen aan Azure-rollen op bestemmingen
In deze sectie wordt beschreven hoe u de identiteit voor uw onderwerp of domein toevoegt aan een Azure-rol. 

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken
U kunt de Azure Portal gebruiken om het onderwerp of de domein-id toe te wijzen aan een geschikte rol, zodat het onderwerp of het domein gebeurtenissen naar de bestemming kan door sturen. 

In het volgende voor beeld wordt een beheerde identiteit voor een event grid-onderwerp met de naam **msitesttopic** toegevoegd aan de rol voor het **verzenden van Azure Service Bus gegevens** voor een service bus naam ruimte die een wachtrij of onderwerp-resource bevat. Wanneer u aan de rol op het niveau van de naam ruimte toevoegt, kan het onderwerp gebeurtenissen door sturen naar alle entiteiten in de naam ruimte. 

1. Ga naar de **naam ruimte** van uw service bus in de [Azure Portal](https://portal.azure.com). 
1. Selecteer **Access Control** in het linkerdeel venster. 
1. Selecteer **toevoegen** in de sectie **toewijzing van een rol toevoegen** . 
1. Voer op de pagina **een roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de rol. In dit geval is het **Azure Service Bus gegevens verzender**. 
    1. Selecteer de **identiteit** voor uw onderwerp of domein. 
    1. Selecteer **Opslaan** om de configuratie op te slaan.

De stappen zijn vergelijkbaar voor het toevoegen van een identiteit aan andere rollen die in de tabel worden genoemd. 

### <a name="use-the-azure-cli"></a>Azure CLI gebruiken
In het voor beeld in deze sectie wordt beschreven hoe u de Azure CLI gebruikt om een identiteit toe te voegen aan een Azure-rol. De voorbeeld opdrachten zijn voor Event grid-onderwerpen. De opdrachten voor Event grid-domeinen zijn vergelijkbaar. 

#### <a name="get-the-principal-id-for-the-topics-system-identity"></a>De principal-ID voor de systeem identiteit van het onderwerp ophalen 
Haal eerst de principal-ID op van de door het systeem beheerde identiteit van het onderwerp en wijs de identiteit toe aan de juiste rollen.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Een roltoewijzing maken voor Event hubs in verschillende bereiken 
In het volgende CLI-voor beeld ziet u hoe u de identiteit van een onderwerp kunt toevoegen aan de rol van **Azure Event hubs data Sender** op het niveau van de naam ruimte of op Event hub niveau. Als u de roltoewijzing op het niveau van de naam ruimte maakt, kan het onderwerp gebeurtenissen door sturen naar alle Event hubs in die naam ruimte. Als u een roltoewijzing op Event Hub niveau maakt, kan het onderwerp alleen gebeurtenissen door sturen naar die specifieke Event Hub. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Een roltoewijzing maken voor een Service Bus onderwerp in verschillende bereiken 
In het volgende CLI-voor beeld ziet u hoe u de identiteit van een onderwerp kunt toevoegen aan de rol van de **Azure Service Bus gegevens afzender** op het niveau van de naam ruimte of op het niveau van service bus onderwerp. Als u de roltoewijzing op het niveau van de naam ruimte maakt, kan het event grid-onderwerp gebeurtenissen door sturen naar alle entiteiten (Service Bus wacht rijen of onderwerpen) binnen die naam ruimte. Als u een roltoewijzing maakt op het niveau van de Service Bus wachtrij of het onderwerp, kan het event grid-onderwerp alleen gebeurtenissen door sturen naar die specifieke Service Bus wachtrij of het onderwerp. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>Gebeurtenis abonnementen maken die gebruikmaken van een identiteit
Nadat u een onderwerp of een domein hebt met een door het systeem beheerde identiteit en de identiteit hebt toegevoegd aan de juiste rol op de bestemming, kunt u abonnementen maken die gebruikmaken van de identiteit. 

### <a name="use-the-azure-portal"></a>Azure Portal gebruiken
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
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een event grid-onderwerp waarvoor een eindpunt type is ingesteld op **Service Bus wachtrij**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering en onbestelbare berichten
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een event grid-onderwerp waarvoor een eindpunt type is ingesteld op **Service Bus wachtrij**. Er wordt ook aangegeven dat de door het systeem beheerde identiteit moet worden gebruikt voor onbestelbare berichten. 

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
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een event grid-onderwerp waarvoor een eindpunt type is ingesteld op **Event hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Een gebeurtenis abonnement maken met behulp van een beheerde identiteit voor levering en deadletter 
Met deze voorbeeld opdracht maakt u een gebeurtenis abonnement voor een event grid-onderwerp waarvoor een eindpunt type is ingesteld op **Event hubs**. Er wordt ook aangegeven dat de door het systeem beheerde identiteit moet worden gebruikt voor onbestelbare berichten. 

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
In deze sectie leert u hoe u de Azure CLI gebruikt om het gebruik van een door het systeem toegewezen identiteit in te scha kelen voor het leveren van gebeurtenissen in een Azure Storage wachtrij. De identiteit moet lid zijn van de rol **Storage BLOB data Inzender** op het opslag account.

#### <a name="define-variables"></a>Variabelen definiëren  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

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

Als uw vereisten echter een veilige manier hebben om gebeurtenissen te verzenden met behulp van een versleuteld kanaal en een bekende identiteit van de afzender (in dit geval Event Grid) met behulp van open bare IP-ruimte, kunt u gebeurtenissen leveren aan Event Hubs-, Service Bus-of Azure Storage-service met behulp van een Azure Event grid-onderwerp of een domein met door het systeem beheerde identiteit die is geconfigureerd Vervolgens kunt u een persoonlijke koppeling gebruiken die is geconfigureerd in Azure Functions of uw webhook die is geïmplementeerd in uw virtuele netwerk om gebeurtenissen op te halen. Zie het voor beeld: [verbinding maken met privé-eind punten met Azure functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Onder deze configuratie gaat het verkeer via het open bare IP/Internet van Event Grid naar Event Hubs, Service Bus of Azure Storage, maar kan het kanaal worden versleuteld en wordt een beheerde identiteit van Event Grid gebruikt. Als u uw Azure Functions of webhook die is geïmplementeerd in uw virtuele netwerk configureert om gebruik te maken van een Event Hubs, Service Bus of Azure Storage via een persoonlijke koppeling, blijft die sectie van het verkeer duidelijk binnen Azure.


## <a name="next-steps"></a>Volgende stappen
Zie [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde service-identiteiten. 
