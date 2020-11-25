---
title: Java script gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2
description: Gebruik Azure Storage Data Lake-client bibliotheek voor Java script om directory's en ACL'S (toegangs beheer lijsten) in opslag accounts te beheren waarvoor een hiërarchische naam ruimte (HNS) is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: a929fcbc87a1ce11b226e9def46354c24a151a0c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913365"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Java script gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u Java script gebruikt voor het maken en beheren van mappen, bestanden en machtigingen in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

[Pakket (node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](../common/storage-account-create.md) instructies om er een te maken.
> * Als u dit pakket in een Node.js-toepassing gebruikt, hebt u Node.js 8.0.0 of hoger nodig.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer Data Lake-client bibliotheek voor Java script door een Terminal venster te openen en de volgende opdracht te typen.

```javascript
npm install @azure/storage-file-datalake
```

Importeer het `storage-file-datalake` pakket door deze instructie boven aan het code bestand te plaatsen. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account 

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een account sleutel.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Deze autorisatie methode werkt alleen voor Node.js toepassingen. Als u van plan bent om uw code uit te voeren in een browser, kunt u autoriseren met behulp van Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

U kunt de [Azure Identity client-bibliotheek voor JS](https://www.npmjs.com/package/@azure/identity) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md)om deze waarden op te halen.

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Zie de documentatie [van de Azure Identity client-bibliotheek voor JS](https://www.npmjs.com/package/@azure/identity) voor meer voor beelden.

## <a name="create-a-container"></a>Een container maken

Een container fungeert als bestands systeem voor uw bestanden. U kunt er een maken door een **FileSystemClient** -exemplaar op te halen en vervolgens de methode **FileSystemClient. Create** aan te roepen.

In dit voor beeld wordt een container gemaakt met de naam `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een directory verwijzing door een **DirectoryClient** -exemplaar op te halen en vervolgens de methode **DirectoryClient. Create** aan te roepen.

In dit voor beeld wordt een map met de naam toegevoegd `my-directory` aan een container. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de methode **DirectoryClient. rename** aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in de naam `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

In dit voor beeld wordt een map verplaatst met de naam `my-directory-renamed` naar een submap van een map met de naam `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de methode **DirectoryClient. Delete** aan te roepen.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```



## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Lees eerst een bestand. In dit voor beeld wordt de module Node.js gebruikt `fs` . Maak vervolgens een bestands verwijzing in de doel directory door een **FileClient** -exemplaar te maken en vervolgens de methode **FileClient. Create** aan te roepen. Upload een bestand door de methode **FileClient. Append aan** te roepen. Zorg ervoor dat u de upload voltooit door de methode **FileClient. Flush** aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory` .

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Maak eerst een **FileSystemClient** -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de methode **FileSystemClient. Read** om het bestand te lezen. Schrijf vervolgens het bestand. In dit voor beeld wordt de module Node.js gebruikt `fs` . 

> [!NOTE]
> Deze methode voor het downloaden van een bestand werkt alleen voor Node.js-toepassingen. Als u van plan bent om uw code uit te voeren in een browser, raadpleegt u het bestand [Azure Storage bestand data Lake client bibliotheek voor Java script](https://www.npmjs.com/package/@azure/storage-file-datalake) voor een voor beeld van hoe u dit in een browser kunt doen. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

In dit voor beeld worden de namen afgedrukt van elke map en elk bestand dat zich in een map met de naam bevindt `my-directory` .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
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

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

U kunt ook de toegangs beheer lijst van de hoofdmap van een container ophalen en instellen. Als u de hoofdmap wilt ophalen, geeft u een lege teken reeks () door aan `/` de methode **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="manage-a-file-acl"></a>Een bestands-ACL beheren

In dit voor beeld wordt de ACL van een bestand met de naam opgehaald en ingesteld `upload-file.txt` . In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer in azure data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>Zie ook

* [Pakket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)