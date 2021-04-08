---
title: 'Zelf studie: bericht routering voor Azure IoT Hub configureren met een Azure Resource Manager sjabloon'
description: 'Zelf studie: bericht routering voor Azure IoT Hub configureren met een Azure Resource Manager sjabloon'
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53217340b0d91f3de77e5e0d8c0a82e30599d6ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621425"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Zelfstudie: Een Azure Resource Manager-sjabloon gebruiken om IoT Hub-berichtroutering te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Berichtroutering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>De sjabloon en het parameterbestand downloaden

Voor het tweede deel van deze zelfstudie downloadt u een Visual Studio-toepassing en voert u deze uit om berichten naar de IoT Hub te verzenden. Er is een map in de download die de Azure Resource Manager-sjabloon en het parameterbestand bevat, evenals de Azure CLI- en PowerShell-scripts.

Ga nu verder met het downloaden van de [Azure IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Pak het bestand master.zip uit. De Resource Manager-sjabloon en het parameterbestand bevinden zich op /iot-hub/Tutorials/Routing/SimulatedDevice/resources/als **template_iothub.json** en **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Uw resources maken

U gaat een Azure Resource Manager (RM)-sjabloon gebruiken om al uw resources te maken. De Azure CLI-en PowerShell-scripts kunnen met een aantal regels tegelijk worden uitgevoerd. Een RM-sjabloon wordt in één stap geïmplementeerd. In dit artikel wordt beschreven hoe u de secties afzonderlijk kunt begrijpen. Vervolgens wordt uitgelegd hoe u de sjabloon implementeert en maakt u het virtuele apparaat om te testen. Nadat de sjabloon is geïmplementeerd, kunt u de configuratie voor berichtroutering bekijken in het portal.

Er zijn een aantal resourcenamen die wereldwijd uniek moeten zijn, zoals de naam van de IoT Hub en van het opslagaccount. Om de naamgeving van de resources gemakkelijker te maken, worden deze resourcenamen ingesteld om een willekeurige alfanumerieke waarde toe te voegen die is gegenereerd op basis van de huidige datum/tijd. 

Als u de sjabloon bekijkt, ziet u dat er variabelen worden ingesteld voor deze resources waarvoor de parameter wordt doorgegeven en wordt *randomValue* toegevoegd aan de parameter. 

In de volgende sectie worden de gebruikte parameters uitgelegd.

### <a name="parameters"></a>Parameters

De meeste van deze parameters hebben standaardwaarden. De taken die eindigen op **_in** worden samengevoegd met *randomValue* om ze globaal uniek te maken. 

**randomValue**: Deze waarde wordt gegenereerd op basis van de huidige datum/tijd wanneer u de sjabloon implementeert. Dit veld bevindt zich niet in het parameterbestand, omdat dit in de sjabloon zelf wordt gegenereerd.

**subscriptionId**: Dit veld wordt voor u ingesteld op het abonnement waarin u de sjabloon implementeert. Dit veld bevindt zich niet in het parameterbestand omdat het voor u is ingesteld.

**IoTHubName_in**: Dit veld is de basis IoT Hub-naam, die wordt samengevoegd met de randomValue die wereldwijd uniek moet zijn.

**location**: Dit veld is de Azure-regio waarin u implementeert, zoals "westus".

**consumer_group**: Dit veld is de gebruikersgroep die is ingesteld voor berichten die afkomstig zijn van het eindpunt van de routering. Het wordt gebruikt voor het filteren van resultaten in Azure Stream Analytics. Zo is de hele stroom waar u alles krijgt, of als u gegevens hebt ontvangen via consumer_group ingesteld op **Contoso**, kunt u een Azure Stream Analytics-stroom (en Power BI-rapport) instellen om alleen die vermeldingen weer te geven. Dit veld wordt gebruikt in deel 2 van deze zelfstudie.

**sku_name**: Dit veld is de schaal voor de IoT Hub. Deze waarde moet S1 of hoger zijn. Een gratis laag werkt niet voor deze zelfstudie omdat er geen meerdere eindpunten zijn toegestaan.

**sku_units**: Dit veld komt samen met de **sku_name** en is het aantal IoT Hub-eenheden dat kan worden gebruikt.

**d2c_partitions**: Dit veld is het aantal partities dat wordt gebruikt voor de gebeurtenisstroom.

**storageAccountName_in**: Dit veld is de naam van het opslagaccount dat moet worden gemaakt. Berichten worden doorgestuurd naar een container in het opslagaccount. Dit veld wordt samengevoegd met de randomValue om het globaal uniek te maken.

**storageContainerName**: Dit veld is de naam van de container waarin de berichten worden opgeslagen die naar het opslagaccount worden doorgestuurd.

**storage_endpoint**: Dit veld is de naam voor het eindpunt van het opslagaccount dat wordt gebruikt door de berichtroutering.

**service_bus_namespace_in**: Dit veld is de naam van de Service Bus-naamruimte die moet worden gemaakt. Deze waarde wordt samengevoegd met de randomValue om deze wereldwijd uniek te maken.

**service_bus_queue_in**: Dit veld is de naam van de Service Bus-wachtrij die wordt gebruikt voor de routering van berichten. Deze waarde wordt samengevoegd met de randomValue om deze wereldwijd uniek te maken.

**AuthRules_sb_queue**: Dit veld bevat de autorisatieregels voor de service bus-wachtrij, die wordt gebruikt om de verbindingsreeks voor de wachtrij op te halen.

### <a name="variables"></a>Variabelen

Deze waarden worden gebruikt in de sjabloon en worden meestal afgeleid van parameters.

**queueAuthorizationRuleResourceId**: Dit veld is het ResourceId voor de autorisatieregel voor de Service Bus-wachtrij. ResourceId wordt weer gebruikt om de verbindingsreeks voor de wachtrij op te halen.

**iotHubName**: Dit veld is de naam van de IoT Hub nadat randomValue is samengevoegd. 

**storageAccountName**: Dit veld is de naam van het opslagaccount nadat randomValue is samengevoegd. 

**service_bus_namespace**: Dit veld is de naamruimte nadat randomValue is samengevoegd.

**service_bus_queue**: Dit veld is de Service Bus-wachtrij nadat randomValue is samengevoegd.

**sbVersion**: de versie van de Service Bus-API die moet worden gebruikt. In dit geval is dat "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Resources: Opslagaccount en -container

De eerste resource die wordt gemaakt, is het opslagaccount, samen met de container waarnaar berichten worden doorgestuurd. De container is een resource onder het opslagaccount. Het bevat een `dependsOn`-component voor het opslagaccount, waarvoor het opslagaccount moet worden gemaakt vóór de container.

Dit gedeelte ziet er als volgt uit:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Resources: Service Bus-naamruimte en -wachtrij

De tweede resource die wordt gemaakt, is de Service Bus-naamruimte, samen met de Service Bus-wachtrij waarnaar berichten worden doorgestuurd. De SKU is ingesteld op standaard. De API-versie wordt opgehaald uit de variabelen. Ze wordt ook ingesteld om de Service Bus-naamruimte te activeren bij het implementeren van deze sectie (status: Actief). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

In deze sectie wordt de Service Bus-wachtrij gemaakt. Dit deel van het script heeft een `dependsOn`-component waarmee wordt gegarandeerd dat de naamruimte wordt gemaakt vóór de wachtrij.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Resources: IOT hub en berichtroutering

Nu het opslagaccount en de Service Bus-wachtrij zijn gemaakt, kunt u de IoT Hub maken waarmee berichten hiernaar worden doorgestuurd. De RM-sjabloon maakt gebruik van `dependsOn`-componenten, zodat er niet wordt geprobeerd de hub te maken voordat de Service Bus-resources en het opslagaccount zijn gemaakt. 

Hier volgt het eerste deel van de sectie IoT Hub. Dit deel van de sjabloon stelt de afhankelijkheden in en begint met de eigenschappen.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

De volgende sectie is de sectie voor de routeringsconfiguratie voor berichten voor de IOT-hub. Eerst komt de sectie voor de eindpunten. In dit deel van de sjabloon worden de routeringseindpunten voor de Service Bus-wachtrij en het opslagaccount, met inbegrip van de verbindingsreeksen, ingesteld.

Als u de verbindingsreeks voor de wachtrij wilt maken, hebt u de queueAuthorizationRulesResourcedId nodig, die inline wordt opgehaald. Als u de verbindingsreeks voor het opslagaccount wilt maken, haalt u de primaire opslagsleutel op en gebruikt u deze in de indeling voor de verbindingsreeks.

De eindpuntconfiguratie is ook de locatie waar u de blob-indeling instelt op `AVRO` of `JSON`.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Deze volgende sectie is voor de berichtroutes naar de eindpunten. Er is één voor elk eindpunt ingesteld, dus er is een voor de Service Bus-wachtrij en één voor de container van het opslagaccount.

Houd er rekening mee dat de query-voorwaarde voor de berichten die naar de opslag worden doorgestuurd, `level="storage"` is, en dat de queryvoorwaarde voor de berichten die worden doorgestuurd naar de Service Bus-wachtrij `level="critical"` is.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Deze json toont de rest van de sectie IoT Hub, die standaardinformatie en de SKU voor de hub bevat.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Resources: Autorisatieregels voor Service Bus-wachtrij

De autorisatieregel voor de Service Bus-wachtrij wordt gebruikt om de verbindingsreeks voor de Service Bus-wachtrij op te halen. Er wordt een `dependsOn` -component gebruikt om ervoor te zorgen dat deze niet wordt gemaakt vóór de Service Bus-naamruimte en de Service Bus-wachtrij.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Resources: Consumentengroep

In deze sectie maakt u een consumentengroep voor de IoT Hub-gegevens die door de Azure Stream Analytics in het tweede deel van deze zelfstudie worden gebruikt.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Resources: Uitvoer

Als u een waarde wilt verzenden naar het implementatiescript dat moet worden weergegeven, gebruikt u een uitvoersectie. Dit deel van de sjabloon retourneert de verbindingsreeks voor de Service Bus-wachtrij. Het retourneren van een waarde is niet vereist, deze is opgenomen als een voorbeeld van het retourneren van resultaten naar het aanroepende script.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>De RM-sjabloon implementeren

Als u de sjabloon wilt implementeren in Azure, uploadt u de sjabloon en het parameterbestand naar Azure Cloud Shell en voert u vervolgens een script uit om de sjabloon te implementeren. Open Azure Cloud Shell en meld u aan. In dit voorbeeld wordt PowerShell gebruikt.

Als u de bestanden wilt uploaden, selecteert u het pictogram **Uploaden/downloaden van bestanden** in de menubalk en kiest u vervolgens Uploaden.

![Schermopname waarin het pictogram Bestanden uploaden/downloaden is gemarkeerd.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Gebruik de Verkenner die u wilt gebruiken om de bestanden op uw lokale schijf te zoeken en te selecteren, en kies vervolgens **Openen**.

Nadat de bestanden zijn geüpload, wordt een dialoogvenster met resultaten weergegeven zoals in de volgende afbeelding.

![Cloud Shell menu balk met resultaten voor uploaden/downloaden gemarkeerd](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

De bestanden worden geüpload naar de share die wordt gebruikt door uw Cloud Shell-exemplaar. 

Voer het script uit om de implementatie uit te voeren. Met de laatste regel van dit script wordt de variabele opgehaald die is ingesteld om te worden geretourneerd: de verbindingsreeks voor de Service Bus-wachtrij.

In het script worden de volgende variabelen ingesteld en gebruikt:

**$RGName** is de naam van de resourcegroep waarin de sjabloon moet worden geïmplementeerd. Dit veld wordt gemaakt voordat de sjabloon wordt geïmplementeerd.

**$location** is de Azure-locatie die moet worden gebruikt voor de sjabloon, zoals "westus".

**deploymentname** is een naam die u toewijst aan de implementatie om de retourwaarde van de variabele op te halen.

Hier is het PowerShell-script. Kopieer dit PowerShell-script en plak het in het Cloud Shell-venster en druk vervolgens op ENTER om het uit te voeren.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Als u scriptfouten hebt, kunt u het script lokaal bewerken, het opnieuw uploaden naar de Cloud Shell en het script opnieuw uitvoeren. Wanneer het uitvoeren van het script is voltooid, gaat u verder met de volgende stap.

## <a name="create-simulated-device"></a>Een gesimuleerd apparaat maken

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Berichtroutering weergeven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de berichtroutes hebt geconfigureerd, gaat u door naar de volgende zelfstudie om te leren hoe u de informatie over de gerouteerde berichten kunt verwerken en weergeven.

> [!div class="nextstepaction"]
> [Deel 2 - De resultaten van berichtroutering weergeven](tutorial-routing-view-message-routing-results.md)
