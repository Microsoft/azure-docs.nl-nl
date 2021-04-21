---
title: Uw eerste functie maken met behulp van Azure Resource Manager-sjablonen
description: Maak en implementeer in Azure een eenvoudige met HTTP geactiveerde serverloze functie, door gebruik te maken van een ARM-sjabloon (Azure Resource Manager).
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 0badc233597c13228e9826ed062cc15828c65cf2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833378"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Quickstart: Azure Functions-resources maken en implementeren vanuit een ARM-sjabloon

In dit artikel gebruikt u een ARM-sjabloon (Azure Resource Manager) om een functie te maken die reageert op HTTP-aanvragen. 

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

### <a name="azure-account"></a>Azure-account 

Voordat u begint, moet u een Azure-account hebben met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

### <a name="create-a-local-functions-project"></a>Een lokaal Functions-project maken

Voor dit artikel moet een lokaal Functions-codeproject worden uitgevoerd in de Azure-resources die u maakt. Als u niet eerst een project maakt om te publiceren, kunt u de implementatiesectie van dit artikel niet voltooien. 

Kies een van de volgende tabbladen, klik op de koppeling en voltooi de sectie om een functie-app te maken in de taal van uw keuze:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Maak uw lokale Functions-project in uw gekozen taal in Visual Studio Code:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Een lokaal Functions-project maken in Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Opdrachtregel](#tab/command-line)

Maak uw lokale Functions-project in uw gekozen taal via de opdrachtregel:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

Nadat u het project lokaal hebt gemaakt, maakt u de resources die zijn vereist om de nieuwe functie uit te voeren in Azure. 

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/).

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

De volgende vier Azure-resources worden gemaakt met deze sjabloon:

+ [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): een Azure Storage-account maken, wat is vereist voor Functions.
+ [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): een serverloos hostingabonnement op basis van verbruik maken voor de functie-app.
+ [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): een functie-app maken.
+ [**microsoft.insights/components**](/azure/templates/microsoft.insights/components): een Application Insights-instantie maken voor bewaking.

## <a name="deploy-the-template"></a>De sjabloon implementeren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>De implementatie valideren

Vervolgens valideert u de hostingresources voor de functie-app die u hebt gemaakt, door het project te publiceren in Azure en het HTTP-eindpunt van de functie aan te roepen.

### <a name="publish-the-function-project-to-azure"></a>Het functieproject publiceren in Azure

Gebruik de volgende stappen om uw project te publiceren in de nieuwe Azure-resources:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

Kopieer in de uitvoer de URL van de HTTP-trigger. U kunt dit gebruiken om de functie te testen die wordt uitgevoerd in Azure. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Kies in **Een publicatiedoel kiezen** de optie **Azure Functions-abonnement op basis van verbruik** met **Bestaande selecteren**, en selecteer **Profiel maken**.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Een bestaand publicatiedoel kiezen":::

1. Kies uw **Abonnement**, vouw de resourcegroep uit, selecteer de functie-app en selecteer **OK**.

1. Nadat de publicatie is voltooid, kopieert u de **Site-URL**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="De site-URL kopiëren vanuit het publicatieoverzicht":::

1. Voeg het pad `/api/<FUNCTION_NAME>?name=Functions` toe, waarbij `<FUNCTION_NAME>` de naam van uw functie is. De URL die uw HTTP-triggerfunctie aanroept, heeft de volgende indeling:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

U kunt deze URL gebruiken om de HTTP-triggerfunctie te testen die wordt uitgevoerd in Azure.

# <a name="command-line"></a>[Opdrachtregel](#tab/command-line)

Als u uw lokale code wilt publiceren in een functie-app in Azure, gebruikt u de opdracht `publish`:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

Vervang in dit voorbeeld `<FUNCTION_APP_NAME>` door de naam van de functie-app. Mogelijk moet u zich opnieuw aanmelden met behulp van `az login`. 

Kopieer in de uitvoer de URL van de HTTP-trigger. U kunt dit gebruiken om de functie te testen die wordt uitgevoerd in Azure.

---

### <a name="invoke-the-function-on-azure"></a>De functie aanroepen in Azure

Plak de URL die u hebt gekopieerd voor de HTTP-aanvraag, in de adresbalk van de browser. Zorg ervoor dat de querytekenreeks `name` als `?name=Functions` is toegevoegd aan het eind van deze URL, en voer vervolgens de aanvraag uit. 

U ziet een antwoord dat er ongeveer als volgt uitziet:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende stap en een uitvoerbinding voor een Azure Storage-wachtrij toevoegt, kunt u alle resources het beste op dezelfde plaats laten staan. U gaat ze namelijk gebruiken in deze stap.

Gebruik anders de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Vervang `<RESOURCE_GROUP_NAME>` door de naam van de resourcegroep.

## <a name="next-steps"></a>Volgende stappen

Nu u uw eerste functie hebt gepubliceerd, kunt u meer leren door een uitvoerbinding toe te voegen aan de functie.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Opdrachtregel](#tab/command-line)

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md)

---
