---
title: 'Zelfstudie: een gekoppelde sjabloon implementeren'
description: Meer informatie over het implementeren van een gekoppelde sjabloon
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: b69d4e8d2748cffec6a4f0cddfa20e6722653c76
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519011"
---
# <a name="tutorial-deploy-a-linked-template"></a>Zelfstudie: Een gekoppelde sjabloon implementeren

In de [eerdere zelfstudies](./deployment-tutorial-local-template.md)hebt u geleerd hoe u een sjabloon kunt implementeren die is opgeslagen op de lokale computer. Als u complexe oplossingen wilt implementeren, kunt u een sjabloon opsplitsen in een groot aantal sjablonen en deze sjablonen implementeren via een hoofdsjabloon. In deze zelfstudie leert u hoe u een hoofdsjabloon met de verwijzing naar een gekoppelde sjabloon implementeert. Wanneer de hoofdsjabloon wordt geïmplementeerd, wordt de implementatie van de gekoppelde sjabloon geactiveerd. U leert ook hoe u de sjablonen kunt opslaan en beveiligen met behulp van SAS-tokens. Dit neemt ongeveer **12 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de vorige zelfstudie te voltooien, maar dit is niet verplicht.

## <a name="review-template"></a>Sjabloon controleren

In de vorige zelfstudies hebt u een sjabloon geïmplementeerd waarmee u een opslagaccount, App Service-plan en web-app hebt gemaakt. Daarvoor hebt u deze sjabloon gebruikt:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Een gekoppelde sjabloon maken

U kunt de resource van het opslagaccount verplaatsen naar een gekoppelde sjabloon:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

De volgende sjabloon is de hoofdsjabloon. Het gemarkeerde object `Microsoft.Resources/deployments` laat zien hoe u een gekoppelde sjabloon aanroept. De gekoppelde sjabloon kan niet worden opgeslagen als een lokaal bestand of een bestand dat alleen beschikbaar is in uw lokale netwerk. U kunt ofwel een URI-waarde van de gekoppelde sjabloon opgeven die HTTP of HTTPS bevat, of de eigenschap _relativePath_ gebruiken om een extern gekoppelde sjabloon te implementeren op een locatie ten opzichte van de bovenliggende sjabloon. Een optie is om zowel de hoofd sjabloon als de gekoppelde sjabloon in een opslag account te plaatsen.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>De gekoppelde sjabloon opslaan

Beide hoofd sjablonen en gekoppelde sjablonen worden opgeslagen in GitHub:

Met het volgende Power shell-script maakt u een opslag account, maakt u een container en kopieert u de twee sjablonen van een GitHub-opslag plaats naar de container. Deze twee sjablonen zijn:

- De hoofdsjabloon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- De gekoppelde sjabloon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Selecteer **Uitproberen** om Cloud Shell te openen, selecteer **Kopiëren** om het PowerShell-script te kopiëren en klik met de rechtermuisknop op het deelvenster van de shell om het script te plakken:

> [!IMPORTANT]
> Opslagaccountnamen moeten tussen 3 en 24 tekens lang zijn en mogen alleen getallen en kleine letters bevatten. De naam moet uniek zijn. De opslagaccountnaam in de sjabloon is de projectnaam met achteraan toegevoegd: **store**. De projectnaam moet tussen 3 en 11 tekens lang zijn. De projectnaam moet dus voldoen aan de vereisten voor de opslagaccountnaam en minder dan 11 tekens lang zijn.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

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

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Sjabloon implementeren

Als u sjablonen wilt implementeren in een opslag account, genereert u een SAS-token en levert u dit aan de para meter _-query_ server. Stel de vervaltijd zo in, dat er genoeg tijd is om de implementatie te voltooien. De blobs die de sjablonen bevatten, zijn alleen toegankelijk voor de eigenaar van het account. Wanneer u echter een SAS-token voor een BLOB maakt, is de BLOB toegankelijk voor iedereen met die SAS-token. Als een andere gebruiker de URI en het SAS-token onderschept, kan die gebruiker toegang krijgen tot de sjabloon. Een SAS-token is een goede manier om de toegang tot uw sjablonen te beperken, maar u mag geen gevoelige gegevens zoals wachtwoorden rechtstreeks in de sjabloon toevoegen.

Zie [Resourcegroep maken](./deployment-tutorial-local-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt.

> [!NOTE]
> In de onderstaande Azure CLI-code is `date`-parameter `-d` een ongeldig argument in macOS. MacOS-gebruikers die 2 uur willen toevoegen aan de huidige tijd in de terminal op macOS, moeten `-v+2H` gebruiken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
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
