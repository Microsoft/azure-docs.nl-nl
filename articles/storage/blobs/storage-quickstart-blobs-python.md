---
title: 'Snelstartgids: Azure Blob Storage bibliotheek V12-python'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 voor python kunt gebruiken om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e315f0f4f7bfff03a659de430e6fe182037f1b8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99096403"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Quickstart: Blobs beheren met Python v12 SDK

In deze quickstart leert u hoe u blobs beheert met behulp van Python. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. U kunt blob, downloaden uploaden en weergeven en u kunt containers maken en verwijderen.

Meer resources:

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

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-client bibliotheek V12 voor python.

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

1. Maak een andere map met de naam *gegevens* aan in de map *blob-quickstart-v12*. In deze map worden de BLOB-gegevens bestanden gemaakt en opgeslagen.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Blob Storage-client bibliotheek voor python-pakket met behulp van de `pip install` opdracht.

```console
pip install azure-storage-blob
```

Met deze opdracht wordt de Azure Blob Storage-client bibliotheek voor python-pakket en alle bibliotheken waarvan deze afhankelijk is, geïnstalleerd. In dit geval is dat alleen de Azure-kernbibliotheek voor Python.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open een nieuw tekstbestand in uw code-editor
1. Voeg `import`-instructies toe
1. Maak de structuur voor het programma, waaronder eenvoudige afhandeling van uitzonderingen

    Hier volgt de code:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

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

In deze voorbeeld code fragmenten ziet u hoe u de volgende taken kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor python:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

Met de volgende code wordt het opslag account connection string opgehaald uit de omgevings variabele die in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) is gemaakt.

Voeg deze code toe in het `try`-blok:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Een container maken

Verzin een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de container naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) door de methode [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) aan te roepen. Roep vervolgens de methode [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) aan om de container in uw opslagaccount te maken.

Voeg deze code toe aan het einde van het `try`-blok:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Hiermee maakt u een lokale map voor het opslaan van gegevens bestanden.
1. Hiermee maakt u een tekstbestand aan in de lokale map.
1. Hiermee wordt een verwijzing naar een [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient)-object opgehaald door de methode [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) te gebruiken op de [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) van het gedeelte [Een container maken](#create-a-container).
1. Hiermee wordt het lokale tekstbestand geüpload naar de blob door de methode [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) aan te roepen.

Voeg deze code toe aan het einde van het `try`-blok:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Geeft de blobs in de container weer door de methode [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) aan te roepen. In dit geval is slechts één blob aan de container toegevoegd, zodat met de weergavebewerking alleen die ene blob wordt geretourneerd.

Voeg deze code toe aan het einde van het `try`-blok:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de methode [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) aan te roepen. De voorbeeldcode voegt het achtervoegsel 'DOWNLOAD' toe aan de naam van het bestand, zodat u beide bestanden in het lokale bestandssysteem kunt zien.

Voeg deze code toe aan het einde van het `try`-blok:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Een container verwijderen

De volgende code ruimt de resources die door de app zijn gemaakt op door de hele container te verwijderen met behulp van de methode [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-). U kunt ook de lokale bestanden verwijderen als u dat wilt.

De app pauzeert voor gebruikersinvoer door `input()` aan te roepen voordat deze de blob, container en lokale bestanden verwijdert. Controleer of de resources correct zijn gemaakt voordat ze worden verwijderd.

Voeg deze code toe aan het einde van het `try`-blok:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>De code uitvoeren

Met deze app wordt een test bestand gemaakt in uw lokale map en geüpload naar Azure Blob Storage. In het voor beeld worden de blobs in de container weer gegeven en wordt het bestand met een nieuwe naam gedownload. U kunt de oude en nieuwe bestanden vergelijken.

Navigeer naar de map die het bestand *blob-quickstart-v12.py* bevat, en voer vervolgens de volgende `python`-opdracht uit om de app uit te voeren.

```console
python blob-quickstart-v12.py
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Blob Storage v12 - Python quickstart sample

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

Controleer voordat u begint met het opschonings proces de *gegevensmap* voor de twee bestanden. U kunt ze openen en bekijken dat ze identiek zijn.

Nadat u de bestanden hebt gecontroleerd, drukt u op **Enter** om de testbestanden te verwijderen en het voorbeeld te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u blobs kunt uploaden, downloaden en er een lijst van maken met behulp van Python.

Als u voorbeeld-apps voor Blob-opslag wilt zien, ga dan naar:

> [!div class="nextstepaction"]
> [Voor beelden van Azure Blob Storage SDK V12 python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Zie de [Azure Storage-clientbibliotheken voor Python](/azure/developer/python/sdk/storage/overview) voor meer informatie.
* Ga naar [Azure voor Python-ontwikkelaars](/azure/python/) voor zelfstudies, voorbeelden, quickstarts en andere documentatie.
