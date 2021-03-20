---
title: 'Zelf studie: bericht routering voor Azure IoT Hub configureren met behulp van Azure CLI'
description: 'Zelf studie: bericht routering voor Azure IoT Hub configureren met behulp van de Azure CLI en de Azure Portal'
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: d7a10aac82286616273f7283bc3fba74f2c58f35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625212"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Zelfstudie: Azure CLI en de Azure-portal gebruiken om IoT Hub-berichtroutering te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Azure CLI gebruiken om de basisresources te maken

In deze zelfstudie wordt Azure CLI gebruikt om de basisresources te maken, en wordt vervolgens de [Azure-portal](https://portal.azure.com) gebruikt om te laten zien hoe u berichtroutering configureert en het virtuele apparaat instelt om te testen.

Kopieer en plak het onderstaande script in Cloud Shell en druk op Enter. Het script wordt met één regel tegelijk uitgevoerd. Hiermee worden de basisresources voor deze zelfstudie gemaakt, waaronder het opslagaccount, de IoT Hub, de Service Bus-naamruimte en de Service Bus-wachtrij.

Er zijn een aantal resourcenamen die wereldwijd uniek moeten zijn, zoals de naam van de IoT Hub en van het opslagaccount. Om dit gemakkelijker te maken, wordt aan die resourcenamen een willekeurige alfanumerieke waarde genaamd *randomValue* toegevoegd. De randomValue wordt bovenaan het script eenmaal gegenereerd en waar nodig aan de resourcenamen in het hele script toegevoegd. Als u niet wilt dat de waarde willekeurig is, kunt u deze instellen op een lege tekenreeks of op een specifieke waarde.

> [!TIP]
> Een tip over foutopsporing: dit script maakt gebruik van het vervolgsymbool (de backslash `\`) om het script beter leesbaar te maken. Als u problemen ondervindt met het uitvoeren van het script, zorg dan dat uw Cloud Shell-sessie `bash` uitvoert en dat er geen spatie na een backslash staat.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Nu de basisresources zijn ingesteld, kunt u de berichtroutering configureren in de [Azure-portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Routeren naar een opslagaccount

Stel nu de routering in voor het opslagaccount. Ga naar het deelvenster Berichtroutering en voeg een route toe. Definieer bij het toevoegen van de route een nieuw eindpunt voor de route. Nadat u deze routering hebt ingesteld, worden berichten waarvan de eigenschap **Niveau** is ingesteld op **Opslag** automatisch naar een opslagaccount geschreven. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Selecteer in de [Azure-portal](https://portal.azure.com) de optie **Resourcegroepen** en selecteer vervolgens uw resourcegroep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

2. Selecteer de IoT Hub in de lijst met resources. In deze zelfstudie wordt gebruikgemaakt van **ContosoTestHub**.

3. Selecteer **Berichtroutering**. Selecteer in het deelvenster **Berichtroutering** de optie **+Toevoegen**. Selecteer in het deel venster **een route toevoegen** +**eind punt toevoegen** naast het veld eind punt om de ondersteunde eind punten weer te geven, zoals wordt weer gegeven in de volgende afbeelding:

   ![Beginnen met het toevoegen van een eindpunt voor een route](./media/tutorial-routing/message-routing-add-a-route-with-storage-endpoint-ver2.png)

4. Selecteer **Opslag**. Het deelvenster **Een opslageindpunt toevoegen** wordt weergegeven.

   ![Een eindpunt toevoegen](./media/tutorial-routing/message-routing-add-storage-endpoint-ver2.png)

5. Voer een naam in voor het eindpunt. In deze zelfstudie wordt gebruikgemaakt van **ContosoStorageEndpoint**.

6. Selecteer **Een container kiezen**. Er wordt een lijst met opslagaccounts weergegeven. Selecteer het account dat u hebt ingesteld in de voorbereidende stappen. In deze zelfstudie wordt gebruikgemaakt van **contosostorage**. Er wordt een lijst met containers in die opslagaccount weergegeven. **Selecteer** de container die u hebt ingesteld in de voorbereidende stappen. In deze zelfstudie wordt gebruikgemaakt van **contosoresults**. U keert terug naar het deelvenster **Een opslageindpunt toevoegen** en ziet de selecties die u hebt gemaakt.

7. Stel de codering in op AVRO of JSON. Gebruik ten behoeve van deze zelfstudie de standaardinstellingen voor de rest van de velden. Dit veld wordt grijs weergegeven als de geselecteerde regio JSON-codering niet ondersteunt.

   > [!NOTE]
   > U kunt de indeling van de blob-naam instellen met de **indeling van blobbestandsnaam**. De standaardwaarde is `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. De indeling moet {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH}, en {mm} bevatten, in willekeurige volgorde.
   >
   > Als u bijvoorbeeld de indeling van de standaardnaam voor het blobbestand gebruikt, de naam van de hub ContosoTestHub is en de datum en tijd 30 oktober 2018 om 10:56 uur, dan ziet de blobnaam er als volgt uit: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > De blobs worden geschreven in de Avro-indeling.
   >

8. Selecteer **Maken** het opslageindpunt te maken en toe te voegen aan de route. U gaat terug naar het deelvenster **Route toevoegen**.

9. Vul nu de rest van de informatie voor de routeringsquery in. Deze query specificeert de criteria voor het verzenden van berichten naar de opslagcontainer die u zojuist hebt toegevoegd als eindpunt. Vul de velden in op het scherm.

   **Naam**: Voer een naam in voor uw routeringsquery. In deze zelfstudie wordt gebruikgemaakt van **ContosoStorageRoute**.

   **Eindpunt**: Geeft het eindpunt weer dat u net hebt ingesteld.

   **Gegevensbron**: Selecteer **Telemetrieberichten apparaat** uit de vervolgkeuzelijst.

   **Route inschakelen**: Zorg dat dit veld is ingesteld op `enabled`.
   
   **Routeringsquery**: Voer `level="storage"` in als querytekenreeks.

   ![Een routeringsquery maken voor het opslagaccount](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Selecteer **Opslaan**. Wanneer dit is voltooid, gaat u terug naar het deelvenster Berichtroutering, waar u uw nieuwe routeringsquery voor opslag kunt zien. Sluit het deelvenster Routes, waarna u terugkeert naar de pagina Resourcegroep.

### <a name="route-to-a-service-bus-queue"></a>Routeren naar een Service Bus-wachtrij

Stel nu de routering in voor de Service Bus-wachtrij. Ga naar het deelvenster Berichtroutering en voeg een route toe. Definieer bij het toevoegen van de route een nieuw eindpunt voor de route. Nadat u deze route hebt ingesteld, worden berichten waarvan de eigenschap **Niveau** is ingesteld op **Kritiek** naar de Service Bus-wachtrij geschreven, die een logische app activeert waarmee vervolgens een e-mailbericht met de informatie wordt verzonden.

1. Selecteer op de pagina Resourcegroep uw IoT Hub en selecteer vervolgens **Berichtroutering**.

2. Selecteer in het deelvenster **Berichtroutering** de optie **+Toevoegen**.

3. Selecteer in het deelvenster **Een route toevoegen** de optie **+Toevoegen** naast het veld Eindpunt. Selecteer een **Service Bus-wachtrij**. Het deelvenster **Service Bus-eindpunt toevoegen** wordt weergegeven.

   ![Een Service Bus-eindpunt toevoegen](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Vul de velden in:

   **Naam van het eindpunt**: Voer een naam in voor het eindpunt. In deze zelfstudie wordt gebruikgemaakt van **ContosoSBQueueEndpoint**.
   
   **Service Bus-naamruimte**: Gebruik de vervolgkeuzelijst om de Service Bus-naamruimte te selecteren die u hebt ingesteld in de voorbereidende stappen. In deze zelfstudie wordt gebruikgemaakt van **ContosoSBNamespace**.

   **Service Bus-wachtrij**: Gebruik de vervolgkeuzelijst om de Service Bus-wachtrij te selecteren. In deze zelfstudie wordt gebruikgemaakt van **contososbqueue**.

5. Selecteer **Maken** om het Service Bus-wachtrij-eindpunt toe te voegen. U gaat terug naar het deelvenster **Route toevoegen**.

6. Vul nu de rest van de informatie voor de routeringsquery in. Deze query specificeert de criteria voor het verzenden van berichten naar de Service Bus-wachtrij die u zojuist hebt toegevoegd als eindpunt. Vul de velden in op het scherm. 

   **Naam**: Voer een naam in voor uw routeringsquery. In deze zelfstudie wordt gebruikgemaakt van **ContosoSBQueueRoute**. 

   **Eindpunt**: Geeft het eindpunt weer dat u net hebt ingesteld.

   **Gegevensbron**: Selecteer **Telemetrieberichten apparaat** uit de vervolgkeuzelijst.

   **Routeringsquery**: Voer `level="critical"` in als querytekenreeks. 

   ![Een routeringsquery maken voor de Service Bus-wachtrij](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Selecteer **Opslaan**. Wanneer u teruggaat naar het deelvenster Routes, ziet u de twee nieuwe routeringsregels zoals deze hier zijn weergegeven.

   ![De routes die u net hebt ingesteld](./media/tutorial-routing/message-routing-show-both-routes.png)

8. U kunt de aangepaste eindpunten die u hebt ingesteld weergeven door **Aangepaste eindpunten** te selecteren.

   ![Het aangepaste eindpunt dat u net hebt ingesteld](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Sluit het deelvenster Berichtroutering, waarna u terugkeert naar het deelvenster Resourcegroep.

## <a name="create-a-simulated-device"></a>Een gesimuleerd apparaat maken

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de berichtroutes hebt geconfigureerd, gaat u door naar de volgende zelfstudie om te leren hoe u berichten naar de IoT Hub verzendt en om te zien hoe ze naar de verschillende bestemmingen worden gerouteerd. 

> [!div class="nextstepaction"]
> [Deel 2 - De resultaten van berichtroutering weergeven](tutorial-routing-view-message-routing-results.md)