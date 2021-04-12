---
title: 'Azure-snelstart: een blob maken in objectopslag met PHP | Microsoft Docs'
description: Leer snel objecten over te dragen naar/van Azure Blob-opslag met PHP. Upload, download en vermeld blok-blobs in een container in Azure Blob-opslag.
author: twooley
ms.author: twooley
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 400982b1cacd6ccd409559679376edcfc886ed5c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280051"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Objecten overdragen naar/van Azure Blob-opslag met PHP
In deze snelstart leert u hoe u PHP kunt gebruiken om blok-bobs te uploaden, te downloaden en weer te geven in een container in Azure Blob-opslag. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Zorg dat u de volgende aanvullende vereisten hebt geïnstalleerd:

* [PHP](https://php.net/downloads.php)
* [Azure Storage SDK voor PHP](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden
De [voorbeeldtoepassing](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) die in deze snelstart wordt gebruikt, is een PHP-basistoepassing.  

Gebruik [git](https://git-scm.com/) om een kopie van de toepassing naar uw ontwikkelomgeving te downloaden. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Met deze opdracht wordt de opslagplaats naar uw lokale git-map gekloond. Als u de PHP- voorbeeldtoepassing wilt openen, zoekt u naar de map storage-blobs-php-quickstart en opent u het bestand phpqs.php.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren
In de toepassing moet u de naam en de sleutel van uw opslagaccount opgeven om een **BlobRestProxy**-exemplaar voor uw toepassing te maken. U wordt aangeraden deze id's binnen een omgevingsvariabele op te slaan op de lokale computer waarop de toepassing wordt uitgevoerd. Gebruik een van de volgende voorbeelden, afhankelijk van uw besturingssysteem, voor het maken van de omgevingsvariabele. Vervang de waarden **youraccountname** en **youraccountkey** door uw accountnaam en -sleutel.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Uw omgeving configureren
Haal de map uit uw lokale git-map en plaats deze in een map die door uw PHP-server wordt gebruikt. Open vervolgens een opdrachtprompt die is afgestemd op diezelfde map en voer het volgende in: `php composer.phar install`

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren
In dit voorbeeld wordt een testbestand in de map '.' gemaakt. Door het voorbeeldprogramma uit te voeren wordt een testbestand geüpload naar de Blob-opslag, worden de blobs in the container vermeld en wordt het bestand met een nieuwe naam gedownload. 

Voet het voorbeeld uit. De volgende uitvoer is een voorbeeld van de uitvoer die wordt geretourneerd wanneer de toepassing wordt uitgevoerd:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Wanneer u op de weergegeven toets drukt, worden de opslagcontainer en de bestanden door het voorbeeldprogramma verwijderd. Voordat u doorgaat, controleert u of de map van de server de twee bestanden bevat. Als u ze opent, ziet u dat ze identiek zijn.

U kunt ook een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com) gebruiken om de bestanden in de Blob-opslag te bekijken. Azure Storage Explorer is een gratis hulpprogramma voor meerdere platforms waarmee u toegang hebt tot de gegevens van uw opslagaccount. 

Nadat u de bestanden hebt gecontroleerd, drukt u op een willekeurige toets om de demo te voltooien en de testbestanden te verwijderen. Nu u weet wat het voorbeeldprogramma doet, opent u het bestand example.rb om de code te bekijken. 

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

We gaan nu de voorbeeldcode bespreken, zodat u begrijpt hoe deze werkt.

### <a name="get-references-to-the-storage-objects"></a>Verwijzingen naar de opslagobjecten ophalen
Als eerste moeten verwijzingen worden gemaakt naar objecten die worden gebruikt voor het verkrijgen van toegang tot de Blob-opslag en voor het beheren ervan. Deze objecten worden boven op elkaar gebouwd en elk ervan wordt door de volgende in de lijst gebruikt.

* Maak een exemplaar van het **BlobRestProxy**-object voor Azure-opslag om verbindingsreferenties in te stellen. 
* Maak het object **BlobService** dat naar de Blob-service in uw opslagaccount verwijst. 
* Maak het object **Container** dat de container vertegenwoordigt die u wilt openen. Containers worden gebruikt om uw blobs te organiseren op een wijze die lijkt op het gebruik van mappen op uw computer om uw bestanden te organiseren.

Zodra het **blobClient**-containerobject is gemaakt, kunt u het blob-object **Block** maken dat verwijst naar de specifieke blob waarin u geïnteresseerd bent. Vervolgens kunt u bewerkingen zoals uploaden, downloaden en kopiëren uitvoeren.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Naming and referencing containers, blobs, and metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Containers, blobs en metagegevens een naam geven en hiernaar verwijzen) voor meer informatie over namen voor blobs en containers.

In dit gedeelte gaat u een exemplaar maken van de Azure-opslagclient, een exemplaar maken van het blob-serviceobject, een nieuwe container maken en machtigingen instellen voor de container, zodat de blobs openbaar zijn. De container heeft de naam **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Blobs uploaden naar de container

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. Omdat blok-blobs het meest worden gebruikt, worden ze in deze Quick Start gebruikt.  

Als u een bestand naar een blob wilt uploaden, hebt u het volledige pad van het bestand nodig dat u vindt door de naam van de map en de bestandsnaam op uw lokale schijf samen te voegen. Vervolgens kunt u het bestand uploaden naar het opgegeven pad met behulp van de methode **createBlockBlob()**. 

De voorbeeldcode maakt gebruik van een lokaal bestand en uploadt dat naar Azure. Het bestand wordt in de code opgeslagen als **myfile** en de naam van de blob als **fileToUpload**. In het volgende voorbeeld wordt het bestand geüpload naar de container met de naam **quickstartblobs**.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Gebruik de methode **createblocklist()** om een gedeeltelijke update van de inhoud van een blok-blob uit te voeren. Blok-blobs kunnen tot 4,7 TB groot zijn en kunnen van alles zijn: van Excel-spreadsheets tot grote videobestanden. Pagina-blobs worden met name gebruikt voor de VHD-bestanden die worden gebruikt ter ondersteuning van IaaS-VM‘s. Toevoeg-blobs worden gebruikt voor logboekregistratie, bijvoorbeeld wanneer u wilt schrijven naar een bestand en vervolgens meer gegevens wilt blijven toevoegen. Een toevoeg-blob moet worden gebruikt in een enkelvoudige schrijfmodus. De meeste objecten die worden opgeslagen in Blob-opslag, zijn blok-blobs.

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

U kunt een lijst met bestanden in de container ophalen met behulp van de methode **listBlobs()**. Met de volgende code wordt de lijst met blobs opgehaald en doorlopen, waarbij de namen van de blobs worden weergegeven die in een container zijn aangetroffen.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>De inhoud van uw blobs ophalen

Haal de inhoud van uw blobs op met behulp van de **getBlob()**-methode. Met de volgende code wordt de inhoud weergegeven van de blob die in een vorige sectie is geüpload.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Resources opschonen
Als u de blobs die in deze snelstart zijn geüpload niet langer nodig hebt, kunt u de volledige container verwijderen met behulp van de methode **deletecontainer()**. Als de bestanden die zijn gemaakt niet meer nodig zijn, gebruikt u de methode **deleteblob()** om ze te verwijderen.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Resources voor het ontwikkelen van PHP-toepassingen met blobs

Zie de volgende aanvullende bronnen voor PHP-ontwikkeling met Blob-opslag:

- Bekijk, download en installeer de [broncode voor de PHP-clientbibliotheek](https://github.com/Azure/azure-storage-php) voor Azure Storage op GitHub.
- Verken [Blob-opslagvoorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob) die zijn geschreven met de PHP-clientbibliotheek.

## <a name="next-steps"></a>Volgende stappen
 
In deze snelstart hebt u geleerd hoe u bestanden overbrengt tussen een lokale schijf en Azure Blob-opslag met behulp van PHP. Ga naar ons PHP-ontwikkelaarscentrum voor meer informatie over werken met PHP.

> [!div class="nextstepaction"]
> [PHP-ontwikkelaarscentrum](https://azure.microsoft.com/develop/php/)


Zie [Azure Blob-opslagresources beheren met Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over Storage Explorer en blobs.