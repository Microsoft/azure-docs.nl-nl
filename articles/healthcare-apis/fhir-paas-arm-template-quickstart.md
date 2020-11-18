---
title: 'Snelstart: Azure API for FHIR implementeren met behulp van een ARM-sjabloon'
description: In deze snelstart leert u hoe u Azure API for Fast Healthcare Interoperability Resources (FHIR®) kunt implementeren, met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: cavoeg
ms.date: 10/06/2020
ms.openlocfilehash: 0cc31c26a71775dfdf3e9ee94852fda8af2c0cfc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844536"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Snelstart: Een ARM-sjabloon gebruiken om Azure API for FHIR te implementeren

In deze snelstart leert u hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) kunt gebruiken voor het implementeren van Azure API voor Fast Healthcare Interoperability Resources (FHIR®). U kunt Azure API for FHIR implementeren via de Azure Portal, PowerShell of CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend zodra u zich hebt aangemeld.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeer in Azure een Azure API for FHIR-service met een ARM-sjabloon in de Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

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

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/).

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

Het sjabloon definieert één Azure-resource:

* **Microsoft.HealthcareApis/services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>De sjabloon implementeren

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer de volgende koppeling om de Azure for FHIR te implementeren met behulp van de ARM-sjabloon in Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeer in Azure een Azure API for FHIR-service met de ARM-sjabloon in de Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

Op de pagina **Azure API for FHIR implementeren**:

1. Indien gewenst, wijzigt u het **Abonnement** van de standaardinstelling naar een ander abonnement.

2. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**, voer een naam in voor de nieuwe resourcegroep en selecteer **OK**.

3. Als u een nieuwe resourcegroep hebt gemaakt, selecteert u een **Regio** voor de resourcegroep.

4. Voer een nieuwe **Servicenaam** in en kies de **Locatie** van de Azure API for FHIR. De locatie kan hetzelfde zijn als of anders zijn dan de regio van de resourcegroep.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="De Azure API for FHIR implementeren met behulp van de ARM-sjabloon in de Azure Portal.":::

5. Selecteer **Controleren + maken**.

6. Lees de voorwaarden en selecteer vervolgens **Maken**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Als u de PowerShell-scripts lokaal wilt uitvoeren, voert u eerst `Connect-AzAccount` in om uw Azure-referenties in te stellen.

Als de `Microsoft.HealthcareApis`-resourceprovider nog niet voor uw abonnement is geregistreerd, kunt u deze registreren met de volgende interactieve code. Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **Probeer het nu** in de linkerbovenhoek van een willekeurig codeblok.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Gebruik de volgende code om de Azure API for FHIR-service te implementeren met de ARM-sjabloon. De code vraagt u om de nieuwe FHIR-servicenaam, de naam van een nieuwe resourcegroep en de locaties voor elk van deze.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Als de `Microsoft.HealthcareApis`-resourceprovider nog niet voor uw abonnement is geregistreerd, kunt u deze registreren met de volgende interactieve code. Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **Probeer het nu** in de linkerbovenhoek van een willekeurig codeblok.

```azurecli-interactive
az extension add --name healthcareapis
```

Gebruik de volgende code om de Azure API for FHIR-service te implementeren met de ARM-sjabloon. De code vraagt u om de nieuwe FHIR-servicenaam, de naam van een nieuwe resourcegroep en de locaties voor elk van deze.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Het duurt enkele minuten om de implementatie te voltooien. Noteer de namen van de Azure API for FHIR-service en de resourcegroep die u gebruikt om de geïmplementeerde resources later te bekijken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om een overzicht van uw nieuwe Azure API for FHIR-service te bekijken:

1. Zoek en selecteer [Azure API for FHIR](https://portal.azure.com) in de **Azure Portal**.

2. Selecteer uw nieuwe server in de FHIR-lijst. De pagina **Overzicht** voor de nieuwe Azure API for FHIR-service wordt weergegeven.

3. Als u wilt controleren of het nieuwe FHIR-API-account is ingericht, selecteert u de link naast het **Eindpunt van FHIR-metagegevens** om de FHIR API-mogelijkheidsinstructie op te halen. De link heeft de indeling `https://<service-name>.azurehealthcareapis.com/metadata`. Als het account is ingericht, wordt een groot JSON-bestand weergegeven.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Voer de volgende interactieve code uit om details te bekijken van uw Azure API for FHIR-service. U moet de naam van de nieuwe server en de resourcegroep opgeven.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Voer de volgende interactieve code uit om details te bekijken van uw Azure API for FHIR-service. U moet de naam van de nieuwe server en de resourcegroep opgeven.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

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

In deze quickstart hebt u de Azure-API voor FHIR geïmplementeerd in uw abonnement. Als u aanvullende instellingen wilt configureren in uw Azure-API voor FHIR, gaat u verder met de instructiegids Aanvullende instellingen. Als u klaar bent om te beginnen met het gebruik van Azure API for FHIR, leest u de informatie over het registreren van toepassingen.

>[!div class="nextstepaction"]
>[Aanvullende instellingen in de Azure-API voor FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Overzicht toepassingen registreren](fhir-app-registration.md)
