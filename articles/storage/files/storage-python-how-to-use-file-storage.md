---
title: Ontwikkelen voor Azure Files met Python | Microsoft Docs
description: Meer informatie over het ontwikkelen van Python-toepassingen en -services die gebruikmaken van Azure Files voor het opslaan van bestandsgegevens.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8739bfaf1a41758ef3267c71cba883ef2445c39d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817788"
---
# <a name="develop-for-azure-files-with-python"></a>Ontwikkelen voor Azure Files met Python

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Leer de basisbeginselen van het gebruik van Python om apps of services te ontwikkelen die gebruikmaken van Azure Files voor het opslaan van bestandsgegevens. Maak een eenvoudige console-app en leer hoe u basisacties kunt uitvoeren met Python en Azure Files:

- Azure-bestands shares maken
- Directory's maken
- Bestanden en mappen in een Azure-bestands share opsnoemen
- Een bestand uploaden, downloaden en verwijderen
- Back-ups van bestands share maken met behulp van momentopnamen

> [!NOTE]
> Omdat Azure Files toegankelijk zijn via SMB, is het mogelijk om eenvoudige toepassingen te schrijven die toegang hebben tot de Azure-bestands share met behulp van de standaard Python I/O-klassen en -functies. In dit artikel wordt beschreven hoe u apps schrijft die gebruikmaken van de Azure Files Storage Python SDK, die gebruikmaakt van de [Azure Files REST API](/rest/api/storageservices/file-service-rest-api) om met uw Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>De SDK Azure Storage Python downloaden en installeren

> [!NOTE]
> Als u een upgrade van de Azure Storage SDK voor Python versie 0.36 of eerder wilt uitvoeren, verwijdert u de oudere SDK met behulp van voordat u het nieuwste `pip uninstall azure-storage` pakket installeert.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Voor [de Azure File Storage-clientbibliotheek v12.x voor Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) is Python 2.7 of 3.6+ vereist.

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

De [Azure Storage SDK voor Python](https://github.com/azure/azure-storage-python) vereist Python 2.7 of 3.6+.

---

## <a name="install-via-pypi"></a>Installeren via PyPI

Als u wilt installeren via de Python Package Index (PyPI), typt u:

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>De voorbeeldtoepassing weergeven

Zie [Azure Storage: Aan de slag with Azure Files in Python](https://github.com/Azure-Samples/storage-file-python-getting-started)als u een voorbeeldtoepassing wilt weergeven en uitvoeren die laat zien hoe u Python gebruikt met Azure Files.

Als u de voorbeeldtoepassing wilt uitvoeren, moet u ervoor zorgen dat u zowel de pakketten als `azure-storage-file` `azure-storage-common` hebt geïnstalleerd.

---

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor het gebruik van Azure Files

Voeg het volgende toe boven aan een Python-bronbestand om de codefragmenten in dit artikel te gebruiken.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Een verbinding instellen met Azure Files

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

[Met ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) kunt u werken met shares, mappen en bestanden. Met de volgende code maakt u `ShareServiceClient` een -object met behulp van het opslagaccount connection string.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Met [het FileService-object](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) kunt u werken met shares, mappen en bestanden. Met de volgende code maakt u `FileService` een -object met behulp van de naam van het opslagaccount en de accountsleutel. Vervang `<myaccount>` en `<mykey>` door uw accountnaam en -sleutel.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

In het volgende codevoorbeeld wordt een [ShareClient-object](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) gebruikt om de share te maken als deze niet bestaat.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

In het volgende codevoorbeeld wordt [een FileService-object](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) gebruikt om de share te maken als deze niet bestaat.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Een map maken

U kunt de opslag ordenen door bestanden in subdirectory's te plaatsen in plaats van ze allemaal in de hoofdmap te plaatsen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Met de volgende methode maakt u een map in de hoofdmap van de opgegeven bestands share met behulp van een [ShareDirectoryClient-object.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Met de onderstaande code maakt u een submap met de *naam sampledir* onder de hoofdmap.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Een bestand uploaden

In deze sectie leert u hoe u een bestand uit lokale opslag uploadt naar Azure File Storage.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Met de volgende methode wordt de inhoud van het opgegeven bestand geüpload naar de opgegeven map in de opgegeven Azure-bestands share.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Een Azure-bestands share bevat ten minste een hoofdmap waarin bestanden zich kunnen bevinden. Als u een bestand wilt maken en gegevens wilt uploaden, gebruikt u de methoden [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)of [create_file_from_text.](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) Dit zijn methoden op hoog niveau die de benodigde segmentering uitvoeren wanneer de grootte van de gegevens groter is dan 64 MB.

`create_file_from_path` uploadt de inhoud van een bestand van het opgegeven pad en uploadt de inhoud van een reeds `create_file_from_stream` geopend bestand/stream. `create_file_from_bytes` uploadt een matrix van bytes en uploadt de opgegeven tekstwaarde met behulp van de opgegeven codering `create_file_from_text` (standaard ingesteld op UTF-8).

In het volgende voorbeeld wordt de inhoud van het *sunset.png* bestand geüpload naar **het bestand myfile.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen in een Azure-bestands share opsnoemen

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Als u de bestanden en mappen in een subdirectory wilt bekijken, gebruikt u [de list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) methode . Deze methode retourneert een door itereerbare functie voor automatisch pagineren. Met de volgende code wordt de **naam van** elk bestand en elke submap in de opgegeven map naar de console uitgevoerd.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Als u de bestanden en mappen in een share wilt bekijken, gebruikt u [de list_directories_and_files-methode.](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) Deze methode retourneert een generator. Met de volgende code wordt de **naam van** elk bestand en elke map in een share naar de console uitgevoerd.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Een bestand downloaden

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Als u gegevens uit een bestand wilt downloaden, gebruikt [u download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

In het volgende voorbeeld wordt gedemonstreerd hoe u gebruikt om de inhoud van het opgegeven bestand op te halen en lokaal op te slaan met `download_file` **GEDOWNLOAD-** voorbereid op de bestandsnaam.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Als u gegevens uit een bestand wilt downloaden, gebruikt [u get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)of [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). Dit zijn methoden op hoog niveau die de benodigde segmentering uitvoeren wanneer de grootte van de gegevens groter is dan 64 MB.

In het volgende voorbeeld wordt gedemonstreerd hoe u gebruikt om de inhoud van het `get_file_to_path` **bestand myfile** te downloaden en op te slaan in *out-sunset.png* bestand.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Momentopname van het maken van een share

U kunt een tijdsexemplaar van uw hele bestands share maken.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Momentopname van share maken met metagegevens**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Shares en momentopnamen op een lijst zetten

U kunt alle momentopnamen voor een bepaalde share bekijken.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Bladeren door momentopname van share

U kunt door elke momentopname van de share bladeren om bestanden en mappen op te halen vanaf dat moment.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Bestand op halen uit momentopname van share

U kunt een bestand downloaden van een momentopname van een share. Hiermee kunt u een eerdere versie van een bestand herstellen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Een momentopname van één share verwijderen
U kunt een momentopname van één share verwijderen.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Een bestand verwijderen

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Als u een bestand wilt verwijderen, roept u [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-).

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Als u een bestand wilt verwijderen, roept u [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-).

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Share verwijderen wanneer momentopnamen van share bestaan

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Als u een share wilt verwijderen die momentopnamen bevat, roept u [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) aan met `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Een share die momentopnamen bevat, kan niet worden verwijderd, tenzij eerst alle momentopnamen worden verwijderd.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw Azure Files met Python, volgt u deze koppelingen voor meer informatie.

- [Python Developer Center](/azure/developer/python/)
- [REST-API voor Azure Storage-services](/rest/api/azure/)
- [Microsoft Azure Storage SDK voor Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
