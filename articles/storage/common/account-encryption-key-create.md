---
title: Een account maken dat door de klant beheerde sleutels voor tabellen en wachtrijen ondersteunt
titleSuffix: Azure Storage
description: Meer informatie over het maken van een opslagaccount dat ondersteuning biedt voor het configureren van door de klant beheerde sleutels voor tabellen en wachtrijen. Gebruik de Azure CLI of een Azure Resource Manager om een opslagaccount te maken dat afhankelijk is van de accountversleutelingssleutel voor Azure Storage versleuteling. Vervolgens kunt u door de klant beheerde sleutels voor het account configureren.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c86811ee72d2713fced6320a17d1ccde1866d99
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769945"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Een account maken dat door de klant beheerde sleutels voor tabellen en wachtrijen ondersteunt

Azure Storage versleutelt alle gegevens in een opslagaccount at rest. Queue Storage en Table Storage gebruiken standaard een sleutel die is ingesteld op de service en wordt beheerd door Microsoft. U kunt er ook voor kiezen om door de klant beheerde sleutels te gebruiken om wachtrij- of tabelgegevens te versleutelen. Als u door de klant beheerde sleutels wilt gebruiken met wachtrijen en tabellen, moet u eerst een opslagaccount maken dat gebruikmaakt van een versleutelingssleutel die is beperkt tot het account in plaats van de service. Nadat u een account hebt gemaakt dat gebruikmaakt van de accountversleutelingssleutel voor wachtrij- en tabelgegevens, kunt u door de klant beheerde sleutels voor dat opslagaccount configureren.

In dit artikel wordt beschreven hoe u een opslagaccount maakt dat afhankelijk is van een sleutel die is beperkt tot het account. Wanneer het account voor het eerst wordt gemaakt, gebruikt Microsoft de accountsleutel om de gegevens in het account te versleutelen en beheert Microsoft de sleutel. U kunt vervolgens door de klant beheerde sleutels voor het account configureren om te profiteren van deze voordelen, waaronder de mogelijkheid om uw eigen sleutels op te geven, de sleutelversie bij te werken, de sleutels te roteren en toegangsbesturingselementen in te trekken.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Een account maken dat gebruikmaakt van de accountversleutelingssleutel

U moet een nieuw opslagaccount configureren voor het gebruik van de accountversleutelingssleutel voor wachtrijen en tabellen op het moment dat u het opslagaccount maakt. Het bereik van de versleutelingssleutel kan niet worden gewijzigd nadat het account is gemaakt.

Het opslagaccount moet van het type algemeen gebruik v2 zijn. U kunt het opslagaccount maken en dit zo configureren dat deze afhankelijk is van de accountversleutelingssleutel met behulp van Azure CLI of een Azure Resource Manager sjabloon.

> [!NOTE]
> U kunt optioneel alleen Queue- en Table Storage configureren om gegevens te versleutelen met de accountversleutelingssleutel wanneer het opslagaccount wordt gemaakt. Blob Storage en Azure Files altijd de accountversleutelingssleutel gebruiken om gegevens te versleutelen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u PowerShell wilt gebruiken om een opslagaccount te maken dat afhankelijk is van de accountversleutelingssleutel, moet u ervoor zorgen dat u de Azure PowerShell-module, versie 3.4.0 of hoger, hebt geïnstalleerd. Zie Install the Azure PowerShell module (De module [Azure PowerShell installeren) voor meer informatie.](/powershell/azure/install-az-ps)

Maak vervolgens een v2-opslagaccount voor algemeen gebruik door de opdracht [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) aan te roepen met de juiste parameters:

- Neem de optie `-EncryptionKeyTypeForQueue` op en stel de waarde ervan in op om de `Account` accountversleutelingssleutel te gebruiken voor het versleutelen van gegevens in Queue Storage.
- Neem de optie `-EncryptionKeyTypeForTable` op en stel de waarde ervan in op om de `Account` accountversleutelingssleutel te gebruiken voor het versleutelen van gegevens in Table Storage.

In het volgende voorbeeld ziet u hoe u een v2-opslagaccount voor algemeen gebruik maakt dat is geconfigureerd voor geografisch redundante opslag met leestoegang (RA-GRS) en dat gebruikmaakt van de accountversleutelingssleutel om gegevens te versleutelen voor zowel Queue- als Table Storage. Vergeet niet om de tijdelijke aanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gebruiken om een opslagaccount te maken dat afhankelijk is van de accountversleutelingssleutel, moet u ervoor zorgen dat u Azure CLI versie 2.0.80 of hoger hebt geïnstalleerd. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Maak vervolgens een v2-opslagaccount voor algemeen gebruik door de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create) aan te roepen, met de juiste parameters:

- Neem de optie `--encryption-key-type-for-queue` op en stel de waarde ervan in op om de `Account` accountversleutelingssleutel te gebruiken voor het versleutelen van gegevens in Queue Storage.
- Neem de optie `--encryption-key-type-for-table` op en stel de waarde ervan in op om de `Account` accountversleutelingssleutel te gebruiken voor het versleutelen van gegevens in Table Storage.

In het volgende voorbeeld ziet u hoe u een v2-opslagaccount voor algemeen gebruik maakt dat is geconfigureerd voor geografisch redundante opslag met leestoegang (RA-GRS) en dat gebruikmaakt van de accountversleutelingssleutel om gegevens te versleutelen voor zowel Queue- als Table Storage. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Sjabloon](#tab/template)

In het volgende JSON-voorbeeld wordt een v2-opslagaccount voor algemeen gebruik gemaakt dat is geconfigureerd voor geografisch redundante opslag met leestoegang (RA-GRS) en dat gebruikmaakt van de accountversleutelingssleutel voor het versleutelen van gegevens voor zowel Queue- als Table Storage. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vierkante haken te vervangen door uw eigen waarden:

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
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Nadat u een account hebt gemaakt dat afhankelijk is van de accountversleutelingssleutel, kunt u door de klant beheerde sleutels configureren die zijn opgeslagen in Azure Key Vault of in Key Vault Managed Hardware Security Model (HSM) (preview). Zie Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault voor meer informatie over het opslaan van door de klant [beheerde sleutels in Azure Key Vault.](customer-managed-keys-configure-key-vault.md) Zie Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen [in Azure Key Vault Managed HSM (preview)](customer-managed-keys-configure-key-vault-hsm.md)voor meer informatie over het opslaan van door de klant beheerde sleutels in een beheerde HSM.

## <a name="verify-the-account-encryption-key"></a>De accountversleutelingssleutel controleren

Als u wilt controleren of een service in een opslagaccount de accountversleutelingssleutel gebruikt, roept u de Azure [CLI-opdracht az storage account](/cli/azure/storage/account#az_storage_account_show) aan. Deze opdracht retourneert een set eigenschappen van het opslagaccount en de waarden. Zoek het veld `keyType` voor elke service in de versleutelings-eigenschap en controleer of deze is ingesteld op `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u wilt controleren of een service in een opslagaccount de accountversleutelingssleutel gebruikt, roept u de [opdracht Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) aan. Deze opdracht retourneert een set eigenschappen van het opslagaccount en de waarden. Zoek het veld voor elke service in de eigenschap `KeyType` en controleer of deze is ingesteld op `Encryption` `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u wilt controleren of een service in een opslagaccount de accountversleutelingssleutel gebruikt, roept u de [opdracht az storage account show](/cli/azure/storage/account#az_storage_account_show) aan. Deze opdracht retourneert een set eigenschappen van het opslagaccount en de waarden. Zoek het veld `keyType` voor elke service binnen de versleutelings-eigenschap en controleer of deze is ingesteld op `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Sjabloon](#tab/template)

N.v.t.

---

## <a name="pricing-and-billing"></a>Prijzen en facturering

Een opslagaccount dat is gemaakt voor het gebruik van een versleutelingssleutel die is beperkt tot het account, wordt gefactureerd voor De opslagcapaciteit van de tabel en transacties tegen een ander tarief dan een account dat gebruikmaakt van de standaardsleutel binnen het servicebereik. Zie Prijzen voor [Azure Table Storage voor meer informatie.](https://azure.microsoft.com/pricing/details/storage/tables/)

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](customer-managed-keys-overview.md)
- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)