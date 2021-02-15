---
title: 'Zelfstudie: een lokaal Azure Resource Manager-sjabloon implementeren'
description: Ontdek hoe u een ARM-sjabloon (Azure Resource Manager) kunt implementeren vanaf de lokale computer
ms.date: 02/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: d8d54acfa345994edcc401170e70495b3826bfdf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384228"
---
# <a name="tutorial-deploy-a-local-arm-template"></a>Zelfstudie: Een lokale ARM-sjabloon implementeren

Ontdek hoe u een ARM-sjabloon (Azure Resource Manager) kunt implementeren vanaf de lokale machine. Dit neemt ongeveer **8 minuten** in beslag.

Deze zelfstudie is de eerste van een reeks. In deze reeks deelt u de sjabloon op in modules door een gekoppelde sjabloon te maken. U slaat het gekoppelde sjabloon op in een opslagaccount en beveiligt dit met een SAS-token. U leert ook hoe u een DevOps-pijplijn maakt om sjablonen te implementeren. Deze reeks gaat over de implementatie van sjablonen. Als u wilt leren hoe u een sjabloon kunt implementeren, bekijk dan de [zelfstudies voor beginners](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Hulpprogramma's installeren

Laten we er allereerst voor zorgen dat u de nodige hulpprogramma's heeft om sjablonen te implementeren.

### <a name="command-line-deployment"></a>Implementatie via opdrachtregels

U heeft Azure PowerShell of Azure CLI nodig om het sjabloon te implementeren. Zie voor installatie-instructies:

- [Azure PowerShell installeren](/powershell/azure/install-az-ps)
- [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows)
- [Azure CLI installeren in Linux](/cli/azure/install-azure-cli-linux)
- [Azure CLI installeren in macOS](/cli/azure/install-azure-cli-macos)

Nadat u Azure PowerShell of Azure CLI hebt geïnstalleerd moet u zich voor de eerste keer aanmelden. Bekijk [Aanmelden - PowerShell](/powershell/azure/install-az-ps#sign-in) of [Aanmelden - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) voor ondersteuning.

### <a name="editor-optional"></a>Editor (optioneel)

Sjablonen zijn JSON-bestanden. Om sjablonen te controleren/bewerken heeft u een goede JSON-editor nodig. We raden Visual Studio Code met de extensie Resource Manager Tools aan. Als u deze hulpprogramma's wilt installeren, raadpleegt u [quickstart: ARM-sjablonen maken met Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="review-template"></a>Sjabloon controleren

Het sjabloon implementeert een opslagaccount, App Service-plan en webtoepassing. Als u de sjabloon wilt aanmaken, doorloop dan de [zelfstudie over Quickstart-sjablonen](template-tutorial-quickstart-template.md). Dat is echter geen vereiste om deze zelfstudie te voltooien.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Opslagaccountnamen moeten tussen 3 en 24 tekens lang zijn en mogen alleen getallen en kleine letters bevatten. De naam moet uniek zijn. De opslagaccountnaam in de sjabloon is de projectnaam met achteraan toegevoegd: **store**. De projectnaam moet tussen 3 en 11 tekens lang zijn. De projectnaam moet dus voldoen aan de vereisten voor de opslagaccountnaam en minder dan 11 tekens lang zijn.

Sla een kopie van de sjabloon op de lokale computer op met de extensie _.json_, bijvoorbeeld _azuredeploy.json_. Verderop in deze sjabloon implementeert u deze sjabloon.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan met uw Azure-referenties om aan de slag te gaan met Azure PowerShell/Azure CLI en een sjabloon te implementeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az login
```

---

Als u meerdere Azure-abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken. Vervang `[SubscriptionID/SubscriptionName]` en de vierkante haken `[]` door uw abonnementsgegevens:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Een resourcegroep maken

Wanneer u een sjabloon implementeert, geeft u een resourcegroep op die de resources zal bevatten. Voordat u de opdracht voor de implementatie uitvoert, maakt u de resourcegroep met behulp van Azure CLI of Azure PowerShell. Selecteer de tabbladen in de volgende codesectie om te kiezen tussen Azure PowerShell en Azure CLI. De CLI-voorbeelden in dit artikel zijn geschreven voor de Bash-shell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik een of beide implementatie-opties om de sjabloon te implementeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Zie [Resources implementeren met ARM-sjablonen en Azure PowerShell](./deploy-powershell.md) voor meer informatie over de implementatie van sjablonen met Azure PowerShell.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Zie [Resources implementeren met ARM-sjablonen en Azure CLI](./deploy-cli.md) voor meer informatie over de implementatie van sjablonen met Azure CLI.

---

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd resources op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een lokale sjabloon kunt implementeren. In de volgende zelfstudie splitst u de sjabloon op in een hoofdsjabloon en een gekoppelde sjabloon en leert u hoe u de gekoppelde sjabloon kunt opslaan en beveiligen.

> [!div class="nextstepaction"]
> [Een gekoppelde sjabloon implementeren](./deployment-tutorial-linked-template.md)
