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

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Wanneer u [een asset hebt gemaakt met behulp van Postman of een andere REST-methode en de SAS-URL voor de asset hebt opgehaald](how-to-create-asset.md?tabs=rest), gebruikt u de Azure Storage API's of SDK's (bijvoorbeeld de [Storage-REST API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)).

---
<!-- add these to the tabs when available -->
Zie de [Azure Storage-documentatie](../../storage/blobs/index.yml) voor andere methoden om met blobs te werken in [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md) en [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Volgende stappen

> [Overzicht van Media Services](media-services-overview.md)