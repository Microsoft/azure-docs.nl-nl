---
title: Java script (Node.js) gebruiken om Acl's in Azure Data Lake Storage Gen2 in te stellen
description: Gebruik Azure Storage Data Lake-client bibliotheek voor Java script voor het beheren van toegangs beheer lijsten (ACL) in opslag accounts waarvoor HNS (hiërarchische naam ruimte) is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 21b4977102a484d8a3a680450a9cb6f77c7e3fbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722749"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Java script SDK in Node.js gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u Node.js kunt gebruiken om de toegangs beheer lijsten van mappen en bestanden op te halen, in te stellen en bij te werken. 

[Pakket (node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- Azure CLI-versie `2.6.0` of hoger.

- Een van de volgende beveiligings machtigingen:

  - Een [beveiligings-principal](../../role-based-access-control/overview.md#security-principal) ingericht Azure Active Directory (AD) waaraan de rol [Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van de doel container, de bovenliggende resource groep of het abonnement.  

  - De gebruiker die eigenaar is van de doel container of-map waarvoor u de ACL-instellingen wilt Toep assen. Als u Acl's recursief wilt instellen, geldt dit voor alle onderliggende items in de doel container of directory.
  
  - Sleutel van het opslag account..

## <a name="set-up-your-project"></a>Uw project instellen

Installeer Data Lake-client bibliotheek voor Java script door een Terminal venster te openen en de volgende opdracht te typen.

```javascript
npm install @azure/storage-file-datalake
```

Importeer het `storage-file-datalake` pakket door deze instructie boven aan het code bestand te plaatsen. 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om toegang te verlenen, moet u ervoor zorgen dat aan uw beveiligingsprincipal de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)is toegewezen. Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer model in azure data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

U kunt de [Azure Identity client-bibliotheek voor JS](https://www.npmjs.com/package/@azure/identity) gebruiken om uw toepassing te verifiëren met Azure AD.

Haal een client-ID, een client geheim en een Tenant-ID op. Zie voor dit doen [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md). Als onderdeel van dit proces moet u een van de volgende [Azure-rollen op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) toewijzen aan uw beveiligings-principal. 

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  

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

## <a name="get-and-set-a-directory-acl"></a>Een directory-ACL ophalen en instellen

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

### <a name="get-and-set-a-file-acl"></a>Een bestands-ACL ophalen en instellen

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

- [Pakket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)
- [Access Control model in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Toegangs beheer lijsten (Acl's) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)