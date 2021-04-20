---
title: Sleutels voor opslagaccounts beheren met Azure Key Vault en de Azure CLI
description: Sleutels voor opslagaccounts bieden een naadloze integratie tussen Azure Key Vault en op sleutels gebaseerde toegang tot een Azure-opslagaccount.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 871c9f83d7bdc3f4c9e2f3b53c0b855483995152
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748606"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Sleutels voor opslagaccounts beheren met Key Vault en de Azure CLI
> [!IMPORTANT]
> We adviseren om Azure Storage-integratie te gebruiken met Azure Active Directory (Azure AD), de identiteits- en toegangsbeheerservice van Microsoft op basis van de cloud. Azure AD-integratie is beschikbaar voor [Azure-blobs en -wachtrijen](../../storage/common/storage-auth-aad.md) en biedt toegang tot Azure Storage op basis van OAuth2-tokens (net als Azure Key Vault). Met Azure AD kunt u uw clienttoepassing verifiëren met behulp van een toepassings- of gebruikers-id in plaats van de referenties van het opslagaccount. U kunt een [beheerde identiteit van Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) gebruiken wanneer u met Azure werkt. Door beheerde identiteiten te gebruiken, zijn clientverificatie en opslag van referenties in of met uw toepassing niet meer nodig. Gebruik de onderstaande oplossing alleen wanneer Azure AD-verificatie niet mogelijk is.

Een Azure-opslagaccount gebruikt referenties die bestaan uit een accountnaam en een sleutel. De sleutel wordt automatisch gegenereerd en fungeert als een wachtwoord in plaats van een cryptografische sleutel. Key Vault beheert de sleutels voor opslagaccounts door ze periodiek opnieuw te genereren in het opslagaccount en biedt SAS-tokens (Shared Access Signature) voor gedelegeerde toegang tot resources in uw opslagaccount.

U kunt de functie voor sleutels van beheerde opslagaccounts van Key Vault gebruiken om de sleutels voor een Azure-opslagaccount op te vragen (synchroniseren) en om de sleutels periodiek opnieuw te genereren (roteren). U kunt sleutels beheren voor zowel opslagaccounts als klassieke opslagaccounts.

Houd rekening met het volgende wanneer u de functie voor sleutels van beheerde opslagaccounts gebruikt:

- Sleutelwaarden worden nooit geretourneerd als antwoord op een aanroep.
- De sleutels van uw opslagaccount mogen alleen door Key Vault worden beheerd. Beheer de sleutels niet zelf en voorkom conflicten met Key Vault-processen.
- De sleutels van een opslagaccount mogen alleen door één Key Vault-object worden beheerd. Sta geen sleutelbeheer door meerdere objecten toe.
- Genereer sleutels alleen opnieuw met behulp van Key Vault. Genereer de sleutels voor uw opslagaccount niet handmatig opnieuw.

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

- [Installeer de Azure CLI](/cli/azure/install-azure-cli).
- [Maak een sleutelkluis](quick-create-cli.md).
- [Maak een Azure Storage-account](../../storage/common/storage-account-create.md?tabs=azure-cli). De naam van het opslagaccount mag alleen kleine letters en cijfers bevatten. De naam moet bestaan uit 3 tot 24 tekens.
      
## <a name="manage-storage-account-keys"></a>Sleutels voor opslagaccounts beheren

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Verifieer uw Azure CLI-sessie met behulp van de [az login](/powershell/module/az.accounts/connect-azaccount)-opdrachten.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault toegang geven tot uw opslagaccount

Gebruik de Azure CLI-opdracht [az role assignment create](/cli/azure/role/assignment) om Key Vault toegang tot uw opslagaccount te geven. Geef de opdracht de volgende parameterwaarden:

- `--role`: Geef de Azure-rol 'servicerol Sleuteloperator voor opslagaccounts' door. Deze rol beperkt het toegangsbereik voor uw opslagaccount. Geef voor een klassiek opslagaccount de rol 'servicerol Sleuteloperator voor klassieke opslagaccounts' door.
- `--assignee`: Geef de waarde 'https://vault.azure.net ' door. Dit is de URL voor Key Vault in de openbare Azure-cloud. (Gebruik voor de Azure Government-cloud '--asingee-object-id'; raadpleeg [Toepassings-id van de service-principal](#service-principal-application-id).)
- `--scope`: Geef de resource-id voor uw opslagaccount op. Deze ziet eruit als `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Als u uw abonnements-id wilt vinden, gebruikt u de Azure CLI-opdracht [az account list](/cli/azure/account?#az-account-list). Als u wilt zoeken naar de naam van het opslagaccount en de resourcegroep van het opslagaccount, gebruikt u de Azure CLI-opdracht [az storage account list](/cli/azure/storage/account?#az-storage-account-list).

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Uw gebruikersaccount machtigingen geven voor beheerde opslagaccounts

Gebruik de Azure CLI-cmdlet [az keyvault-set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) om het toegangsbeleid van Key Vault bij te werken en machtigingen voor opslagaccounts te verlenen aan uw gebruikersaccount.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Houd er rekening mee dat machtigingen voor opslagaccounts niet beschikbaar zijn op de pagina Toegangsbeleid van het opslagaccount in Azure Portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Een door Key Vault beheerd opslagaccount maken

 Maak een door Key Vault beheerd opslagaccount met behulp van de Azure CLI-opdracht [az keyvault storage](/cli/azure/keyvault/storage?#az-keyvault-storage-add). Stel een periode voor opnieuw genereren van 90 dagen in. Wanneer het tijd is om te roteren, genereert Key Vault de sleutel die niet actief is opnieuw. Vervolgens wordt de zojuist gemaakte sleutel als actief ingesteld. Er wordt altijd slechts één van de sleutels gebruikt voor het uitgeven van SAS-tokens; dit is de actieve sleutel. Geef de opdracht de volgende parameterwaarden:

- `--vault-name`: Geef de naam van de sleutelkluis door. Als u de naam van de sleutelkluis wilt vinden, gebruikt u de Azure CLI-opdracht [az keyvault list](/cli/azure/keyvault?#az-keyvault-list).
- `-n`: Geef de naam van het opslagaccount door. Als u de naam van uw opslagaccount wilt vinden, gebruikt u de Azure CLI-opdracht [az storage account list](/cli/azure/storage/account?#az-storage-account-list).
- `--resource-id`: Geef de resource-id voor uw opslagaccount op. Deze ziet eruit als `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Als u uw abonnements-id wilt vinden, gebruikt u de Azure CLI-opdracht [az account list](/cli/azure/account?#az-account-list). Als u wilt zoeken naar de naam van het opslagaccount en de resourcegroep van het opslagaccount, gebruikt u de Azure CLI-opdracht [az storage account list](/cli/azure/storage/account?#az-storage-account-list).
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>SAS-tokens

U kunt Key Vault ook instellen om zogenaamde SAS-tokens (Shared Access Signature, handtekening voor gedeelde toegang) te genereren. Een SAS (een handtekening voor gedeelde toegang) biedt gedelegeerde toegang tot resources in uw opslagaccount. Met behulp van een SAS kunt u toegang geven tot resources in uw opslagaccount zonder dat u de sleutels van uw account hoeft te delen. Een SAS biedt een veilige manier om opslagresources te delen zonder dat uw accountsleutels in gevaar komen.

Met de opdrachten in deze sectie voert u de volgende acties uit:

- Geef een SAS-definitie `<YourSASDefinitionName>` op voor het account. De definitie wordt ingesteld op een door Key Vault beheerd opslagaccount `<YourStorageAccountName>` in uw sleutelkluis `<YourKeyVaultName>`.
- Maak een SAS-accounttoken voor Blob-, File-, Table- en Queue-services. Het token wordt gemaakt voor de resourcetypen Service, Container en Object. Het token wordt gemaakt met alle machtigingen, via https, en met de opgegeven begin- en einddatum.
- Geef een definitie op voor de SAS van door Key Vault beheerde opslag in de kluis. De definitie bevat de sjabloon-URI van het SAS-token dat is gemaakt. De definitie heeft het SAS-type `account` en is N dagen geldig.
- Controleer of de SAS als een geheim is opgeslagen in de sleutelkluis.

### <a name="create-a-shared-access-signature-token"></a>Een SAS-token maken

Maak een SAS-definitie met behulp van de Azure CLI-opdracht [az storage account generate-sas](/cli/azure/storage/account?#az-storage-account-generate-sas). Voor deze bewerking zijn de machtigingen `storage` en `setsas` vereist.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Nadat de bewerking is uitgevoerd, kopieert u de uitvoer.

```console
"se=2020-01-01&sp=***"
```

Deze uitvoer wordt in de volgende stap doorgegeven aan de parameter `--template-uri`.

### <a name="generate-a-shared-access-signature-definition"></a>Een SAS-definitie genereren

Gebruik de Azure CLI-opdracht [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?#az-keyvault-storage-sas-definition-create), waarbij de uitvoer van de vorige stap wordt doorgegeven aan de parameter `--template-uri`, om een SAS-definitie te maken.  U kunt elke gewenste naam doorgeven aan de parameter `-n`.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>De SAS-definitie verifiëren

U kunt controleren of de SAS-definitie is bewaard in uw sleutelkluis met de Azure CLI-opdracht [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show).

U kunt nu de opdracht [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) en de eigenschap `id` gebruiken om de inhoud van dat geheim weer te geven.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [sleutels, geheimen en certificaten](/rest/api/keyvault/).
- Lees artikelen op het [Azure Key Vault-teamblog](/archive/blogs/kv/).
- Bekijk de naslagdocumentatie voor [az keyvault storage](/cli/azure/keyvault/storage).
