---
title: 'Quickstart: Azure Blob Storage-clientbibliotheek v2.1 voor Python'
description: In deze snelstart maakt u een opslagaccount en een container in object(blob)-opslag. Vervolgens gebruikt u de opslagclientbibliotheek v2.1 voor Python om een blob in Azure Storage te uploaden, een blob te downloaden en de blobs in een container te vermelden.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 34205caf03d2f2d7255f75ea6203c5572c4c429b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523298"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Quickstart: Blobs beheren met Python v 2.1 SDK

In deze quickstart leert u hoe u blobs beheert met behulp van Python. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. U kunt blob, downloaden uploaden en weergeven en u kunt containers maken en verwijderen.

> [!NOTE]
> In deze snelstart wordt gebruikgemaakt van een oudere versie van de Azure Blob Storage-clientbibliotheek. Als u aan de slag wilt gaan met de vorige versie, raadpleegt u [Quickstart: Blobs beheren met Python v12 SDK](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [Azure Storage SDK voor Python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De [voorbeeldtoepassing](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) in deze snelstart is een Python-basistoepassing.  

Gebruik de volgende [git](https://git-scm.com/)-opdracht om een kopie van de toepassing te downloaden naar de ontwikkelomgeving. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Als u het Python-programma wilt controleren, opent u het bestand *example.py* in de hoofdmap van de opslagplaats.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Geef in de toepassing de naam en accountsleutel van uw opslagaccount op om een `BlockBlobService`-object te maken.

1. Open het bestand *example.py* vanuit de Solution Explorer in uw IDE.

1. Vervang de waarden van `accountname` en `accountkey` door de naam en sleutel van uw opslagaccount:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Sla het bestand op en sluit het.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Door het voorbeeldprogramma uit te voeren, wordt een testbestand aangemaakt in uw map *Documenten* en geüpload naar de Blob-opslag, worden de blobs in het bestand vermeld en wordt het bestand met een nieuwe naam gedownload.

1. De afhankelijkheden installeren:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Ga naar de voorbeeldtoepassing:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Voer het voorbeeld uit:

    ```console
    python example.py
    ```

    U ziet berichten die vergelijkbaar zijn met de volgende uitvoer:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Voordat u doorgaat, gaat u naar de map *Documenten* en controleert u of deze twee bestanden bevat.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_\<universally-unique-identifier\>_DOWNLOADED*

1. Als u ze opent, ziet u dat ze hetzelfde zijn.

    U kunt ook een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com) gebruiken. Het is handig om bestanden in Blob Storage weer te geven. Azure Storage Explorer is een gratis hulpprogramma voor meerdere platforms waarmee u toegang hebt tot de informatie over uw opslagaccount. 

1. Nadat u de bestanden hebt bekeken, drukt u op een willekeurige toets om het voorbeeld te voltooien en de testbestanden te verwijderen.

## <a name="learn-about-the-sample-code"></a>Meer informatie over de voorbeeldcode

Nu u weet wat het voorbeeldprogramma doet, opent u het bestand *example.py* om de code te bekijken.

### <a name="get-references-to-the-storage-objects"></a>Verwijzingen naar de opslagobjecten ophalen

In deze sectie gaat u exemplaren maken van de objecten, een nieuwe container maken en vervolgens machtigingen instellen voor de container, zodat de blobs openbaar zijn. U roept de container `quickstartblobs` aan. 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Eerst maakt u verwijzingen naar objecten die worden gebruikt om toegang te krijgen tot de Blob-opslag en deze te beheren. Deze objecten worden boven op elkaar gebouwd en elk ervan wordt door de volgende in de lijst gebruikt.

* Maak een exemplaar van het object **BlockBlobService** dat naar de Blob-service in uw opslagaccount verwijst. 

* Maak een exemplaar van het **CloudBlobContainer**-object dat de container vertegenwoordigt die u wilt openen. Het systeem gebruikt containers om uw blobs te organiseren op een wijze die lijkt op het gebruik van mappen op uw computer om uw bestanden te organiseren.

Wanneer u de Cloud Blob-container hebt, instantieert u het **CloudBlockBlob**-object dat wijst naar de specifieke blob waarin u bent geïnteresseerd. Daarna kunt u de blob uploaden, downloaden en kopiëren wanneer dat nodig is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Containers, blobs en metagegevens een naam geven en hiernaar verwijzen) voor meer informatie over de namen van containers en blobs.

### <a name="upload-blobs-to-the-container"></a>Blobs uploaden naar de container

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. Blok-blobs kunnen tot 4,7 TB groot zijn en kunnen van alles zijn: van Excel-spreadsheets tot grote videobestanden. U kunt toevoeg-blobs voor logboekregistratie gebruiken wanneer u gegevens wilt wegschrijven naar een bestand en vervolgens gegevens wilt blijven toevoegen. Pagina-blobs worden voornamelijk gebruikt voor de VHD-bestanden (virtuele harde schijf) die virtuele machines voor IaaS (infrastructuur als een dienst) ondersteunen. Blok-blobs worden het meest gebruikt. Deze quickstart maakt gebruik van blok-blobs.

Als u een bestand naar een blob wilt uploaden, hebt u het volledige bestandspad nodig. U verkrijgt dit door de naam van de map en de bestandsnaam op uw lokale schijf samen te voegen. Daarna kunt u het bestand met de methode `create_blob_from_path` uploaden naar het opgegeven pad. 

De voorbeeldcode maakt een lokaal bestand dat het systeem gebruikt voor het uploaden en downloaden, en slaat het bestand op dat het systeem uploadt als *volledig_pad_naar_het_bestand* waarbij de blob de naam *lokale_bestandsnaam* krijgt. In dit voorbeeld wordt het bestand geüpload naar de container met de naam `quickstartblobs`:

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Er zijn meerdere uploadmethoden die u kunt gebruiken met Blob-opslag. Als u bijvoorbeeld een geheugenstroom hebt, kunt u de methode `create_blob_from_stream` gebruiken in plaats van `create_blob_from_path`. 

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

De volgende code maakt een `generator` voor de `list_blobs`-methode. De code doorloopt de lijst met blobs in de container en geeft hun namen weer in de console.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>De blobs downloaden


Download blobs naar uw lokale schijf met de methode `get_blob_to_path`.
Met de volgende code wordt een blob gedownload die u daarvoor heeft geüpload. Het systeem voegt het achtervoegsel *_DOWNLOADED* toe aan de naam van de blob, zodat u beide bestanden op uw lokale schijf kunt zien.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Resources opschonen
Als u de blobs die in deze snelstart zijn geüpload niet langer nodig hebt, kunt u de volledige container verwijderen met de methode `delete_container`. Als u in plaats daarvan afzonderlijke bestanden wilt verwijderen, gebruikt u de methode `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Resources voor het ontwikkelen van Python-toepassingen met blobs

Raadpleeg de volgende aanvullende resources voor meer informatie over Python-ontwikkeling met Blob-opslag:

### <a name="binaries-and-source-code"></a>Binaire bestanden en broncode

- Bekijk, download en installeer de [broncode voor de Python-clientbibliotheek](https://github.com/Azure/azure-storage-python) voor Azure Storage op GitHub.

### <a name="client-library-reference-and-samples"></a>Naslaginformatie en voorbeelden voor de .NET-clientbibliotheek

- Raadpleeg de [ Azure Storage-bibliotheken voor Python](/python/api/overview/azure/storage) voor meer informatie over de Python-clientbibliotheek.
- Verken met behulp van de Python-clientbibliotheek geschreven [Blob-opslagvoorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob).

## <a name="next-steps"></a>Volgende stappen
 
In deze snelstart hebt u geleerd hoe u bestanden overbrengt tussen een lokale schijf en Azure Blob-opslag met behulp van Python. 

Zie [Azure Blob-opslagresources beheren met Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over Storage Explorer en blobs.