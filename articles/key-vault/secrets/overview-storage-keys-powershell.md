---
title: Beheerde opslagaccount van Azure Key Vault - PowerShell-versie
description: De functie voor beheerde opslagaccounts biedt een naadloze integratie tussen Azure Key Vault en een Azure-opslagaccount.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa4daf7c8d951a7b42479533a3a5a50c06c5c144
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748480"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Sleutels voor opslagaccounts beheren met Key Vault en Azure PowerShell
> [!IMPORTANT]
> We adviseren om Azure Storage-integratie te gebruiken met Azure Active Directory (Azure AD), de identiteits- en toegangsbeheerservice van Microsoft op basis van de cloud. Azure AD-integratie is beschikbaar voor [Azure-blobs en -wachtrijen](../../storage/common/storage-auth-aad.md) en biedt toegang tot Azure Storage op basis van OAuth2-tokens (net als Azure Key Vault).
> Met Azure AD kunt u uw clienttoepassing verifiëren met behulp van een toepassings- of gebruikers-id in plaats van de referenties van het opslagaccount. U kunt een [beheerde identiteit van Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) gebruiken wanneer u met Azure werkt. Door beheerde identiteiten te gebruiken, zijn clientverificatie en opslag van referenties in of met uw toepassing niet meer nodig. Gebruik de onderstaande oplossing alleen wanneer Azure AD-verificatie niet mogelijk is.

Een Azure-opslagaccount gebruikt referenties die bestaan uit een accountnaam en een sleutel. De sleutel wordt automatisch gegenereerd en fungeert als wachtwoord in plaats van als een cryptografische sleutel. Key Vault beheert de sleutels voor opslagaccounts door ze periodiek opnieuw te genereren in het opslagaccount en biedt SAS-tokens (Shared Access Signature) voor gedelegeerde toegang tot resources in uw opslagaccount.

U kunt de functie voor sleutels van beheerde opslagaccounts van Key Vault gebruiken om de sleutels voor een Azure-opslagaccount op te vragen (synchroniseren) en om de sleutels periodiek opnieuw te genereren (roteren). U kunt sleutels beheren voor zowel opslagaccounts als klassieke opslagaccounts.

Houd rekening met het volgende wanneer u de functie voor sleutels van beheerde opslagaccounts gebruikt:

- Sleutelwaarden worden nooit geretourneerd als antwoord op een aanroep.
- De sleutels van uw opslagaccount mogen alleen door Key Vault worden beheerd. Beheer de sleutels niet zelf en voorkom conflicten met Key Vault-processen.
- De sleutels van een opslagaccount mogen alleen door één Key Vault-object worden beheerd. Sta geen sleutelbeheer door meerdere objecten toe.
- Genereer sleutels alleen opnieuw met behulp van Key Vault. Genereer de sleutels voor uw opslagaccount niet handmatig opnieuw.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Toepassings-id in de vorm van een service-principal

Een Azure AD-tenant voorziet elke geregistreerde toepassing van een [service-principal](../../active-directory/develop/developer-glossary.md#service-principal-object). De service-principal fungeert als de toepassings-id, die wordt gebruikt tijdens het instellen van de autorisatie voor toegang tot andere Azure-resources via Azure RBAC.

Key Vault is een Microsoft-toepassing die vooraf wordt geregistreerd in alle Azure AD-tenants. Key Vault wordt in elke Azure-cloud geregistreerd onder dezelfde toepassings-id.

| Tenants | Cloud | Toepassings-id |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Openbare Azure-peering | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Anders  | Alle | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Vereisten

U moet eerst het volgende doen om deze handleiding te voltooien:

- [Installeer de Azure PowerShell-module](/powershell/azure/install-az-ps).
- [Maak een sleutelkluis](quick-create-powershell.md).
- [Maak een Azure Storage-account](../../storage/common/storage-account-create.md?tabs=azure-powershell). De naam van het opslagaccount mag alleen kleine letters en cijfers bevatten. De naam moet bestaan uit 3 tot 24 tekens.


## <a name="manage-storage-account-keys"></a>Sleutels voor opslagaccounts beheren

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Verifieer uw PowerShell-sessie met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

```azurepowershell-interactive
Connect-AzAccount
```
Als u meerdere Azure-abonnementen hebt, kunt u deze weergeven met behulp van de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) en vervolgens het gewenste abonnement opgeven met de cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Variabelen instellen

Stel eerst de variabelen in die door de PowerShell-cmdlets moeten worden gebruikt in de volgende stappen. Zorg ervoor dat u de tijdelijke aanduidingen "YourResourceGroupName", "YourStorageAccountName" en "YourKeyVaultName" bijwerkt en $keyVaultSpAppId instelt op `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (zoals hierboven is aangegeven in [Toepassings-id in de vorm van een service-principal](#service-principal-application-id)).

We gebruiken ook de Azure PowerShell-cmdlets [Get-AzContext](/powershell/module/az.accounts/get-azcontext) en [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) om uw gebruikers-id en de context van uw Azure opslagaccount op te halen.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> Gebruik voor een klassiek opslagaccount 'primary' en 'secondary' voor $storageAccountKey. <br>
> Gebruik 'Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName' in plaats van 'Get-AzStorageAccount' voor een klassiek opslagaccount.

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault toegang geven tot uw opslagaccount

Voordat Key Vault toegang kan krijgen tot de sleutels voor uw opslagaccount en deze kan beheren, moet u de toegang tot uw opslagaccount autoriseren. De Key Vault-toepassing vereist machtigingen voor het *opvragen* en *opnieuw genereren* van sleutels voor uw opslagaccount. Deze machtigingen worden ingeschakeld via de ingebouwde Azure-rol [De servicerol Sleuteloperator voor opslagaccounts](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role).

Wijs deze rol toe aan de service-principal van Key Vault Service om het bereik te beperken tot uw opslagaccount. Gebruik hiervoor de Azure PowerShell-cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Als de rol is toegewezen, ziet u uitvoer die lijkt op het volgende voorbeeld:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Als Key Vault al is toegevoegd aan de rol van uw opslagaccount, ziet u tekst dat *de roltoewijzing al bestaat.* fout. U kunt de roltoewijzing ook controleren met behulp van de pagina Toegangsbeheer (IAM) van het opslagaccount in Azure Portal.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Uw gebruikersaccount machtigingen geven voor beheerde opslagaccounts

Gebruik de Azure PowerShell-cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om het toegangsbeleid van Key Vault bij te werken en machtigingen voor het opslagaccount te verlenen aan uw gebruikersaccount.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Houd er rekening mee dat machtigingen voor opslagaccounts niet beschikbaar zijn op de pagina Toegangsbeleid van het opslagaccount in Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Een beheerd opslagaccount toevoegen aan uw Key Vault-instantie

Gebruik de Azure PowerShell-cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) om een beheerd opslagaccount te maken in uw instantie van Key Vault. De schakeloptie `-DisableAutoRegenerateKey` geeft aan dat de sleutels voor het opslagaccount NIET opnieuw moeten worden gegenereerd.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Wanneer het opslagaccount is toegevoegd zonder opnieuw een sleutel te genereren, ziet u uitvoer die lijkt op het volgende voorbeeld:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

### <a name="enable-key-regeneration"></a>Opnieuw genereren van sleutel inschakelen

Als u wilt dat Key Vault de sleutels voor uw opslagaccount periodiek opnieuw genereert, kunt u de Azure PowerShell-cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) gebruiken om hiervoor een periode in te stellen. In dit voorbeeld willen we dat er elke drie dagen een nieuwe sleutel wordt gegenereerd. Wanneer het tijd is om te roteren, genereert Key Vault de sleutel die niet actief is opnieuw. Vervolgens wordt de zojuist gemaakte sleutel als actief ingesteld. Er wordt altijd slechts een van de sleutels gebruikt voor het uitgeven van SAS-tokens. Dat is de actieve sleutel.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Wanneer het opslagaccount is toegevoegd en er een nieuwe sleutel is gegenereerd, ziet u uitvoer die lijkt op het volgende voorbeeld:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

## <a name="shared-access-signature-tokens"></a>SAS-tokens

U kunt Key Vault ook instellen om zogenaamde SAS-tokens (Shared Access Signature, handtekening voor gedeelde toegang) te genereren. Een SAS (een handtekening voor gedeelde toegang) biedt gedelegeerde toegang tot resources in uw opslagaccount. Met behulp van een SAS kunt u toegang geven tot resources in uw opslagaccount zonder dat u de sleutels van uw account hoeft te delen. Een SAS biedt een veilige manier om opslagresources te delen zonder dat uw accountsleutels in gevaar komen.

Met de opdrachten in deze sectie voert u de volgende acties uit:

- Definitie opgeven voor SAS van account.
- Een SAS-token maken voor de Blob-, File-, Table- en Queue-services. Het token wordt gemaakt voor de resourcetypen Service, Container en Object. Het token wordt gemaakt met alle machtigingen, via https, en met de opgegeven begin- en einddatum.
- Geef een definitie op voor de SAS van door Key Vault beheerde opslag in de kluis. De definitie bevat de sjabloon-URI van het SAS-token dat is gemaakt. De definitie heeft het SAS-type `account` en is N dagen geldig.
- Controleer of de SAS als een geheim is opgeslagen in de sleutelkluis.
-
### <a name="set-variables"></a>Variabelen instellen

Stel eerst de variabelen in die door de PowerShell-cmdlets moeten worden gebruikt in de volgende stappen. Zorg ervoor dat u de tijdelijke aanduidingen <YourStorageAccountName> en <YourKeyVaultName> bijwerkt.

We gebruiken ook de Azure PowerShell-cmdlet [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) om de context van uw Azure opslagaccount op te halen.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Een SAS-token maken

Maak een definitie van een SAS met behulp van de Azure PowerShell-cmdlet [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken).

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
De waarde van $sasToken ziet er ongeveer als volgt uit.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Een SAS-definitie genereren

Gebruik de Azure PowerShell-cmdlet [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) om een definitie van een SAS te maken.  U kunt elke gewenste naam doorgeven aan de parameter `-Name`.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>De SAS-definitie verifiëren

Gebruik de Azure PowerShell-cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) om te controleren of de definitie van de SAS is opgeslagen in uw sleutelkluis.

Zoek eerst de definitie van de SAS in uw sleutelkluis.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Het geheim dat overeenkomt met uw SAS-definitie heeft de volgende eigenschappen:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

U kunt nu de cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) gebruiken met de parameters `VaultName` en `Name` om de inhoud van dat geheim te bekijken.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

De uitvoer van deze opdracht bevat de tekenreeks van de SAS-definitie.

## <a name="next-steps"></a>Volgende stappen

- [Voorbeelden van sleutels voor beheerde opslagaccounts](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [PowerShell-naslaghandleiding voor Key Vault](/powershell/module/az.keyvault/#key_vault)
