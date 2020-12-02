---
title: Azure Blob-opslagbibliotheek v12 - Java
description: In deze quickstart leert u hoe u de Azure Blob Storage-clientbibliotheek versie 12 voor Java kunt gebruiken om een container en een blob-in-blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 12/01/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 02752af77d443e19278a96206bb7516fedd59d7f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501121"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Quickstart: Blobs beheren met Java v12 SDK

In deze quickstart leert u hoe u blobs beheert met behulp van Java. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. U kunt blob, downloaden uploaden en weergeven en u kunt containers maken en verwijderen.

Aanvullende bronnen:

* [API-referentiedocumentatie](/java/api/overview/azure/storage-blob-readme)
* [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob)
* [Pakket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob)
* [Voorbeelden](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Java Development Kit (JDK)](/java/azure/jdk/) versie 8 of hoger.
- [Apache Maven](https://maven.apache.org/download.cgi).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Instellen

In dit gedeelte wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-clientbibliotheek v12 voor Java.

### <a name="create-the-project"></a>Het project maken

Maak een Java-toepassing met de naam *blob-quickstart-v12*.

1. Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) Maven om een nieuwe console-app te maken met de naam *blob-quickstart-v12*. Typ de volgende **mvn**-opdracht om een 'Hallo wereld!' te maken. Java-project.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.blobs.quickstart `
        --define artifactId=blob-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.blobs.quickstart \
        --define artifactId=blob-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. De uitvoer van het project zou er ongeveer als volgt moeten uitzien:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. Maak een andere map met de naam *gegevens* aan in de map *blob-quickstart-v12*. Hier worden de blob-gegevensbestanden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand *pom.xml* in uw teksteditor. Voeg het volgende afhankelijkheidselement toe aan de groep met afhankelijkheden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.6.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Ga naar de map */src/main/java/com/blobs/quickstart*
1. Open het bestand *App.java* in uw editor
1. Verwijder de `System.out.println("Hello world!");`-instructie
1. Voeg `import`-instructies toe

Hier volgt de code:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende Java-klassen om te communiceren met deze resources:

* [BlobServiceClient](/java/api/com.azure.storage.blob.blobserviceclient): Met de klasse `BlobServiceClient` kunt u Azure Storage-resources en blob-containers bewerken. Het opslagaccount biedt de naamruimte op het hoogste niveau voor de Blob service.
* De klasse [ biedt een fluent builder-API ter ondersteuning van het configureren en instantiëren van ](/java/api/com.azure.storage.blob.blobserviceclientbuilder)-objecten.
* [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient): Met de klasse `BlobContainerClient` kunt u Azure Storage-containers en de bijbehorende blobs bewerken.
* Met de klasse [ kunt u Azure Storage-blobs bewerken.
* [BlobItem](/java/api/com.azure.storage.blob.models.blobitem): De klasse `BlobItem` vertegenwoordigt afzonderlijke blobs die worden geretourneerd uit een aanroep van [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs).

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende acties kunt uitvoeren met de Azure Blob Storage-clientbibliotheek voor Java:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op van de omgevingsvariabele die is gemaakt in de sectie [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe in de methode `Main`:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Een container maken

Verzin een naam voor de nieuwe container. Met de onderstaande code wordt een UUID-waarde aan de containernaam toegevoegd om te verzekeren dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak vervolgens een exemplaar van de klasse [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient) en roep de methode [create](/java/api/com.azure.storage.blob.blobcontainerclient.create) aan om de container in uw opslagaccount daadwerkelijk te maken.

Voeg deze code toe aan het einde van de `Main`-methode:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Hiermee maakt u een tekstbestand in de lokale map *gegevens*.
1. Hiermee wordt een verwijzing naar een [BlobClient](/java/api/com.azure.storage.blob.blobclient)-object opgehaald door de methode [getBlobClient](/java/api/com.azure.storage.blob.blobcontainerclient.getblobclient) aan te roepen voor de container vanuit de sectie [Een container maken](#create-a-container).
1. Hiermee wordt het lokale tekstbestand geüpload naar de blob door de methode [uploadFromFile](/java/api/com.azure.storage.blob.blobclient.uploadfromfile) aan te roepen. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, maar niet overschreven als dat wel het geval is.

Voeg deze code toe aan het einde van de `Main`-methode:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Hiermee worden de blobs in de container weergegeven door de methode [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs) aan te roepen. In dit geval is slechts één blob aan de container toegevoegd, zodat met de weergavebewerking alleen die ene blob wordt geretourneerd.

Voeg deze code toe aan het einde van de `Main`-methode:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de methode [downloadToFile](/java/api/com.azure.storage.blob.specialized.blobclientbase.downloadtofile) aan te roepen. De voorbeeldcode voegt het achtervoegsel 'DOWNLOAD' toe aan de naam van het bestand, zodat u beide bestanden in het lokale bestandssysteem kunt zien.

Voeg deze code toe aan het einde van de `Main`-methode:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Een container verwijderen

De volgende code ruimt de resources die door de app zijn gemaakt op door de hele container te verwijderen met behulp van de methode [delete](/java/api/com.azure.storage.blob.blobcontainerclient.delete). Ook worden de lokale bestanden verwijderd die door de app zijn gemaakt.

De app pauzeert voor gebruikersinvoer door `System.console().readLine()` aan te roepen voordat deze de blob, container en lokale bestanden verwijdert. Dit is een goede gelegenheid om te controleren dat de resources correct zijn gemaakt, voordat ze worden verwijderd.

Voeg deze code toe aan het einde van de `Main`-methode:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app wordt een testbestand gemaakt in uw lokale map en geüpload naar de Blob-opslag. Vervolgens wordt een lijst gemaakt van de blobs in de container en wordt het bestand gedownload met een nieuwe naam, zodat u het oude en nieuwe bestand kunt vergelijken.

Navigeer naar de map die het bestand *pom.xml* bevat en compileer het project met behulp van de volgende `mvn`-opdracht.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de volgende `mvn`-opdracht uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Voordat u begint met opschonen, controleert u of de twee bestanden zich in de map *gegevens* bevinden. Als u ze opent, ziet u dat ze identiek zijn.

Nadat u de bestanden hebt gecontroleerd, drukt u op **Enter** om de testbestanden te verwijderen en het voorbeeld te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u blobs kunt uploaden, downloaden en er een lijst van maken met behulp van Java.

Als u voorbeeld-apps voor Blob-opslag wilt zien, ga dan naar:

> [!div class="nextstepaction"]
> [Azure Blob-opslag-SDK v12 Java-voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Zie de [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md) voor meer informatie.
* Ga naar [Azure voor Java-cloudontwikkelaars](/azure/developer/java/) voor zelfstudies, voorbeelden, quickstarts en andere documentatie.