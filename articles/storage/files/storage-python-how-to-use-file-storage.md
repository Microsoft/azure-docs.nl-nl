---
title: Ontwikkelen voor Azure Files met python | Microsoft Docs
description: Meer informatie over het ontwikkelen van python-toepassingen en-services die gebruikmaken van Azure Files voor het opslaan van bestands gegevens.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8bef69037fad8bf8ee9537e90f26ca967560b9d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91876094"
---
# <a name="develop-for-azure-files-with-python"></a>Ontwikkelen voor Azure Files met Python

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Leer de basis beginselen van het gebruik van python voor het ontwikkelen van apps of services die gebruikmaken van Azure Files om bestands gegevens op te slaan. Maak een eenvoudige console-app en leer hoe u basis bewerkingen uitvoert met python en Azure Files:

- Azure-bestands shares maken
- Directory's maken
- Bestanden en mappen in een Azure-bestands share opsommen
- Een bestand uploaden, downloaden en verwijderen
- Back-ups van bestands shares maken met behulp van moment opnamen

> [!NOTE]
> Omdat Azure Files mogelijk toegankelijk is via SMB, is het mogelijk om eenvoudige toepassingen te schrijven die toegang hebben tot de Azure-bestands share met behulp van de Standard python I/O-klassen en-functies. In dit artikel wordt beschreven hoe u apps schrijft die gebruikmaken van de Azure Files Storage python SDK, die gebruikmaakt van de [Azure Files rest API](/rest/api/storageservices/file-service-rest-api) om te praten met Azure files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK voor python downloaden en installeren

> [!NOTE]
> Als u een upgrade uitvoert van de Azure Storage SDK voor python versie 0,36 of eerder, verwijdert u de oudere SDK met `pip uninstall azure-storage` voordat u het meest recente pakket installeert.

# <a name="python-v12"></a>[Python-V12](#tab/python)

De [Azure File Storage-client bibliotheek V12. x voor python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) vereist python 2,7 of 3,5.

# <a name="python-v2"></a>[Python v2](#tab/python2)

De [Azure Storage SDK voor python](https://github.com/azure/azure-storage-python) vereist python 2,7, 3,3, 3,4, 3,5 of 3,6.

---

## <a name="install-via-pypi"></a>Installeren via PyPI

Als u wilt installeren via de python-pakket index (PyPI), typt u:

# <a name="python-v12"></a>[Python-V12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>De voorbeeld toepassing weer geven

Zie [Azure Storage: aan de slag met Azure files in python](https://github.com/Azure-Samples/storage-file-python-getting-started)om een voorbeeld toepassing weer te geven en uit te voeren die laat zien hoe u python gebruikt met Azure files.

Als u de voorbeeld toepassing wilt uitvoeren, moet u ervoor zorgen dat u zowel de als-pakketten hebt geïnstalleerd `azure-storage-file` `azure-storage-common` .

---

## <a name="set-up-your-application-to-use-azure-files"></a>Uw toepassing instellen voor gebruik Azure Files

Voeg het volgende toe boven aan een python-bron bestand om de code fragmenten in dit artikel te gebruiken.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Een verbinding met Azure Files instellen

# <a name="python-v12"></a>[Python-V12](#tab/python)

Met [ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) kunt u werken met shares, mappen en bestanden. Met de volgende code wordt een- `ShareServiceClient` object gemaakt met behulp van het opslag account Connection String.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Met het [File Service](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) -object kunt u werken met shares, mappen en bestanden. Met de volgende code wordt een `FileService` object gemaakt met behulp van de naam van het opslag account en de account sleutel. Vervang `<myaccount>` en `<mykey>` door uw accountnaam en -sleutel.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

# <a name="python-v12"></a>[Python-V12](#tab/python)

In het volgende code voorbeeld wordt een [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) -object gebruikt om de share te maken als deze niet bestaat.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

In het volgende code voorbeeld wordt een [File Service](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) -object gebruikt om de share te maken als deze niet bestaat.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Een map maken

U kunt opslag indelen door bestanden in submappen te plaatsen in plaats van deze allemaal in de hoofdmap.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Met de volgende methode maakt u een map in de hoofdmap van de opgegeven bestands share met behulp van een [ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) -object.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Met de onderstaande code wordt een submap gemaakt met de naam *sampledir* onder de hoofdmap.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Een bestand uploaden

In deze sectie leert u hoe u een bestand van lokale opslag uploadt naar Azure File Storage.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Met de volgende methode wordt de inhoud van het opgegeven bestand geüpload naar de opgegeven map in de opgegeven Azure-bestands share.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Een Azure-bestands share bevat ten minste een hoofdmap waarin bestanden kunnen worden opgeslagen. Gebruik de methoden [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)of [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) om een bestand te maken en gegevens te uploaden. Dit zijn methoden op hoog niveau die de benodigde Chunking uitvoeren wanneer de grootte van de gegevens groter is dan 64 MB.

`create_file_from_path` uploadt de inhoud van een bestand uit het opgegeven pad en `create_file_from_stream` uploadt de inhoud van een al geopend bestand/stream. `create_file_from_bytes` uploadt een byte matrix en `create_file_from_text` uploadt de opgegeven tekst waarde met behulp van de opgegeven code ring (wordt standaard ingesteld op UTF-8).

In het volgende voor beeld wordt de inhoud van het *sunset.png* -bestand geüpload naar het bestand **MyFile** .

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Bestanden en mappen in een Azure-bestands share opsommen

# <a name="python-v12"></a>[Python-V12](#tab/python)

Als u de bestanden en mappen in een submap wilt weer geven, gebruikt u de methode [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) . Deze methode retourneert een automatisch pagineren die kan worden gebruikt. Met de volgende code wordt de **naam** van elk bestand en elke submap in de opgegeven map naar de-console uitgevoerd.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Gebruik de methode [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) om de bestanden en mappen in een share weer te geven. Deze methode retourneert een generator. Met de volgende code wordt de **naam** van elk bestand en elke map in een share naar de-console uitgevoerd.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Een bestand downloaden

# <a name="python-v12"></a>[Python-V12](#tab/python)

Als u gegevens uit een bestand wilt downloaden, gebruikt u [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

Het volgende voor beeld wordt gebruikt `download_file` om de inhoud van het opgegeven bestand op te halen en lokaal op te slaan met **down load-voor-** voor voegsel van de bestands naam.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Als u gegevens uit een bestand wilt downloaden, gebruikt u [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)of [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). Dit zijn methoden op hoog niveau die de benodigde Chunking uitvoeren wanneer de grootte van de gegevens groter is dan 64 MB.

In het volgende voor beeld ziet u hoe u `get_file_to_path` de inhoud van het bestand **MyFile** downloadt en opslaat in het *out-sunset.png* -bestand.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Momentopname van het maken van een share

U kunt een punt in de tijd kopie van uw volledige bestands share maken.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Een moment opname van een share maken met meta gegevens**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Shares en moment opnamen weer geven

U kunt alle moment opnamen voor een bepaalde share weer geven.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Bladeren in share-moment opname

U kunt door elke moment opname van een share bladeren om bestanden en mappen vanaf dat moment op te halen.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Bestand van moment opname van share ophalen

U kunt een bestand downloaden van een moment opname van een share. Zo kunt u een eerdere versie van een bestand herstellen.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Een moment opname van één share verwijderen
U kunt één moment opname van een share verwijderen.

# <a name="python-v12"></a>[Python-V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Een bestand verwijderen

# <a name="python-v12"></a>[Python-V12](#tab/python)

Als u een bestand wilt verwijderen, roept u [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-)aan.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Als u een bestand wilt verwijderen, roept u [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-)aan.

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Share verwijderen wanneer moment opnamen van shares bestaan

# <a name="python-v12"></a>[Python-V12](#tab/python)

Als u een share met moment opnamen wilt verwijderen, roept u [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) aan `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Een share die moment opnamen bevat, kan pas worden verwijderd als alle moment opnamen eerst worden verwijderd.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure Files kunt bewerken met python, volgt u deze koppelingen voor meer informatie.

- [Python Developer Center](/azure/developer/python/)
- [REST-API voor Azure Storage-services](/rest/api/azure/)
- [Microsoft Azure Storage SDK voor python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
