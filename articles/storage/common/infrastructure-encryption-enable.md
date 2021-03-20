---
title: Een opslag account maken waarvoor infrastructuur versleuteling is ingeschakeld voor dubbele versleuteling van gegevens
titleSuffix: Azure Storage
description: Klanten die behoefte hebben aan een hogere mate van zekerheid dat hun gegevens veilig zijn, kunnen ook 256-bits AES-versleuteling inschakelen op het niveau van de Azure Storage-infra structuur. Wanneer infrastructuur versleuteling is ingeschakeld, worden gegevens in een opslag account twee keer versleuteld met twee verschillende versleutelings algoritmen en twee verschillende sleutels.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 612ba18ba71a22ad6c346b26008e688195c1d1e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746571"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Een opslag account maken waarvoor infrastructuur versleuteling is ingeschakeld voor dubbele versleuteling van gegevens

Azure Storage versleutelt automatisch alle gegevens in een opslag account op service niveau met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en compatibel met FIPS 140-2. Klanten die behoefte hebben aan een hogere mate van zekerheid dat hun gegevens veilig zijn, kunnen ook 256-bits AES-versleuteling inschakelen op het niveau van de Azure Storage-infra structuur. Wanneer infrastructuur versleuteling is ingeschakeld, worden gegevens in een opslag account twee maal versleuteld &mdash; op het niveau van de service en eenmaal op het niveau van de infra structuur &mdash; met twee verschillende versleutelings algoritmen en twee verschillende sleutels. Dubbele versleuteling van Azure Storage gegevens beveiligt tegen een scenario waarbij een van de versleutelings algoritmen of-sleutels kan worden aangetast. In dit scenario blijft de extra laag versleuteling uw gegevens beveiligen.

Versleuteling op service niveau biedt ondersteuning voor het gebruik van door micro soft beheerde sleutels of door de klant beheerde sleutels met Azure Key Vault of Key Vault beheerde hardware security model (HSM). Versleuteling op infrastructuur niveau is afhankelijk van door micro soft beheerde sleutels en maakt altijd gebruik van een afzonderlijke sleutel. Zie [about Encryption Key Management](storage-service-encryption.md#about-encryption-key-management)(Engelstalig) voor meer informatie over sleutel beheer met Azure Storage versleuteling.

Als u uw gegevens wilt versleutelen, moet u eerst een opslag account maken dat is geconfigureerd voor infrastructuur versleuteling. In dit artikel wordt beschreven hoe u een opslag account maakt dat infrastructuur versleuteling mogelijk maakt.

## <a name="register-to-use-infrastructure-encryption"></a>Registreren voor het gebruik van infrastructuur versleuteling

Als u een opslag account wilt maken waarvoor infrastructuur versleuteling is ingeschakeld, moet u zich eerst registreren voor het gebruik van deze functie met Azure met behulp van Power shell of Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u zich wilt registreren bij Power shell, roept u de opdracht [REGI ster-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) aan.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Als u de status van uw registratie met Power shell wilt controleren, roept u de opdracht [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) aan.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Nadat de registratie is goedgekeurd, moet u de Azure Storage Resource provider opnieuw registreren. Als u de resource provider opnieuw wilt registreren bij Power shell, roept u de opdracht [REGI ster-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) aan.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u zich wilt registreren bij Azure CLI, roept u de opdracht [AZ feature REGI ster](/cli/azure/feature#az-feature-register) aan.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Als u de status van uw registratie met Azure CLI wilt controleren, roept u de opdracht [AZ functie](/cli/azure/feature#az-feature-show) aan.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Nadat de registratie is goedgekeurd, moet u de Azure Storage Resource provider opnieuw registreren. Als u de resource provider opnieuw wilt registreren bij Azure CLI, roept u de opdracht [AZ provider REGI ster](/cli/azure/provider#az-provider-register) aan.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Een account maken waarvoor infrastructuur versleuteling is ingeschakeld

U moet een opslag account configureren voor het gebruik van infrastructuur versleuteling op het moment dat u het account maakt. Het opslag account moet van het type voor algemeen gebruik v2 zijn.

Infrastructuur versleuteling kan niet worden ingeschakeld of uitgeschakeld nadat het account is gemaakt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u Power shell wilt gebruiken om een opslag account te maken waarvoor infrastructuur versleuteling is ingeschakeld, voert u de volgende stappen uit:

1. Navigeer in het Azure Portal naar de pagina **opslag accounts** .
1. Klik op de knop **toevoegen** om een nieuw v2-opslag account voor algemeen gebruik toe te voegen.
1. Op het tabblad **Geavanceerd** gaat u naar **infrastructuur** versleuteling en selecteert u **ingeschakeld**.
1. Selecteer **beoordeling + maken** om het opslag account te maken.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Scherm afbeelding die laat zien hoe infrastructuur versleuteling wordt ingeschakeld bij het maken van het account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u Power shell wilt gebruiken voor het maken van een opslag account waarvoor infrastructuur versleuteling is ingeschakeld, moet u ervoor zorgen dat u de [module AZ. Storage Power shell](https://www.powershellgallery.com/packages/Az.Storage), versie 2.2.0 of hoger hebt geïnstalleerd. Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie.

Maak vervolgens een voor algemeen gebruik v2-opslag account door de opdracht [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) aan te roepen. Neem de `-RequireInfrastructureEncryption` optie op voor het inschakelen van infrastructuur versleuteling.

In het volgende voor beeld ziet u hoe u een v2-opslag account voor algemeen gebruik maakt dat is geconfigureerd voor geografisch redundante opslag met lees toegang (RA-GRS) en waarvoor infrastructuur versleuteling is ingeschakeld voor dubbele versleuteling van gegevens. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken voor het maken van een opslag account waarvoor infrastructuur versleuteling is ingeschakeld, moet u ervoor zorgen dat u Azure CLI-versie 2.8.0 of hoger hebt geïnstalleerd. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Maak vervolgens een voor algemeen gebruik v2-opslag account door de opdracht [AZ Storage account create](/cli/azure/storage/account#az-storage-account-create) aan te roepen en de `--require-infrastructure-encryption option` infra structuur-versleuteling in te scha kelen.

In het volgende voor beeld ziet u hoe u een v2-opslag account voor algemeen gebruik maakt dat is geconfigureerd voor geografisch redundante opslag met lees toegang (RA-GRS) en waarvoor infrastructuur versleuteling is ingeschakeld voor dubbele versleuteling van gegevens. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

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

In het volgende JSON-voor beeld wordt een v2-opslag account voor algemeen gebruik gemaakt dat is geconfigureerd voor geografisch redundante opslag met lees toegang (RA-GRS) en waarvoor infrastructuur versleuteling is ingeschakeld voor dubbele versleuteling van gegevens. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

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

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Controleren of infrastructuur versleuteling is ingeschakeld

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om te controleren of infrastructuur versleuteling is ingeschakeld voor een opslag account met de Azure Portal:

1. Ga in Azure Portal naar uw opslagaccount.
1. Kies onder **instellingen** de optie **versleuteling**.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Scherm afbeelding die laat zien hoe u kunt controleren of infrastructuur versleuteling is ingeschakeld voor het account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u wilt controleren of infrastructuur versleuteling is ingeschakeld voor een opslag account met Power shell, roept u de opdracht [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) aan. Met deze opdracht wordt een set eigenschappen van het opslag account en de bijbehorende waarden geretourneerd. Haal het `RequireInfrastructureEncryption` veld op in de `Encryption` eigenschap en controleer of het is ingesteld op `True` .

In het volgende voor beeld wordt de waarde van de `RequireInfrastructureEncryption` eigenschap opgehaald. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u wilt controleren of infrastructuur versleuteling is ingeschakeld voor een opslag account met Azure CLI, roept u de opdracht [AZ Storage account show](/cli/azure/storage/account#az-storage-account-show) aan. Met deze opdracht wordt een set eigenschappen van het opslag account en de bijbehorende waarden geretourneerd. Zoek het `requireInfrastructureEncryption` veld in de `encryption` eigenschap en controleer of het is ingesteld op `true` .

In het volgende voor beeld wordt de waarde van de `requireInfrastructureEncryption` eigenschap opgehaald. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

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
