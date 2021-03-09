---
title: Een werk ruimte met Azure Resource Manager sjabloon maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van een Azure Resource Manager sjabloon voor het maken van een nieuwe Azure Machine Learning-werk ruimte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.openlocfilehash: d64dfdad0c51950228288b704b3fdb7579496abb
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488066"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Een Azure Resource Manager sjabloon gebruiken om een werk ruimte te maken voor Azure Machine Learning


<br>

In dit artikel leert u verschillende manieren om een Azure Machine Learning-werk ruimte te maken met behulp van Azure Resource Manager sjablonen. Met een resource manager-sjabloon kunt u eenvoudig resources maken als één gecoördineerde bewerking. Een sjabloon is een JSON-document waarmee de benodigde resources voor een implementatie worden gedefinieerd. Het kan ook implementatie parameters opgeven. Para meters worden gebruikt om invoer waarden op te geven wanneer u de sjabloon gebruikt.

Zie [een toepassing implementeren met Azure Resource Manager sjabloon](../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Als u een sjabloon van een CLI wilt gebruiken, moet u [Azure PowerShell](/powershell/azure/?view=azps-1.2.0) of de [Azure cli](/cli/azure/install-azure-cli).

* Voor sommige scenario's moet u een ondersteunings ticket openen. Deze scenario's zijn:

    * __Werkruimte met Private Link, met een door de klant beheerde sleutel__
    * __Azure Container Registry voor de werkruimte achter uw virtuele netwerk__

    Zie [Quota's beheren en verhogen](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)voor meer informatie.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="workspace-resource-manager-template"></a>Werkruimte Resource Manager-sjabloon

De Azure Resource Manager sjabloon die in dit document wordt gebruikt, vindt u in de map [201-machine learning-Advanced](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) van de GitHub-opslag plaats voor Azure Quick Start-sjablonen.

Met deze sjabloon worden de volgende Azure-Services gemaakt:

* Azure Storage-account
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-werkruimte

De resource groep is de container waarin de services worden bewaard. De verschillende services zijn vereist voor de Azure Machine Learning-werk ruimte.

De voorbeeld sjabloon heeft twee **vereiste** para meters:

* De **locatie** waar de resources worden gemaakt.

    De sjabloon maakt gebruik van de locatie die u selecteert voor de meeste resources. De uitzonde ring is de Application Insights-service, die niet beschikbaar is op alle locaties die de andere services zijn. Als u een locatie selecteert waar deze niet beschikbaar is, wordt de service gemaakt op de locatie Zuid-Centraal vs.

* De naam van de **werk ruimte**. Dit is de beschrijvende namen van de Azure machine learning-werk ruimte.

    > [!NOTE]
    > De naam van de werk ruimte is niet hoofdletter gevoelig.

    De namen van de andere services worden wille keurig gegenereerd.

> [!TIP]
> Terwijl de sjabloon die aan dit document is gekoppeld, een nieuwe Azure Container Registry maakt, kunt u ook een nieuwe werk ruimte maken zonder container register te maken. Er wordt een gemaakt wanneer u een bewerking uitvoert waarvoor een container register is vereist. Bijvoorbeeld training of implementatie van een model.
>
> U kunt ook verwijzen naar een bestaand container register of opslag account in de Azure Resource Manager-sjabloon in plaats van een nieuwe te maken. Als u dit doet, moet u [een beheerde identiteit](how-to-use-managed-identities.md) (preview) gebruiken of [het beheerders account inschakelen](../container-registry/container-registry-authentication.md#admin-account) voor het container register.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Zie de volgende artikelen voor meer informatie over sjablonen:

* [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)
* [Een toepassing implementeren met Azure Resource Manager sjablonen](../azure-resource-manager/templates/deploy-powershell.md)
* [Resource typen van micro soft. MachineLearningServices](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Sjabloon implementeren

Als u uw sjabloon wilt implementeren, moet u een resource groep maken.

Zie de sectie [Azure Portal](#use-the-azure-portal) als u de Graphical User Interface wilt gebruiken.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

Zodra de resource groep is gemaakt, implementeert u de sjabloon met de volgende opdracht:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Standaard zijn alle resources die zijn gemaakt als onderdeel van de sjabloon nieuw. U hebt echter ook de mogelijkheid om bestaande resources te gebruiken. Als u aanvullende para meters voor de sjabloon opgeeft, kunt u bestaande resources gebruiken. Als u bijvoorbeeld een bestaand opslag account wilt gebruiken, stelt u de waarde **storageAccountOption** in op **bestaande** en geeft u de naam van uw opslag account op in de para meter **storageAccountName** .

> [!IMPORTANT]
> Als u een bestaand Azure Storage account wilt gebruiken, kan het geen Premium-account zijn (Premium_LRS en Premium_GRS). Het kan ook geen hiërarchische naam ruimte hebben (gebruikt met Azure Data Lake Storage Gen2). Geen enkele Premium-opslag of hiërarchische naam ruimte wordt ondersteund met het standaard opslag account van de werk ruimte. Geen enkele Premium-opslag of hiërarchische naam ruimten worden ondersteund met het _standaard_ opslag account van de werk ruimte. U kunt Premium-opslag of een hiërarchische naam ruimte gebruiken met _niet-standaard_ opslag accounts.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Een versleutelde werk ruimte implementeren

In de volgende voorbeeld sjabloon ziet u hoe u een werk ruimte met drie instellingen maakt:

* Sterke vertrouwelijkheids instellingen inschakelen voor de werk ruimte. Hiermee maakt u een nieuw Cosmos DB-exemplaar.
* Versleuteling inschakelen voor de werk ruimte.
* Maakt gebruik van een bestaande Azure Key Vault om door de klant beheerde sleutels op te halen. Door de klant beheerde sleutels worden gebruikt om een nieuw Cosmos DB-exemplaar voor de werk ruimte te maken.

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> Als een werk ruimte eenmaal is gemaakt, kunt u de instellingen voor vertrouwelijke gegevens, versleuteling, sleutel kluis-ID of sleutel-id's niet wijzigen. Als u deze waarden wilt wijzigen, moet u een nieuwe werk ruimte maken met behulp van de nieuwe waarden.

Zie [versleuteling bij rest](concept-data-encryption.md#encryption-at-rest)voor meer informatie.

> [!IMPORTANT]
> Er zijn enkele specifieke vereisten waaraan uw abonnement moet voldoen voordat u deze sjabloon gebruikt:
> * U moet een bestaande Azure Key Vault hebben die een versleutelings sleutel bevat.
> * De Azure Key Vault moet zich in dezelfde regio bevinden waar u van plan bent de Azure Machine Learning-werk ruimte te maken.
> * U moet de ID van de Azure Key Vault en de URI van de versleutelings sleutel opgeven.

__Als u de waarden__ voor de `cmk_keyvault` (ID van de Key Vault) en de `resource_cmk_uri` para meters (Key URI) wilt ophalen die nodig zijn voor deze sjabloon, gebruikt u de volgende stappen:    

1. Als u de Key Vault-ID wilt ophalen, gebruikt u de volgende opdracht:  

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` .  

1. Als u de waarde voor de URI voor de door de klant beheerde sleutel wilt ophalen, gebruikt u de volgende opdracht:    

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` . 

> [!IMPORTANT]  
> Als een werk ruimte eenmaal is gemaakt, kunt u de instellingen voor vertrouwelijke gegevens, versleuteling, sleutel kluis-ID of sleutel-id's niet wijzigen. Als u deze waarden wilt wijzigen, moet u een nieuwe werk ruimte maken met behulp van de nieuwe waarden.

Als u het gebruik van door de klant beheerde sleutels wilt inschakelen, stelt u de volgende para meters in bij het implementeren van de sjabloon:

* **Encryption_status** **ingeschakeld**.
* **cmk_keyvault** de `cmk_keyvault` waarde die in de vorige stappen is verkregen.
* **resource_cmk_uri** de `resource_cmk_uri` waarde die in de vorige stappen is verkregen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

Wanneer u een door de klant beheerde sleutel gebruikt, maakt Azure Machine Learning een secundaire resource groep die de Cosmos DB instantie bevat. Zie [versleuteling bij rest-Cosmos DB](concept-data-encryption.md#encryption-at-rest)voor meer informatie.

U kunt voor uw gegevens een extra configuratie opgeven door de para meter **confidential_data** in te stellen op **True**. Dit doet u door de volgende handelingen uit te voeren:

* Hiermee wordt het versleutelen van de lokale werk schijf voor Azure Machine Learning compute-clusters gestart, zodat u geen eerdere clusters in uw abonnement hebt gemaakt. Als u eerder een cluster in het abonnement hebt gemaakt, opent u een ondersteunings ticket om versleuteling te hebben van de Scratch-schijf die is ingeschakeld voor uw reken clusters.
* Hiermee wordt de lokale Scratch schijf opgeschoond tussen uitvoeringen.
* Hiermee wordt de referenties voor het opslag account, het container register en het SSH-account veilig door gegeven aan uw reken clusters met behulp van sleutel kluis.
* IP-filtering inschakelen om ervoor te zorgen dat de onderliggende batch-groepen niet kunnen worden aangeroepen door andere externe services dan AzureMachineLearningService.

    > [!IMPORTANT]
    > Als een werk ruimte eenmaal is gemaakt, kunt u de instellingen voor vertrouwelijke gegevens, versleuteling, sleutel kluis-ID of sleutel-id's niet wijzigen. Als u deze waarden wilt wijzigen, moet u een nieuwe werk ruimte maken met behulp van de nieuwe waarden.

  Zie [versleuteling bij rest](concept-data-encryption.md#encryption-at-rest)voor meer informatie.

## <a name="deploy-workspace-behind-a-virtual-network"></a>Een werk ruimte implementeren achter een virtueel netwerk

Door de `vnetOption` parameter waarde in te stellen op `new` of `existing` , kunt u de resources maken die worden gebruikt door een werk ruimte achter een virtueel netwerk.

> [!IMPORTANT]
> Alleen de SKU ' Premium ' wordt ondersteund voor container Registry.

> [!IMPORTANT]
> Application Insights biedt geen ondersteuning voor implementatie achter een virtueel netwerk.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Alleen werk ruimte implementeren achter een persoonlijk eind punt

Als uw gekoppelde resources zich niet achter een virtueel netwerk bevinden, kunt u de **privateEndpointType** -para meter instellen op `AutoAproval` of `ManualApproval` de werk ruimte implementeren achter een persoonlijk eind punt. Dit kan worden gedaan voor zowel nieuwe als bestaande werk ruimten. Wanneer u een bestaande werk ruimte bijwerkt, vult u de sjabloon parameters in met de informatie van de bestaande werk ruimte.

> [!IMPORTANT]
> Het gebruik van een Azure Machine Learning werk ruimte met een persoonlijke koppeling is niet beschikbaar in de Azure Government regio's.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Een nieuw virtueel netwerk gebruiken

Als u een bron achter een nieuw virtueel netwerk wilt implementeren, stelt u de **vnetOption** in op **Nieuw** samen met de instellingen van het virtuele netwerk voor de betreffende resource. In de onderstaande implementatie ziet u hoe u een werk ruimte implementeert met de opslag account bron achter een nieuw virtueel netwerk.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

U kunt ook meerdere of alle afhankelijke resources achter een virtueel netwerk implementeren.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Bestaande virtuele netwerk & bronnen gebruiken

Als u een werk ruimte wilt implementeren met bestaande gekoppelde resources, moet u de para meter **vnetOption** instellen op **bestaande** in combi natie met subnet-para meters. U moet echter service-eind punten in het virtuele netwerk maken voor elk van de resources **vóór** de implementatie. Net als bij nieuwe implementaties van virtuele netwerken kunt u een of meer van uw resources achter een virtueel netwerk hebben.

> [!IMPORTANT]
> Het subnet moet een `Microsoft.Storage` service-eind punt hebben

> [!IMPORTANT]
> Voor subnetten is het maken van persoonlijke eind punten niet toegestaan. Schakel het persoonlijke eind punt uit om het subnet in te scha kelen.

1. Schakel service-eind punten in voor de resources.

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. De werk ruimte implementeren

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Volg de stappen in [resources implementeren vanuit aangepaste sjabloon](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template). Wanneer u op het scherm __een sjabloon selecteren__ klikt, kiest u in de vervolg keuzelijst de sjabloon **201-machine learning-Advanced** .
1. Selecteer __sjabloon selecteren__ om de sjabloon te gebruiken. Geef de volgende vereiste informatie en alle andere para meters op, afhankelijk van uw implementatie scenario.

   * Abonnement: Selecteer het Azure-abonnement dat u wilt gebruiken voor deze resources.
   * Resource groep: Selecteer of maak een resource groep om de services te bevatten.
   * Regio: Selecteer de Azure-regio waar de resources worden gemaakt.
   * Werkruimte naam: de naam die moet worden gebruikt voor de Azure Machine Learning werk ruimte die wordt gemaakt. De naam van de werk ruimte moet tussen de 3 en 33 tekens lang zijn. De naam mag alleen alfanumerieke tekens en '-' bevatten.
   * Locatie: Selecteer de locatie waar de resources worden gemaakt.
1. Selecteer __Controleren + maken__.
1. Ga in het scherm __bekijken en maken__ naar de vermelde voor waarden en selecteer __maken__.

Zie [resources implementeren vanuit een aangepaste sjabloon](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten van de resource provider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault toegangs beleid en Azure Resource Manager sjablonen

Wanneer u een Azure Resource Manager sjabloon gebruikt om de werk ruimte en de gekoppelde resources (inclusief Azure Key Vault) meerdere keren te maken. Gebruik bijvoorbeeld de sjabloon meerdere keren met dezelfde para meters als onderdeel van een continue integratie-en implementatie pijplijn.

De meeste bewerkingen voor het maken van resources via sjablonen zijn idempotent, maar Key Vault wist het toegangs beleid telkens wanneer de sjabloon wordt gebruikt. Als u het toegangs beleid wist, wordt de toegang tot de Key Vault verbroken voor een bestaande werk ruimte waarin deze wordt gebruikt. Bijvoorbeeld: stop/Create-functionaliteit van Azure Notebooks VM kan mislukken.  

Om dit probleem te voor komen, raden we u aan een van de volgende benaderingen te volgen:

* Implementeer de sjabloon niet meer dan één keer voor dezelfde para meters. Of verwijder de bestaande resources voordat u de sjabloon opnieuw maakt.

* Controleer de Key Vault toegangs beleid en gebruik vervolgens dit beleid om de `accessPolicies` eigenschap van de sjabloon in te stellen. Gebruik de volgende Azure CLI-opdracht om het toegangs beleid weer te geven:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    `accessPolicies`Zie de [AccessPolicyEntry-object verwijzing](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)voor meer informatie over het gebruik van de sectie van de sjabloon.

* Controleer of de Key Vault resource al bestaat. Als dit het geval is, moet u het niet opnieuw maken via de sjabloon. Als u bijvoorbeeld de bestaande Key Vault wilt gebruiken in plaats van een nieuwe te maken, moet u de volgende wijzigingen aanbrengen in de sjabloon:

    * **Voeg** een para meter toe die de id van een bestaande Key Vault resource accepteert:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Verwijder** de sectie waarmee een Key Vault resource wordt gemaakt:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Verwijder** de `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` regel uit de `dependsOn` sectie van de werk ruimte. **Wijzig** ook de `keyVault` vermelding in de `properties` sectie van de werk ruimte om te verwijzen naar de `keyVaultId` para meter:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Nadat u deze wijzigingen hebt aangebracht, kunt u de ID van de bestaande Key Vault resource opgeven wanneer u de sjabloon uitvoert. Vervolgens wordt de Key Vault door de sjabloon opnieuw gebruikt door de `keyVault` eigenschap van de werk ruimte in te stellen op de bijbehorende id.

    Als u de ID van de Key Vault wilt ophalen, kunt u verwijzen naar de uitvoer van de oorspronkelijke sjabloon run of de Azure CLI gebruiken. De volgende opdracht is een voor beeld van het gebruik van de Azure CLI om de Key Vault Resource-ID op te halen:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>Het virtuele netwerk is niet gekoppeld aan de privé-DNS-zone

Wanneer u een werk ruimte met een persoonlijk eind punt maakt, maakt de sjabloon een Privé-DNS zone met de naam __privatelink.API.azureml.MS__. Een __koppeling naar een virtueel netwerk__ wordt automatisch toegevoegd aan deze privé-DNS-zone. De koppeling wordt alleen toegevoegd voor de eerste werk ruimte en het persoonlijke eind punt dat u in een resource groep maakt. Als u een ander virtueel netwerk en een werk ruimte met een persoonlijk eind punt in dezelfde resource groep maakt, wordt het tweede virtuele netwerk mogelijk niet toegevoegd aan de privé-DNS-zone.

Gebruik de volgende Azure CLI-opdracht om de virtuele netwerk koppelingen weer te geven die al bestaan voor de privé-DNS-zone:

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

Gebruik de volgende stappen om het virtuele netwerk met een andere werk ruimte en een persoonlijk eind punt toe te voegen:

1. Als u de virtuele netwerk-ID wilt vinden van het netwerk dat u wilt toevoegen, gebruikt u de volgende opdracht:

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met '/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet '. Sla deze waarde op en gebruik deze in de volgende stap.

2. Gebruik de volgende opdracht om een virtuele netwerk koppeling toe te voegen aan de privatelink.api.azureml.ms-zone Privé-DNS. Voor de `--virtual-network` para meter gebruikt u de uitvoer van de vorige opdracht:

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>Volgende stappen

* [Resources implementeren met Resource Manager-sjablonen en Resource Manager-rest API](../azure-resource-manager/templates/deploy-rest.md).
* [Azure-resource groepen maken en implementeren via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
* [Raadpleeg de opslag plaats voor Azure Quick Start-sjablonen voor andere sjablonen die betrekking hebben op Azure Machine Learning.](https://github.com/Azure/azure-quickstart-templates)