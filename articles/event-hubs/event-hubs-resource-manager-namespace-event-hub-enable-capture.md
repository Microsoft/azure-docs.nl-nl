---
title: Een event hub maken met Capture ingeschakeld - Azure Event Hubs | Microsoft Docs
description: Een Azure Event Hubs-naamruimte met een gebeurtenishub maken en Capture inschakelen met behulp van een Azure Resource Manager-sjabloon
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 17157e05e4ad123ba2bbdffa199c111df9f8912e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653020"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Een naamruimte maken met Event Hub en Capture inschakelen met behulp van een sjabloon

In dit artikel wordt beschreven hoe u een Azure Resource Manager-sjabloon gebruikt om een [Event Hubs](./event-hubs-about.md)-naamruimte te maken met één exemplaar van de gebeurtenishub en de [functie Capture](event-hubs-capture-overview.md) op de gebeurtenishub in te schakelen. In het artikel wordt beschreven hoe u kunt aangeven welke resources worden geïmplementeerd en hoe u de parameters definieert die bij de uitvoering van de implementatie zijn opgegeven. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Ook wordt uitgelegd hoe u kunt opgeven dat gebeurtenissen worden vastgelegd in Azure Storage-blobs of in een Azure Data Lake Store, op basis van de bestemming die u kiest.

Zie [Azure Resource Manager-sjablonen samenstellen][Authoring Azure Resource Manager templates] voor meer informatie over het maken van sjablonen. Zie [Microsoft.EventHub resource types](/azure/templates/microsoft.eventhub/allversions) (Microsoft.EventHub-resourcetypen) voor de JSON-syntaxis en eigenschappen die u kunt gebruiken in een sjabloon.

Zie [Naming Conventions][Azure Resources naming conventions] (Naamgevingsconventies) voor meer informatie over de patronen en procedures voor naamgevingsconventies van Azure Resources.

Voor de volledige-sjablonen klikt u op de volgende GitHub-koppelingen:

- [Event hub and enable Capture to Storage template][Event Hub and enable Capture to Storage template] 
- [Event hub and enable Capture to Azure Data Lake Store template][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> Om te controleren op de meest recente sjablonen, gaat u naar de galerie [Azure-snelstartsjablonen][Azure Quickstart Templates] en zoekt u naar Event Hubs.
> 
> 

## <a name="what-will-you-deploy"></a>Wat wilt u implementeren?

Met deze sjabloon implementeert u een Event Hubs-naamruimte met een gebeurtenishub en schakelt u tevens [Event Hubs Cinapture](event-hubs-capture-overview.md) in. Met Event Hubs Capture kunt u automatisch de gegevensstroom in uw gebeurtenishubs bezorgen in een Azure Blob-opslagaccount of Azure Data Lake Store van uw keuze, binnen een opgegeven periode of grootte van uw keuze. Klik op de volgende knop om Event Hubs Capture in te schakelen in Azure Storage:

[![Implementeren in Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Klik op de volgende knop om Event Hubs Capture in te schakelen in Azure Data Lake Store:

[![Implementeren in Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Parameters

Met Azure Resource Manager kunt u parameters definiëren voor waarden die u wilt opgeven wanneer de sjabloon wordt geïmplementeerd. De sjabloon bevat een sectie met de naam `Parameters` die alle parameterwaarden bevat. U moet een parameter definiëren voor de waarden die variëren op basis van het project dat u wilt implementeren of op basis van de omgeving waarin u gaat implementeren. Definieer geen parameters voor waarden die altijd hetzelfde blijven. De waarde van elke parameter wordt gebruikt in de sjabloon voor het definiëren van de resources die worden geïmplementeerd.

De sjabloon definieert de volgende parameters.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

De naam van de Event Hubs-naamruimte die wordt gemaakt.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

De naam van de gebeurtenishub die in de Event Hubs-naamruimte wordt gemaakt.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Het aantal dagen dat u de berichten in de gebeurtenishub wilt bewaren. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Het aantal partities dat in de gebeurtenishub wordt gemaakt.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Capture inschakelen op de gebeurtenishub.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

De coderingsindeling die u opgeeft voor het serialiseren van de gebeurtenisgegevens.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Het tijdsinterval waarin Event Hubs Capture begint met het vastleggen van de gegevens.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
De grootte waarbij Capture begint met het vastleggen van de gegevens.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

De naamindeling die door Event Hubs Capture wordt gebruikt om de Avro-bestanden weg te schrijven. De indeling voor Capture moet de velden `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` en `{Second}` bevatten. Deze velden kunnen in willekeurige volgorde worden gerangschikt, met of zonder scheidingstekens.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

De API-versie van de sjabloon.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Gebruik de volgende parameters als u Azure Storage als de bestemming kiest.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

Capture vereist een resource-id van het Azure Storage-account om te kunnen vastleggen naar uw gewenste opslagaccount.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

De blob-container waarin uw gebeurtenisgegevens worden vastgelegd.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Gebruik de volgende para meters als u Azure Data Lake Store gen 1 als uw bestemming kiest. U moet machtigingen instellen voor het Data Lake Store-pad waarin u de gebeurtenis wilt vastleggen. Zie [gegevens vastleggen voor Azure data Lake Storage gen 1](event-hubs-capture-enable-through-portal.md#capture-data-to-azure-data-lake-storage-gen-1)om machtigingen in te stellen.

### <a name="subscriptionid"></a>subscriptionId

De abonnements-id voor de Event Hubs-naamruimte en Azure Data Lake Store. Deze twee resources moeten onder dezelfde abonnements-id vallen.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

De Azure Data Lake Store-naam voor de vastgelegde gebeurtenissen.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

Het pad naar de doelmap voor de vastgelegde gebeurtenissen. Dit is de map in uw Data Lake Store waarnaar de gebeurtenissen tijdens het vastleggen wordt gestuurd. Zie [Azure Data Lake Store gebruiken voor het vastleggen van gegevens uit Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md) voor het instellen van machtigingen voor deze map.

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Resources om te implementeren met Azure Storage als doel voor vastgelegde gebeurtenissen

Hiermee maakt u een naamruimte van het type **EventHub** met één gebeurtenishub, en wordt ook Capture ingeschakeld voor gebruik met Azure Blob Storage.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Resources om te implementeren met Azure Data Lake Store als doel

Hiermee maakt u een naamruimte van het type **EventHub** met één gebeurtenishub, en wordt ook Capture ingeschakeld voor gebruik met Azure Data Lake Store.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> Gebruik de eigenschap **skipEmptyArchives** om het verzenden van lege bestanden in of uit te schakelen wanneer er geen gebeurtenissen optreden tijdens de periode voor vastleggen. 

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Een sjabloon implementeren om Event Hubs Capture in te schakelen in Azure Storage:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Een sjabloon implementeren om Event Hubs Capture in te schakelen in Azure Data Lake Store:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

Azure Blob Storage als doel:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Azure Data Lake Store als doel:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Volgende stappen

U kunt Event Hubs Capture ook configureren via de [Azure-portal](https://portal.azure.com). Zie voor meer informatie [Event Hubs Capture inschakelen met behulp van de Azure-portal](event-hubs-capture-enable-through-portal.md).

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](./event-hubs-about.md)
* [Een Event Hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
