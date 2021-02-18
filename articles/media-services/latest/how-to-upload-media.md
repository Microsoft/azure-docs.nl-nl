---
Titel: Upload media: beschrijving van Azure Media Services: meer informatie over het uploaden van media voor streaming of code ring.
Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila editor: ' '

MS. service: Media-Services MS. workload: MS. topic: How-to MS. date: 08/31/2020 MS. Author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>Media uploaden voor streaming of codering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Media Services uploadt u uw digitale bestanden (media) naar een blob-container die is gekoppeld aan een asset. De entiteit [Asset](/rest/api/media/operations/asset) kan video, audio, afbeeldingen, verzamelingen miniaturen, tekstsporen en ondertitelingsbestanden (en de metagegevens over deze bestanden) bevatten. Zodra de bestanden in de container van de asset zijn geüpload, wordt de inhoud veilig opgeslagen in de cloud voor verdere verwerking en streaming.

Voordat u aan de slag gaat, moet u echter een paar waarden verzamelen of bedenken.

1. Het lokale bestandspad naar het bestand dat u wilt uploaden
1. De asset-id voor de asset (container)
1. De naam die u wilt gebruiken voor het geüploade bestand, inclusief de extensie
1. De naam van het opslagaccount dat u gebruikt
1. De toegangssleutel voor uw opslagaccount

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

Ervan uitgaande dat uw code al een verificatie heeft ingesteld en u al een invoer element hebt gemaakt, gebruikt u het volgende code fragment voor het uploaden van lokale bestanden naar die asset (in_container).

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
Zie de [Azure Storage-documentatie](../../storage/blobs/index.yml) voor andere methoden om met blobs te werken in [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md) en [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Volgende stappen

> [Overzicht van Media Services](media-services-overview.md)
