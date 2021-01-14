---
title: 'Zelfstudie: een gekoppelde sjabloon implementeren'
description: Meer informatie over het implementeren van een gekoppelde sjabloon
ms.date: 01/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4ec49fad35e958f010461abf2ee0e3dab8077d55
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134191"
---
# <a name="tutorial-deploy-a-linked-template"></a>Zelfstudie: Een gekoppelde sjabloon implementeren

In de [eerdere zelfstudies](./deployment-tutorial-local-template.md)hebt u geleerd hoe u een sjabloon kunt implementeren die is opgeslagen op de lokale computer. Als u complexe oplossingen wilt implementeren, kunt u een sjabloon opsplitsen in een groot aantal sjablonen en deze sjablonen implementeren via een hoofdsjabloon. In deze zelfstudie leert u hoe u een hoofdsjabloon met de verwijzing naar een gekoppelde sjabloon implementeert. Wanneer de hoofdsjabloon wordt geïmplementeerd, wordt de implementatie van de gekoppelde sjabloon geactiveerd. U leert ook hoe u de gekoppelde sjabloon kunt opslaan en beveiligen met behulp van een SAS-token. Dit neemt ongeveer **12 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de vorige zelfstudie te voltooien, maar dit is niet verplicht.

## <a name="review-template"></a>Sjabloon controleren

In de vorige zelfstudies hebt u een sjabloon geïmplementeerd waarmee u een opslagaccount, App Service-plan en web-app hebt gemaakt. Daarvoor hebt u deze sjabloon gebruikt:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Een gekoppelde sjabloon maken

U kunt de resource van het opslagaccount verplaatsen naar een gekoppelde sjabloon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

De volgende sjabloon is de hoofdsjabloon. Het gemarkeerde object `Microsoft.Resources/deployments` laat zien hoe u een gekoppelde sjabloon aanroept. De gekoppelde sjabloon kan niet worden opgeslagen als een lokaal bestand of een bestand dat alleen beschikbaar is in uw lokale netwerk. U kunt alleen een URI-waarde opgeven die HTTP of HTTP bevat. De sjabloon moet toegankelijk zijn voor Resource Manager. U kunt ook de gekoppelde sjabloon in een opslagaccount plaatsen en de URI voor dat item gebruiken. De URI wordt doorgegeven aan de sjabloon met behulp van een parameter. Zie de definitie van de gemarkeerde parameter.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Sla een kopie van de hoofdsjabloon op de lokale computer op met de extensie _.json_, bijvoorbeeld _azuredeploy.json_. U hoeft geen kopie van de gekoppelde sjabloon op te slaan. De gekoppelde sjabloon wordt gekopieerd van een GitHub-opslagplaats naar een opslagaccount.

## <a name="store-the-linked-template"></a>De gekoppelde sjabloon opslaan

Met het volgende PowerShell-script maakt u een opslagaccount, maakt u een container en kopieert u de gekoppelde sjabloon vanuit een GitHub-opslagplaats naar de container. Er wordt een kopie van de gekoppelde sjabloon opgeslagen in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Selecteer **Uitproberen** om Cloud Shell te openen, selecteer **Kopiëren** om het PowerShell-script te kopiëren en klik met de rechtermuisknop op het deelvenster van de shell om het script te plakken:

> [!IMPORTANT]
> Opslagaccountnamen moeten tussen 3 en 24 tekens lang zijn en mogen alleen getallen en kleine letters bevatten. De naam moet uniek zijn. De opslagaccountnaam in de sjabloon is de projectnaam met achteraan toegevoegd: **store**. De projectnaam moet tussen 3 en 11 tekens lang zijn. De projectnaam moet dus voldoen aan de vereisten voor de opslagaccountnaam en minder dan 11 tekens lang zijn.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Sjabloon implementeren

Als u een privésjabloon in een opslagaccount wilt implementeren, genereert u een SAS-token en neemt u het op in de URI voor de sjabloon. Stel de vervaltijd zo in, dat er genoeg tijd is om de implementatie te voltooien. De blob met de sjabloon is alleen toegankelijk voor de eigenaar van het account. Maar als u een SAS-token voor de blob maakt, is de blob toegankelijk voor iedereen met die URI. Als een andere gebruiker de URI onderschept, kan die gebruiker toegang krijgen tot de sjabloon. Een SAS-token is een goede manier om de toegang tot uw sjablonen te beperken, maar u mag geen gevoelige gegevens zoals wachtwoorden rechtstreeks in de sjabloon toevoegen.

Zie [Resourcegroep maken](./deployment-tutorial-local-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt.

> [!NOTE]
> In de onderstaande Azure CLI-code is `date`-parameter `-d` een ongeldig argument in macOS. MacOS-gebruikers die 2 uur willen toevoegen aan de huidige tijd in de terminal op macOS, moeten `-v+2H` gebruiken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd resources op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een gekoppelde sjabloon kunt implementeren. In de volgende zelfstudie leert u hoe u een DevOps-pijplijn voor het implementeren van een sjabloon maakt.

> [!div class="nextstepaction"]
> [Een pijplijn maken](./deployment-tutorial-pipeline.md)
