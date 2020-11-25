---
title: Azure Data Lake Storage Gen2 Java SDK voor bestanden & Acl's
description: Gebruik Azure Storage bibliotheken voor Java voor het beheren van mappen en toegangs beheer lijsten (ACL'S) voor bestands-en Directory bestanden in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: f6e8219f744a91628f9860f0af133c07eddb4253
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913382"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Java gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u Java gebruikt om directory's, bestanden en machtigingen te maken en te beheren in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

[Pakket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API-verwijzing](/java/api/overview/azure/storage-file-datalake-readme)  |  Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](../common/storage-account-create.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Open [Deze pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) en zoek de meest recente versie van de Java-bibliotheek om aan de slag te gaan. Open vervolgens het *pom.xml* -bestand in de tekst editor. Voeg een afhankelijkheids element toe dat verwijst naar die versie.

Als u van plan bent om uw client toepassing te verifiëren met behulp van Azure Active Directory (AD), voegt u een afhankelijkheid toe aan de Azure Secret-client bibliotheek. Zie  [het geheim-client bibliotheek pakket toevoegen aan uw project](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Voeg vervolgens deze Imports-instructies toe aan het code bestand.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account 

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Verbinding maken met behulp van Azure Active Directory (Azure AD)

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


## <a name="create-a-container"></a>Een container maken

Een container fungeert als bestands systeem voor uw bestanden. U kunt er een maken door de methode **DataLakeServiceClient. createFileSystem** aan te roepen.

In dit voor beeld wordt een container gemaakt met de naam `my-file-system` . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een verwijzing naar een directory door de methode **DataLakeFileSystemClient. createDirectory** aan te roepen.

In dit voor beeld wordt een map met de naam van een `my-directory` container toegevoegd en wordt vervolgens een submap met de naam toegevoegd `my-subdirectory` . 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de methode **DataLakeDirectoryClient. rename** aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in de naam `my-subdirectory-renamed` .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

In dit voor beeld wordt een map verplaatst met de naam `my-subdirectory-renamed` naar een submap van een map met de naam `my-directory-2` . 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de methode **DataLakeDirectoryClient. deleteWithResponse** aan te roepen.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Maak eerst een bestands verwijzing in de doel directory door een instantie van de klasse **DataLakeFileClient** te maken. Upload een bestand door de methode **DataLakeFileClient. Append aan** te roepen. Zorg ervoor dat u de upload voltooit door de methode **DataLakeFileClient. FlushAsync** aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory` .

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new BufferedInputStream(new FileInputStream(file));

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Als de bestands grootte groot is, moet uw code meerdere aanroepen naar de methode **DataLakeFileClient. append** maken. U kunt in plaats daarvan de methode **DataLakeFileClient. uploadFromFile** gebruiken. Op die manier kunt u het volledige bestand in één aanroep uploaden. 
>
> Zie de volgende sectie voor een voor beeld.

## <a name="upload-a-large-file-to-a-directory"></a>Een groot bestand uploaden naar een map

Gebruik de methode **DataLakeFileClient. uploadFromFile** om grote bestanden te uploaden zonder meerdere aanroepen naar de methode **DataLakeFileClient. append** te hoeven maken.

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Maak eerst een **DataLakeFileClient** -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de methode **DataLakeFileClient. Read** om het bestand te lezen. Gebruik een API voor het verwerken van .NET-bestanden om bytes van de stroom naar een bestand op te slaan. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

In dit voor beeld worden de namen van elk bestand dat zich in een map met de naam bevindt `my-directory` .

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="manage-access-control-lists-acls"></a>Toegangs beheer lijsten (Acl's) beheren

U kunt toegangs machtigingen van mappen en bestanden ophalen, instellen en bijwerken.

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om toegang te verlenen, moet u ervoor zorgen dat aan uw beveiligingsprincipal de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)is toegewezen. Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer in azure data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Een directory-ACL beheren

In dit voor beeld wordt de ACL van een directory met de naam opgehaald en ingesteld `my-directory` . In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer in azure data Lake Storage Gen2](./data-lake-storage-access-control.md).

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

U kunt ook de toegangs beheer lijst van de hoofdmap van een container ophalen en instellen. Als u de hoofdmap wilt ophalen, geeft u een lege teken reeks () door aan `""` de methode **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="manage-a-file-acl"></a>Een bestands-ACL beheren

In dit voor beeld wordt de ACL van een bestand met de naam opgehaald en ingesteld `upload-file.txt` . In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer in azure data Lake Storage Gen2](./data-lake-storage-access-control.md).

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

### <a name="set-an-acl-recursively"></a>Recursief instellen van een ACL

U kunt Acl's recursief toevoegen, bijwerken en verwijderen voor de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen. Zie [acl's (Access Control Lists) recursief instellen voor Azure data Lake Storage Gen2](recursive-access-control-lists.md)voor meer informatie.

## <a name="see-also"></a>Zie ook

* [API-referentiedocumentatie](/java/api/overview/azure/storage-file-datalake-readme)
* [Pakket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)