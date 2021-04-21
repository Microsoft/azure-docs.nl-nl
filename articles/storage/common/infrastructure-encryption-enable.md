---
title: Een opslagaccount maken met infrastructuurversleuteling ingeschakeld voor dubbele versleuteling van gegevens
titleSuffix: Azure Storage
description: Klanten die een hogere mate van zekerheid nodig hebben dat hun gegevens veilig zijn, kunnen ook 256-bits AES-versleuteling inschakelen op Azure Storage infrastructuurniveau. Wanneer infrastructuurversleuteling is ingeschakeld, worden gegevens in een opslagaccount twee keer versleuteld met twee verschillende versleutelingsalgoritmen en twee verschillende sleutels.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23b3ca919be030490cca06f31dac623d7f80be44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790379"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Een opslagaccount maken met infrastructuurversleuteling ingeschakeld voor dubbele versleuteling van gegevens

Azure Storage versleutelt automatisch alle gegevens in een opslagaccount op serviceniveau met behulp van 256-bits AES-versleuteling. Dit is een van de sterkste blokcoderingssleutels die compatibel is met FIPS 140-2. Klanten die een hogere mate van zekerheid nodig hebben dat hun gegevens veilig zijn, kunnen ook 256-bits AES-versleuteling inschakelen op Azure Storage infrastructuurniveau. Wanneer infrastructuurversleuteling is ingeschakeld, worden gegevens in een opslagaccount twee keer op serviceniveau en één keer op infrastructuurniveau versleuteld met twee verschillende versleutelingsalgoritmen en &mdash; &mdash; twee verschillende sleutels. Dubbele versleuteling Azure Storage gegevens beschermt tegen een scenario waarin een van de versleutelingsalgoritmen of sleutels kan worden aangetast. In dit scenario blijft de extra versleutelingslaag uw gegevens beschermen.

Versleuteling op serviceniveau ondersteunt het gebruik van door Microsoft beheerde sleutels of door de klant beheerde sleutels met Azure Key Vault of Key Vault Managed Hardware Security Model (HSM) (preview). Versleuteling op infrastructuurniveau is afhankelijk van door Microsoft beheerde sleutels en maakt altijd gebruik van een afzonderlijke sleutel. Zie Informatie over versleutelingssleutelbeheer Azure Storage meer informatie [over sleutelbeheer met versleuteling.](storage-service-encryption.md#about-encryption-key-management)

Als u uw gegevens twee keer wilt versleutelen, moet u eerst een opslagaccount maken dat is geconfigureerd voor infrastructuurversleuteling. In dit artikel wordt beschreven hoe u een opslagaccount maakt dat infrastructuurversleuteling mogelijk maakt.

## <a name="register-to-use-infrastructure-encryption"></a>Registreren voor het gebruik van infrastructuurversleuteling

Als u een opslagaccount wilt maken waar infrastructuurversleuteling is ingeschakeld, moet u zich eerst registreren om deze functie met Azure te gebruiken met behulp van PowerShell of Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u zich wilt registreren bij PowerShell, roept [u de opdracht Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) aan.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Als u de status van uw registratie met PowerShell wilt controleren, roept u [de opdracht Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) aan.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Nadat uw registratie is goedgekeurd, moet u de resourceprovider Azure Storage registreren. Als u de resourceprovider opnieuw wilt registreren bij PowerShell, roept u de [opdracht Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) aan.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u zich wilt registreren bij Azure CLI, roept u de [opdracht az feature register](/cli/azure/feature#az_feature_register) aan.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Als u de status van uw registratie met Azure CLI wilt controleren, roept u de [opdracht az feature](/cli/azure/feature#az_feature_show) aan.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Nadat uw registratie is goedgekeurd, moet u de resourceprovider Azure Storage registreren. Als u de resourceprovider opnieuw wilt registreren bij Azure CLI, roept u de [opdracht az provider register](/cli/azure/provider#az_provider_register) aan.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Een account maken met infrastructuurversleuteling ingeschakeld

U moet een opslagaccount configureren voor het gebruik van infrastructuurversleuteling op het moment dat u het account maakt. Het opslagaccount moet van het type algemeen gebruik v2 zijn.

Infrastructuurversleuteling kan niet worden ingeschakeld of uitgeschakeld nadat het account is gemaakt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u PowerShell wilt gebruiken om een opslagaccount te maken met infrastructuurversleuteling ingeschakeld, volgt u deze stappen:

1. Ga in Azure Portal naar de **pagina Opslagaccounts.**
1. Kies de **knop Toevoegen** om een nieuw v2-opslagaccount voor algemeen gebruik toe te voegen.
1. Ga op **het tabblad** Geavanceerd naar **Infrastructuurversleuteling** en selecteer **Ingeschakeld.**
1. Selecteer **Beoordelen en maken om** het maken van het opslagaccount te voltooien.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Schermopname die laat zien hoe u infrastructuurversleuteling kunt inschakelen bij het maken van een account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u PowerShell wilt gebruiken om een opslagaccount te maken met infrastructuurversleuteling ingeschakeld, moet u ervoor zorgen dat u de [Az.Storage PowerShell-module](https://www.powershellgallery.com/packages/Az.Storage), versie 2.2.0 of hoger hebt geïnstalleerd. Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie.

Maak vervolgens een v2-opslagaccount voor algemeen gebruik door de opdracht [New-AzStorageAccount aan te](/powershell/module/az.storage/new-azstorageaccount) roepen. Neem de optie `-RequireInfrastructureEncryption` op om infrastructuurversleuteling in teschakelen.

In het volgende voorbeeld ziet u hoe u een v2-opslagaccount voor algemeen gebruik maakt dat is geconfigureerd voor geografisch redundante opslag met leestoegang (RA-GRS) en infrastructuurversleuteling is ingeschakeld voor dubbele versleuteling van gegevens. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken om een opslagaccount te maken met infrastructuurversleuteling ingeschakeld, moet u Ervoor zorgen dat u Azure CLI versie 2.8.0 of hoger hebt geïnstalleerd. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Maak vervolgens een v2-opslagaccount voor algemeen gebruik door de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) aan te roepen en de op te nemen om `--require-infrastructure-encryption option` infrastructuurversleuteling in te stellen.

In het volgende voorbeeld ziet u hoe u een v2-opslagaccount voor algemeen gebruik maakt dat is geconfigureerd voor geografisch redundante opslag met leestoegang (RA-GRS) en infrastructuurversleuteling is ingeschakeld voor dubbele versleuteling van gegevens. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Sjabloon](#tab/template)

In het volgende JSON-voorbeeld wordt een v2-opslagaccount voor algemeen gebruik gemaakt dat is geconfigureerd voor geografisch redundante opslag met leestoegang (RA-GRS) en dat infrastructuurversleuteling heeft ingeschakeld voor dubbele versleuteling van gegevens. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Controleren of infrastructuurversleuteling is ingeschakeld

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u wilt controleren of infrastructuurversleuteling is ingeschakeld voor een opslagaccount met de Azure Portal volgt u deze stappen:

1. Ga in Azure Portal naar uw opslagaccount.
1. Kies **versleuteling** onder **Instellingen.**

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Schermopname die laat zien hoe u kunt controleren of infrastructuurversleuteling is ingeschakeld voor het account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u wilt controleren of infrastructuurversleuteling is ingeschakeld voor een opslagaccount met PowerShell, roept u de [opdracht Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) aan. Deze opdracht retourneert een set eigenschappen van het opslagaccount en de waarden. Haal het `RequireInfrastructureEncryption` veld in de eigenschap op en controleer of het is ingesteld op `Encryption` `True` .

In het volgende voorbeeld wordt de waarde van de eigenschap `RequireInfrastructureEncryption` opgehaald. Vergeet niet om de tijdelijke aanduidingswaarden tussen vierkante haken te vervangen door uw eigen waarden:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u wilt controleren of infrastructuurversleuteling is ingeschakeld voor een opslagaccount met Azure CLI, roept u de [opdracht az storage account show](/cli/azure/storage/account#az_storage_account_show) aan. Deze opdracht retourneert een set eigenschappen van het opslagaccount en de waarden. Zoek het veld `requireInfrastructureEncryption` in de eigenschap en controleer of het is ingesteld op `encryption` `true` .

In het volgende voorbeeld wordt de waarde van de eigenschap `requireInfrastructureEncryption` opgehaald. Vergeet niet om de tijdelijke aanduidingswaarden tussen vierkante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](customer-managed-keys-overview.md)
