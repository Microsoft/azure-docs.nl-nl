---
title: Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault
titleSuffix: Azure Storage
description: Meer informatie over het configureren van Azure Storage versleuteling met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault met behulp van de Azure Portal, Power shell of Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 24fbe843986b732a04c9e356c54f3d768d6739be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100558185"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault

Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u uw eigen sleutels beheren. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault of Key Vault beheerde hardware security model (HSM) (preview).

In dit artikel wordt beschreven hoe u versleuteling configureert met door de klant beheerde sleutels die zijn opgeslagen in een sleutel kluis met behulp van de Azure Portal, Power shell of Azure CLI. Zie [Configure Encryption with door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault Managed HSM (preview)](customer-managed-keys-configure-key-vault-hsm.md)voor meer informatie over het configureren van versleuteling met door de klant beheerde sleutels die zijn opgeslagen in een beheerde HSM.

> [!NOTE]
> Azure Key Vault en Azure Key Vault beheerde HSM ondersteunen dezelfde Api's en beheer interfaces voor configuratie.

## <a name="configure-a-key-vault"></a>Een sleutel kluis configureren

U kunt een nieuwe of bestaande sleutel kluis gebruiken om door de klant beheerde sleutels op te slaan. Het opslag account en de sleutel kluis moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

Voor het gebruik van door de klant beheerde sleutels met Azure Storage versleuteling moet zowel de functie voor voorlopig verwijderen als voor leegmaken worden ingeschakeld voor de sleutel kluis. Voorlopig verwijderen is standaard ingeschakeld bij het maken van een nieuwe sleutel kluis en kan niet worden uitgeschakeld. U kunt het leegmaken van de beveiliging inschakelen wanneer u de sleutel kluis maakt of nadat deze is gemaakt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Zie [Quick Start: een sleutel kluis maken met behulp van de Azure Portal](../../key-vault/general/quick-create-portal.md)voor meer informatie over het maken van een sleutel kluis met de Azure Portal. Wanneer u de sleutel kluis maakt, selecteert u **beveiliging opschonen inschakelen**, zoals wordt weer gegeven in de volgende afbeelding.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Scherm afbeelding die laat zien hoe u het leegmaken van beveiliging inschakelt bij het maken van een sleutel kluis":::

Voer de volgende stappen uit om de beveiliging op een bestaande sleutel kluis leeg te maken:

1. Navigeer naar uw sleutel kluis in de Azure Portal.
1. Kies onder **instellingen** de optie **Eigenschappen**.
1. Kies **beveiliging opschonen inschakelen** in de sectie **beveiliging opschonen** .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een nieuwe sleutel kluis met Power shell wilt maken, installeert u versie 2.0.0 of hoger van de [AZ.](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) de Power shell-module. Roep vervolgens [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) aan om een nieuwe sleutel kluis te maken. Met versie 2.0.0 en hoger van de module AZ. key kluis, wordt zacht verwijderen standaard ingeschakeld wanneer u een nieuwe sleutel kluis maakt.

In het volgende voor beeld wordt een nieuwe sleutel kluis gemaakt met zowel de functie voor voorlopig verwijderen als de beveiliging opschonen ingeschakeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Zie voor meer informatie over het inschakelen van schone beveiliging op een bestaande sleutel kluis met Power shell voor [het gebruik van voorlopig verwijderen met Power shell](../../key-vault/general/key-vault-recovery.md).

Wijs vervolgens een door het systeem toegewezen beheerde identiteit toe aan uw opslag account. U gebruikt deze beheerde identiteit om de machtigingen voor het opslag account te verlenen voor toegang tot de sleutel kluis. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over door het systeem toegewezen beheerde identiteiten.

Als u een beheerde identiteit wilt toewijzen met behulp van Power shell, roept u [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount):

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Ten slotte configureert u het toegangs beleid voor de sleutel kluis zodat het opslag account toegang heeft tot het. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslag account hebt toegewezen.

Om het toegangs beleid voor de sleutel kluis in te stellen, roept u [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een nieuwe sleutel kluis wilt maken met behulp van Azure CLI, roept u de opdracht [AZ Key kluis Create](/cli/azure/keyvault#az-keyvault-create)aan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Zie voor meer informatie over het inschakelen van schone beveiliging op een bestaande sleutel kluis met Azure CLI [hoe u zacht-Delete gebruikt met CLI](../../key-vault/general/key-vault-recovery.md).

Wijs vervolgens een door het systeem toegewezen beheerde identiteit toe aan het opslag account. U gebruikt deze beheerde identiteit om de machtigingen voor het opslag account te verlenen voor toegang tot de sleutel kluis. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over door het systeem toegewezen beheerde identiteiten.

Als u een beheerde identiteit wilt toewijzen met behulp van Azure CLI, roept u de [Update AZ Storage account](/cli/azure/storage/account#az-storage-account-update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Ten slotte configureert u het toegangs beleid voor de sleutel kluis zodat het opslag account toegang heeft tot het. In deze stap gebruikt u de beheerde identiteit die u eerder aan het opslag account hebt toegewezen.

Om het toegangs beleid voor de sleutel kluis in te stellen, roept u het [beleid AZ-set-Policy](/cli/azure/keyvault#az-keyvault-set-policy):

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

Voeg vervolgens een sleutel toe aan de sleutel kluis.

Azure Storage versleuteling ondersteunt RSA-en RSA-HSM-sleutels met een grootte van 2048, 3072 en 4096. Zie [about Keys](../../key-vault/keys/about-keys.md)(Engelstalig) voor meer informatie over sleutels.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voor informatie over het toevoegen van een sleutel met de Azure Portal, raadpleegt u [Quick Start: een sleutel instellen en ophalen van Azure Key Vault met behulp van de Azure Portal](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een sleutel wilt toevoegen met Power shell, roept u [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)aan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een sleutel wilt toevoegen met Azure CLI, roept u de [sleutel maken AZ Key kluis Create](/cli/azure/keyvault/key#az-keyvault-key-create). Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

Configureer vervolgens uw Azure Storage-account voor het gebruik van door de klant beheerde sleutels met Azure Key Vault en geef vervolgens de sleutel op die u wilt koppelen aan het opslag account.

Wanneer u versleuteling met door de klant beheerde sleutels configureert, kunt u ervoor kiezen om automatisch de sleutel versie bij te werken die wordt gebruikt voor Azure Storage versleuteling wanneer een nieuwe versie beschikbaar is in de bijbehorende sleutel kluis. U kunt ook expliciet een sleutel versie opgeven die moet worden gebruikt voor versleuteling totdat de sleutel versie hand matig wordt bijgewerkt.

> [!NOTE]
> Als u een sleutel wilt draaien, maakt u een nieuwe versie van de sleutel in Azure Key Vault. Azure Storage de draai hoek van de sleutel in Azure Key Vault niet verwerkt. u moet de sleutel dus hand matig draaien of een functie maken om deze op een schema te draaien.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Versleuteling configureren voor het automatisch bijwerken van belang rijke versies

Azure Storage kunt de door de klant beheerde sleutel die wordt gebruikt voor versleuteling automatisch bijwerken om de nieuwste sleutel versie te gebruiken. Wanneer de door de klant beheerde sleutel is gedraaid in Azure Key Vault, zal Azure Storage automatisch de meest recente versie van de sleutel gebruiken voor versleuteling.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u door de klant beheerde sleutels wilt configureren met automatische updates van de sleutel versie in de Azure Portal, voert u de volgende stappen uit:

1. Ga naar uw opslagaccount.
1. Klik op de Blade **instellingen** voor het opslag account op **versleuteling**. Sleutel beheer is standaard ingesteld op door **micro soft beheerde sleutels**, zoals wordt weer gegeven in de volgende afbeelding.

    ![Scherm opname van de portal met versleutelings optie](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Selecteer de optie door de **klant beheerde sleutels** .
1. Kies de optie **selecteren uit Key Vault** .
1. Selecteer **een sleutel kluis en sleutel selecteren**.
1. Selecteer de sleutel kluis met de sleutel die u wilt gebruiken.
1. Selecteer de sleutel in de sleutel kluis.

   ![Scherm afbeelding die laat zien hoe u de sleutel kluis en de sleutel selecteert](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Sla uw wijzigingen op.

Nadat u de sleutel hebt opgegeven, geeft het Azure Portal aan dat automatisch bijwerken van de sleutel versie is ingeschakeld en geeft de sleutel versie weer die momenteel wordt gebruikt voor versleuteling.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Scherm opname van het automatisch bijwerken van de sleutel versie ingeschakeld":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u door de klant beheerde sleutels wilt configureren met automatische updates van de sleutel versie met behulp van Power shell, installeert u de [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) -module, versie 2.0.0 of hoger.

Als u de sleutel versie automatisch wilt bijwerken voor een door de klant beheerde sleutel, laat u de sleutel versie weg wanneer u versleuteling configureert met door de klant beheerde sleutels voor het opslag account. Roep [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelings instellingen van het opslag account bij te werken, zoals wordt weer gegeven in het volgende voor beeld, en neem de optie **-KeyvaultEncryption** op om door de klant beheerde sleutels voor het opslag account in te scha kelen.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u door de klant beheerde sleutels wilt configureren met automatische updates van de sleutel versie met Azure CLI, installeert u [Azure CLI versie 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Als u de sleutel versie automatisch wilt bijwerken voor een door de klant beheerde sleutel, laat u de sleutel versie weg wanneer u versleuteling configureert met door de klant beheerde sleutels voor het opslag account. Roep [AZ Storage account update](/cli/azure/storage/account#az-storage-account-update) aan om de versleutelings instellingen van het opslag account bij te werken, zoals wordt weer gegeven in het volgende voor beeld. Neem de `--encryption-key-source` para meter op en stel deze in om door `Microsoft.Keyvault` de klant beheerde sleutels voor het account in te scha kelen.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

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

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Versleuteling configureren voor het hand matig bijwerken van belang rijke versies

Als u de sleutel versie liever hand matig bijwerkt, geeft u de versie expliciet op op het moment dat u versleuteling configureert met door de klant beheerde sleutels. In dit geval wordt de sleutel versie in Azure Storage niet automatisch bijgewerkt wanneer er een nieuwe versie wordt gemaakt in de sleutel kluis. Als u een nieuwe sleutel versie wilt gebruiken, moet u de versie die voor Azure Storage versleuteling wordt gebruikt, hand matig bijwerken.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u door de klant beheerde sleutels wilt configureren met hand matig bijwerken van de sleutel versie in de Azure Portal, geeft u de sleutel-URI op, inclusief de versie. Voer de volgende stappen uit om een sleutel als URI op te geven:

1. Als u de sleutel-URI in de Azure Portal wilt zoeken, navigeert u naar uw sleutel kluis en selecteert u de instelling **sleutels** . Selecteer de gewenste sleutel en klik vervolgens op de sleutel om de versies ervan weer te geven. Selecteer een sleutel versie om de instellingen voor die versie weer te geven.
1. Kopieer de waarde van het veld **sleutel-id** , dat de URI levert.

    ![Scherm opname van sleutel kluis sleutel-URI](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. Kies in de instellingen voor de versleutelings **sleutel** voor uw opslag account de optie **sleutel-URI opgeven** .
1. Plak de URI die u hebt gekopieerd in het veld **sleutel-URI** . Laat de sleutel versie van de URI weg om automatische updates van de sleutel versie in te scha kelen.

   ![Scherm afbeelding die laat zien hoe de sleutel-URI moet worden ingevoerd](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Geef het abonnement op dat de sleutel kluis bevat.
1. Sla uw wijzigingen op.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u door de klant beheerde sleutels wilt configureren met hand matig bijwerken van de sleutel versie, geeft u de sleutel versie expliciet op wanneer u versleuteling configureert voor het opslag account. Roep [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelings instellingen van het opslag account bij te werken, zoals wordt weer gegeven in het volgende voor beeld, en neem de optie **-KeyvaultEncryption** op om door de klant beheerde sleutels voor het opslag account in te scha kelen.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Wanneer u de sleutel versie hand matig bijwerkt, moet u de versleutelings instellingen van het opslag account bijwerken om de nieuwe versie te gebruiken. Roep eerst [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) aan om de meest recente versie van de sleutel op te halen. Roep vervolgens [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan om de versleutelings instellingen van het opslag account bij te werken voor het gebruik van de nieuwe versie van de sleutel, zoals wordt weer gegeven in het vorige voor beeld.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u door de klant beheerde sleutels wilt configureren met hand matig bijwerken van de sleutel versie, geeft u de sleutel versie expliciet op wanneer u versleuteling configureert voor het opslag account. Roep [AZ Storage account update](/cli/azure/storage/account#az-storage-account-update) aan om de versleutelings instellingen van het opslag account bij te werken, zoals wordt weer gegeven in het volgende voor beeld. Neem de `--encryption-key-source` para meter op en stel deze in om door `Microsoft.Keyvault` de klant beheerde sleutels voor het account in te scha kelen.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden.

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

Wanneer u de sleutel versie hand matig bijwerkt, moet u de versleutelings instellingen van het opslag account bijwerken om de nieuwe versie te gebruiken. Eerst moet u een query uitvoeren voor de sleutel kluis-URI door het aanroepen van [AZ Key kluis show](/cli/azure/keyvault#az-keyvault-show), en voor de sleutel versie door het aanroepen van [AZ sleutel kluis Key List-versies](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Roep vervolgens [AZ Storage account update](/cli/azure/storage/account#az-storage-account-update) aan om de versleutelings instellingen van het opslag account bij te werken om de nieuwe versie van de sleutel te gebruiken, zoals wordt weer gegeven in het vorige voor beeld.

---

## <a name="change-the-key"></a>De sleutel wijzigen

U kunt de sleutel die u gebruikt voor Azure Storage versleuteling op elk gewenst moment wijzigen.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om de sleutel te wijzigen met de Azure Portal:

1. Navigeer naar uw opslag account en geef de **versleutelings** instellingen weer.
1. Selecteer de sleutel kluis en kies een nieuwe sleutel.
1. Sla uw wijzigingen op.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de sleutel met Power shell wilt wijzigen, roept u [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan, zoals wordt weer gegeven in [versleuteling configureren met door de klant beheerde sleutels](#configure-encryption-with-customer-managed-keys) en de nieuwe sleutel naam en versie opgeven. Als de nieuwe sleutel zich in een andere sleutel kluis bevindt, moet u ook de sleutel kluis-URI bijwerken.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de sleutel wilt wijzigen met Azure CLI, roept u de [Update AZ Storage account](/cli/azure/storage/account#az-storage-account-update) aan, zoals wordt weer gegeven in [versleuteling configureren met door de klant beheerde sleutels](#configure-encryption-with-customer-managed-keys) en de nieuwe sleutel naam en-versie opgeven. Als de nieuwe sleutel zich in een andere sleutel kluis bevindt, moet u ook de sleutel kluis-URI bijwerken.

---

## <a name="revoke-customer-managed-keys"></a>Door de klant beheerde sleutels intrekken

Als u een door de klant beheerde sleutel intrekt, wordt de koppeling tussen het opslag account en de sleutel kluis verwijderd.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u door de klant beheerde sleutels wilt intrekken met de Azure Portal, schakelt u de sleutel uit zoals beschreven in door de [klant beheerde sleutels uitschakelen](#disable-customer-managed-keys).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

U kunt door de klant beheerde sleutels intrekken door het toegangs beleid voor de sleutel kluis te verwijderen. Als u een door de klant beheerde sleutel wilt intrekken met Power shell, roept u de opdracht [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) aan, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt door de klant beheerde sleutels intrekken door het toegangs beleid voor de sleutel kluis te verwijderen. Als u een door de klant beheerde sleutel wilt intrekken met Azure CLI, roept u de opdracht [AZ sleutel kluis delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) aan, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door de klant beheerde sleutels uitschakelt, wordt uw opslag account opnieuw versleuteld met door micro soft beheerde sleutels.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om door de klant beheerde sleutels uit te scha kelen in de Azure Portal:

1. Navigeer naar uw opslag account en geef de **versleutelings** instellingen weer.
1. Schakel het selectie vakje naast de instelling **uw eigen sleutel gebruiken** uit.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u door de klant beheerde sleutels wilt uitschakelen met Power shell, roept u [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) met de `-StorageEncryption` optie aan, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u door de klant beheerde sleutels wilt uitschakelen met Azure CLI, roept u de [Update AZ Storage account](/cli/azure/storage/account#az-storage-account-update) aan en stelt `--encryption-key-source parameter` u de in op `Microsoft.Storage` , zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden en de variabelen te gebruiken die in de voor gaande voor beelden zijn gedefinieerd.

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
- [Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault beheerde HSM (preview-versie)](customer-managed-keys-configure-key-vault-hsm.md)
