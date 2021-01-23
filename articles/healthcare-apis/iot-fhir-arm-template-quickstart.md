---
title: 'Snelstartgids: Azure IoT Connector implementeren voor FHIR (preview) met behulp van een ARM-sjabloon'
description: In deze Quick Start leert u hoe u Azure IoT connector voor FHIR (preview) implementeert met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon).
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b45c63031596c96886a96a21bf693803088cc006
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745222"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Quick Start: een Azure Resource Manager-sjabloon (ARM) gebruiken voor het implementeren van Azure IoT connector voor FHIR (preview)

In deze Quick Start leert u hoe u een Azure Resource Manager sjabloon (ARM-sjabloon) kunt gebruiken voor het implementeren van Azure IoT connector voor snelle bronnen voor de gezondheids zorg (FHIR&#174;) *, een functie van Azure API voor FHIR. Voor het implementeren van een werkend exemplaar van Azure IoT connector voor FHIR implementeert deze sjabloon ook een bovenliggende Azure API voor FHIR-service en een Azure IoT Central-toepassing die telemetrie van een apparaat Simulator exporteert naar Azure IoT connector voor FHIR. U kunt een ARM-sjabloon uitvoeren om Azure IoT connector voor FHIR te implementeren via de Azure Portal, Power shell of CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend zodra u zich hebt aangemeld.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeer naar Azure een Azure IoT-connector voor FHIR met een ARM-sjabloon in de Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).
* Als u de code lokaal wilt uitvoeren, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).
* Als u de code lokaal wilt uitvoeren:
    * Een Bash-shell (zoals Git Bash, die deel uitmaakt van [Git voor Windows](https://gitforwindows.org)).
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>De sjabloon controleren

De [sjabloon](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) definieert de volgende Azure-resources:

* **Microsoft.HealthcareApis/services**
* **Micro soft. HealthcareApis/Services/iomtconnectors**
* **Micro soft. IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>De sjabloon implementeren

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer de volgende koppeling om de Azure IoT-connector voor FHIR te implementeren met behulp van de ARM-sjabloon in de Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeer naar Azure een Azure IoT-connector voor de FHIR-service met de ARM-sjabloon in de Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Op de pagina **Azure API for FHIR implementeren**:

1. Indien gewenst, wijzigt u het **Abonnement** van de standaardinstelling naar een ander abonnement.

2. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**, voer een naam in voor de nieuwe resourcegroep en selecteer **OK**.

3. Als u een nieuwe resourcegroep hebt gemaakt, selecteert u een **Regio** voor de resourcegroep.

4. Voer een naam in voor de nieuwe Azure-API voor FHIR-instantie in de **FHIR-service naam**.

5. Kies de **locatie** voor uw Azure-API voor FHIR. De locatie kan hetzelfde zijn als of anders zijn dan de regio van de resourcegroep.

6. Geef een naam op voor uw Azure IoT-connector voor FHIR-instantie in de naam van de **IOT-connector**.

7. Geef een naam op voor een verbinding die is gemaakt in azure IoT connector voor FHIR in de naam van de **verbinding**. Deze verbinding wordt door Azure IoT Central-toepassing gebruikt om gesimuleerde telemetrie van apparaten te pushen naar Azure IoT connector voor FHIR.

8. Voer een naam in voor uw nieuwe Azure IoT Central-toepassing in **IOT Central-naam**. In deze toepassing wordt gebruikgemaakt van *continue patiënten monitoring* -sjabloon om een apparaat te simuleren.

9. Kies de locatie van uw IoT Central-toepassing in de vervolg keuzelijst **IOT Central locatie** . 

10. Selecteer **Controleren + maken**.

11. Lees de voorwaarden en selecteer vervolgens **Maken**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Als u de PowerShell-scripts lokaal wilt uitvoeren, voert u eerst `Connect-AzAccount` in om uw Azure-referenties in te stellen.

Als de `Microsoft.HealthcareApis`-resourceprovider nog niet voor uw abonnement is geregistreerd, kunt u deze registreren met de volgende interactieve code. Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **Probeer het nu** in de linkerbovenhoek van een willekeurig codeblok.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Als de `Microsoft.IoTCentral`-resourceprovider nog niet voor uw abonnement is geregistreerd, kunt u deze registreren met de volgende interactieve code. Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **Probeer het nu** in de linkerbovenhoek van een willekeurig codeblok.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Gebruik de volgende code om de Azure IoT-connector voor de FHIR-service te implementeren met behulp van de ARM-sjabloon.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Als de `Microsoft.HealthcareApis`-resourceprovider nog niet voor uw abonnement is geregistreerd, kunt u deze registreren met de volgende interactieve code. Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **Probeer het nu** in de linkerbovenhoek van een willekeurig codeblok.

```azurecli-interactive
az extension add --name healthcareapis
```

Als de `Microsoft.IoTCentral`-resourceprovider nog niet voor uw abonnement is geregistreerd, kunt u deze registreren met de volgende interactieve code.

```azurecli-interactive
az extension add --name azure-iot
```

Gebruik de volgende code om de Azure IoT-connector voor de FHIR-service te implementeren met behulp van de ARM-sjabloon.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Het duurt enkele minuten om de implementatie te voltooien. Noteer de namen van de Azure API voor de FHIR-service, de Azure IoT Central-toepassing en de resource groep die u gebruikt om de geïmplementeerde resources later te bekijken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om een overzicht van uw nieuwe Azure API for FHIR-service te bekijken:

1. Zoek en selecteer [Azure API for FHIR](https://portal.azure.com) in de **Azure Portal**.

2. Selecteer uw nieuwe server in de FHIR-lijst. De pagina **Overzicht** voor de nieuwe Azure API for FHIR-service wordt weergegeven.

3. Als u wilt controleren of het nieuwe FHIR-API-account is ingericht, selecteert u de link naast het **Eindpunt van FHIR-metagegevens** om de FHIR API-mogelijkheidsinstructie op te halen. De link heeft de indeling `https://<service-name>.azurehealthcareapis.com/metadata`. Als het account is ingericht, wordt een groot JSON-bestand weergegeven.

4. Als u wilt controleren of de nieuwe Azure IoT-connector voor FHIR is ingericht, selecteert u de **IOT-connector (preview)** in het navigatie menu aan de linkerkant om de pagina **IOT-connectors** te openen. Op de pagina moet de ingerichte Azure IoT-connector worden weer gegeven voor FHIR met de *status* waarde *online*, *verbindingen* waarde als *1*, en zowel *apparaattoewijzing* als *toewijzing van FHIR* weer geven is *geslaagd* .

5. Zoek en selecteer **IOT Central toepassingen** In de [Azure Portal](https://portal.azure.com).

6. Selecteer uw nieuwe service in de lijst met IoT Central toepassingen. De **overzichts** pagina voor de nieuwe IOT Central toepassing wordt weer gegeven.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Voer de volgende interactieve code uit om details te bekijken van uw Azure API for FHIR-service. U moet de naam van de nieuwe FHIR-service en de resource groep invoeren.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> Azure IoT connector voor FHIR biedt op dit moment geen Power shell-opdrachten. Als u wilt valideren of uw Azure IoT-connector voor FHIR correct is ingericht, gebruikt u het validatie proces dat op het tabblad **Portal** is geleverd.

Voer de volgende interactieve code uit om details over uw Azure IoT Central-toepassing weer te geven. U moet de naam van de nieuwe IoT Central-toepassing en de resource groep invoeren.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

Voer de volgende interactieve code uit om details te bekijken van uw Azure API for FHIR-service. U moet de naam van de nieuwe FHIR-service en de resource groep invoeren.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Azure IoT connector voor FHIR biedt op dit moment geen CLI-opdrachten. Als u wilt valideren of uw Azure IoT-connector voor FHIR correct is ingericht, gebruikt u het validatie proces dat op het tabblad **Portal** is geleverd.

Voer de volgende interactieve code uit om details over uw Azure IoT Central-toepassing weer te geven. U moet de naam van de nieuwe IoT Central-toepassing en de resource groep invoeren.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Uw IoT-gegevens verbinden met Azure IoT Connector for FHIR (preview)
> [!IMPORTANT]
> Het sjabloon voor apparaattoewijzing die in deze handleiding wordt gegeven, is ontworpen voor het werken met gegevensexport (verouderd) in IoT Central.

IoT Central toepassing biedt momenteel geen ARM-sjabloon of Power shell-en CLI-opdrachten voor het instellen van gegevens export. Volg de onderstaande instructies voor het gebruik van Azure Portal.  

Nadat u uw IoT Central-toepassing hebt geïmplementeerd, wordt telemetrische gegevens gegenereerd met de twee kant-en-klare gesimuleerde apparaten. Voor deze zelfstudie wordt de telemetrie van de *Smart Vitals Patch*-simulator via Azure IoT Connector for FHIR opgenomen. Als u uw IoT-gegevens wilt exporteren naar de Azure IoT-connector voor FHIR, kunt u [een gegevens export (verouderd) instellen in IOT Central](../iot-central/core/howto-export-data-legacy.md). Op de pagina gegevens export (verouderd):
- Kies *Azure Event Hubs* als de exportbestemming.
- Selecteer de waarde *Een verbindingsreeks gebruiken* voor het veld **Event Hubs-naamruimte**.
- Geef de verbindingsreeks van Azure IoT Connector for FHIR op die u in een vorige stap hebt verkregen voor het veld **Verbindingsreeks**.
- Laat de optie **Telemetrie** ingesteld op *Aan* voor het veld **Te exporteren gegevens**.

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Apparaatgegevens weergeven in Azure API for FHIR

U kunt de op FHIR gebaseerde observatie resource (s) die zijn gemaakt door Azure IoT connector voor FHIR op uw FHIR-server weer geven met behulp van postman. [Stel Postman in voor toegang tot Azure API for FHIR](access-fhir-postman-tutorial.md) en maak een `GET`-aanvraag naar `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` om FHIR-observatieresources met de hartslagwaarde weer te geven.

> [!TIP]
> Zorg ervoor dat uw gebruiker de juiste toegang heeft tot het Azure API for FHIR-gegevensvlak. Gebruik [Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](configure-azure-rbac.md) om gegevensvlakrollen toe te wijzen.

---

## <a name="clean-up-resources"></a>Resources opschonen

Als de resourcegroep niet meer nodig is, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zoek en selecteer [Resourcegroepen](https://portal.azure.com) in **Azure Portal**.

2. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.

3. Selecteer op de pagina **Overzicht** van uw resourcegroep de optie **Resourcegroep verwijderen**.

4. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Zie de [zelfstudie voor het maken en implementeren van uw eerste ARM-sjabloon](../azure-resource-manager/templates/template-tutorial-create-first-template.md) voor een stapsgewijze zelfstudie die u door het proces van het maken van een ARM-sjabloon leidt

## <a name="next-steps"></a>Volgende stappen

In deze quickstart-gids hebt u Azure IoT Connector for FHIR geïmplementeerd in uw Azure API for FHIR-resource. Selecteer een van de onderstaande volgende stappen voor meer informatie over Azure IoT Connector for FHIR:

Meer informatie over verschillende stadia van de gegevensstroom in Azure IoT Connector for FHIR.

>[!div class="nextstepaction"]
>[Gegevensstroom van Azure IoT Connector for FHIR](iot-data-flow.md)

Meer informatie over het configureren van IoT-connector met behulp van FHIR-toewijzingssjablonen.

>[!div class="nextstepaction"]
>[Toewijzingssjablonen in Azure IoT Connector for FHIR](iot-mapping-templates.md)

*In Azure Portal wordt Azure IoT Connector for FHIR aangeduid als IoT Connector (preview). FHIR is een gedeponeerd handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.
