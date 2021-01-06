---
title: Acl's recursief instellen voor Azure Data Lake Storage Gen2 | Microsoft Docs
description: U kunt toegangs beheer lijsten recursief toevoegen, bijwerken en verwijderen voor bestaande onderliggende items van een bovenliggende map.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: fc407978f18198c9d9525a49a9c8b66de8663065
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934489"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Acl's (toegangs beheer lijsten) recursief instellen voor Azure Data Lake Storage Gen2

ACL-overname is al beschikbaar voor nieuwe onderliggende items die zijn gemaakt onder een bovenliggende map. U kunt nu ook recursief toevoegen, bijwerken en verwijderen van de Acl's voor bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen.

[Bibliotheken](#libraries)  |  Voor [beelden](#code-samples)  |  [Aanbevolen procedures](#best-practice-guidelines)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- De juiste machtigingen om het recursieve ACL-proces uit te voeren. De juiste machtiging bevat een van de volgende opties: 

  - Een [beveiligings-principal](../../role-based-access-control/overview.md#security-principal) ingericht Azure Active Directory (AD) waaraan de rol [Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van de doel container, de bovenliggende resource groep of het abonnement.   

  - De gebruiker die eigenaar is van de doel container of-map waarop u het recursieve ACL-proces wilt Toep assen. Dit omvat alle onderliggende items in de doel container of-map. 

- Uitleg over hoe Acl's worden toegepast op directory's en bestanden. Zie [toegangs beheer in azure data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Zie de sectie **uw project instellen** in dit artikel voor informatie over de installatie richtlijnen voor Power shell, .NET SDK en python SDK.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer de benodigde bibliotheken.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Controleer of de versie van Power shell die is geïnstalleerd, `5.1` of hoger is met behulp van de volgende opdracht.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Zie [bestaande Windows Power shell upgraden](/powershell/scripting/install/installing-windows-powershell) voor informatie over het bijwerken van uw versie van Power shell
    
2. Installeer **AZ. Storage** -module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van Power shell-modules.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Open de [Azure Cloud shell](../../cloud-shell/overview.md)of open een opdracht console toepassing zoals Windows Power shell als u de Azure cli lokaal hebt [geïnstalleerd](/cli/azure/install-azure-cli) .

2. Controleer of de versie van de Azure CLI die is geïnstalleerd `2.14.0` of hoger is met behulp van de volgende opdracht.

   ```azurecli
    az --version
   ```
   Als uw versie van Azure CLI lager is dan `2.14.0` , installeert u een nieuwere versie. Raadpleeg [De Azure CLI installeren](/cli/azure/install-azure-cli).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Open een opdracht venster (bijvoorbeeld: Windows Power shell).

2. Installeer in de projectmap het bestand Azure. storage. file. DataLake Preview met behulp van de `dotnet add package` opdracht.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Voeg deze instructies toe aan de bovenkant van het code bestand.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

Open [Deze pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) en zoek de meest recente versie van de Java-bibliotheek om aan de slag te gaan. Open vervolgens het *pom.xml* -bestand in de tekst editor. Voeg een afhankelijkheids element toe dat verwijst naar die versie.

Als u van plan bent om uw client toepassing te verifiëren met behulp van Azure Active Directory (AD), voegt u een afhankelijkheid toe aan de Azure Secret-client bibliotheek. Zie  [het geheim-client bibliotheek pakket toevoegen aan uw project](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Voeg vervolgens deze Imports-instructies toe aan het code bestand.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Down load de [Azure data Lake Storage-client bibliotheek voor python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D).

2. Installeer de bibliotheek die u hebt gedownload met behulp van [PIP](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Voeg deze import instructies toe aan de bovenkant van het code bestand.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>Verbinding maken met uw account

U kunt verbinding maken met behulp van Azure Active Directory (AD) of met behulp van een account sleutel. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Open een Windows Power shell-opdracht venster en meld u vervolgens aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account waarin u directory's wilt maken en beheren. In dit voor beeld vervangt `<subscription-id>` u de waarde van de tijdelijke aanduiding door de id van uw abonnement.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Kies vervolgens hoe u wilt dat uw opdrachten autorisatie aanvragen voor het opslag account. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Optie 1: autorisatie verkrijgen met behulp van Azure Active Directory (AD)

Met deze methode zorgt het systeem ervoor dat uw gebruikers account beschikt over de juiste toewijzings-en ACL-machtigingen van Azure op rollen gebaseerde toegangs beheer (Azure RBAC). 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

In de volgende tabel ziet u alle ondersteunde rollen en de instellingen voor de ACL-instelling.

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Optie 2: autorisatie verkrijgen met behulp van de sleutel van het opslag account

Met deze methode controleert het systeem geen Azure RBAC-of ACL-machtigingen.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Als u Azure CLI lokaal gebruikt, voert u de aanmeldings opdracht uit.

   ```azurecli
   az login
   ```

   Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

   Als dat niet het geval is, opent u een browser pagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatie code in die wordt weer gegeven in uw Terminal. Meld u vervolgens aan met uw account referenties in de browser.

   Zie voor meer informatie over verschillende verificatie methoden [toegang verlenen tot BLOB-of wachtrij gegevens met Azure cli](./authorize-data-operations-cli.md).

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

> [!NOTE]
> In het voor beeld in dit artikel wordt de autorisatie Azure Active Directory (AD) weer gegeven. Zie [toegang verlenen aan BLOB-of wachtrij gegevens met Azure cli](./authorize-data-operations-cli.md)voor meer informatie over verificatie methoden.

### <a name="net"></a>[.NET](#tab/dotnet)

Als u de fragmenten in dit artikel wilt gebruiken, moet u een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar maken dat het opslag account vertegenwoordigt.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

U kunt de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) gebruiken om uw toepassing te verifiëren met Azure AD.

Nadat u het pakket hebt geïnstalleerd, voegt u deze met de instructie toe aan de bovenkant van het code bestand.

```csharp
using Azure.Identity;
```

Haal een client-ID, een client geheim en een Tenant-ID op. Zie voor dit doen [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md). Als onderdeel van dit proces moet u een van de volgende [Azure-rollen op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) toewijzen aan uw beveiligings-principal. 

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

In dit voor beeld wordt een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

#### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Deze methode is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar gemaakt met behulp van een account sleutel.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

> [!NOTE]
> Zie de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) -documentatie voor meer voor beelden.

### <a name="java"></a>[Java](#tab/java)

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. 

#### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een account sleutel.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Verbinding maken met behulp van Azure Active Directory (Azure AD)

U kunt de [Azure Identity client-bibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md)om deze waarden op te halen.

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Zie de [Azure Identity client-bibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) -documentatie voor meer voor beelden.

### <a name="python"></a>[Python](#tab/python)

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

U kunt de [Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md)om deze waarden op te halen. Als onderdeel van dit proces moet u een van de volgende [Azure-rollen op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) toewijzen aan uw beveiligings-principal. 

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Zie de documentatie [van de Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) voor meer voor beelden.

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een account sleutel.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Vervang de waarde van de tijdelijke plaatsaanduiding `storage_account_name` door de naam van uw opslagaccount.

- Vervang de `storage_account_key` waarde van de tijdelijke aanduiding door de toegangs sleutel van uw opslag account.

---

## <a name="set-an-acl-recursively"></a>Recursief instellen van een ACL

Wanneer u een ACL *instelt* , **vervangt** u de volledige ACL, inclusief alle items. Als u het machtigings niveau van een beveiligingsprincipal wilt wijzigen of een nieuwe beveiligingsprincipal wilt toevoegen aan de ACL zonder dat dit van invloed is op andere bestaande vermeldingen, moet u de toegangs beheer lijst in plaats daarvan *bijwerken* . Als u een ACL wilt bijwerken in plaats van deze te vervangen, raadpleegt u de sectie [een recursief bijwerken van een ACL](#update-an-acl-recursively) in dit artikel.  

Als u ervoor kiest om de ACL in te *stellen* , moet u een vermelding toevoegen voor de gebruiker die eigenaar is, een vermelding voor de groep die eigenaar is en een vermelding voor alle andere gebruikers. Zie [gebruikers en identiteiten](data-lake-storage-access-control.md#users-and-identities)voor meer informatie over de gebruiker die eigenaar is, de groep die eigenaar is en alle andere gebruikers. 

Deze sectie bevat voor beelden voor het instellen van een ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Stel een recursief-ACL in met behulp van de cmdlet **set-AzDataLakeGen2AclRecursive** .

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt instellen, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Zie het artikel [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference voor een voor beeld van het recursief instellen van acl's in batches door een batch grootte op te geven.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Stel op recursief een ACL in met behulp van de opdracht [AZ Storage FS Access set-recursief](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt instellen, voegt u het voor voegsel toe `default:` aan elk item. Bijvoorbeeld `default:user::rwx` of `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Stel een recursief-ACL in door de methode **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** aan te roepen. Geef deze methode een [lijst](/dotnet/api/system.collections.generic.list-1) van [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Elk [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt instellen, kunt u de eigenschap [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) instellen op **True**. 

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of de standaard-ACL moet worden ingesteld. Deze para meter wordt gebruikt in de constructor van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). De vermeldingen van de ACL geven de machtigingen gebruiker lezen, schrijven en uitvoeren, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en verleent alle anderen geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Als u een voor beeld wilt weer geven waarin Acl's in batches worden ingesteld door een batch grootte op te geven, raadpleegt u het .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Stel een recursief-ACL in door de methode **DataLakeDirectoryClient. setAccessControlRecursive** aan te roepen. Geef deze methode een [lijst](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) met [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objecten. Elk [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt instellen, kunt u de methode **SetDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) aanroepen en de waarde **True** door geven. 

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of de standaard-ACL moet worden ingesteld. Deze para meter wordt gebruikt in elke aanroep van de methode **setDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). De vermeldingen van de ACL geven de machtigingen gebruiker lezen, schrijven en uitvoeren, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en verleent alle anderen geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```java
static public void SetACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){
    
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create owner entry.
    PathAccessControlEntry ownerEntry = new PathAccessControlEntry();

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    ownerEntry.setDefaultScope(isDefaultScope);
    ownerEntry.setAccessControlType(AccessControlType.USER);
    ownerEntry.setPermissions(ownerPermission);

    pathAccessControlEntries.add(ownerEntry);

    // Create group entry.
    PathAccessControlEntry groupEntry = new PathAccessControlEntry();

    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    groupEntry.setDefaultScope(isDefaultScope);
    groupEntry.setAccessControlType(AccessControlType.GROUP);
    groupEntry.setPermissions(groupPermission);

    pathAccessControlEntries.add(groupEntry);

    // Create other entry.
    PathAccessControlEntry otherEntry = new PathAccessControlEntry();

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setExecutePermission(false).setReadPermission(false).setWritePermission(false);

    otherEntry.setDefaultScope(isDefaultScope);
    otherEntry.setAccessControlType(AccessControlType.OTHER);
    otherEntry.setPermissions(otherPermission);

    pathAccessControlEntries.add(otherEntry);

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.setAccessControlRecursive(pathAccessControlEntries);        

}
```

### <a name="python"></a>[Python](#tab/python)

Stel een recursief-ACL in door de **DataLakeDirectoryClient.set_access_control_recursive** methode aan te roepen.

Als u een **standaard** -ACL-vermelding wilt instellen, voegt u de teken reeks toe `default:` aan het begin van elke ACL-invoer teken reeks. 

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . 

Deze methode accepteert een Booleaanse para meter `is_default_scope` met de naam die aangeeft of de standaard-ACL moet worden ingesteld. Als deze para meter is `True` , wordt de lijst met ACL-vermeldingen voorafgegaan door de teken reeks `default:` . 

De vermeldingen van de ACL geven de machtigingen gebruiker lezen, schrijven en uitvoeren, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en verleent alle anderen geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren. Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

---

## <a name="update-an-acl-recursively"></a>Recursief bijwerken van een ACL

Wanneer u een ACL *bijwerkt* , wijzigt u de ACL in plaats van de ACL te vervangen. U kunt bijvoorbeeld een nieuwe beveiligingsprincipal toevoegen aan de ACL zonder dat dit van invloed is op andere beveiligings-principals die worden vermeld in de ACL.  Als u de ACL wilt vervangen in plaats van deze bij te werken, raadpleegt u de sectie [een recursief van ACL instellen](#set-an-acl-recursively) in dit artikel. 

Als u een ACL wilt bijwerken, maakt u een nieuw ACL-object met de ACL-vermelding die u wilt bijwerken en gebruikt u dat object in de ACL-bewerking voor updates. Haal de bestaande ACL niet op. Zorg dat u alleen ACL-vermeldingen kunt bijwerken.

Deze sectie bevat voor beelden voor het bijwerken van een ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Een ACL recursief bijwerken met behulp van de cmdlet  **Update-AzDataLakeGen2AclRecursive** . 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt bijwerken, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Zie het artikel Naslag informatie over [Update AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) voor een voor beeld van het recursief bijwerken van acl's in batches door een batch grootte op te geven.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Een ACL recursief bijwerken met behulp van de opdracht  [AZ Storage FS Access update-recursieve](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) . 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt bijwerken, voegt u het voor voegsel toe `default:` aan elk item. Bijvoorbeeld `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

Werk een ACL recursief bij door de methode **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** aan te roepen.  Geef deze methode een [lijst](/dotnet/api/system.collections.generic.list-1) van [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Elk [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt bijwerken, kunt u de eigenschap [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) instellen op **True**. 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt. Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of de standaard-ACL moet worden bijgewerkt. Deze para meter wordt gebruikt in de constructor van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Zie .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)voor een voor beeld van het recursief bijwerken van acl's in batches door een batch grootte op te geven.

### <a name="java"></a>[Java](#tab/java)

Werk een ACL recursief bij door de methode **DataLakeDirectoryClient. updateAccessControlRecursive** aan te roepen.  Geef deze methode een [lijst](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) met [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objecten. Elk [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt bijwerken, kunt u de methode **SetDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) en geeft u de waarde **True** door. 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt. Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of de standaard-ACL moet worden bijgewerkt. Die para meter wordt gebruikt bij het aanroepen van de methode **setDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

```java
static public void UpdateACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.updateAccessControlRecursive(pathAccessControlEntries);          
}
```

### <a name="python"></a>[Python](#tab/python)

Een ACL recursief bijwerken door het aanroepen van de **DataLakeDirectoryClient.update_access_control_recursive** methode. Als u een **standaard** -ACL-vermelding wilt bijwerken, voegt u de teken reeks toe `default:` aan het begin van elke ACL-invoer teken reeks. 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt.

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `is_default_scope` met de naam die aangeeft of de standaard-ACL moet worden bijgewerkt. Als deze para meter is `True` , wordt de bijgewerkte ACL-vermelding voorafgegaan door de teken reeks `default:` .  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

---

## <a name="remove-acl-entries-recursively"></a>ACL-vermeldingen recursief verwijderen

U kunt een of meer ACL-vermeldingen recursief verwijderen. Als u een ACL-vermelding wilt verwijderen, maakt u een nieuw ACL-object voor de ACL-vermelding die moet worden verwijderd, en gebruikt u dat object in de bewerking ACL'S verwijderen. Haal de bestaande ACL niet op. Geef de ACL-vermeldingen op die moeten worden verwijderd. 

Deze sectie bevat voor beelden voor het verwijderen van een ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwijder ACL-vermeldingen met behulp van de cmdlet **Remove-AzDataLakeGen2AclRecursive** . 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de hoofd directory van de container.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt verwijderen, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Zie het artikel Naslag informatie over [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) om een voor beeld te zien van het recursief verwijderen van acl's in batches door een batch grootte op te geven.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Verwijder ACL-vermeldingen met behulp van de opdracht [AZ Storage FS Access Remove-recursieve](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) . 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de hoofd directory van de container.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt verwijderen, voegt u het voor voegsel toe `default:` aan elk item. Bijvoorbeeld `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

Verwijder ACL-vermeldingen door de methode **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** aan te roepen. Geef deze methode een [lijst](/dotnet/api/system.collections.generic.list-1) van [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Elk [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt verwijderen, kunt u de eigenschap [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) instellen op **True**. 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de toegangs beheer lijst van de map met de naam `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of het item uit de standaard-ACL moet worden verwijderd. Deze para meter wordt gebruikt in de constructor van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Zie .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)voor een voor beeld van het recursief verwijderen van acl's in batches door een batch grootte op te geven.

### <a name="java"></a>[Java](#tab/java)

Verwijder ACL-vermeldingen door de methode **DataLakeDirectoryClient. removeAccessControlRecursive** aan te roepen. Geef deze methode een [lijst](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) met [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objecten. Elk [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt verwijderen, kunt u de methode **SetDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) en geeft u de waarde **True** door.  

In dit voor beeld wordt een ACL-vermelding verwijderd uit de toegangs beheer lijst van de map met de naam `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of het item uit de standaard-ACL moet worden verwijderd. Die para meter wordt gebruikt bij het aanroepen van de methode **setDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).


```java
static public void RemoveACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathRemoveAccessControlEntry> pathRemoveAccessControlEntries = 
        new ArrayList<PathRemoveAccessControlEntry>();

    // Create named user entry.
    PathRemoveAccessControlEntry userEntry = new PathRemoveAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"); 
    
    pathRemoveAccessControlEntries.add(userEntry);
    
    directoryClient.removeAccessControlRecursive(pathRemoveAccessControlEntries);      

}
```

### <a name="python"></a>[Python](#tab/python)

Verwijder ACL-vermeldingen door de **DataLakeDirectoryClient.remove_access_control_recursive** methode aan te roepen. Als u een **standaard** -ACL-vermelding wilt verwijderen, moet u de teken reeks toevoegen `default:` aan het begin van de teken reeks voor de ACL-vermelding. 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de toegangs beheer lijst van de map met de naam `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `is_default_scope` met de naam die aangeeft of het item uit de standaard-ACL moet worden verwijderd. Als deze para meter is `True` , wordt de bijgewerkte ACL-vermelding voorafgegaan door de teken reeks `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

---

## <a name="recover-from-failures"></a>Herstellen van fouten

Er kunnen runtime-of machtigings fouten optreden. Start voor runtime-fouten het proces vanaf het begin. Machtigings fouten kunnen optreden als de beveiligingsprincipal niet voldoende machtigingen heeft om de toegangs beheer lijst van een map of bestand te wijzigen dat zich in de Directory-hiërarchie bevindt die wordt gewijzigd. Adresseer het probleem met de machtiging en kies vervolgens het proces hervatten vanaf het moment van de fout met behulp van een vervolg token of start het proces opnieuw vanaf het begin. U hoeft het vervolg token niet te gebruiken als u de voor keur hebt om vanaf het begin opnieuw op te starten. U kunt ACL-vermeldingen zonder negatieve gevolgen opnieuw Toep assen.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In dit voor beeld worden resultaten geretourneerd naar de variabele en vervolgens worden mislukte items naar een ingedeelde tabel gepiped.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Op basis van de uitvoer van de tabel kunt u eventuele machtigings fouten oplossen en de uitvoering hervatten met behulp van het vervolg token.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Zie het artikel [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference voor een voor beeld van het recursief instellen van acl's in batches door een batch grootte op te geven.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als er een fout optreedt, kunt u een vervolg token retour neren door de `--continue-on-failure` para meter in te stellen op `false` . Nadat u de fouten hebt verhelpen, kunt u het proces hervatten vanaf het moment van de fout door de opdracht opnieuw uit te voeren en vervolgens de `--continuation` para meter in te stellen op het vervolg token. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

In dit voor beeld wordt een vervolg token geretourneerd in het geval van een fout. De toepassing kan deze voorbeeld methode opnieuw aanroepen nadat de fout is opgelost en door gegeven in het vervolg token. Als deze voorbeeld methode voor de eerste keer wordt aangeroepen, kan de toepassing de waarde `null` voor de vervolg token parameter door geven. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Als u een voor beeld wilt weer geven waarin Acl's in batches worden ingesteld door een batch grootte op te geven, raadpleegt u het .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

In dit voor beeld wordt een vervolg token geretourneerd in het geval van een fout. De toepassing kan deze voorbeeld methode opnieuw aanroepen nadat de fout is opgelost en door gegeven in het vervolg token. Als deze voorbeeld methode voor de eerste keer wordt aangeroepen, kan de toepassing de waarde `null` voor de vervolg token parameter door geven. 

```java
static public String ResumeSetACLRecursively(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList, 
String continuationToken){

    try{
        PathSetAccessControlRecursiveOptions options = new PathSetAccessControlRecursiveOptions(accessControlList);
        
        options.setContinuationToken(continuationToken);
    
        Response<AccessControlChangeResult> accessControlChangeResult =  
            directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

        if (accessControlChangeResult.getValue().getCounters().getFailedChangesCount() > 0)
        {
            continuationToken =
                accessControlChangeResult.getValue().getContinuationToken();
        }
    
        return continuationToken;

    }
    catch(Exception ex){
    
        System.out.println(ex.toString());
        return continuationToken;
    }
}
```

### <a name="python"></a>[Python](#tab/python)

In dit voor beeld wordt een vervolg token geretourneerd in het geval van een fout. De toepassing kan deze voorbeeld methode opnieuw aanroepen nadat de fout is opgelost en door gegeven in het vervolg token. Als deze voorbeeld methode voor de eerste keer wordt aangeroepen, kan de toepassing de waarde ``None`` voor de vervolg token parameter door geven. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

---

Als u wilt dat het proces wordt afgebroken door machtigings fouten, kunt u dit opgeven.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In dit voor beeld wordt de `ContinueOnFailure` para meter gebruikt, zodat de uitvoering wordt voortgezet, zelfs als er een machtigings fout optreedt in de bewerking. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Zie het artikel [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference voor een voor beeld van het recursief instellen van acl's in batches door een batch grootte op te geven.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Om ervoor te zorgen dat het proces wordt voltooid, stelt u de `--continue-on-failure` para meter in op `true` . 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

Om ervoor te zorgen dat het proces wordt voltooid, geeft u een **AccessControlChangedOptions** -object door en stelt u de eigenschap **ContinueOnFailure** van dat object in op ``true`` .

In dit voor beeld worden ACL-vermeldingen recursief ingesteld. Als met deze code een machtigings fout optreedt, wordt die fout geregistreerd en wordt de uitvoering voortgezet. In dit voor beeld wordt het aantal fouten naar de console afgedrukt. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Als u een voor beeld wilt weer geven waarin Acl's in batches worden ingesteld door een batch grootte op te geven, raadpleegt u het .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Om ervoor te zorgen dat het proces wordt voltooid, roept u de methode **setContinueOnFailure** van een [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -object aan en geeft u de waarde **True** door.

In dit voor beeld worden ACL-vermeldingen recursief ingesteld. Als met deze code een machtigings fout optreedt, wordt die fout geregistreerd en wordt de uitvoering voortgezet. In dit voor beeld wordt het aantal fouten naar de console afgedrukt. 

```java
static public void ContinueOnFailure(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList){
    
    PathSetAccessControlRecursiveOptions options = 
        new PathSetAccessControlRecursiveOptions(accessControlList);
        
    options.setContinueOnFailure(true);
    
    Response<AccessControlChangeResult> accessControlChangeResult =  
        directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

    AccessControlChangeCounters counters = accessControlChangeResult.getValue().getCounters();

    System.out.println("Number of directories changes: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of files changed: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of failures: " + 
        counters.getChangedDirectoriesCount());
}
```

### <a name="python"></a>[Python](#tab/python)

Om ervoor te zorgen dat het proces wordt voltooid, geeft u geen vervolg token door aan de **DataLakeDirectoryClient.set_access_control_recursive** methode.

In dit voor beeld worden ACL-vermeldingen recursief ingesteld. Als met deze code een machtigings fout optreedt, wordt die fout geregistreerd en wordt de uitvoering voortgezet. In dit voor beeld wordt het aantal fouten naar de console afgedrukt. 

```python
def continue_on_failure():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl)

        print("Summary: {} directories and {} files were updated successfully, {} failures were counted."
          .format(acl_change_result.counters.directories_successful, acl_change_result.counters.files_successful,
                  acl_change_result.counters.failure_count))
        
    except Exception as e:
     print(e)
```

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

---

## <a name="resources"></a>Resources

Deze sectie bevat koppelingen naar bibliotheken en code voorbeelden.

#### <a name="libraries"></a>Bibliotheken

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Azure-CLI](/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)
- [REST](/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>Codevoorbeelden

- Power shell: [Leesmij](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)-voor  |  [beeld](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- Azure CLI: voor [beeld](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: [Leesmij](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%2fSc2aDVW3De4A%2fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)-voor  |  [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Leesmij](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)-voor  |  [beeld](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>Richt lijnen voor best practices

In deze sectie vindt u enkele best practice richt lijnen voor het recursief instellen van Acl's. 

#### <a name="handling-runtime-errors"></a>Runtime-fouten afhandelen

Een runtime-fout kan verschillende oorzaken hebben (bijvoorbeeld: een storing of een probleem met de client verbinding). Als er een runtime-fout optreedt, start u het recursieve ACL-proces opnieuw. Acl's kunnen opnieuw worden toegepast op items zonder dat dit een negatieve invloed heeft veroorzaakt. 

#### <a name="handling-permission-errors-403"></a>Afhandeling van machtigings fouten (403)

Als er een uitzonde ring voor Access Control optreedt tijdens het uitvoeren van een recursieve ACL-proces, heeft uw AD- [beveiligings-principal](../../role-based-access-control/overview.md#security-principal) mogelijk onvoldoende machtigingen om een ACL toe te passen op een of meer onderliggende items in de Directory-hiërarchie. Als er een machtigings fout optreedt, wordt het proces gestopt en wordt er een vervolg token gegeven. Los het probleem met de machtiging op en gebruik vervolgens de vervolg token om de resterende gegevensset te verwerken. De mappen en bestanden die al zijn verwerkt, hoeven niet opnieuw te worden verwerkt. U kunt er ook voor kiezen om het recursieve ACL-proces opnieuw te starten. Acl's kunnen opnieuw worden toegepast op items zonder dat dit een negatieve invloed heeft veroorzaakt. 

#### <a name="credentials"></a>Referenties 

We raden u aan een Azure AD-beveiligings-principal in te richten waaraan de rol van [BLOB-gegevens eigenaar voor opslag](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van het doel opslag account of de container. 

#### <a name="performance"></a>Prestaties 

Als u de latentie wilt beperken, raden we u aan het recursieve ACL-proces uit te voeren op een virtuele Azure-machine (VM) die zich in dezelfde regio bevindt als uw opslag account. 

#### <a name="acl-limits"></a>ACL-limieten

Het maximum aantal Acl's dat u kunt Toep assen op een map of bestand is 32 toegangs-Acl's en standaard-32-Acl's. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md) voor meer informatie.

## <a name="see-also"></a>Zie ook

- [Toegangsbeheer in Data Lake Storage Gen2](./data-lake-storage-access-control.md)
- [Bekende problemen](data-lake-storage-known-issues.md)