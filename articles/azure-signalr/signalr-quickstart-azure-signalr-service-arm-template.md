---
title: 'Quickstart: Een Azure SignalR-service maken - ARM-sjabloon'
description: In deze quickstart leert u hoe u een Azure SignalR-service maakt met een Azure Resource Manager-sjabloon (ARM-sjabloon).
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: zhshang
ms.date: 10/02/2020
ms.openlocfilehash: 4ab029048b37a4dcb44ef405249dcb9e20de70cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841612"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Quickstart: een Azure SignalR-service implementeren met behulp van een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een ARM-sjabloon (Azure Resource Manager) gebruikt om een Azure SignalR-service te maken. U kunt de Azure SignalR-service implementeren via Azure Portal, PowerShell of de CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend zodra u zich hebt aangemeld.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Knop voor het implementeren van Azure SignalR Service in Azure met behulp van een ARM-sjabloon in Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

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

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Het sjabloon definieert één Azure-resource:

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>De sjabloon implementeren

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer de volgende koppeling om Azure SignalR Service te implementeren met behulp van de ARM-sjabloon in Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Knop voor het implementeren van Azure SignalR Service in Azure met behulp van de ARM-sjabloon in Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Op de pagina **Een Azure SignalR-service implementeren**:

1. Wijzig desgewenst **Abonnement** van de standaardinstelling in een ander abonnement.

2. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**, voer een naam in voor de nieuwe resourcegroep en selecteer **OK**.

3. Als u een nieuwe resourcegroep hebt gemaakt, selecteert u een **Regio** voor de resourcegroep.

4. Voer desgewenst een nieuwe **naam** en de **locatie** in (zoals **eastus2**) van de Azure SignalR-service. Als u geen naam opgeeft, wordt deze automatisch gegenereerd. De locatie voor de Azure SignalR-service kan verschillen van de regio van de resourcegroep. Als u geen locatie opgeeft, wordt deze ingesteld op de regio van de resourcegroep.

5. Kies de **prijscategorie** (**Free_F1** of **Standard_S1**), voer de **capaciteit** (het aantal SignalR-eenheden) in en kies de **servicemodus** **Standaard** (hiervoor is een centrale server vereist), **Serverloos** (er wordt geen serververbinding toegestaan) of **Klassiek** (alleen routeren naar de centrale server als de hub is verbonden met de server). Schakel **Connectiviteitslogboeken** of **Messaging-logboeken** in.

    > [!NOTE]
    > De capaciteit van de prijscategorie **Free_F1** is beperkt tot 1 eenheid.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Schermopname van de ARM-sjabloon voor het maken van een Azure SignalR-service in Azure Portal.":::

6. Selecteer **Controleren + maken**.

7. Lees de voorwaarden en selecteer vervolgens **Maken**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Als u de PowerShell-scripts lokaal wilt uitvoeren, voert u eerst `Connect-AzAccount` in om uw Azure-referenties in te stellen.

Gebruik de volgende code om de Azure SignalR-service te implementeren met behulp van de ARM-sjabloon. U wordt gevraagd om de volgende items op te geven:

* De naam en regio van de nieuwe Azure SignalR-service
* De naam en regio van een nieuwe resourcegroep
* De Azure-prijscategorie (**Free_F1** of **Standard_S1**)
* De capaciteit van de SignalR-eenheid (1, 2, 5, 10, 20, 50 of 100)
  > [!NOTE]
  > De capaciteit van de prijscategorie **Free_F1** is beperkt tot 1 eenheid.
* De servicemodus: **Standaard** (een centrale server is vereist), **Serverloos** (verbinding met de server is niet toegestaan) of **Klassiek** (alleen routeren naar een centrale server als de hub is verbonden met de server)
* Of logboeken voor connectiviteit of berichten moeten worden ingeschakeld (**waar** of **niet waar**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Gebruik de volgende code om de Azure SignalR-service te implementeren met behulp van de ARM-sjabloon. U wordt gevraagd om de volgende items op te geven:

* De naam en regio van de nieuwe Azure SignalR-service
* De naam en regio van een nieuwe resourcegroep
* De Azure-prijscategorie (**Free_F1** of **Standard_S1**)
* De capaciteit van de SignalR-eenheid (1, 2, 5, 10, 20, 50 of 100)
    > [!NOTE]
    > De capaciteit van de prijscategorie **Free_F1** is beperkt tot 1 eenheid.
* De servicemodus: **Standaard** (een centrale server is vereist), **Serverloos** (verbinding met de server is niet toegestaan) of **Klassiek** (alleen routeren naar een centrale server als de hub is verbonden met de server)
* Of logboeken voor connectiviteit of berichten moeten worden ingeschakeld (**waar** of **niet waar**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> De implementatie kan enkele minuten duren. Noteer de namen van de Azure SignalR-service en de resourcegroep die u gebruikt om de geïmplementeerde resources later te bekijken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om een overzicht van uw nieuwe Azure SignalR-service te bekijken:

1. Zoek en selecteer **SignalR** in de [Azure-portal](https://portal.azure.com).

2. Selecteer uw nieuwe SignalR-service in de lijst. De pagina **Overzicht** voor de nieuwe Azure SignalR-service wordt weergegeven.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Voer de volgende interactieve code uit om details te bekijken van uw Azure SignalR-service. U moet de naam van de nieuwe server en de resourcegroep opgeven.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Voer de volgende interactieve code uit om details te bekijken van uw Azure SignalR-service. U moet de naam van de nieuwe server en de resourcegroep opgeven.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
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

## <a name="next-steps"></a>Volgende stappen

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een ARM-sjabloon leidt:

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
