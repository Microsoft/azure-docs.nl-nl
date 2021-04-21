---
title: Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault
titleSuffix: Azure Storage
description: Meer informatie over het configureren Azure Storage versleuteling met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault met behulp van Azure Portal, PowerShell of Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a01a270f47ddacb71962188e7fedd0a0a9f6d0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790433"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault

Azure Storage versleutelt alle gegevens in een opslagaccount at rest. Gegevens worden standaard versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels kunt u uw eigen sleutels beheren. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault of Key Vault Managed Hardware Security Model (HSM) (preview).

In dit artikel wordt beschreven hoe u versleuteling configureert met door de klant beheerde sleutels die zijn opgeslagen in een sleutelkluis met behulp van Azure Portal, PowerShell of Azure CLI. Zie Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen Azure Key Vault in een beheerde HSM voor meer informatie over het configureren van versleuteling met door de klant beheerde sleutels die zijn opgeslagen in een beheerde [HSM.](customer-managed-keys-configure-key-vault-hsm.md)

> [!NOTE]
> Azure Key Vault en Azure Key Vault beheerde HSM ondersteunen dezelfde API's en beheerinterfaces voor configuratie.

## <a name="configure-a-key-vault"></a>Een sleutelkluis configureren

U kunt een nieuwe of bestaande sleutelkluis gebruiken om door de klant beheerde sleutels op te slaan. Het opslagaccount en de sleutelkluis moeten zich in dezelfde regio, maar ze kunnen zich in verschillende abonnementen.

Als u door de klant beheerde sleutels Azure Storage versleuteling, moet zowel de beveiliging voor het verwijderen als het opsluizen van de sleutelkluis zijn ingeschakeld. Soft Delete is standaard ingeschakeld wanneer u een nieuwe sleutelkluis maakt en kan niet worden uitgeschakeld. U kunt beveiliging tegen opsluizen inschakelen wanneer u de sleutelkluis maakt of nadat deze is gemaakt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Zie Quickstart: Een sleutelkluis maken met behulp van de Azure Portal voor meer informatie over het maken [van een sleutelkluis Azure Portal.](../../key-vault/general/quick-create-portal.md) Wanneer u de sleutelkluis maakt, selecteert u **Beveiliging tegen opsluizen inschakelen,** zoals wordt weergegeven in de volgende afbeelding.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Schermopname die laat zien hoe u beveiliging tegen opsluizen inschakelen bij het maken van een sleutelkluis":::

Volg deze stappen om beveiliging tegen opsluizen in te stellen voor een bestaande sleutelkluis:

1. Navigeer naar uw sleutelkluis in Azure Portal.
1. Kies **onder Instellingen** de optie **Eigenschappen.**
1. Kies in **de sectie Beveiliging ops** manier ops **manier beveiliging inschakelen.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een nieuwe sleutelkluis wilt maken met PowerShell, installeert u versie 2.0.0 of hoger van de [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell-module. Roep vervolgens [New-AzKeyVault aan om](/powershell/module/az.keyvault/new-azkeyvault) een nieuwe sleutelkluis te maken. Met versie 2.0.0 en hoger van de Az.KeyVault-module is het gebruik van 'soft delete' standaard ingeschakeld wanneer u een nieuwe sleutelkluis maakt.

In het volgende voorbeeld wordt een nieuwe sleutelkluis gemaakt met zowel de beveiliging voor verwijderen als opsluizen ingeschakeld. Vergeet niet om de waarden van de tijdelijke aanduiding tussen haakjes te vervangen door uw eigen waarden.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Zie How [to use soft-delete with PowerShell (Soft-delete](../../key-vault/general/key-vault-recovery.md)gebruiken met PowerShell) voor meer informatie over het inschakelen van beveiliging tegen opsluizen in een bestaande sleutelkluis met PowerShell.

Wijs vervolgens een door het systeem toegewezen beheerde identiteit toe aan uw opslagaccount. U gebruikt deze beheerde identiteit om het opslagaccount toegang te verlenen tot de sleutelkluis. Zie Wat zijn beheerde identiteiten voor Azure-resources? voor meer informatie over door het systeem toegewezen [beheerde identiteiten.](../../active-directory/managed-identities-azure-resources/overview.md)

Als u een beheerde identiteit wilt toewijzen met behulp van PowerShell, roept [u Set-AzStorageAccount aan:](/powershell/module/az.storage/set-azstorageaccount)

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Configureer ten slotte het toegangsbeleid voor de sleutelkluis, zodat het opslagaccount toegangsrechten heeft. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslagaccount hebt toegewezen.

Als u het toegangsbeleid voor de sleutelkluis wilt instellen, roept u [Set-AzKeyVaultAccessPolicy aan:](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een nieuwe sleutelkluis wilt maken met behulp van Azure CLI, roept [u az keyvault create aan.](/cli/azure/keyvault#az_keyvault_create) Vergeet niet om de tijdelijke aanduidingswaarden tussen vierkante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Zie How [to use soft-delete with CLI (Soft-Delete](../../key-vault/general/key-vault-recovery.md)gebruiken met CLI) voor meer informatie over het inschakelen van beveiliging tegen opsluizen op een bestaande sleutelkluis met Azure CLI.

Wijs vervolgens een door het systeem toegewezen beheerde identiteit toe aan het opslagaccount. U gebruikt deze beheerde identiteit om het opslagaccount toegang te verlenen tot de sleutelkluis. Zie Wat zijn beheerde identiteiten voor Azure-resources? voor meer informatie over door het systeem toegewezen [beheerde identiteiten.](../../active-directory/managed-identities-azure-resources/overview.md)

Als u een beheerde identiteit wilt toewijzen met behulp van Azure CLI, roept u [az storage account update aan:](/cli/azure/storage/account#az_storage_account_update)

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Configureer ten slotte het toegangsbeleid voor de sleutelkluis, zodat het opslagaccount toegangsrechten heeft. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslagaccount hebt toegewezen.

Als u het toegangsbeleid voor de sleutelkluis wilt instellen, roept u [az keyvault set-policy aan:](/cli/azure/keyvault#az_keyvault_set_policy)

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Een sleutel toevoegen

Voeg vervolgens een sleutel toe aan de sleutelkluis.

Azure Storage-versleuteling ondersteunt RSA- en RSA-HSM-sleutels van grootten 2048, 3072 en 4096. Zie Over sleutels voor meer informatie [over sleutels.](../../key-vault/keys/about-keys.md)

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Zie [Quickstart:](../../key-vault/keys/quick-create-portal.md)Set and retrieve a key from Azure Key Vault using the Azure Portal Azure Portal (Snelstart: Een sleutel instellen en ophalen uit een Azure Key Vault met behulp van de Azure Portal.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een sleutel wilt toevoegen met PowerShell, roept [u Add-AzKeyVaultKey aan.](/powershell/module/az.keyvault/add-azkeyvaultkey) Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden en om de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een sleutel wilt toevoegen met Azure CLI, roept u [az keyvault key create aan.](/cli/azure/keyvault/key#az_keyvault_key_create) Vergeet niet om de waarden van de tijdelijke aanduiding tussen haakjes te vervangen door uw eigen waarden.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

Configureer vervolgens uw Azure Storage voor het gebruik van door de klant beheerde sleutels met Azure Key Vault en geef vervolgens de sleutel op die u aan het opslagaccount wilt koppelen.

Wanneer u versleuteling configureert met door de klant beheerde sleutels, kunt u ervoor kiezen om automatisch de sleutelversie bij te werken die wordt gebruikt voor Azure Storage-versleuteling wanneer er een nieuwe versie beschikbaar is in de gekoppelde sleutelkluis. U kunt ook expliciet een sleutelversie opgeven die moet worden gebruikt voor versleuteling totdat de sleutelversie handmatig wordt bijgewerkt.

> [!NOTE]
> Als u een sleutel wilt roteren, maakt u een nieuwe versie van de sleutel in Azure Key Vault. Azure Storage verwerkt de rotatie van de sleutel in Azure Key Vault niet, dus u moet uw sleutel handmatig roteren of een functie maken om deze volgens een schema te roteren.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Versleuteling configureren voor het automatisch bijwerken van sleutelversies

Azure Storage kan de door de klant beheerde sleutel die wordt gebruikt voor versleuteling automatisch bijwerken om de nieuwste sleutelversie te gebruiken. Wanneer de door de klant beheerde sleutel wordt geroteerd in Azure Key Vault, Azure Storage automatisch de nieuwste versie van de sleutel voor versleuteling gaan gebruiken.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u door de klant beheerde sleutels wilt configureren met het automatisch bijwerken van de sleutelversie in Azure Portal, volgt u deze stappen:

1. Ga naar uw opslagaccount.
1. Klik op **de** blade Instellingen voor het opslagaccount op **Versleuteling.** Sleutelbeheer is standaard ingesteld op **Door Microsoft beheerde sleutels**, zoals wordt weergegeven in de volgende afbeelding.

    ![Schermopname van de portal met de versleutelingsoptie](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Selecteer de **optie Door de klant beheerde** sleutels.
1. Kies de **optie Selecteren Key Vault** selecteren.
1. Selecteer **Een sleutelkluis en sleutel selecteren.**
1. Selecteer de sleutelkluis met de sleutel die u wilt gebruiken.
1. Selecteer de sleutel in de sleutelkluis.

   ![Schermopname die laat zien hoe u de sleutelkluis en sleutel selecteert](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Sla uw wijzigingen op.

Nadat u de sleutel hebt opgegeven, geeft de Azure Portal aan dat automatisch bijwerken van de sleutelversie is ingeschakeld en wordt de sleutelversie weergegeven die momenteel wordt gebruikt voor versleuteling.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Schermopname van het automatisch bijwerken van de ingeschakelde sleutelversie":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u door de klant beheerde sleutels wilt configureren met het automatisch bijwerken van de sleutelversie met PowerShell, installeert u de [Az.Storage-module,](https://www.powershellgallery.com/packages/Az.Storage) versie 2.0.0 of hoger.

Als u de sleutelversie voor een door de klant beheerde sleutel automatisch wilt bijwerken, laat u de sleutelversie weg wanneer u versleuteling configureert met door de klant beheerde sleutels voor het opslagaccount. Roep [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelingsinstellingen van het opslagaccount bij te werken, zoals wordt weergegeven in het volgende voorbeeld, en neem de optie **-KeyvaultEncryption** op om door de klant beheerde sleutels in teschakelen voor het opslagaccount.

Vergeet niet om de tijdelijke aanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en om de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u door de klant beheerde sleutels wilt configureren met het automatisch bijwerken van de sleutelversie met Azure CLI, installeert u Azure CLI versie [2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Als u de sleutelversie voor een door de klant beheerde sleutel automatisch wilt bijwerken, laat u de sleutelversie weg wanneer u versleuteling configureert met door de klant beheerde sleutels voor het opslagaccount. Roep [az storage account update aan om](/cli/azure/storage/account#az_storage_account_update) de versleutelingsinstellingen van het opslagaccount bij te werken, zoals wordt weergegeven in het volgende voorbeeld. Neem de `--encryption-key-source` parameter op en stel deze in op om door de klant `Microsoft.Keyvault` beheerde sleutels voor het account in teschakelen.

Vergeet niet om de waarden van de tijdelijke aanduiding tussen haakjes te vervangen door uw eigen waarden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Versleuteling configureren voor handmatig bijwerken van sleutelversies

Als u liever handmatig de sleutelversie bijwerkt, geeft u expliciet de versie op het moment dat u versleuteling configureert met door de klant beheerde sleutels. In dit geval wordt Azure Storage sleutelversie niet automatisch bijgewerkt wanneer er een nieuwe versie wordt gemaakt in de sleutelkluis. Als u een nieuwe sleutelversie wilt gebruiken, moet u handmatig de versie bijwerken die wordt gebruikt voor Azure Storage versleuteling.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u door de klant beheerde sleutels wilt configureren met het handmatig bijwerken van de sleutelversie in de Azure Portal, geeft u de sleutel-URI op, inclusief de versie. Volg deze stappen om een sleutel op te geven als een URI:

1. Als u de sleutel-URI in de Azure Portal, gaat u naar uw sleutelkluis en selecteert u de **instelling** Sleutels. Selecteer de gewenste sleutel en klik vervolgens op de sleutel om de versies ervan te bekijken. Selecteer een sleutelversie om de instellingen voor die versie te bekijken.
1. Kopieer de waarde van het **veld Sleutel-id,** dat de URI levert.

    ![Schermopname van sleutel-URI van sleutelkluis](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. Kies bij **Versleutelingssleutelinstellingen** voor uw opslagaccount de **optie Sleutel-URI** invoeren.
1. Plak de URI die u hebt gekopieerd in het **veld Sleutel-URI.** Laat de sleutelversie weg uit de URI om automatisch bijwerken van de sleutelversie mogelijk te maken.

   ![Schermopname die laat zien hoe u de sleutel-URI kunt invoeren](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Geef het abonnement op dat de sleutelkluis bevat.
1. Sla uw wijzigingen op.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u door de klant beheerde sleutels wilt configureren met het handmatig bijwerken van de sleutelversie, geeft u expliciet de sleutelversie op wanneer u versleuteling voor het opslagaccount configureert. Roep [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelingsinstellingen van het opslagaccount bij te werken, zoals wordt weergegeven in het volgende voorbeeld, en neem de optie **-KeyvaultEncryption** op om door de klant beheerde sleutels in teschakelen voor het opslagaccount.

Vergeet niet om de tijdelijke aanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en om de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Wanneer u de sleutelversie handmatig bijwerkt, moet u de versleutelingsinstellingen van het opslagaccount bijwerken om de nieuwe versie te gebruiken. Roep eerst [Get-AzKeyVaultKey aan om](/powershell/module/az.keyvault/get-azkeyvaultkey) de nieuwste versie van de sleutel op te halen. Roep vervolgens [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelingsinstellingen van het opslagaccount bij te werken om de nieuwe versie van de sleutel te gebruiken, zoals wordt weergegeven in het vorige voorbeeld.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u door de klant beheerde sleutels wilt configureren met het handmatig bijwerken van de sleutelversie, geeft u expliciet de sleutelversie op wanneer u versleuteling voor het opslagaccount configureert. Roep [az storage account update aan om](/cli/azure/storage/account#az_storage_account_update) de versleutelingsinstellingen van het opslagaccount bij te werken, zoals wordt weergegeven in het volgende voorbeeld. Neem de `--encryption-key-source` parameter op en stel deze in op om door de klant `Microsoft.Keyvault` beheerde sleutels voor het account in teschakelen.

Vergeet niet om de waarden van de tijdelijke aanduiding tussen vierkante haken te vervangen door uw eigen waarden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

Wanneer u de sleutelversie handmatig bijwerkt, moet u de versleutelingsinstellingen van het opslagaccount bijwerken om de nieuwe versie te gebruiken. Zoek eerst naar de sleutelkluis-URI door [az keyvault show](/cli/azure/keyvault#az_keyvault_show)aan te roepen en voor de sleutelversie door [az keyvault key list-versions aan te roepen.](/cli/azure/keyvault/key#az_keyvault_key_list-versions) Roep vervolgens [az storage account update aan om](/cli/azure/storage/account#az_storage_account_update) de versleutelingsinstellingen van het opslagaccount bij te werken om de nieuwe versie van de sleutel te gebruiken, zoals wordt weergegeven in het vorige voorbeeld.

---

## <a name="change-the-key"></a>De sleutel wijzigen

U kunt de sleutel die u gebruikt voor Azure Storage versleuteling op elk moment wijzigen.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u de sleutel wilt wijzigen met Azure Portal, volgt u deze stappen:

1. Navigeer naar uw opslagaccount en geef de **versleutelingsinstellingen** weer.
1. Selecteer de sleutelkluis en kies een nieuwe sleutel.
1. Sla uw wijzigingen op.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de sleutel wilt wijzigen met PowerShell, roept u [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan, zoals wordt weergegeven [in](#configure-encryption-with-customer-managed-keys) Versleuteling configureren met door de klant beheerde sleutels, en geeft u de nieuwe sleutelnaam en -versie op. Als de nieuwe sleutel zich in een andere sleutelkluis, moet u ook de sleutelkluis-URI bijwerken.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de sleutel wilt wijzigen met Azure CLI, roept u [az storage account update](/cli/azure/storage/account#az_storage_account_update) aan, zoals wordt weergegeven in Versleuteling configureren met door de klant beheerde sleutels en geeft u de nieuwe sleutelnaam en -versie op. [](#configure-encryption-with-customer-managed-keys) Als de nieuwe sleutel zich in een andere sleutelkluis, moet u ook de sleutelkluis-URI bijwerken.

---

## <a name="revoke-customer-managed-keys"></a>Door de klant beheerde sleutels intrekken

Als u een door de klant beheerde sleutel inroept, wordt de associatie tussen het opslagaccount en de sleutelkluis verwijderd.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u door de klant beheerde sleutels wilt intrekken met Azure Portal, schakelt u de sleutel uit zoals beschreven in [Door de klant beheerde sleutels uitschakelen.](#disable-customer-managed-keys)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

U kunt door de klant beheerde sleutels intrekken door het toegangsbeleid voor de sleutelkluis te verwijderen. Als u een door de klant beheerde sleutel wilt intrekken met PowerShell, roept u de opdracht [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) aan, zoals wordt weergegeven in het volgende voorbeeld. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden en om de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt door de klant beheerde sleutels intrekken door het toegangsbeleid voor de sleutelkluis te verwijderen. Als u een door de klant beheerde sleutel wilt intrekken met Azure CLI, roept u de opdracht [az keyvault delete-policy](/cli/azure/keyvault#az_keyvault_delete_policy) aan, zoals wordt weergegeven in het volgende voorbeeld. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden en om de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door de klant beheerde sleutels uit schakelen, wordt uw opslagaccount opnieuw versleuteld met door Microsoft beheerde sleutels.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u door de klant beheerde sleutels wilt uitschakelen in Azure Portal, volgt u deze stappen:

1. Navigeer naar uw opslagaccount en geef de **versleutelingsinstellingen** weer.
1. Schakel het selectievakje naast de instelling Uw eigen sleutel **gebruiken uit.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u door de klant beheerde sleutels wilt uitschakelen met PowerShell, roept u [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan met de `-StorageEncryption` optie , zoals wordt weergegeven in het volgende voorbeeld. Vergeet niet om de tijdelijke aanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en om de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u door de klant beheerde sleutels wilt uitschakelen met Azure CLI, roept u [az storage account update aan](/cli/azure/storage/account#az_storage_account_update) en stelt u de in op , zoals wordt weergegeven in het volgende `--encryption-key-source parameter` `Microsoft.Storage` voorbeeld. Vergeet niet om de tijdelijke aanduidingswaarden tussen haakjes te vervangen door uw eigen waarden en om de variabelen te gebruiken die in de vorige voorbeelden zijn gedefinieerd.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](customer-managed-keys-overview.md)
- [Versleuteling configureren met door de klant beheerde sleutels die zijn Azure Key Vault beheerde HSM (preview)](customer-managed-keys-configure-key-vault-hsm.md)
