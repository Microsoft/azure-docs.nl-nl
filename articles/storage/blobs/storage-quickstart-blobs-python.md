---
title: 'Quickstart: Azure Blob Storage-bibliotheek v12 - Python'
description: In deze quickstart leert u hoe u de Azure Blob Storage-clientbibliotheek versie 12 voor Python kunt gebruiken om een container te maken en een blob-in-blob-opslag (object). Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: dbac832165c57e14e737372e12c4f4b2f3039c0d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995251"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Quickstart: Blobs beheren met Python v12 SDK

In deze quickstart leert u hoe u blobs beheert met behulp van Python. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. U kunt blob, downloaden uploaden en weergeven en u kunt containers maken en verwijderen.

Aanvullende bronnen:

* [API-referentiedocumentatie](/python/api/azure-storage-blob)
* [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Pakket (Python-pakketindex)](https://pypi.org/project/azure-storage-blob/)
* [Voorbeelden](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 of hoger.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Instellen

In dit gedeelte wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-clientbibliotheek v12 voor Python.

### <a name="create-the-project"></a>Het project maken

Maak een Python-toepassing met de naam *blob-quickstart-v12*.

1. Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor het project.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Schakel over naar de zojuist gemaakte map *blob-quickstart-v12*.

    ```console
    cd blob-quickstart-v12
    ```

1. Maak een andere map met de naam *gegevens* aan in de map *blob-quickstart-v12*. Hier worden de blob-gegevensbestanden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de toepassingsmap en installeer de Azure Blob Storage-clientbibliotheek voor het Python-pakket met behulp van de opdracht `pip install`.

```console
pip install azure-storage-blob
```

Met deze opdracht wordt het pakket Azure Blob Storage-clientbibliotheek voor Python geïnstalleerd, evenals alle bibliotheken waarvan het afhankelijk is. In dit geval is dat alleen de Azure-kernbibliotheek voor Python.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open een nieuw tekstbestand in uw code-editor
1. Voeg `import`-instructies toe
1. Maak de structuur voor het programma, waaronder eenvoudige afhandeling van uitzonderingen

    Hier volgt de code:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, __version__

    try:
        print("Azure Blob storage v" + __version__ + " - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Sla het nieuwe bestand op als *blob-quickstart-v12.py* in de map *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende Python-klassen om te communiceren met deze resources:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): Met de klasse `BlobServiceClient` kunt u Azure Storage-resources en blob-containers bewerken.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): Met de klasse `ContainerClient` kunt u Azure Storage-containers en de bijbehorende blobs bewerken.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): Met de klasse `BlobClient` kunt u Azure Storage-blobs bewerken.

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende acties kunt uitvoeren met de Azure Blob Storage-clientbibliotheek voor Python:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op van de omgevingsvariabele die is gemaakt in de sectie [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe in het `try`-blok:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Een container maken

Verzin een naam voor de nieuwe container. Met de onderstaande code wordt een UUID-waarde aan de containernaam toegevoegd om te verzekeren dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) door de methode [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) aan te roepen. Roep vervolgens de methode [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) aan om de container in uw opslagaccount te maken.

Voeg deze code toe aan het einde van het `try`-blok:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Hiermee maakt u een tekstbestand aan in de lokale map.
1. Hiermee wordt een verwijzing naar een [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient)-object opgehaald door de methode [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) te gebruiken op de [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) van het gedeelte [Een container maken](#create-a-container).
1. Hiermee wordt het lokale tekstbestand geüpload naar de blob door de methode [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) aan te roepen.

Voeg deze code toe aan het einde van het `try`-blok:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Geeft de blobs in de container weer door de methode [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) aan te roepen. In dit geval is slechts één blob aan de container toegevoegd, zodat met de weergavebewerking alleen die ene blob wordt geretourneerd.

Voeg deze code toe aan het einde van het `try`-blok:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de methode [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) aan te roepen. De voorbeeldcode voegt het achtervoegsel 'DOWNLOAD' toe aan de naam van het bestand, zodat u beide bestanden in het lokale bestandssysteem kunt zien.

Voeg deze code toe aan het einde van het `try`-blok:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Een container verwijderen

De volgende code ruimt de resources die door de app zijn gemaakt op door de hele container te verwijderen met behulp van de methode [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-). U kunt ook de lokale bestanden verwijderen als u dat wilt.

De app pauzeert voor gebruikersinvoer door `input()` aan te roepen voordat deze de blob, container en lokale bestanden verwijdert. Dit is een goede gelegenheid om te controleren dat de resources correct zijn gemaakt, voordat ze worden verwijderd.

Voeg deze code toe aan het einde van het `try`-blok:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app wordt een testbestand gemaakt in uw lokale map en geüpload naar de Blob-opslag. Vervolgens wordt een lijst gemaakt van de blobs in de container en wordt het bestand gedownload met een nieuwe naam, zodat u het oude en nieuwe bestand kunt vergelijken.

Navigeer naar de map die het bestand *blob-quickstart-v12.py* bevat, en voer vervolgens de volgende `python`-opdracht uit om de app uit te voeren.

```console
python blob-quickstart-v12.py
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Voordat u begint met opschonen, controleert u of de twee bestanden zich in de map *gegevens* bevinden. Als u ze opent, ziet u dat ze identiek zijn.

Nadat u de bestanden hebt gecontroleerd, drukt u op **Enter** om de testbestanden te verwijderen en het voorbeeld te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u blobs kunt uploaden, downloaden en er een lijst van maken met behulp van Python.

Als u voorbeeld-apps voor Blob-opslag wilt zien, ga dan naar:

> [!div class="nextstepaction"]
> [Azure Blob SDK v12 Python-voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Zie de [Azure Storage-clientbibliotheken voor Python](/azure/developer/python/sdk/storage/overview) voor meer informatie.
* Ga naar [Azure voor Python-ontwikkelaars](/azure/python/) voor zelfstudies, voorbeelden, quickstarts en andere documentatie.