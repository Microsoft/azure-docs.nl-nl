---
title: Sjabloon veilig implementeren met SAS-token
description: Resources implementeren in azure met een Azure Resource Manager sjabloon die wordt beveiligd met een SAS-token. Toont Azure PowerShell en Azure CLI.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8b35e82da8ebca98ec9fe1fb7441612bf61fb142
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88855657"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Een persoonlijke ARM-sjabloon met SAS-token implementeren

Wanneer uw Azure Resource Manager sjabloon (ARM-sjabloon) zich in een opslag account bevindt, kunt u de toegang tot de sjabloon beperken om te voor komen dat deze openbaar wordt weer gegeven. U opent een beveiligde sjabloon door een SAS-token (Shared Access Signature) te maken voor de sjabloon en dat token tijdens de implementatie op te geven. In dit artikel wordt uitgelegd hoe u Azure PowerShell of Azure CLI gebruikt voor het implementeren van een sjabloon met een SAS-token.

> [!IMPORTANT]
> In plaats van uw sjabloon te beveiligen met een SAS-token, kunt u de [sjabloon specificaties](template-specs.md)gebruiken. Met sjabloon specificaties kunt u uw sjablonen delen met andere gebruikers in uw organisatie en de toegang tot de sjablonen beheren via Azure RBAC.

## <a name="create-storage-account-with-secured-container"></a>Een opslag account maken met een beveiligde container

Met het volgende script maakt u een opslag account en een container waarvoor open bare toegang is uitgeschakeld.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Sjabloon uploaden naar het opslag account

U bent nu klaar om uw sjabloon te uploaden naar het opslag account. Geef het pad op naar de sjabloon die u wilt gebruiken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>SAS-token opgeven tijdens de implementatie

Als u een privésjabloon in een opslagaccount wilt implementeren, genereert u een SAS-token en neemt u het op in de URI voor de sjabloon. Stel de vervaltijd zo in, dat er genoeg tijd is om de implementatie te voltooien.

> [!IMPORTANT]
> De blob met de sjabloon is alleen toegankelijk voor de eigenaar van het account. Maar als u een SAS-token voor de blob maakt, is de blob toegankelijk voor iedereen met die URI. Als een andere gebruiker de URI onderschept, kan die gebruiker toegang krijgen tot de sjabloon. Een SAS-token is een goede manier om de toegang tot uw sjablonen te beperken, maar u mag geen gevoelige gegevens zoals wachtwoorden rechtstreeks in de sjabloon toevoegen.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Het volgende voor beeld werkt met de bash-omgeving in Cloud Shell. Andere omgevingen hebben mogelijk een andere syntaxis nodig om de verloop tijd voor het SAS-token te maken.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Zie voor een voor beeld van het gebruik van een SAS-token met gekoppelde sjablonen [gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Volgende stappen
* Zie [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md)voor een inleiding tot het implementeren van sjablonen.
* Zie [ontwerp sjablonen](template-syntax.md#parameters)voor het definiëren van para meters in de sjabloon.
