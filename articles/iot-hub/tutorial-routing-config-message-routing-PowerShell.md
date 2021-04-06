---
title: 'Zelf studie: berichten routering voor Azure IoT Hub configureren met Azure PowerShell'
description: 'Zelf studie: Configureer bericht routering voor Azure IoT Hub met behulp van Azure PowerShell. Afhankelijk van de eigenschappen in het bericht, kunt u berichten routeren naar een opslagaccount of een Service Bus-wachtrij.'
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: be1560bcc03ec7a26f4bc374392c746243cd731a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624069"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Zelfstudie: Azure PowerShell gebruiken om IoT Hub-berichtroutering te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Het script downloaden (optioneel)

Voor het tweede deel van deze zelfstudie downloadt u een Visual Studio-toepassing en voert u deze uit om berichten naar de IoT Hub te verzenden. Een map in de download bevat de Azure Resource Manager-sjabloon en het parameterbestand, evenals de Azure CLI- en PowerShell-scripts. 

Als u het voltooide script wilt weergeven, downloadt u de [C#-voorbeelden voor Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Pak het bestand master.zip uit. U vindt het Azure CLI-script in de map /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ onder de naam **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Resources maken

Maak eerst de resources met behulp van PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>PowerShell gebruiken voor het maken van uw basisresources

Kopieer en plak het onderstaande script in Cloud Shell en druk op Enter. Het script wordt met één regel tegelijk uitgevoerd. Met de eerste sectie van het script worden de basisresources voor deze zelfstudie gemaakt, waaronder het opslagaccount, de IoT Hub, de Service Bus-naamruimte en de Service Bus-wachtrij. Bij het doorlopen van de zelfstudie, kopieert u elk scriptblok en plakt u het in Cloud Shell om het uit te voeren.

Er zijn een aantal resourcenamen die wereldwijd uniek moeten zijn, zoals de naam van de IoT Hub en van het opslagaccount. Om dit gemakkelijker te maken, wordt aan die resourcenamen een willekeurige alfanumerieke waarde genaamd *randomValue* toegevoegd. De randomValue wordt bovenaan het script eenmaal gegenereerd en waar nodig aan de resourcenamen in het hele script toegevoegd. Als u niet wilt dat de waarde willekeurig is, kunt u deze instellen op een lege tekenreeks of op een specifieke waarde. 

> [!IMPORTANT]
> De variabelen die in het eerste script zijn ingesteld, worden ook gebruikt door het routeringsscript, dus voer het hele script uit in dezelfde Cloud Shell-sessie. Als u een nieuwe sessie opent om het script uit te voeren voor het instellen van de routering, ontbreken er verschillende waarden voor variabelen. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Een gesimuleerd apparaat maken

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Nu de basisresources zijn ingesteld, kunt u de berichtroutering configureren.

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Gebruik [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) om een routeringseindpunt te maken. Gebruik [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) om de berichtenroute voor het eindpunt te maken

### <a name="route-to-a-storage-account"></a>Routeren naar een opslagaccount 

Stel eerst het eindpunt voor het opslagaccount in, en maak vervolgens de berichtenroute.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Dit zijn de variabelen die worden gebruikt door het script dat moet worden ingesteld in uw Cloud Shell-sessie:

**resourceGroup**: Dit veld komt twee keer voor: stel beide in op uw resourcegroep.

**name**: Dit veld bevat de naam van de IoT Hub waarop de routering van toepassing moet zijn.

**endpointName**: Dit veld geeft de naam van het eindpunt aan. 

**endpointType**: Dit veld is het type eindpunt. Deze waarde moet worden ingesteld op `azurestoragecontainer`, `eventhub`, `servicebusqueue` of `servicebustopic`. Stel deze hier in op `azurestoragecontainer`.

**subscriptionId**: dit veld wordt ingesteld op de subscriptionId voor uw Azure-account.

**storageConnectionString**: Deze waarde wordt opgehaald uit het opslagaccount dat in het vorige script is ingesteld. Het wordt door de routering gebruikt om toegang te krijgen tot het opslagaccount.

**containerName**: Dit veld is de naam van de container in het opslagaccount waarnaar gegevens worden geschreven.

**encoding**: stel dit veld in op `AVRO` of `JSON`. Dit geeft de indeling aan waarin de gegevens worden opgeslagen. De standaardindeling is AVRO.

**routeName**: Dit veld is de naam van de route die u instelt. 

**condition**: Dit veld is de query die wordt gebruikt als filter voor berichten die naar dit eindpunt worden verzonden. De queryvoorwaarde voor de berichten die naar de opslag worden doorgestuurd, is `level="storage"`.

**enabled**: Dit veld wordt standaard ingesteld op `true`, wat aangeeft dat de berichtroute moet worden ingeschakeld nadat deze is gemaakt.

Kopieer dit script en, plak het in het Cloud Shell-venster.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

De volgende stap is het maken van het eindpunt van de routering voor het opslagaccount. U geeft ook de container op waarin de resultaten worden opgeslagen. De container is gemaakt toen het opslagaccount werd gemaakt.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Maak vervolgens de berichtroute voor het eindpunt van de opslag. De berichtroute geeft aan waarnaartoe de berichten moeten worden verzonden die voldoen aan de query.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Routeren naar een Service Bus-wachtrij

Stel nu de routering in voor de Service Bus-wachtrij. Als u de verbindingsreeks voor de Service Bus-wachtrij wilt ophalen, moet u een autorisatieregel maken waarvoor de juiste rechten zijn gedefinieerd. Met het volgende script maakt u een autorisatieregel voor de Service Bus-wachtrij met de naam `sbauthrule`, en stelt u de rechten in op `Listen Manage Send`. Zodra deze autorisatieregel is ingesteld, kunt u deze gebruiken om de verbindingsreeks voor de wachtrij op te halen.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Gebruik nu de autorisatieregel om de sleutel voor de Service Bus-wachtrij op te halen. Deze autorisatieregel wordt gebruikt om de verbindingsreeks verderop in het script op te halen.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Stel nu het eindpunt van de routering in en de berichtroute voor de Service Bus-wachtrij. Dit zijn de variabelen die worden gebruikt door het script dat moet worden ingesteld in uw Cloud Shell-sessie:

**endpointName**: Dit veld geeft de naam van het eindpunt aan. 

**endpointType**: Dit veld is het type eindpunt. Deze waarde moet worden ingesteld op `azurestoragecontainer`, `eventhub`, `servicebusqueue` of `servicebustopic`. Stel deze hier in op `servicebusqueue`.

**routeName**: Dit veld is de naam van de route die u instelt. 

**condition**: Dit veld is de query die wordt gebruikt als filter voor berichten die naar dit eindpunt worden verzonden. De queryvoorwaarde voor de berichten die worden gerouteerd naar de Service Bus-wachtrij, is `level="critical"`.

Hier ziet u het Azure PowerShell-script voor de berichtenroutering voor de Service Bus-wachtrij.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Berichtroutering weergeven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de berichtroutes hebt geconfigureerd, gaat u door naar de volgende zelfstudie om te leren hoe u berichten naar de IoT Hub verzendt en om te zien hoe ze naar de verschillende bestemmingen worden gerouteerd. 

> [!div class="nextstepaction"]
> [Deel 2 - De resultaten van berichtroutering weergeven](tutorial-routing-view-message-routing-results.md)
