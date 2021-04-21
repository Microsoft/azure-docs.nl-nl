---
title: 'Zelfstudie: Azure IoT Hub-berichten verrijken'
description: Zelfstudie waarin wordt getoond hoe u berichtverrijkingen gebruikt voor Azure IoT Hub berichten
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 0d6c90120d050b6896161f50332faf447c3ed67b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788849"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Zelfstudie: Azure IoT Hub-berichten verrijken

*Berichtverrijkingen* beschrijft de mogelijkheid Azure IoT Hub  berichten te voorzien van aanvullende informatie voordat de berichten naar het aangewezen eindpunt worden verzonden. Een reden om berichtverrijkingen te gebruiken, is om gegevens op te nemen die kunnen worden gebruikt om downstreamverwerking te vereenvoudigen. Het verrijken van telemetrieberichten van apparaten met een apparaattweetag kan bijvoorbeeld de belasting voor klanten verminderen om API-aanroepen van apparaattweeling voor deze informatie uit te voeren. Zie Overzicht van berichtverrijkingen [voor meer informatie.](iot-hub-message-enrichments-overview.md)

In deze zelfstudie ziet u twee manieren om de resources te maken en configureren die nodig zijn om de berichtverrijkingen voor een IoT-hub te testen. De resources bevatten één opslagaccount met twee opslagcontainers. De ene container bevat de verrijkte berichten en een andere container bevat de oorspronkelijke berichten. Er is ook een IoT-hub opgenomen om de berichten te ontvangen en ze door te sturen naar de juiste opslagcontainer op basis van het verrijkte of niet.

* De eerste methode is om de Azure CLI te gebruiken om de resources te maken en de berichtroutering te configureren. Vervolgens definieert u de verrijkingen handmatig met behulp van [Azure Portal](https://portal.azure.com).

* De tweede methode is het gebruik van Azure Resource Manager sjabloon om zowel *de* resources als de configuraties voor de berichtroutering en berichtverrijkingen te maken.

Nadat de configuraties voor de berichtroutering en berichtverrijkingen zijn voltooid, gebruikt u een toepassing om berichten te verzenden naar de IoT-hub. De hub routeert ze vervolgens naar beide opslagcontainers. Alleen de berichten die naar het eindpunt voor de verrijkte **opslagcontainer** worden verzonden, worden verrijkt.

Dit zijn de taken die u uitvoert om deze zelfstudie te voltooien:

**Berichtverrijkingen IoT Hub gebruiken**
> [!div class="checklist"]
> * Eerste methode: Resources maken en berichtroutering configureren met behulp van de Azure CLI. Configureer de berichtverrijkingen handmatig met behulp van [Azure Portal](https://portal.azure.com).
> * Tweede methode: Maak resources en configureer berichtroutering en berichtverrijkingen met behulp van een Resource Manager sjabloon. 
> * Voer een app uit die simuleert dat een IoT-apparaat berichten naar de hub verstuurt.
> * Bekijk de resultaten en controleer of de berichtverrijkingen werken zoals verwacht.

## <a name="prerequisites"></a>Vereisten

- U hebt een abonnement op Azure nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

- Installeer [Visual Studio](https://www.visualstudio.com/).

- Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in deze zelfstudie wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>De opslagplaats met IoT C#-voorbeelden ophalen

Download de [IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) van GitHub en los ze uit. Deze opslagplaats bevat verschillende toepassingen, scripts en Resource Manager sjablonen. De die voor deze zelfstudie moeten worden gebruikt, zijn als volgt:

* Voor de handmatige methode is er een CLI-script dat wordt gebruikt om de resources te maken. Dit script staat in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Met dit script maakt u de resources en configureert u de berichtroutering. Nadat u dit script hebt uitgevoerd, maakt u de berichtverrijkingen handmatig met behulp van [de Azure Portal](https://portal.azure.com).
* Voor de geautomatiseerde methode is er een Azure Resource Manager sjabloon. De sjabloon staat in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Met deze sjabloon worden de resources gemaakt, de berichtroutering geconfigureerd en vervolgens de berichtverrijkingen geconfigureerd.
* De derde toepassing die u gebruikt, is de apparaatsimulatie-app, die u gebruikt om berichten naar de IoT-hub te verzenden en de berichtverrijkingen te testen.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Handmatig instellen en configureren met behulp van de Azure CLI

Naast het maken van de benodigde resources configureert het Azure CLI-script ook de twee routes naar de eindpunten die afzonderlijke opslagcontainers zijn. Zie de routeringszelfstudie voor meer informatie over het configureren van de [berichtroutering.](tutorial-routing.md) Nadat de resources zijn ingesteld, gebruikt u [de](https://portal.azure.com) Azure Portal om berichtverrijkingen voor elk eindpunt te configureren. Ga vervolgens verder met de teststap.

> [!NOTE]
> Alle berichten worden doorgeleid naar beide eindpunten, maar alleen de berichten die naar het eindpunt gaan met geconfigureerde berichtverrijkingen worden verrijkt.
>

U kunt het volgende script gebruiken of u kunt het script openen in de map /resources van de gedownloade opslagplaats. Met het script voert u de volgende stappen uit:

* Maak een IoT-hub.
* Een opslagaccount maken.
* Maak twee containers in het opslagaccount. De ene container is voor de verrijkte berichten en een andere container is voor berichten die niet zijn verrijkt.
* Routering instellen voor de twee verschillende opslagaccounts:
    * Maak een eindpunt voor elke opslagaccountcontainer.
    * Maak een route naar elk van de container-eindpunten van het opslagaccount.

Er zijn verschillende resourcenamen die globaal uniek moeten zijn, zoals de naam van de IoT-hub en de naam van het opslagaccount. Om het uitvoeren van het script gemakkelijker te maken, worden deze resourcenamen toegevoegd met een willekeurige alfanumerieke waarde met de *naam randomValue*. De willekeurige waarde wordt eenmaal boven aan het script gegenereerd. Deze wordt waar nodig toegevoegd aan de resourcenamen in het script. Als u niet wilt dat de waarde willekeurig is, kunt u deze instellen op een lege tekenreeks of op een specifieke waarde.

Als u dit nog niet hebt gedaan, opent u een Azure [Cloud Shell](https://shell.azure.com) en zorgt u ervoor dat dit is ingesteld op Bash. Open het script in de uitgepakte opslagplaats, selecteer Ctrl+A om alles te selecteren en selecteer vervolgens Ctrl+C om het te kopiëren. U kunt ook het volgende CLI-script kopiëren of rechtstreeks in de Cloud Shell. Plak het script in het Cloud Shell door met de rechtermuisknop op de opdrachtregel te klikken en **Plakken te selecteren.** Het script voert één instructie tegelijk uit. Nadat het script niet meer wordt uitgevoerd, **selecteert u Enter** om te zorgen dat de laatste opdracht wordt uitgevoerd. Het volgende codeblok toont het script dat wordt gebruikt, met opmerkingen waarin wordt uitgelegd wat het doet.

Dit zijn de resources die door het script zijn gemaakt. *Verrijkt* betekent dat de resource is voor berichten met verrijkingen. *Oorspronkelijk* betekent dat de resource is voor berichten die niet zijn verrijkt.

| Name | Waarde |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| containernaam | Origineel  |
| containernaam | Verrijkt  |
| Naam van IoT-apparaat | Contoso-Test-Device |
| IoT Hub naam | ContosoTestHubMsgEn |
| opslagaccountnaam | contosostorage |
| eindpuntnaam 1 | ContosoStorageEndpointOriginal |
| eindpuntnaam 2 | ContosoStorageEndpointEntred|
| routenaam 1 | ContosoStorageRouteOriginal |
| routenaam 2 | ContosoStorageRouteEntred |

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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Op dit moment zijn de resources allemaal ingesteld en is de berichtroutering geconfigureerd. U kunt de configuratie voor berichtroutering bekijken in de portal en de berichtverrijkingen instellen voor berichten die naar de **verrijkte** opslagcontainer gaan.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Configureer de berichtverrijkingen handmatig met behulp van de Azure Portal

1. Ga naar uw IoT-hub door **Resourcegroepen te selecteren.** Selecteer vervolgens de resourcegroep die is ingesteld voor deze zelfstudie (**ContosoResourcesMsgEn**). Zoek de IoT-hub in de lijst en selecteer deze. Selecteer **Berichtroutering** voor de IoT-hub.

   ![Berichtroutering selecteren](./media/tutorial-message-enrichments/select-iot-hub.png)

   Het deelvenster berichtroutering heeft drie tabbladen met het label **Routes,** **Aangepaste eindpunten** en **Berichten verrijken.** Blader door de eerste twee tabbladen om de configuratie te zien die door het script is ingesteld. Gebruik het derde tabblad om berichtverrijkingen toe te voegen. Laten we berichten verrijken die naar het eindpunt gaan voor de opslagcontainer met de naam **enriched**. Vul de naam en waarde in en selecteer vervolgens het eindpunt **ContosoStorageEndpointEnverrijkd** in de vervolgkeuzelijst. Hier is een voorbeeld van het instellen van een verrijking die de naam van de IoT-hub aan het bericht toevoegt:

   ![Eerste verrijking toevoegen](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Voeg deze waarden toe aan de lijst voor het eindpunt ContosoStorageEndpointEnaffised.

   | Sleutel | Waarde | Eindpunt (vervolgkeuzelijst) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEntred |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEntred |
   |Klantid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEntred |

   > [!NOTE]
   > Als uw apparaat geen dubbel heeft, wordt de waarde die u hier in opteert, gestempeld als een tekenreeks voor de waarde in de berichtverrijkingen. Als u de gegevens van de apparaattweeling wilt zien, gaat u naar uw hub in de portal en **selecteert u IoT-apparaten.** Selecteer uw apparaat en selecteer vervolgens **Apparaat dubbel** boven aan de pagina.
   >
   > U kunt de gegevens van de tweeling bewerken om tags toe te voegen, zoals locatie, en deze instellen op een specifieke waarde. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](iot-hub-devguide-device-twins.md) voor meer informatie.

3. Wanneer u klaar bent, ziet uw deelvenster er ongeveer als de volgende afbeelding uit:

   ![Tabel met alle verrijkingen toegevoegd](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selecteer **Toepassen om** de wijzigingen op te slaan. Ga naar de [sectie Verrijkingen van testbericht.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Maken en configureren met behulp van een Resource Manager sjabloon
U kunt een Resource Manager gebruiken om de resources, berichtroutering en berichtverrijkingen te maken en te configureren.

1. Meld u aan bij Azure Portal. Selecteer **+ Een resource maken** om een zoekvak te openen. Voer *sjabloonimplementatie* in en zoek deze. Selecteer in het resultatenvenster Sjabloonimlementatie **(implementeren met aangepaste sjabloon).**

   ![Sjabloonimlementatie in de Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selecteer **Maken** in **het Sjabloonimlementatie** deelvenster.

1. Selecteer in **het deelvenster Aangepaste** implementatie Uw eigen sjabloon bouwen in de **editor**.

1. Selecteer in **het deelvenster** Sjabloon bewerken de optie **Bestand laden.** Windows Verkenner wordt weergegeven. Zoek de **template_messageenrichments.js** bestand in het uitgepakte repo-bestand in **/iot-hub/Tutorials/Routing/SimulatedDevice/resources.** 

   ![Sjabloon selecteren op lokale computer](./media/tutorial-message-enrichments/template-select.png)

1. Selecteer **Openen om** het sjabloonbestand van de lokale computer te laden. Deze wordt geladen en weergegeven in het bewerkingsvenster.

   Deze sjabloon is ingesteld voor het gebruik van een wereldwijd unieke naam voor de IoT-hub en de naam van het opslagaccount door een willekeurige waarde toe te voegen aan het einde van de standaardnamen, zodat u de sjabloon kunt gebruiken zonder wijzigingen aan te brengen.

   Dit zijn de resources die zijn gemaakt door de sjabloon te laden. **Verrijkt** betekent dat de resource is voor berichten met verrijkingen. **Oorspronkelijk** betekent dat de resource is voor berichten die niet zijn verrijkt. Dit zijn dezelfde waarden die worden gebruikt in het Azure CLI-script.

   | Name | Waarde |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | containernaam | Origineel  |
   | containernaam | Verrijkt  |
   | Naam van IoT-apparaat | Contoso-Test-Device |
   | IoT Hub naam | ContosoTestHubMsgEn |
   | opslagaccountnaam | contosostorage |
   | eindpuntnaam 1 | ContosoStorageEndpointOriginal |
   | eindpuntnaam 2 | ContosoStorageEndpointEntred|
   | routenaam 1 | ContosoStorageRouteOriginal |
   | routenaam 2 | ContosoStorageRouteEntred |

1. Selecteer **Opslaan**. Het **deelvenster Aangepaste implementatie** wordt weergegeven met alle parameters die door de sjabloon worden gebruikt. Het enige veld dat u hoeft in te stellen, is **Resourcegroep**. Maak een nieuwe of selecteer een optie in de vervolgkeuzelijst.

   Dit is de bovenste helft van het **deelvenster Aangepaste** implementatie. U kunt zien waar u de resourcegroep invult.

   ![Bovenste helft van het deelvenster Aangepaste implementatie](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Dit is de onderste helft van het **deelvenster Aangepaste** implementatie. U kunt de rest van de parameters en de voorwaarden bekijken. 

   ![Onderste helft van het deelvenster Aangepaste implementatie](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Schakel het selectievakje in om akkoord te gaan met de voorwaarden. Selecteer vervolgens **Kopen om** door te gaan met de sjabloonimplementatie.

1. Wacht tot de sjabloon volledig is geïmplementeerd. Selecteer het belpictogram bovenaan het scherm om de voortgang te controleren. Wanneer dit is voltooid, gaat u verder met de [sectie Berichtverrijkingen](#test-message-enrichments) testen.

## <a name="test-message-enrichments"></a>Berichtverrijkingen testen

Selecteer Resourcegroepen om de berichtverrijkingen **weer te geven.** Selecteer vervolgens de resourcegroep die u gebruikt voor deze zelfstudie. Selecteer de IoT-hub in de lijst met resources en ga naar **Berichten.** De configuratie van berichtroutering en de geconfigureerde verrijkingen worden weergegeven.

Nu de berichtverrijkingen zijn geconfigureerd voor het eindpunt, kunt u de toepassing Gesimuleerd apparaat uitvoeren om berichten te verzenden naar de IoT-hub. De hub is ingesteld met instellingen die de volgende taken uitvoeren:

* Berichten die worden gerouteerd naar het opslag-eindpunt ContosoStorageEndpointOriginal worden niet verrijkt en worden opgeslagen in de opslagcontainer `original` .

* Berichten die worden gerouteerd naar het opslag-eindpunt ContosoStorageEndpointEnchromed worden verrijkt en opgeslagen in de opslagcontainer `enriched` .

De toepassing Simulated Device is een van de toepassingen in de uitgepakte download. De toepassing verzendt berichten voor elk van de verschillende berichtrouteringsmethoden in de zelfstudie [Routering,](tutorial-routing.md)die Azure Storage.

Dubbelklik op het oplossingsbestand **IoT_SimulatedDevice.sln om** de code in Visual Studio openen en open **program.cs.** Vervang de naam van de IoT-hub door de markering `{your hub name}` . De indeling van de hostnaam van de IoT-hub is **{uw hubnaam}.azure-devices.net**. Voor deze zelfstudie is de naam van de hubhost ContosoTestHubMsgEn.azure-devices.net. Vervang vervolgens de apparaatsleutel die u eerder hebt opgeslagen tijdens het uitvoeren van het script om de resources voor de markering te `{your device key}` maken.

Als u niet over de apparaatsleutel hebt, kunt u deze ophalen uit de portal. Nadat u zich hebt aanmelden, gaat u naar **Resourcegroepen,** selecteert u de resourcegroep en selecteert u vervolgens uw IoT-hub. Zoek onder **IoT-apparaten** voor uw testapparaat en selecteer uw apparaat. Selecteer het kopieerpictogram naast Primaire **sleutel om** het naar het klembord te kopiëren.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Uitvoeren en testen

Voer de consoletoepassing een paar minuten uit. De berichten die worden verzonden, worden weergegeven op het consolescherm van de toepassing.

Deze app verzendt elke seconde een nieuw apparaat-naar-cloud-bericht naar de IoT Hub. Het bericht bevat een JSON-geserialiseerd object met een apparaat-id, temperatuur, vochtigheid en berichtniveau, die als standaardwaarde `normal` heeft. Er wordt willekeurig een niveau van of toegewezen, waardoor het bericht wordt gerouteerd naar het opslagaccount of `critical` naar het standaard `storage` eindpunt. De berichten die naar de **verrijkte** container in het opslagaccount worden verzonden, worden verrijkt.

Nadat er verschillende opslagberichten zijn verzonden, bekijkt u de gegevens.

1. Selecteer **Resourcegroepen**. Zoek de resourcegroep **ContosoResourcesMsgEn** en selecteer deze.

2. Selecteer uw opslagaccount, **contosostorage.** Selecteer vervolgens **Storage Explorer (preview)** in het linkerdeelvenster.

   ![Selecteer Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selecteer **BLOBCONTAINERS** om de twee containers te zien die kunnen worden gebruikt.

   ![De containers in het opslagaccount bekijken](./media/tutorial-message-enrichments/show-blob-containers.png)

De berichten in de container met de naam **enriched** hebben de berichtverrijkingen die zijn opgenomen in de berichten. De berichten in de container met de naam **origineel** hebben de onbewerkte berichten zonder verrijkingen. Zoom in op een van de containers totdat u onderaan komt en open het meest recente berichtbestand. Doe vervolgens hetzelfde voor de andere container om te controleren of er geen verrijkingen zijn toegevoegd aan berichten in die container.

Wanneer u berichten bekijkt die zijn verrijkt, ziet u 'mijn IoT Hub' met de naam van de hub en de locatie en de klant-id, zoals:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Hier is een niet-gefingeerd bericht. U ziet dat 'mijn IoT Hub', 'devicelocation' en 'customerID' hier niet worden weer geven, omdat deze velden worden toegevoegd door de verrijkingen. Dit eindpunt heeft geen verrijkingen.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze zelfstudie hebt gemaakt, verwijdert u de resourcegroep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT-hub, het opslagaccount en de resourcegroep zelf verwijderd.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Resources opschonen met de Azure-CLI

U kunt de resourcegroep verwijderen met de opdracht [az group delete](/cli/azure/group#az_group_delete). U herinnert zich misschien dat aan het begin van deze zelfstudie is ingesteld op `$resourceGroup` **ContosoResourcesMsgEn.**

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het toevoegen van berichtverrijkingen aan IoT Hub geconfigureerd en getest met behulp van de volgende stappen:

**Uw IoT Hub gebruiken**

> [!div class="checklist"]
> * Eerste methode: Maak resources en configureer berichtroutering met behulp van de Azure CLI. Configureer de berichtverrijkingen handmatig met behulp van [Azure Portal](https://portal.azure.com).
> * Tweede methode: Maak resources en configureer berichtroutering en berichtverrijkingen met behulp van een Azure Resource Manager sjabloon.
> * Voer een app uit die een IoT-apparaat simuleert dat berichten naar de hub verstuurt.
> * Bekijk de resultaten en controleer of de berichtverrijkingen werken zoals verwacht.

Zie Overzicht van berichtverrijkingen voor meer informatie over [berichtverrijkingen.](iot-hub-message-enrichments-overview.md)

Zie de volgende artikelen voor meer informatie over berichtroutering:

> [!div class="nextstepaction"]
> [Gebruik IoT Hub berichtroutering om apparaat-naar-cloud-berichten te verzenden naar verschillende eindpunten](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Zelfstudie: IoT Hub routering](tutorial-routing.md)