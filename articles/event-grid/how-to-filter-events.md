---
title: Gebeurtenissen filteren voor Azure Event Grid
description: In dit artikel wordt beschreven hoe u gebeurtenissen (op gebeurtenis type, op onderwerp, op basis van Opera tors en gegevens, enzovoort) filtert bij het maken van een Event Grid-abonnement.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 99fb00f99a055033ccfcd99e32a52d423878fb44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86105567"
---
# <a name="filter-events-for-event-grid"></a>Gebeurtenissen filteren voor Event Grid

In dit artikel wordt beschreven hoe u gebeurtenissen filtert bij het maken van een Event Grid-abonnement. Zie [gebeurtenis filters begrijpen voor Event grid-abonnementen](event-filtering.md)voor meer informatie over de opties voor het filteren van gebeurtenissen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filteren op gebeurtenis type

Wanneer u een Event Grid-abonnement maakt, kunt u opgeven welke [gebeurtenis typen](event-schema.md) moeten worden verzonden naar het eind punt. De voor beelden in deze sectie maken gebeurtenis abonnementen voor een resource groep, maar beperken de gebeurtenissen die worden verzonden naar `Microsoft.Resources.ResourceWriteFailure` en `Microsoft.Resources.ResourceWriteSuccess` . Zie filteren op geavanceerde Opera tors en gegevens velden als u meer flexibiliteit nodig hebt bij het filteren van gebeurtenissen op gebeurtenis typen.

Voor Power shell gebruikt u de `-IncludedEventType` para meter bij het maken van het abonnement.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Voor Azure CLI gebruikt u de `--included-event-types` para meter. In het volgende voor beeld wordt Azure CLI gebruikt in een bash-shell:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Voor een resource manager-sjabloon gebruikt u de `includedEventTypes` eigenschap.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filteren op onderwerp

U kunt gebeurtenissen filteren op het onderwerp in de gebeurtenis gegevens. U kunt een waarde opgeven die moet overeenkomen voor het begin of het einde van het onderwerp. Zie filteren op geavanceerde Opera tors en gegevens velden als u meer flexibiliteit nodig hebt bij het filteren van gebeurtenissen op onderwerp.

In het volgende Power shell-voor beeld maakt u een gebeurtenis abonnement dat wordt gefilterd op het begin van het onderwerp. U kunt de `-SubjectBeginsWith` para meter gebruiken om gebeurtenissen te beperken tot records voor een specifieke resource. U geeft de bron-ID van een netwerk beveiligings groep door.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

In het volgende Power shell-voor beeld wordt een abonnement gemaakt voor een Blob-opslag. Er worden gebeurtenissen beperkt met een onderwerp dat eindigt op `.jpg` .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

In het volgende Azure CLI-voor beeld maakt u een gebeurtenis abonnement dat wordt gefilterd op het begin van het onderwerp. U kunt de `--subject-begins-with` para meter gebruiken om gebeurtenissen te beperken tot records voor een specifieke resource. U geeft de bron-ID van een netwerk beveiligings groep door.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

In het volgende Azure CLI-voor beeld wordt een abonnement gemaakt voor een Blob-opslag. Er worden gebeurtenissen beperkt met een onderwerp dat eindigt op `.jpg` .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

In het volgende voor beeld van een resource manager-sjabloon maakt u een gebeurtenis abonnement dat wordt gefilterd op het begin van het onderwerp. U kunt de `subjectBeginsWith` eigenschap gebruiken om gebeurtenissen te beperken tot records voor een specifieke resource. U geeft de bron-ID van een netwerk beveiligings groep door.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

In het volgende voor beeld van een resource manager-sjabloon wordt een abonnement gemaakt voor een Blob-opslag. Er worden gebeurtenissen beperkt met een onderwerp dat eindigt op `.jpg` .

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filteren op Opera tors en gegevens

Voor meer flexibiliteit bij het filteren kunt u Opera tors en gegevens eigenschappen gebruiken om gebeurtenissen te filteren.

### <a name="subscribe-with-advanced-filters"></a>Abonneren met geavanceerde filters

Zie [Geavanceerde filtering](event-filtering.md#advanced-filtering)voor meer informatie over de Opera tors en sleutels die u kunt gebruiken voor geavanceerde filters.

In deze voor beelden wordt een aangepast onderwerp gemaakt. Ze abonneren zich op het aangepaste onderwerp en filteren op een waarde in het gegevens object. Gebeurtenissen waarvan de eigenschap Color is ingesteld op blauw, rood of groen, worden verzonden naar het abonnement.

Gebruik voor Azure CLI:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

U ziet dat er een [vervaldatum](concepts.md#event-subscription-expiration) is ingesteld voor het abonnement.

Gebruik voor PowerShell:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Test filter

Als u het filter wilt testen, verzendt u een gebeurtenis met het veld kleur ingesteld op groen. Omdat groen een van de waarden in het filter is, wordt de gebeurtenis aan het eind punt geleverd.

Gebruik voor Azure CLI:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Gebruik voor PowerShell:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Als u een scenario wilt testen waarbij de gebeurtenis niet wordt verzonden, verzendt u een gebeurtenis met het veld kleur ingesteld op geel. Geel is niet een van de waarden die zijn opgegeven in het abonnement, waardoor de gebeurtenis niet wordt bezorgd in uw abonnement.

Gebruik voor Azure CLI:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Gebruik voor PowerShell:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie over de verificatie sleutel.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
