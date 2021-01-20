---
title: 'Quickstart: Azure Blob-opslagclientbibliotheek v8 voor Java'
description: Maak een opslagaccount en een container in object(Blob)-opslag. Vervolgens gebruikt u de Azure Storage-clientbibliotheek voor Java v8 om een blob in Azure Storage te uploaden, een blob te downloaden en de blobs in een container te vermelden.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b3bb8736eb7a8e24f47812fc4feecbf96628dfb6
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599809"
---
# <a name="quickstart-manage-blobs-with-java-v8-sdk"></a>Quickstart: Blobs beheren met Java v8 SDK

In deze quickstart leert u hoe u blobs beheert met behulp van Java. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. U uploadt, downloadt en vermeldt blobs. U maakt en verwijdert ook containers en stelt er machtigingen voor in.

> [!NOTE]
> In deze snelstart wordt gebruikgemaakt van een oudere versie van de Azure Blob Storage-clientbibliotheek. Als u aan de slag wilt gaan met de vorige versie, raadpleegt u [Quickstart: Blobs beheren met Java v12 SDK](storage-quickstart-blobs-java.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- Een IDE met Maven-integratie. In deze handleiding wordt [Eclipse](https://www.eclipse.org/downloads/) gebruikt met de configuratie Eclipse IDE for Java Developers.

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De [voorbeeldtoepassing](https://github.com/Azure-Samples/storage-blobs-java-quickstart) is een eenvoudige consoletoepassing.

Gebruik [git](https://git-scm.com/) om een kopie van de toepassing naar uw ontwikkelomgeving te downloaden.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Met deze opdracht wordt de opslagplaats naar uw lokale git-map gekloond. Om het project te openen, start u Eclipse en sluit u het welkomstscherm. Selecteer **Bestand** en vervolgens **Projecten openen vanuit bestandssysteem**. Zorg ervoor dat **Detect and configure project natures** is ingeschakeld. Selecteer **Directory** en navigeer naar de locatie van de gekloonde opslagplaats. Selecteer in de gekloonde opslagplaats de map **blobAzureApp**. Zorg ervoor dat het project **blobAzureApp** wordt weergegeven als een Eclipse-project en selecteer vervolgens **Voltooien**.

Zodra het project is geïmporteerd, opent u **AzureApp.java** (in **blobQuickstart.blobAzureApp** in **src/main/java**) en vervangt u de `accountname`en `accountkey` in de tekenreeks `storageConnectionString`. Voer de toepassing vervolgens uit. Specifieke instructies voor het voltooien van deze taken worden beschreven in de volgende secties.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

In de toepassing moet u de verbindingsreeks voor uw opslagaccount opgeven. Open het bestand **AzureApp.Java**. Zoek de `storageConnectionString`-variabele en plak de waarde van de verbindingsreeks die u in de vorige sectie hebt gekopieerd. De variabele `storageConnectionString` moet nu lijken op het volgende codevoorbeeld:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Met deze voorbeeldtoepassing wordt er een testbestand gemaakt in de standaardmap (*C:\Users\<user>\AppData\Local\Temp* voor Windows-gebruikers), wordt dit bestand geüpload naar Blob Storage, worden de blobs in de container uitgelijst en wordt het bestand vervolgens met een nieuwe naam gedownload, zodat u het oude en het nieuwe bestand met elkaar kunt vergelijken.

Voer het voorbeeld uit met Maven op de opdrachtregel. Open een shell en navigeer naar **blobAzureApp** binnen de gekloonde map. Voer vervolgens `mvn compile exec:java` uit.

Hieronder ziet u een voorbeeld van uitvoer als u de toepassing zou uitvoeren in Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Voordat u doorgaat, controleert u of in de standaardmap (*C:\Users\<user>\AppData\Local\Temp* voor Windows-gebruikers) het voorbeeldbestand staat. Kopieer de URL voor de blob in het consolevenster en plak deze in een browser om de inhoud van het bestand in de Blob-opslag weer te geven. Als u het voorbeeldbestand in de directory vergelijkt met de inhoud die is opgeslagen in Blob Storage, ziet u dat ze hetzelfde zijn.

  >[!NOTE]
  >U kunt ook een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken om de bestanden in de Blob-opslag te bekijken. Azure Storage Explorer is een gratis hulpprogramma voor meerdere platforms waarmee u toegang hebt tot de gegevens van uw opslagaccount.

Nadat u de bestanden hebt gecontroleerd, drukt u op **Enter** om het voorbeeld te voltooien en de testbestanden te verwijderen. Nu u weet wat het voorbeeld doet, opent u het bestand **AzureApp.java** om de code te bekijken.

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

We gaan nu de voorbeeldcode bespreken, zodat u begrijpt hoe deze werkt.

### <a name="get-references-to-the-storage-objects"></a>Verwijzingen naar de opslagobjecten ophalen

Als eerste moeten verwijzingen worden gemaakt naar objecten die worden gebruikt voor het verkrijgen van toegang tot de Blob-opslag en voor het beheren ervan. Deze objecten zijn afhankelijk van elkaar en elk object vormt de basis voor het volgende object in de lijst.

* Maak een exemplaar van het [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount)-object, dat naar het opslagaccount wijst.

    Het object e **CloudStorageAccount** is een voorstelling van uw opslagaccount en biedt u de mogelijkheid om via programmacode eigenschappen van het opslagaccount in te stellen en op te vragen. Met behulp van het object **CloudStorageAccount** kunt u een instantie van **CloudBlobClient** maken, die nodig is voor toegang tot de blob-service.

* Maak een instantie van het object **CloudBlobClient**, dat naar de [Blob-service](/java/api/com.microsoft.azure.storage.blob.cloudblobclient) in uw opslagaccount wijst.

    **CloudBlobClient** biedt een toegangspunt voor de blob-service, zodat u via programmacode eigenschappen van Blob Storage kunt instellen en opvragen. Met behulp van **CloudBlobClient** kunt u een instantie van het object **CloudBlobContainer** maken, die nodig is voor het maken van containers.

* Maak een instantie van het object [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer), die de container voorstelt die u raadpleegt. Gebruik containers om uw blobs te organiseren op een wijze die lijkt op het gebruik van mappen op uw computer om uw bestanden te organiseren.

    Zodra u de **CloudBlobContainer** hebt, kunt u een instantie maken van het object [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob) dat verwijst naar de specifieke blob waarin u bent geïnteresseerd. U kunt bewerkingen uitvoeren zoals uploaden, downloaden en kopiëren.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Naamgeving van en verwijzen naar containers, blobs en metagegevens](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over containers.

### <a name="create-a-container"></a>Een container maken

In deze sectie gaat u een exemplaar maken van de objecten, een nieuwe container maken en vervolgens machtigingen instellen voor de container, zodat de blobs openbaar zijn en via slechts een URL kunnen worden geopend. De container heeft de naam **quickstartcontainer**.

In dit voorbeeld wordt [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists) gebruikt omdat we bij elke nieuwe uitvoering van het voorbeeld een nieuwe container willen maken. In een productieomgeving waarin u overal in een toepassing dezelfde container gebruikt, is het beter om **CreateIfNotExists** maar één keer aan te roepen. U kunt de container ook vooraf maken, zodat u dit niet in de code hoeft te doen.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Blobs uploaden naar de container

Als u een bestand naar een blok-blob wilt uploaden, haalt u een verwijzing naar de blob op in de doelcontainer. Zodra u de blobverwijzing hebt, kunt u er gegevens naar uploaden met behulp van [CloudBlockBlob.Upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload). Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat, of overschreven als deze wel al bestaat.

Met de voorbeeldcode wordt een lokaal bestand gemaakt voor het uploaden en downloaden, waarmee het bestand dat moet worden geüpload als **bron** en de naam van de blob in **blob** worden opgeslagen. In het volgende voorbeeld wordt het bestand geüpload naar de container met de naam **quickstartcontainer**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Er zijn verschillende methoden voor `upload`, waaronder [upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadstandardblobtier) en [uploadText](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadtext) die u kunt gebruiken met Blob Storage. Als u bijvoorbeeld een tekenreeks hebt, kunt u de methode `UploadText` gebruiken in plaats van de methode `Upload`.

Blok-bobs kunnen bestaan uit elk type tekstbestand of binair bestand. Pagina-blobs worden hoofdzakelijk gebruikt voor de VHD-bestanden die worden gebruikt als back-up voor IaaS-VM's. Gebruik toevoeg-blobs voor logboekregistratie, bijvoorbeeld wanneer u gegevens wilt wegschrijven naar een bestand en vervolgens gegevens wilt blijven toevoegen. De meeste objecten die worden opgeslagen in Blob-opslag, zijn blok-blobs.

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

U kunt een lijst met bestanden in de container opvragen met behulp van [CloudBlobContainer.ListBlobs](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobs). Met de volgende code wordt de lijst met blobs opgehaald en doorlopen, waarbij de URI‘s van de gevonden blobs worden weergegeven. U kunt de URL uit het opdrachtvenster kopiëren en in een browser plakken om het bestand weer te geven.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Blobs downloaden

Download blobs naar uw lokale schijf met behulp van [CloudBlob.DownloadToFile](/java/api/com.microsoft.azure.storage.blob.cloudblob.downloadtofile).

Met de volgende code wordt de blob gedownload die in een eerdere sectie is geüpload, waarbij het achtervoegsel '_DOWNLOADED' wordt toegevoegd aan de blobnaam, zodat u beide bestanden op de lokale schijf kunt zien.

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and
// haven't changed the blob we're interested in, so we can reuse it.
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Resources opschonen

Als u de blobs die u hebt geüpload niet meer nodig hebt, kunt u de volledige container verwijderen met behulp van [CloudBlobContainer.DeleteIfExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists). Met deze methode worden ook de bestanden in de container verwijderd.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();

if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u bestanden overbrengt tussen een lokale schijf en Azure Blob-opslag met behulp van Java. Voor meer informatie over het werken met Java gaat u verder naar onze opslagplaats van GitHub-broncode.

> [!div class="nextstepaction"]
> [Java API-referentie](/java/api/overview/azure/storage?view=azure-java-legacy&preserve-view=true)
> [Codevoorbeelden voor Java](../common/storage-samples-java.md)