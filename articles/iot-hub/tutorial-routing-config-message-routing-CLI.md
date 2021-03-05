---
title: 'Zelf studie: bericht routering voor Azure IoT Hub configureren met behulp van Azure CLI'
description: 'Zelf studie: berichten routering voor Azure IoT Hub configureren met behulp van de Azure CLI. Afhankelijk van de eigenschappen in het bericht, kunt u berichten routeren naar een opslagaccount of een Service Bus-wachtrij.'
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a1cce7264e56b16d285685392186a05e2f075439
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199761"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Zelfstudie: Azure CLI gebruiken om IoT Hub-berichtroutering te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Het script downloaden (optioneel)

Voor het tweede deel van deze zelfstudie downloadt u een Visual Studio-toepassing en voert u deze uit om berichten naar de IoT Hub te verzenden. Een map in de download bevat de Azure Resource Manager-sjabloon en het parameterbestand, evenals de Azure CLI- en PowerShell-scripts.

Als u het voltooide script wilt weergeven, downloadt u de [C#-voorbeelden voor Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Pak het bestand master.zip uit. U vindt het Azure CLI-script in de map /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ onder de naam **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>De ADe Azure-CLI gebruiken om uw resources te maken

Kopieer en plak het onderstaande script in Cloud Shell en druk op Enter. Het script wordt met één regel tegelijk uitgevoerd. Met de eerste sectie van het script worden de basisresources voor deze zelfstudie gemaakt, waaronder het opslagaccount, de IoT Hub, de Service Bus-naamruimte en de Service Bus-wachtrij. Bij het doorlopen van de rest van de zelfstudie kopieert u elk scriptblok en plakt u het in Cloud Shell om het uit te voeren.

> [!TIP]
> Een tip over foutopsporing: dit script maakt gebruik van het vervolgsymbool (de backslash `\`) om het script beter leesbaar te maken. Als u problemen ondervindt met het uitvoeren van het script, zorg dan dat uw Cloud Shell-sessie `bash` uitvoert en dat er geen spatie na een backslash staat.
> 

Er zijn een aantal resourcenamen die wereldwijd uniek moeten zijn, zoals de naam van de IoT Hub en van het opslagaccount. Om dit gemakkelijker te maken, wordt aan die resourcenamen een willekeurige alfanumerieke waarde genaamd *randomValue* toegevoegd. De randomValue wordt bovenaan het script eenmaal gegenereerd en waar nodig aan de resourcenamen in het hele script toegevoegd. Als u niet wilt dat de waarde willekeurig is, kunt u deze instellen op een lege tekenreeks of op een specifieke waarde. 

> [!IMPORTANT]
> De variabelen die in het eerste script zijn ingesteld, worden ook gebruikt door het routeringsscript, dus voer het hele script uit in dezelfde Cloud Shell-sessie. Als u een nieuwe sessie opent om het script uit te voeren voor het instellen van de routering, ontbreken er verschillende waarden voor variabelen.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Nu de basisresources zijn ingesteld, kunt u de berichtroutering configureren.

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Als u een routeringseindpunt wilt maken, gebruikt u [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create). Als u de berichtroute voor het eindpunt wilt maken, gebruikt u [az iot hub route create](/cli/azure/iot/hub/route#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Routeren naar een opslagaccount

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Stel eerst het eindpunt voor het opslagaccount in, en stel vervolgens de route in. 

Dit zijn de variabelen die worden gebruikt door het script dat moet worden ingesteld in uw Cloud Shell-sessie:

**storageConnectionString**: Deze waarde wordt opgehaald uit het opslagaccount dat in het vorige script is ingesteld. Het wordt gebruikt door de berichtroutering om toegang te krijgen tot het opslagaccount.

  **resourceGroup**: Deze variabele komt twee keer voor: stel beide in op uw resourcegroep.

**endpoint subscriptionID**: Dit veld wordt ingesteld op de Azure-abonnements-id voor het eindpunt. 

**endpointType**: Dit veld is het type eindpunt. Deze waarde moet worden ingesteld op `azurestoragecontainer`, `eventhub`, `servicebusqueue` of `servicebustopic`. Stel deze hier in op `azurestoragecontainer`.

**iotHubName**: Dit veld is de naam van de hub waarop de routering wordt uitgevoerd.

**containerName**: Dit veld is de naam van de container in het opslagaccount waarnaar gegevens worden geschreven.

**encoding**: Dit veld moet worden ingesteld op `avro` of `json`. Dit is de indeling waarin de gegevens worden opgeslagen.

**routeName**: Dit veld is de naam van de route die u instelt. 

**endpointName**: Dit veld geeft de naam van het eindpunt aan. 

**enabled**: Dit veld wordt standaard ingesteld op `true`, wat aangeeft dat de berichtroute moet worden ingeschakeld nadat deze is gemaakt.

**condition**: Dit veld is de query die wordt gebruikt als filter voor berichten die naar dit eindpunt worden verzonden. De queryvoorwaarde voor de berichten die naar de opslag worden doorgestuurd, is `level="storage"`.

Kopieer dit script, plak het in uw Cloud Shell-venster en voer het uit.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

De volgende stap is het maken van het eindpunt van de routering voor het opslagaccount. U geeft ook de container op waarin de resultaten worden opgeslagen. De container is eerder gemaakt toen u het opslagaccount maakte.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Maak vervolgens de route voor het eindpunt van de opslag. De berichtroute geeft aan waarnaartoe de berichten moeten worden verzonden die voldoen aan de query. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Routeren naar een Service Bus-wachtrij

Stel nu de routering in voor de Service Bus-wachtrij. Als u de verbindingsreeks voor de Service Bus-wachtrij wilt ophalen, moet u een autorisatieregel maken waarvoor de juiste rechten zijn gedefinieerd. Met het volgende script maakt u een autorisatieregel voor de Service Bus-wachtrij met de naam `sbauthrule`, en stelt u de rechten in op `Listen Manage Send`. Zodra deze autorisatieregel is gedefinieerd, kunt u deze gebruiken om de verbindingsreeks voor de wachtrij op te halen.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Gebruik nu de autorisatieregel om de verbindingsreeks voor de Service Bus-wachtrij op te halen.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Stel nu het eindpunt van de routering in en de berichtroute voor de Service Bus-wachtrij. Dit zijn de variabelen die worden gebruikt door het script dat moet worden ingesteld in uw Cloud Shell-sessie:

**endpointName**: Dit veld geeft de naam van het eindpunt aan. 

**endpointType**: Dit veld is het type eindpunt. Deze waarde moet worden ingesteld op `azurestoragecontainer`, `eventhub`, `servicebusqueue` of `servicebustopic`. Stel deze hier in op `servicebusqueue`.

**routeName**: Dit veld is de naam van de route die u instelt. 

**condition**: Dit veld is de query die wordt gebruikt als filter voor berichten die naar dit eindpunt worden verzonden. De queryvoorwaarde voor de berichten die worden gerouteerd naar de Service Bus-wachtrij, is `level="critical"`.

Hier is de Azure CLI voor het eindpunt van de routering en de berichtroute voor de Service Bus-wachtrij.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Berichtroutering weergeven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de berichtroutes hebt geconfigureerd, gaat u door naar de volgende zelfstudie om te leren hoe u berichten naar de IoT Hub verzendt en om te zien hoe ze naar de verschillende bestemmingen worden gerouteerd. 

> [!div class="nextstepaction"]
> [Deel 2 - De resultaten van berichtroutering weergeven](tutorial-routing-view-message-routing-results.md)
