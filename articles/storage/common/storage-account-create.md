---
title: Een opslagaccount maken
titleSuffix: Azure Storage
description: Meer informatie over het maken van een opslag account voor het opslaan van blobs, bestanden, wacht rijen en tabellen. Een Azure-opslag account biedt een unieke naam ruimte in Microsoft Azure voor het lezen en schrijven van uw gegevens.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/11/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b8f5932985f90ce042d7b0df0d01e7c685098670
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576506"
---
# <a name="create-a-storage-account"></a>Een opslagaccount maken

Een Azure-opslag account bevat al uw Azure Storage gegevens objecten: blobs, bestanden, wacht rijen, tabellen en schijven. Het opslag account biedt een unieke naam ruimte voor uw Azure Storage gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Gegevens in uw Azure Storage-account zijn duurzaam en Maxi maal beschikbaar, veilig en zeer schaalbaar.

In dit artikel leert u hoe u een opslag account maakt met behulp van de [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](/powershell/azure/), [Azure cli](/cli/azure)of een [Azure Resource Manager sjabloon](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Geen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u een Azure-opslag account wilt maken met Power shell, moet u Azure PowerShell module AZ versie 0,7 of hoger hebben geïnstalleerd. Zie [Inleiding tot de Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie.

Voer de volgende opdracht uit om uw huidige versie te vinden:

```powershell
Get-InstalledModule -Name "Az"
```

Als u Azure PowerShell wilt installeren of upgraden, raadpleegt u [Azure PowerShell module installeren](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt u aanmelden bij Azure en Azure CLI-opdrachten uitvoeren. Dit kan op twee manieren:

- U kunt CLI-opdrachten uitvoeren vanuit Azure Portal, in Azure Cloud Shell.
- U kunt de CLI installeren en CLI-opdrachten lokaal uitvoeren.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell gebruiken

Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. De Azure CLI is vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Klik op de knop **Cloud shell** in het menu rechtsboven in de Azure portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Met de knop start u een interactieve shell die u kunt gebruiken om de stappen uit te voeren die worden beschreven in dit artikel:

[![Scherm opname van het Cloud Shell-venster in de portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>De CLI lokaal installeren

U kunt Azure CLI ook lokaal installeren en gebruiken. Voor de voor beelden in dit artikel is Azure CLI-versie 2.0.4 of hoger vereist. Voer uit `az --version` om de geïnstalleerde versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

# <a name="template"></a>[Sjabloon](#tab/template)

Geen.

---

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Meld u aan bij de [Azure-portal](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Meld u aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm om te verifiëren.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Meld u aan bij de [Azure Portal](https://portal.azure.com)om Azure Cloud shell te starten.

Als u zich wilt aanmelden bij de lokale installatie van de CLI, voert u de opdracht [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Elk opslagaccount moet behoren tot een Azure-resourcegroep. Een resourcegroep is een logische container voor het groeperen van uw Azure-services. Wanneer u een opslagaccount maakt, kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken. In dit artikel wordt beschreven hoe u een nieuwe resourcegroep maakt.

Een **v2-** opslag account voor algemeen gebruik biedt toegang tot alle Azure Storage services: blobs, bestanden, wacht rijen, tabellen en schijven. Met de stappen die hier worden uiteengezet, maakt u een v2-opslagaccount voor algemeen gebruik, maar de stappen voor het maken van een ander soort opslagaccount zijn vergelijkbaar. Zie [Azure-opslagaccountoverzicht](storage-account-overview.md) voor meer informatie over typen opslagaccounts en andere opslagaccountinstellingen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak eerst een nieuwe resourcegroep met PowerShell met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Als u niet zeker weet welke regio u moet opgeven voor de parameter `-Location`, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
```

Maak vervolgens een voor algemeen gebruik v2-opslag account met geografisch redundante opslag met lees toegang (RA-GRS) met behulp van de opdracht [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Houd er rekening mee dat de naam van uw opslag account uniek moet zijn in azure, dus Vervang de waarde van de tijdelijke aanduiding tussen vier Kante haken door uw eigen unieke waarde:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Als u [Azure data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)wilt gebruiken, neemt u `-EnableHierarchicalNamespace $True` in deze lijst met para meters op.

Als u een v2-opslag account voor algemeen gebruik met een andere replicatie optie wilt maken, vervangt u de gewenste waarde in de onderstaande tabel voor de para meter **SkuName** .

|Replicatie-optie  |De parameter SkuName  |
|---------|---------|
|Lokaal redundante opslag (LRS)     |Standard_LRS         |
|Zone-redundante opslag (ZRS)     |Standard_ZRS         |
|Geografisch redundante opslag (GRS)     |Standard_GRS         |
|Geografisch redundante opslag met leestoegang (GRS)     |Standard_RAGRS         |
|Geografisch zone-redundante opslag (GZRS)    |Standard_GZRS         |
|Leestoegang tot geografische zone-redundante opslag (RA-GZRS)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik eerst de opdracht [az group create](/cli/azure/group#az_group_create) om een nieuwe resourcegroep te maken met Azure CLI.

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Als u niet zeker weet welke regio u moet opgeven voor de parameter `--location`, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Maak vervolgens een voor algemeen gebruik v2-opslag account met geografisch redundante opslag met lees toegang met behulp van de opdracht [AZ Storage account create](/cli/azure/storage/account#az_storage_account_create) . Houd er rekening mee dat de naam van uw opslag account uniek moet zijn in azure, dus Vervang de waarde van de tijdelijke aanduiding tussen vier Kante haken door uw eigen unieke waarde:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Als u [Azure data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)wilt gebruiken, neemt u `--enable-hierarchical-namespace true` in deze lijst met para meters op.

Als u een v2-opslag account voor algemeen gebruik met een andere replicatie optie wilt maken, vervangt u de gewenste waarde in de onderstaande tabel voor de **SKU** -para meter.

|Replicatie-optie  |sku-parameter  |
|---------|---------|
|Lokaal redundante opslag (LRS)     |Standard_LRS         |
|Zone-redundante opslag (ZRS)     |Standard_ZRS         |
|Geografisch redundante opslag (GRS)     |Standard_GRS         |
|Geografisch redundante opslag met leestoegang (GRS)     |Standard_RAGRS         |
|Geografisch zone-redundante opslag (GZRS)    |Standard_GZRS         |
|Leestoegang tot geografische zone-redundante opslag (RA-GZRS)    |Standard_RAGZRS         |

# <a name="template"></a>[Sjabloon](#tab/template)

U kunt Azure PowerShell of Azure CLI gebruiken voor het implementeren van een resource manager-sjabloon voor het maken van een opslag account. De sjabloon die in dit artikel wordt gebruikt, is van [Azure Resource Manager Quick](https://azure.microsoft.com/resources/templates/101-storage-account-create/)start-sjablonen. Als u de scripts wilt uitvoeren, selecteert u **proberen** het Azure Cloud shell te openen. Plak het script door met de rechtermuisknop op de shell te klikken en **Plakken** te selecteren.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Deze sjabloon fungeert alleen als voor beeld. Er zijn veel instellingen voor het opslag account die niet zijn geconfigureerd als onderdeel van deze sjabloon. Als u bijvoorbeeld [Azure data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)wilt gebruiken, wijzigt u deze sjabloon door de `isHnsEnabledad` eigenschap van het `StorageAccountPropertiesCreateParameters` object in te stellen op `true` . 

Zie voor meer informatie over het wijzigen van deze sjabloon of het maken van nieuwe sjablonen:

- [Azure Resource Manager documentatie](../../azure-resource-manager/index.yml).
- [Verwijzing naar opslag account sjabloon](/azure/templates/microsoft.storage/allversions).
- [Extra opslag account sjabloon voorbeelden](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

## <a name="delete-a-storage-account"></a>Een opslagaccount verwijderen

Als u een opslag account verwijdert, wordt het hele account verwijderd, inclusief alle gegevens in het account, en dit kan niet ongedaan worden gemaakt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigeer naar het opslag account in de [Azure Portal](https://portal.azure.com).
1. Klik op **Verwijderen**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u het opslag account wilt verwijderen, gebruikt u de opdracht [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u het opslag account wilt verwijderen, gebruikt u de opdracht [AZ Storage account delete](/cli/azure/storage/account#az-storage-account-delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Sjabloon](#tab/template)

Als u het opslag account wilt verwijderen, gebruikt u Azure PowerShell of Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

U kunt ook de resource groep verwijderen, waardoor het opslag account en eventuele andere resources in die resource groep worden verwijderd. Zie [resource groep en resources verwijderen](../../azure-resource-manager/management/delete-resource-group.md)voor meer informatie over het verwijderen van een resource groep.

> [!WARNING]
> Het is niet mogelijk om een verwijderd opslagaccount te herstellen of om inhoud terug te halen die het account vóór verwijdering bevatte. Zorg ervoor dat u een back-up maakt van alles dat u wilt opslaan, voordat u het account verwijdert. Dit geldt ook voor alle resources in het account: als u blobs, tabellen, wachtrijen of bestanden verwijdert, worden deze permanent verwijderd.
>
> Als u probeert een opslagaccount te verwijderen dat is gekoppeld aan een virtuele machine van Azure, ziet u mogelijk een foutbericht dat het account nog in gebruik is. Zie [problemen oplossen bij het verwijderen van opslag accounts](../../virtual-machines/troubleshooting/index.yml)voor hulp bij het oplossen van deze fout.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van opslagaccounts](storage-account-overview.md)
- [Upgraden naar een V2-opslagaccount voor algemeen gebruik](storage-account-upgrade.md)
- [Een Azure Storage-account naar een andere regio verplaatsen](storage-account-move.md)
- [Een verwijderd opslagaccount herstellen](storage-account-recover.md)
