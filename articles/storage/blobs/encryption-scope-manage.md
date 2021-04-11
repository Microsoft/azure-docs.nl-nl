---
title: Versleutelings scopes maken en beheren
description: Meer informatie over het maken van een versleutelings bereik voor het isoleren van blobgegevens op het niveau van de container of BLOB.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: c29282637f6854248c98dff59f8fae46ad1a9d39
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640524"
---
# <a name="create-and-manage-encryption-scopes"></a>Versleutelings scopes maken en beheren

Met een versleutelingsbereik kunt u versleuteling op het niveau van een afzonderlijke blob of container beheren. U kunt versleutelings scopes gebruiken om beveiligde grenzen te maken tussen gegevens die zich in hetzelfde opslag account bevinden, maar deel uitmaakt van verschillende klanten. Zie [encrypties voor Blob Storage](encryption-scope-overview.md)voor meer informatie over versleutelings bereiken.

In dit artikel wordt beschreven hoe u een versleutelings bereik maakt. U ziet ook hoe u een versleutelings bereik opgeeft wanneer u een BLOB of container maakt.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Een versleutelings bereik maken

U kunt een versleutelings bereik maken dat wordt beveiligd met een door micro soft beheerde sleutel of met een door de klant beheerde sleutel die is opgeslagen in een Azure Key Vault of in een Azure Key Vault beheerde hardware security model (HSM) (preview). Als u een versleutelings bereik met een door de klant beheerde sleutel wilt maken, moet u eerst een sleutel kluis of beheerde HSM maken en de sleutel die u wilt gebruiken voor de scope toevoegen. De sleutel kluis of beheerde HSM moet de beveiliging opschonen ingeschakeld hebben en moet zich in dezelfde regio bevinden als het opslag account.

Een versleutelings bereik wordt automatisch ingeschakeld wanneer u het maakt. Nadat u het versleutelings bereik hebt gemaakt, kunt u dit opgeven wanneer u een BLOB maakt. U kunt ook een standaard versleutelings bereik opgeven wanneer u een container maakt, die automatisch van toepassing is op alle blobs in de container.

# <a name="portal"></a>[Portal](#tab/portal)

Voer de volgende stappen uit om een versleutelings bereik te maken in de Azure Portal:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **versleutelings** instelling.
1. Selecteer het tabblad **versleutelings bereik** .
1. Klik op de knop **toevoegen** om een nieuw versleutelings bereik toe te voegen.
1. Voer in het deel venster **versleutelings scope maken** een naam in voor de nieuwe scope.
1. Selecteer het gewenste type ondersteuning voor de coderings sleutel, door **micro soft beheerde sleutels** of door de **klant beheerde sleutels**.
    - Als u door **micro soft beheerde sleutels** hebt geselecteerd, klikt u op **maken** om het versleutelings bereik te maken.
    - Als u door de **klant beheerde sleutels** hebt geselecteerd, selecteert u een abonnement en geeft u een sleutel kluis of een beheerde HSM op en een sleutel die moet worden gebruikt voor dit versleutelings bereik, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Scherm afbeelding die laat zien hoe u het versleutelings bereik maakt in Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een versleutelings bereik met Power shell wilt maken, installeert u de module [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) Power shell, version 3.4.0 of hoger.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Een versleutelings bereik maken dat wordt beveiligd door door micro soft beheerde sleutels

Als u een nieuw versleutelings bereik wilt maken dat wordt beveiligd door door micro soft beheerde sleutels, roept u de opdracht **New-AzStorageEncryptionScope** aan met de `-StorageEncryption` para meter.

Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Een versleutelings bereik maken dat wordt beveiligd door door de klant beheerde sleutels

Als u een nieuw versleutelings bereik wilt maken dat wordt beveiligd door door de klant beheerde sleutels die zijn opgeslagen in een sleutel kluis of beheerde HSM, moet u eerst door de klant beheerde sleutels voor het opslag account configureren. U moet een beheerde identiteit toewijzen aan het opslag account en vervolgens de beheerde identiteit gebruiken om het toegangs beleid voor de sleutel kluis of beheerde HSM te configureren, zodat het opslag account machtigingen heeft om het te openen.

Als u door de klant beheerde sleutels wilt configureren voor gebruik met een versleutelings bereik, moet u de beveiliging opschonen inschakelen op de sleutel kluis of beheerde HSM. De sleutel kluis of beheerde HSM moet zich in dezelfde regio bevinden als het opslag account.

Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

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

Vervolgens roept u de opdracht **New-AzStorageEncryptionScope** aan met de `-KeyvaultEncryption` para meter en geeft u de sleutel-URI op. Inclusief de sleutel versie op de sleutel-URI is optioneel. Als u de sleutel versie weglaat, wordt in het versleutelings bereik automatisch de meest recente sleutel versie gebruikt. Als u de sleutel versie opneemt, moet u de sleutel versie hand matig bijwerken om een andere versie te gebruiken.

Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u een versleutelings bereik met Azure CLI wilt maken, moet u eerst Azure CLI versie 2.20.0 of hoger installeren.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Een versleutelings bereik maken dat wordt beveiligd door door micro soft beheerde sleutels

Als u een nieuw versleutelings bereik wilt maken dat wordt beveiligd door door micro soft beheerde sleutels, roept u de opdracht [AZ Storage account Encryption-Scope Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) aan, waarbij u de `--key-source` para meter opgeeft als `Microsoft.Storage` . Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Een versleutelings bereik maken dat wordt beveiligd door door de klant beheerde sleutels

Als u een nieuw versleutelings bereik wilt maken dat wordt beveiligd door door micro soft beheerde sleutels, roept u de opdracht [AZ Storage account Encryption-Scope Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) aan, waarbij u de `--key-source` para meter opgeeft als `Microsoft.Storage` . Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden:

Als u een nieuw versleutelings bereik wilt maken dat wordt beveiligd door door de klant beheerde sleutels in een sleutel kluis of beheerde HSM, moet u eerst door de klant beheerde sleutels voor het opslag account configureren. U moet een beheerde identiteit toewijzen aan het opslag account en vervolgens de beheerde identiteit gebruiken om het toegangs beleid voor de sleutel kluis te configureren, zodat het opslag account machtigingen heeft om het te openen. Zie [Door de klant beheerde sleutels voor Azure Storage-versleuteling](../common/customer-managed-keys-overview.md) voor meer informatie.

Als u door de klant beheerde sleutels wilt configureren voor gebruik met een versleutelings bereik, moet u de beveiliging opschonen inschakelen op de sleutel kluis of beheerde HSM. De sleutel kluis of beheerde HSM moet zich in dezelfde regio bevinden als het opslag account.

Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

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

Vervolgens roept u de opdracht **AZ Storage account Encryption-Scope Create** aan met de `--key-uri` para meter en geeft u de sleutel-URI op. Inclusief de sleutel versie op de sleutel-URI is optioneel. Als u de sleutel versie weglaat, wordt in het versleutelings bereik automatisch de meest recente sleutel versie gebruikt. Als u de sleutel versie opneemt, moet u de sleutel versie hand matig bijwerken om een andere versie te gebruiken.

Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Raadpleeg de volgende artikelen voor meer informatie over het configureren van Azure Storage versleuteling met door de klant beheerde sleutels in een sleutel kluis of beheerde HSM:

- [Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)
- [Configureer versleuteling met door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault beheerde HSM (preview)](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Versleutelings bereik voor opslag account weer geven

# <a name="portal"></a>[Portal](#tab/portal)

Als u de versleutelings bereiken voor een opslag account in de Azure Portal wilt weer geven, gaat u naar de instelling **versleutelings bereik** voor het opslag account. In dit deel venster kunt u een versleutelings bereik in-of uitschakelen of de sleutel voor een versleutelings bereik wijzigen.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Scherm opname met een lijst met versleutelings bereiken in Azure Portal":::

Als u details wilt weer geven van een door de klant beheerde sleutel, met inbegrip van de sleutel-URI en-versie en of de sleutel versie automatisch wordt bijgewerkt, volgt u de koppeling in de **sleutel** kolom.

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Scherm afbeelding met Details voor een sleutel die wordt gebruikt met een versleutelings bereik":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een lijst wilt weer geven met de versleutelings bereiken die beschikbaar zijn voor een opslag account met Power shell, roept u de opdracht **Get-AzStorageEncryptionScope** aan. Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Als u alle versleutelings bereiken in een resource groep op basis van een opslag account wilt weer geven, gebruikt u de pijplijn syntaxis:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u een lijst wilt weer geven met de versleutelings bereiken die beschikbaar zijn voor een opslag account met Azure CLI, roept u de opdracht [AZ Storage account Encryption-Scope List](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) . Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Een container maken met een standaard versleutelings bereik

Wanneer u een container maakt, kunt u een standaard versleutelings bereik opgeven. Blobs in deze container gebruiken standaard dat bereik.

Een afzonderlijke Blob kan worden gemaakt met een eigen versleutelings bereik, tenzij de container is geconfigureerd om te vereisen dat alle blobs het standaard bereik gebruiken. Zie [versleutelings bereiken voor containers en blobs](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs)voor meer informatie.

# <a name="portal"></a>[Portal](#tab/portal)

Als u een container met een standaard versleutelings bereik in de Azure Portal wilt maken, moet u eerst het versleutelings bereik maken, zoals beschreven in [een versleutelings bereik maken](#create-an-encryption-scope). Voer vervolgens de volgende stappen uit om de container te maken:

1. Navigeer naar de lijst met containers in uw opslag account en selecteer de knop **toevoegen** om een nieuwe container te maken.
1. Vouw de **Geavanceerde** instellingen in het deel venster **nieuwe container** uit.
1. Selecteer in de vervolg keuzelijst **versleutelings bereik** het standaard versleutelings bereik voor de container.
1. Als u wilt dat alle blobs in de container het standaard versleutelings bereik gebruiken, schakelt u het selectie vakje in om **Dit versleutelings bereik te gebruiken voor alle blobs in de container**. Als dit selectie vakje is ingeschakeld, kan een afzonderlijke Blob in de container het standaard versleutelings bereik niet overschrijven.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Scherm opname met container met standaard versleutelings bereik":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een container met een standaard versleutelings bereik met Power shell wilt maken, roept u de opdracht [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) aan, waarbij u het bereik voor de `-DefaultEncryptionScope` para meter opgeeft. Als u wilt afdwingen dat alle blobs in een container het standaard bereik van de container gebruiken, stelt `-PreventEncryptionScopeOverride` u de para meter in op `true` .

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

Als u een container met een standaard versleutelings bereik met Azure CLI wilt maken, roept u de opdracht [AZ storage container Create](/cli/azure/storage/container#az-storage-container-create) aan, waarbij u het bereik voor de `--default-encryption-scope` para meter opgeeft. Als u wilt afdwingen dat alle blobs in een container het standaard bereik van de container gebruiken, stelt `--prevent-encryption-scope-override` u de para meter in op `true` .

In het volgende voorbeeld wordt uw Azure AD-account gebruikt om de bewerking voor het maken van de container te autoriseren. U kunt ook de toegangs sleutel voor het account gebruiken. Zie [Toegang verlenen tot blob- of wachtrijgegevens met Azure CLI](./authorize-data-operations-cli.md) voor meer informatie.

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

Als een-client een scope probeert op te geven bij het uploaden van een BLOB naar een container met een standaard versleutelings bereik en de container is geconfigureerd om te voor komen dat blobs het standaard bereik overschrijven, mislukt de bewerking met een bericht dat aangeeft dat de aanvraag is verboden door het versleutelings beleid van de container.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Een BLOB uploaden met een versleutelings bereik

Wanneer u een BLOB uploadt, kunt u een versleutelings bereik voor die BLOB opgeven of het standaard versleutelings bereik voor de container gebruiken als er een is opgegeven.

# <a name="portal"></a>[Portal](#tab/portal)

Als u via de Azure Portal een blob met een versleutelings bereik wilt uploaden, moet u eerst het versleutelings bereik maken, zoals beschreven in [een versleutelings bereik maken](#create-an-encryption-scope). Voer vervolgens de volgende stappen uit om de BLOB te maken:

1. Ga naar de container waarnaar u de BLOB wilt uploaden.
1. Selecteer de knop **uploaden** en zoek de blob die u wilt uploaden.
1. Vouw de **Geavanceerde** instellingen in het deel venster **BLOB uploaden** uit.
1. Zoek de vervolg keuzelijst **versleutelings bereik** . Standaard wordt de BLOB gemaakt met het standaard versleutelings bereik voor de container, als er een is opgegeven. Als de container vereist dat blobs het standaard versleutelings bereik gebruiken, is deze sectie uitgeschakeld.
1. Als u een ander bereik wilt opgeven voor de blob die u wilt uploaden, selecteert u **een bestaand bereik kiezen** en selecteert u vervolgens het gewenste bereik in de vervolg keuzelijst.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Scherm afbeelding die laat zien hoe een blob met een versleutelings bereik kan worden geüpload":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een BLOB wilt uploaden met een versleutelings bereik via Power shell, roept u de opdracht [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) aan en geeft u het versleutelings bereik voor de BLOB op.

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

Als u een blob met een versleutelings bereik wilt uploaden via Azure CLI, roept u de opdracht [AZ Storage BLOB upload](/cli/azure/storage/blob#az-storage-blob-upload) aan en geeft u het versleutelings bereik voor de BLOB op.

Als u Azure Cloud Shell gebruikt, voert u de stappen uit die worden beschreven in [een BLOB uploaden](storage-quickstart-blobs-cli.md#upload-a-blob) om een bestand in de hoofdmap te maken. U kunt dit bestand vervolgens uploaden naar een blob met behulp van het volgende voor beeld.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>De versleutelings sleutel voor een bereik wijzigen

Als u de sleutel voor het beveiligen van een versleutelings bereik van een door micro soft beheerde sleutel wilt wijzigen in een door de klant beheerde sleutel, moet u eerst controleren of u door de klant beheerde sleutels hebt ingeschakeld met Azure Key Vault of Key Vault HSM voor het opslag account. Zie [Configure Encryption with door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault](../common/customer-managed-keys-configure-key-vault.md) of [Configureer versleuteling met door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)voor meer informatie.

# <a name="portal"></a>[Portal](#tab/portal)

Voer de volgende stappen uit om de sleutel te wijzigen die een bereik in de Azure Portal beschermt:

1. Navigeer naar het tabblad **versleutelings bereiken** om de lijst met versleutelings bereiken voor het opslag account weer te geven.
1. Selecteer de knop **meer** naast het bereik dat u wilt wijzigen.
1. In het deel venster **versleutelings bereik bewerken** kunt u het versleutelings type wijzigen van door micro soft beheerde sleutel naar door de klant beheerde sleutel of andersom.
1. Als u een nieuwe door de klant beheerde sleutel wilt selecteren, selecteert u **een nieuwe sleutel gebruiken** en geeft u de sleutel kluis, sleutel en sleutel versie op.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de sleutel voor het beveiligen van een versleutelings bereik van een door de klant beheerde sleutel wilt wijzigen in een door micro soft beheerde sleutel met Power shell, roept u de opdracht **Update-AzStorageEncryptionScope** aan en geeft u de `-StorageEncryption` para meter:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Vervolgens roept u de opdracht **Update-AzStorageEncryptionScope** aan en geeft u `-KeyUri` de `-KeyvaultEncryption` para meters en door.

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u de sleutel voor het beveiligen van een versleutelings bereik van een door de klant beheerde sleutel wilt wijzigen in een door micro soft beheerde sleutel met Azure CLI, roept u de opdracht [AZ Storage account Encryption-Scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) aan en geeft u de `--key-source` para meter door met de waarde `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Vervolgens roept u de opdracht **AZ Storage account Encryption-Scope update** aan, geeft u de `--key-uri` para meter door en geeft u de `--key-source` para meter door met de waarde `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Een versleutelings bereik uitschakelen

Wanneer een versleutelings bereik is uitgeschakeld, wordt dit niet meer in rekening gebracht. Schakel eventuele versleutelings scopes uit die niet nodig zijn om onnodige kosten te voor komen. Zie [Azure Storage versleuteling voor Data-at-rest](../common/storage-service-encryption.md)voor meer informatie.

# <a name="portal"></a>[Portal](#tab/portal)

Als u een versleutelings bereik in de Azure Portal wilt uitschakelen, gaat u naar de instelling **versleutelings bereik** voor het opslag account, selecteert u het gewenste versleutelings bereik en selecteert u **uitschakelen**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een versleutelings bereik wilt uitschakelen met Power shell, roept u de opdracht Update-AzStorageEncryptionScope aan en neemt u de `-State` para meter op met een waarde van `disabled` , zoals in het volgende voor beeld wordt weer gegeven. Als u een versleutelings bereik opnieuw wilt inschakelen, roept u dezelfde opdracht aan met de `-State` para meter ingesteld op `enabled` . Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u een versleutelings bereik wilt uitschakelen met Azure CLI, roept u de opdracht [AZ Storage account Encryption-Scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) aan en neemt u de `--state` para meter op met een waarde van `Disabled` , zoals in het volgende voor beeld wordt weer gegeven. Als u een versleutelings bereik opnieuw wilt inschakelen, roept u dezelfde opdracht aan met de `--state` para meter ingesteld op `Enabled` . Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Het is niet mogelijk om een versleutelings bereik te verwijderen. Om onverwachte kosten te voor komen, moet u alle versleutelings scopes uitschakelen die u momenteel niet nodig hebt.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](../common/storage-service-encryption.md)
- [Versleutelings bereik voor Blob Storage](encryption-scope-overview.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](../common/customer-managed-keys-overview.md)