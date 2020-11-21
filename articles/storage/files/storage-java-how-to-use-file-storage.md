---
title: Ontwikkelen voor Azure Files met Java | Microsoft Docs
description: Meer informatie over het ontwikkelen van Java-toepassingen en-services die gebruikmaken van Azure Files voor het opslaan van bestands gegevens.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024106"
---
# <a name="develop-for-azure-files-with-java"></a>Ontwikkelen voor Azure Files met Java

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Meer informatie over de basis beginselen voor het ontwikkelen van Java-toepassingen die gebruikmaken van Azure Files om gegevens op te slaan. Maak een console toepassing en leer basis acties met behulp van Azure Files-Api's:

- Azure-bestands shares maken en verwijderen
- Directory's maken en verwijderen
- Bestanden en mappen in een Azure-bestands share opsommen
- Een bestand uploaden, downloaden en verwijderen

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Een Java-toepassing maken

U hebt de Java Development Kit (JDK) en de [Azure Storage SDK voor Java](https://github.com/azure/azure-sdk-for-java)nodig om de voor beelden te maken. U moet ook een Azure-opslag account hebben gemaakt.

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor gebruik Azure Files

Als u de Azure Files-Api's wilt gebruiken, voegt u de volgende code toe boven aan het Java-bestand van waaruit u toegang wilt krijgen tot Azure Files.

# <a name="java-v12"></a>[Java-V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Een Azure Storage-connection string instellen

Als u Azure Files wilt gebruiken, moet u verbinding maken met uw Azure-opslag account. Configureer een connection string en gebruik het om verbinding te maken met uw opslag account. Definieer een statische variabele voor de connection string.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Vervang *\<storage_account_name\>* en *\<storage_account_key\>* door de werkelijke waarden voor uw opslag account.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

Vervang *your_storage_account_name* en *your_storage_account_key* door de werkelijke waarden voor uw opslag account.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Toegang tot Azure Files Storage

# <a name="java-v12"></a>[Java-V12](#tab/java)

Maak een [ShareClient](/java/api/com.azure.storage.file.share.shareclient) -object om toegang te krijgen tot Azure files. Gebruik de klasse [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) om een nieuw **ShareClient** -object te maken.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

Als u toegang wilt krijgen tot uw opslag account, gebruikt u het **Cloud Storage account** -object en geeft u de connection string door aan de methode **parse** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**Cloud Storage account. parse** genereert een InvalidKeyException, zodat u deze in een try/catch-blok moet plaatsen.

---

## <a name="create-a-file-share"></a>Een bestandsshare maken

Alle bestanden en mappen in Azure Files worden opgeslagen in een container die een share wordt genoemd.

# <a name="java-v12"></a>[Java-V12](#tab/java)

De methode [ShareClient. Create](/java/api/com.azure.storage.file.share.shareclient.create) genereert een uitzonde ring als de share al bestaat. Plaats de aanroep om in een blok te **maken** `try/catch` en de uitzonde ring af te handelen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

Maak een Azure Files-client om toegang te krijgen tot een share en de inhoud ervan.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Met behulp van de Azure Files-client kunt u vervolgens een verwijzing naar een share verkrijgen.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Als u de share daad werkelijk wilt maken, gebruikt u de methode **createIfNotExists** van het object **CloudFileShare** .

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Op dit moment bevat **share** een verwijzing naar een share met de naam **voorbeeld share**.

---

## <a name="delete-a-file-share"></a>Een bestandsshare verwijderen

Met de volgende voorbeeld code wordt een bestands share verwijderd.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Verwijder een share door de methode [ShareClient. Delete](/java/api/com.azure.storage.file.share.shareclient.delete) aan te roepen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

Verwijder een share door de methode **deleteIfExists** aan te roepen voor een **CloudFileShare** -object.

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

Beheer opslag door bestanden in submappen in te voegen in plaats van deze allemaal in de hoofdmap te plaatsen.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Met de volgende code wordt een directory gemaakt door [ShareDirectoryClient. Create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create)aan te roepen. De voorbeeld methode retourneert een `Boolean` waarde die aangeeft of de Directory is gemaakt.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

Met de volgende code wordt een submap gemaakt met de naam **sampledir** onder de hoofdmap.

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

Het verwijderen van een directory is een eenvoudige taak. U kunt geen map verwijderen die nog bestanden of submappen bevat.

# <a name="java-v12"></a>[Java-V12](#tab/java)

De methode [ShareDirectoryClient. Delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) genereert een uitzonde ring als de map niet bestaat of niet leeg is. De aanroep **verwijderen** uit een `try/catch` blok en de uitzonde ring afhandelen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen in een Azure-bestands share opsommen

# <a name="java-v12"></a>[Java-V12](#tab/java)

Een lijst met bestanden en mappen ophalen door het aanroepen van [ShareDirectoryClient. listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories). De-methode retourneert een lijst met [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) -objecten waarop u kunt herhalen. De volgende code bevat een lijst met bestanden en mappen in de map die is opgegeven door de para meter *mapnaam* .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

Een lijst met bestanden en mappen ophalen door **listFilesAndDirectories** op een **CloudFileDirectory** -verwijzing aan te roepen. De-methode retourneert een lijst met **ListFileItem** -objecten waarop u kunt herhalen. De volgende code bevat een lijst met bestanden en mappen in de hoofdmap.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Bestand uploaden

Meer informatie over het uploaden van een bestand uit de lokale opslag.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Met de volgende code wordt een lokaal bestand geüpload naar Azure File Storage door de methode [ShareFileClient. uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) aan te roepen. De volgende voorbeeld methode retourneert een `Boolean` waarde die aangeeft of het opgegeven bestand is geüpload.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

Haal een verwijzing op naar de map waarin het bestand wordt geüpload door de methode **getRootDirectoryReference** aan te roepen voor het share-object.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu u een verwijzing naar de hoofdmap van de share hebt, kunt u een bestand uploaden naar de map met de volgende code.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>Een bestand downloaden

Een van de vaker voorkomende bewerkingen is het downloaden van bestanden van Azure Files opslag.

# <a name="java-v12"></a>[Java-V12](#tab/java)

In het volgende voor beeld wordt het opgegeven bestand gedownload naar de lokale map die is opgegeven in de para meter *destDir* . De voorbeeld methode zorgt ervoor dat de gedownloade bestands naam uniek is door in afwachting van de datum en tijd.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

In het volgende voor beeld wordt SampleFile.txt gedownload en wordt de inhoud ervan weer gegeven.

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

Een andere algemene Azure Files bewerking is het verwijderen van een bestand.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Met de volgende code wordt het opgegeven bestand verwijderd. In het voor beeld wordt eerst een [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) gemaakt op basis van de para meter *mapnaam* . Vervolgens krijgt de code een [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) van de Directory-client, op basis van de *filename* -para meter. Ten slotte roept de voorbeeld methode [ShareFileClient. Delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) aan om het bestand te verwijderen.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[Java-V11](#tab/java11)

Met de volgende code wordt een bestand verwijderd met de naam SampleFile.txt opgeslagen in een map met de naam **sampledir**.

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

Als u meer wilt weten over andere Azure Storage-Api's, volgt u deze koppelingen.

- [Azure voor Java-Ontwikkel aars](/azure/developer/java)
- [Azure SDK voor Java](https://github.com/azure/azure-sdk-for-java)
- [Azure SDK voor Android](https://github.com/azure/azure-sdk-for-android)
- [Naslag informatie voor de Azure file share-client bibliotheek voor Java SDK](/java/api/overview/azure/storage-file-share-readme)
- [REST-API voor Azure Storage-services](/rest/api/storageservices/)
- [Blog van het Azure Storage-team](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Gegevens overdragen met het AzCopy-hulp programma van Command-Line](../common/storage-use-azcopy-v10.md)
- [Problemen met betrekking tot Azure Files oplossen - Windows](storage-troubleshoot-windows-file-connection-problems.md)
