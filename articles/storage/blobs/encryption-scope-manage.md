---
title: Versleutelingsbereiken maken en beheren
description: Meer informatie over het maken van een versleutelingsbereik voor het isoleren van blobgegevens op container- of blobniveau.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 656443b0bc9d0e45f43634b1b4c21145de7a5bb5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792539"
---
# <a name="create-and-manage-encryption-scopes"></a>Versleutelingsbereiken maken en beheren

Met een versleutelingsbereik kunt u versleuteling op het niveau van een afzonderlijke blob of container beheren. U kunt versleutelingsbereiken gebruiken om beveiligde grenzen te creëren tussen gegevens die zich in hetzelfde opslagaccount bevinden, maar van verschillende klanten zijn. Zie Versleutelingsbereiken voor Blob Storage voor meer informatie [over versleutelingsbereiken.](encryption-scope-overview.md)

In dit artikel wordt beschreven hoe u een versleutelingsbereik maakt. U ziet ook hoe u een versleutelingsbereik opgeeft wanneer u een blob of container maakt.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Een versleutelingsbereik maken

U kunt een versleutelingsbereik maken dat wordt beveiligd met een door Microsoft beheerde sleutel of met een door de klant beheerde sleutel die is opgeslagen in een Azure Key Vault of in een Azure Key Vault Managed Hardware Security Model (HSM) (preview). Als u een versleutelingsbereik wilt maken met een door de klant beheerde sleutel, moet u eerst een sleutelkluis of beheerde HSM maken en de sleutel toevoegen die u wilt gebruiken voor het bereik. Voor de sleutelkluis of beheerde HSM moet beveiliging tegen opsluizen zijn ingeschakeld en moet de beveiliging zich in dezelfde regio als het opslagaccount hebben.

Een versleutelingsbereik wordt automatisch ingeschakeld wanneer u het maakt. Nadat u het versleutelingsbereik hebt maken, kunt u dit opgeven wanneer u een blob maakt. U kunt ook een standaardversleutelingsbereik opgeven wanneer u een container maakt, die automatisch van toepassing is op alle blobs in de container.

# <a name="portal"></a>[Portal](#tab/portal)

Als u een versleutelingsbereik wilt maken in Azure Portal, volgt u deze stappen:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **instelling Versleuteling.**
1. Selecteer het **tabblad Versleutelingsbereiken.**
1. Klik op de **knop Toevoegen** om een nieuw versleutelingsbereik toe te voegen.
1. Voer in **het deelvenster Versleutelingsbereik** maken een naam in voor het nieuwe bereik.
1. Selecteer het gewenste type ondersteuning voor versleutelingssleutels, ofwel door **Microsoft** beheerde sleutels of door **de klant beheerde sleutels.**
    - Als u door **Microsoft beheerde sleutels hebt geselecteerd,** klikt u **op Maken om** het versleutelingsbereik te maken.
    - Als u **Door** de klant beheerde sleutels hebt geselecteerd, selecteert u een abonnement en geeft u een sleutelkluis of een beheerde HSM op, evenals een sleutel die moet worden gebruikt voor dit versleutelingsbereik, zoals wordt weergegeven in de volgende afbeelding.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Schermopname die laat zien hoe u een versleutelingsbereik maakt in Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een versleutelingsbereik wilt maken met PowerShell, installeert u de [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) PowerShell-module, versie 3.4.0 of hoger.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Een versleutelingsbereik maken dat wordt beveiligd door door Microsoft beheerde sleutels

Als u een nieuw versleutelingsbereik wilt maken dat wordt beveiligd door door Microsoft beheerde sleutels, roept u de **opdracht New-AzStorageEncryptionScope** aan met de `-StorageEncryption` parameter .

Vergeet niet om de tijdelijke aanduidingswaarden in het voorbeeld te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Een versleutelingsbereik maken dat wordt beveiligd door door de klant beheerde sleutels

Als u een nieuw versleutelingsbereik wilt maken dat wordt beveiligd door door de klant beheerde sleutels die zijn opgeslagen in een sleutelkluis of beheerde HSM, configureert u eerst door de klant beheerde sleutels voor het opslagaccount. U moet een beheerde identiteit toewijzen aan het opslagaccount en vervolgens de beheerde identiteit gebruiken om het toegangsbeleid voor de sleutelkluis of beheerde HSM te configureren, zodat het opslagaccount toegangsmachtigingen heeft.

Als u door de klant beheerde sleutels wilt configureren voor gebruik met een versleutelingsbereik, moet beveiliging tegen opsluizen zijn ingeschakeld op de sleutelkluis of beheerde HSM. De sleutelkluis of beheerde HSM moet zich in dezelfde regio als het opslagaccount.

Vergeet niet om de tijdelijke aanduidingswaarden in het voorbeeld te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Roep vervolgens de **opdracht New-AzStorageEncryptionScope** aan met de `-KeyvaultEncryption` parameter en geef de sleutel-URI op. Het is optioneel om de sleutelversie van de sleutel-URI op te nemen. Als u de sleutelversie weglaten, gebruikt het versleutelingsbereik automatisch de meest recente sleutelversie. Als u de sleutelversie op bevat, moet u de sleutelversie handmatig bijwerken om een andere versie te gebruiken.

Vergeet niet om de tijdelijke aanduidingswaarden in het voorbeeld te vervangen door uw eigen waarden:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u een versleutelingsbereik wilt maken met Azure CLI, installeert u eerst Azure CLI versie 2.20.0 of hoger.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Een versleutelingsbereik maken dat wordt beveiligd door door Microsoft beheerde sleutels

Als u een nieuw versleutelingsbereik wilt maken dat wordt beveiligd door door Microsoft beheerde sleutels, roept u de opdracht [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) aan en geeft u de parameter op als `--key-source` `Microsoft.Storage` . Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Een versleutelingsbereik maken dat wordt beveiligd door door de klant beheerde sleutels

Als u een nieuw versleutelingsbereik wilt maken dat wordt beveiligd door door Microsoft beheerde sleutels, roept u de opdracht [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) aan en geeft u de parameter op als `--key-source` `Microsoft.Storage` . Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden:

Als u een nieuw versleutelingsbereik wilt maken dat wordt beveiligd door door de klant beheerde sleutels in een sleutelkluis of beheerde HSM, configureert u eerst door de klant beheerde sleutels voor het opslagaccount. U moet een beheerde identiteit toewijzen aan het opslagaccount en vervolgens de beheerde identiteit gebruiken om het toegangsbeleid voor de sleutelkluis te configureren, zodat het opslagaccount toegangsrechten heeft. Zie [Door de klant beheerde sleutels voor Azure Storage-versleuteling](../common/customer-managed-keys-overview.md) voor meer informatie.

Als u door de klant beheerde sleutels wilt configureren voor gebruik met een versleutelingsbereik, moet beveiliging tegen opsluizen zijn ingeschakeld op de sleutelkluis of beheerde HSM. De sleutelkluis of beheerde HSM moet zich in dezelfde regio als het opslagaccount hebben.

Vergeet niet om de tijdelijke aanduidingen in het voorbeeld te vervangen door uw eigen waarden:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Roep vervolgens de opdracht **az storage account encryption-scope create** aan met de parameter en geef de `--key-uri` sleutel-URI op. Het is optioneel om de sleutelversie van de sleutel-URI op te nemen. Als u de sleutelversie weglaten, gebruikt het versleutelingsbereik automatisch de meest recente sleutelversie. Als u de sleutelversie op neem, moet u de sleutelversie handmatig bijwerken om een andere versie te gebruiken.

Vergeet niet om de tijdelijke aanduidingen in het voorbeeld te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Zie de volgende artikelen voor meer informatie Azure Storage versleuteling configureert met door de klant beheerde sleutels in een sleutelkluis of beheerde HSM:

- [Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)
- [Configureer versleuteling met door de klant beheerde sleutels die zijn Azure Key Vault beheerde HSM (preview)](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Versleutelingsbereiken voor opslagaccounts opslijst

# <a name="portal"></a>[Portal](#tab/portal)

Als u de versleutelingsbereiken voor een opslagaccount  in de Azure Portal, gaat u naar de instelling Versleutelingsbereiken voor het opslagaccount. In dit deelvenster kunt u een versleutelingsbereik in- of uitschakelen of de sleutel voor een versleutelingsbereik wijzigen.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Schermopname van de lijst met versleutelingsbereiken in Azure Portal":::

Als u details wilt weergeven voor een door de klant beheerde sleutel, met inbegrip van de sleutel-URI en -versie en of de sleutelversie automatisch wordt bijgewerkt, volgt u de koppeling in de **kolom** Sleutel.

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Schermopname met details voor een sleutel die wordt gebruikt met een versleutelingsbereik":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de beschikbare versleutelingsbereiken voor een opslagaccount met PowerShell wilt noemen, roept u de **opdracht Get-AzStorageEncryptionScope** aan. Vergeet niet om de tijdelijke aanduidingen in het voorbeeld te vervangen door uw eigen waarden:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Gebruik de pijplijnsyntaxis om alle versleutelingsbereiken in een resourcegroep per opslagaccount weer te geven:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u de beschikbare versleutelingsbereiken voor een opslagaccount met Azure CLI wilt noemen, roept u de [opdracht az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_list) aan. Vergeet niet om de tijdelijke aanduidingen in het voorbeeld te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Een container met een standaardversleutelingsbereik maken

Wanneer u een container maakt, kunt u een standaardversleutelingsbereik opgeven. Blobs in die container gebruiken dat bereik standaard.

Een afzonderlijke blob kan worden gemaakt met een eigen versleutelingsbereik, tenzij de container is geconfigureerd om te vereisen dat alle blobs het standaardbereik gebruiken. Zie [Versleutelingsbereiken voor containers en blobs voor meer informatie.](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs)

# <a name="portal"></a>[Portal](#tab/portal)

Als u een container met een standaardversleutelingsbereik in de Azure Portal, maakt u eerst het versleutelingsbereik zoals beschreven in [Een versleutelingsbereik maken.](#create-an-encryption-scope) Volg vervolgens deze stappen om de container te maken:

1. Navigeer naar de lijst met containers in uw opslagaccount en selecteer de **knop Toevoegen** om een nieuwe container te maken.
1. Vouw de **geavanceerde instellingen** uit in het **deelvenster Nieuwe** container.
1. Selecteer in **de vervolgkeuze** voor versleutelingsbereik het standaardversleutelingsbereik voor de container.
1. Als u wilt vereisen dat alle blobs in de container het standaardversleutelingsbereik gebruiken, selecteert u het selectievakje Dit versleutelingsbereik gebruiken voor alle **blobs in de container**. Als dit selectievakje is ingeschakeld, kan een afzonderlijke blob in de container het standaardversleutelingsbereik niet overschrijven.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Schermopname van container met standaardversleutelingsbereik":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een container wilt maken met een standaardversleutelingsbereik met PowerShell, roept u de opdracht [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) aan en geeft u het bereik voor de `-DefaultEncryptionScope` parameter op. Als u wilt forceer dat alle blobs in een container het standaardbereik van de container gebruiken, stelt u de `-PreventEncryptionScopeOverride` parameter in op `true` .

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u een container wilt maken met een standaardversleutelingsbereik met Azure CLI, roept u de opdracht [az storage container create](/cli/azure/storage/container#az_storage_container_create) aan en geeft u het bereik voor de parameter `--default-encryption-scope` op. Als u wilt forceer dat alle blobs in een container het standaardbereik van de container gebruiken, stelt u de `--prevent-encryption-scope-override` parameter in op `true` .

In het volgende voorbeeld wordt uw Azure AD-account gebruikt om de bewerking voor het maken van de container te autoriseren. U kunt ook de toegangssleutel voor het account gebruiken. Zie [Toegang verlenen tot blob- of wachtrijgegevens met Azure CLI](./authorize-data-operations-cli.md) voor meer informatie.

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Als een client een bereik probeert op te geven bij het uploaden van een blob naar een container met een standaardversleutelingsbereik en de container is geconfigureerd om te voorkomen dat blobs het standaardbereik overschrijven, mislukt de bewerking met een bericht dat aangeeft dat de aanvraag niet is toegestaan door het containerversleutelingsbeleid.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Een blob met een versleutelingsbereik uploaden

Wanneer u een blob uploadt, kunt u een versleutelingsbereik voor die blob opgeven of het standaardversleutelingsbereik voor de container gebruiken als er een is opgegeven.

# <a name="portal"></a>[Portal](#tab/portal)

Als u een blob met een versleutelingsbereik wilt uploaden via Azure Portal, maakt u eerst het versleutelingsbereik zoals beschreven in [Een versleutelingsbereik maken.](#create-an-encryption-scope) Volg vervolgens deze stappen om de blob te maken:

1. Navigeer naar de container waarin u de blob wilt uploaden.
1. Selecteer de **knop Uploaden** en zoek de blob die u wilt uploaden.
1. Vouw de **geavanceerde instellingen** uit in het **deelvenster Blob** uploaden.
1. Ga naar **de vervolgkeuzesectie** Versleutelingsbereik. De blob wordt standaard gemaakt met het standaardversleutelingsbereik voor de container, als er een is opgegeven. Als de container vereist dat blobs het standaardversleutelingsbereik gebruiken, is deze sectie uitgeschakeld.
1. Als u een ander bereik wilt opgeven voor de blob die u uploadt, selecteert u Een bestaand **bereik kiezen** en selecteert u vervolgens het gewenste bereik in de vervolgkeuzekeuze.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Schermopname die laat zien hoe u een blob met een versleutelingsbereik uploadt":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een blob met een versleutelingsbereik wilt uploaden via PowerShell, roept u de opdracht [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) aan en geeft u het versleutelingsbereik voor de blob op.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u een blob met een versleutelingsbereik wilt uploaden via Azure CLI, roept u de opdracht [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) aan en geeft u het versleutelingsbereik voor de blob op.

Als u een Azure Cloud Shell, volgt u de stappen die worden beschreven in [Een blob uploaden om](storage-quickstart-blobs-cli.md#upload-a-blob) een bestand te maken in de hoofdmap. U kunt dit bestand vervolgens uploaden naar een blob met behulp van het volgende voorbeeld.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>De versleutelingssleutel voor een bereik wijzigen

Als u de sleutel die een versleutelingsbereik bebeveiligen van een door Microsoft beheerde sleutel wilt wijzigen in een door de klant beheerde sleutel, moet u eerst ervoor zorgen dat u door de klant beheerde sleutels hebt ingeschakeld met Azure Key Vault of Key Vault HSM voor het opslagaccount. Zie Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen [in Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md) of Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen [in Azure Key Vault.](../common/customer-managed-keys-configure-key-vault.md)

# <a name="portal"></a>[Portal](#tab/portal)

Als u de sleutel wilt wijzigen die een bereik in de Azure Portal, volgt u deze stappen:

1. Navigeer naar **het tabblad Versleutelingsbereiken** om de lijst met versleutelingsbereiken voor het opslagaccount weer te geven.
1. Selecteer de **knop** Meer naast het bereik dat u wilt wijzigen.
1. In het **deelvenster Versleutelingsbereik** bewerken kunt u het versleutelingstype wijzigen van een door Microsoft beheerde sleutel in een door de klant beheerde sleutel of vice versa.
1. Als u een nieuwe door de klant beheerde sleutel wilt selecteren, selecteert u **Een nieuwe** sleutel gebruiken en geeft u de sleutelkluis, sleutel en sleutelversie op.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de sleutel die een versleutelingsbereik bebeveiligen van een door de klant beheerde sleutel wilt wijzigen in een door Microsoft beheerde sleutel met PowerShell, roept u de opdracht **Update-AzStorageEncryptionScope** aan en geeft u de `-StorageEncryption` parameter door:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Roep vervolgens de opdracht **Update-AzStorageEncryptionScope** aan en geef de `-KeyUri` parameters en `-KeyvaultEncryption` door:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u de sleutel die een versleutelingsbereik bebeveiligen van een door de klant beheerde sleutel wilt wijzigen in een door Microsoft beheerde sleutel met Azure CLI, roept u de opdracht [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) aan en geeft u de parameter door met de waarde `--key-source` `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Roep vervolgens de opdracht **az storage account encryption-scope update** aan, geef de parameter door en geef de parameter door met de waarde `--key-uri` `--key-source` `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Een versleutelingsbereik uitschakelen

Wanneer een versleutelingsbereik is uitgeschakeld, wordt u er niet meer voor gefactureerd. Schakel alle versleutelingsbereiken uit die niet nodig zijn om onnodige kosten te voorkomen. Zie versleuteling Azure Storage [data-at-rest voor meer informatie.](../common/storage-service-encryption.md)

# <a name="portal"></a>[Portal](#tab/portal)

Als u een versleutelingsbereik in het Azure Portal wilt uitschakelen, gaat u naar de instelling Versleutelingsbereiken voor het opslagaccount, selecteert u het gewenste **versleutelingsbereik** en selecteert u **Uitschakelen.**

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een versleutelingsbereik wilt uitschakelen met PowerShell, roept u de opdracht Update-AzStorageEncryptionScope aan en gebruikt u de parameter met de waarde , zoals `-State` wordt weergegeven in het volgende `disabled` voorbeeld. Als u een versleutelingsbereik opnieuw wilt inschakelen, roept u dezelfde opdracht aan met `-State` de parameter ingesteld op `enabled` . Vergeet niet om de tijdelijke aanduidingswaarden in het voorbeeld te vervangen door uw eigen waarden:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u een versleutelingsbereik wilt uitschakelen met Azure CLI, roept u de opdracht [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) aan en neemt u de parameter op met de waarde , zoals wordt weergegeven in het volgende `--state` `Disabled` voorbeeld. Als u een versleutelingsbereik opnieuw wilt inschakelen, roept u dezelfde opdracht aan met `--state` de parameter ingesteld op `Enabled` . Vergeet niet om de tijdelijke aanduidingen in het voorbeeld te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Het is niet mogelijk om een versleutelingsbereik te verwijderen. Om onverwachte kosten te voorkomen, moet u eventuele versleutelingsbereiken uitschakelen die u momenteel niet nodig hebt.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](../common/storage-service-encryption.md)
- [Versleutelingsbereiken voor Blob Storage](encryption-scope-overview.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](../common/customer-managed-keys-overview.md)