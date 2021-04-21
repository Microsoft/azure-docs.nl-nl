---
title: Ontwikkelen voor Azure Files met Java | Microsoft Docs
description: Meer informatie over het ontwikkelen van Java-toepassingen en -services die gebruikmaken Azure Files voor het opslaan van bestandsgegevens.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 115c55a5833906aa0dcc616a5b1b659468647282
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814546"
---
# <a name="develop-for-azure-files-with-java"></a>Ontwikkelen voor Azure Files met Java

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Leer de basisbeginselen voor het ontwikkelen van Java-toepassingen Azure Files gebruiken voor het opslaan van gegevens. Maak een consoletoepassing en leer basisacties met behulp van Azure Files API's:

- Azure-bestands shares maken en verwijderen
- Directories maken en verwijderen
- Bestanden en mappen in een Azure-bestands share opsnoemen
- Een bestand uploaden, downloaden en verwijderen

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Een Java-toepassing maken

Als u de voorbeelden wilt maken, hebt u de Java Development Kit (JDK) en de [Azure Storage SDK voor Java nodig.](https://github.com/azure/azure-sdk-for-java) U moet ook een Azure-opslagaccount hebben gemaakt.

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor het gebruik van Azure Files

Als u de Azure Files-API's wilt gebruiken, voegt u de volgende code toe aan de bovenkant van het Java-bestand van waar u toegang wilt krijgen tot Azure Files.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Een Azure Storage-connection string

Als u Azure Files, moet u verbinding maken met uw Azure-opslagaccount. Configureer een connection string en gebruik deze om verbinding te maken met uw opslagaccount. Definieer een statische variabele voor het connection string.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Vervang *\<storage_account_name\>* en door de werkelijke waarden voor uw *\<storage_account_key\>* opslagaccount.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Vervang *your_storage_account_name* en *your_storage_account_key* door de werkelijke waarden voor uw opslagaccount.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-an-azure-file-share"></a>Toegang tot een Azure-bestands share

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Als u toegang Azure Files, maakt u een [ShareClient-object.](/java/api/com.azure.storage.file.share.shareclient) Gebruik de [ShareClientBuilder-klasse](/java/api/com.azure.storage.file.share.shareclientbuilder) om een nieuw **ShareClient-object te** bouwen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Als u toegang wilt krijgen tot uw opslagaccount, gebruikt u het object **CloudStorageAccount** en geeft u de connection string door aan de **parse-methode.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** geeft een InvalidKeyException weer, dus u moet deze in een try/catch-blok zetten.

---

## <a name="create-a-file-share"></a>Een bestandsshare maken

Alle bestanden en mappen in Azure Files worden opgeslagen in een container die een share wordt genoemd.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

De [methode ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create) geeft een uitzondering weer als de share al bestaat. Plaats de aanroep om **een blok** te maken `try/catch` en vereen de uitzondering af te handelen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Als u toegang wilt krijgen tot een share en de inhoud ervan, maakt u een Azure Files client.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Met behulp Azure Files client kunt u vervolgens een verwijzing naar een share verkrijgen.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Als u de share daadwerkelijk wilt maken, gebruikt **u de methode createIfNotExists** van het **object CloudFileShare.**

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Op dit moment bevat **de share** een verwijzing naar een share met de naam **sample share**.

---

## <a name="delete-a-file-share"></a>Een bestandsshare verwijderen

Met de volgende voorbeeldcode wordt een bestands share verwijderd.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Verwijder een share door de methode [ShareClient.delete aan te](/java/api/com.azure.storage.file.share.shareclient.delete) roepen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Verwijder een share door de **methode deleteIfExists aan te** roepen voor een **CloudFileShare-object.**

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>Een map maken

Organiseer de opslag door bestanden in subdirectory's te plaatsen in plaats van ze allemaal in de hoofdmap te plaatsen.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Met de volgende code wordt een map gemaakt door [ShareDirectoryClient.create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create)aan te roepen. De voorbeeldmethode retourneert `Boolean` een waarde die aangeeft of de map is gemaakt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Met de volgende code maakt u een submap met de **naam sampledir** onder de hoofdmap.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>Een map verwijderen

Het verwijderen van een map is een eenvoudige taak. U kunt een map die nog steeds bestanden of subdirectory's bevat, niet verwijderen.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

De [methode ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) geeft een uitzondering als de map niet bestaat of niet leeg is. Plaats de aanroep om **te verwijderen** in een blok en ver handelen de `try/catch` uitzondering af.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen in een Azure-bestands share opsnoemen

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Haal een lijst met bestanden en mappen op door [ShareDirectoryClient.listFilesAndDirectories aan te roepen.](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories) De methode retourneert een lijst [met ShareFileItem-objecten](/java/api/com.azure.storage.file.share.models.sharefileitem) waarop u kunt itereren. De volgende code geeft een lijst van bestanden en mappen in de map die is opgegeven door de *dirName* parameter.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Haal een lijst met bestanden en mappen op door **listFilesAndDirectories** aan te roepen in een **CloudFileDirectory-verwijzing.** De methode retourneert een lijst **met ListFileItem-objecten** waarop u kunt itereren. De volgende code bevat bestanden en mappen in de hoofdmap.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Een bestand uploaden

Meer informatie over het uploaden van een bestand vanuit lokale opslag.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Met de volgende code wordt een lokaal bestand geüpload naar Azure File Storage door de [methode ShareFileClient.uploadFromFile aan te](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) roepen. De volgende voorbeeldmethode retourneert een `Boolean` waarde die aangeeft of het opgegeven bestand is geüpload.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Haal een verwijzing op naar de map waar het bestand wordt geüpload door de methode **getRootDirectoryReference** op het shareobject aan te roepen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu u een verwijzing naar de hoofdmap van de share hebt, kunt u er een bestand naar uploaden met behulp van de volgende code.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>Een bestand downloaden

Een van de meest voorkomende bewerkingen is het downloaden van bestanden van een Azure-bestands share.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

In het volgende voorbeeld wordt het opgegeven bestand gedownload naar de lokale map die is opgegeven in *de parameter destDir.* Met de voorbeeldmethode wordt de gedownloade bestandsnaam uniek door de datum en tijd vooraf te laten gaan.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

In het volgende voorbeeld worden SampleFile.txt gedownload en wordt de inhoud ervan weergegeven.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>Een bestand verwijderen

Een andere veelvoorkomende Azure Files bewerking is het verwijderen van bestanden.

# <a name="azure-java-sdk-v12"></a>[Azure Java SDK v12](#tab/java)

Met de volgende code wordt het opgegeven bestand verwijderd. Eerst wordt in het voorbeeld een [ShareDirectoryClient gemaakt](/java/api/com.azure.storage.file.share.sharedirectoryclient) op basis van de *parameter dirName.* Vervolgens haalt de code een [ShareFileClient op](/java/api/com.azure.storage.file.share.sharefileclient) uit de mapclient, op basis van de parameter *fileName.* Ten slotte roept de voorbeeldmethode [ShareFileClient.delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) aan om het bestand te verwijderen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="azure-java-sdk-v11"></a>[Azure Java SDK v11](#tab/java11)

Met de volgende code wordt een bestand met de SampleFile.txt opgeslagen in een map met de **naam sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over andere Azure Storage-API's, volgt u deze koppelingen.

- [Azure voor Java-ontwikkelaars](/azure/developer/java)
- [Azure SDK voor Java](https://github.com/azure/azure-sdk-for-java)
- [Azure SDK voor Android](https://github.com/azure/azure-sdk-for-android)
- [Naslag voor Azure File Share-clientbibliotheek voor Java SDK](/java/api/overview/azure/storage-file-share-readme)
- [REST-API voor Azure Storage-services](/rest/api/storageservices/)
- [Blog van het Azure Storage-team](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Gegevens overdragen met het hulpprogramma AzCopy Command-Line](../common/storage-use-azcopy-v10.md)
- [Problemen met betrekking tot Azure Files oplossen - Windows](storage-troubleshoot-windows-file-connection-problems.md)
