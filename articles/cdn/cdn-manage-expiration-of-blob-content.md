---
title: Verlopen van Azure Blob Storage beheren
titleSuffix: Azure Content Delivery Network
description: Meer informatie over de opties voor het beheren van time-to-Live voor blobs in Azure CDN caching.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: how-to
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 206ff6f888229356743bebb816cf03e4f7a7504b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778713"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>De verval datum van Azure Blob-opslag in Azure CDN beheren
> [!div class="op_single_selector"]
> * [Azure-webinhoud](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

De [Blob Storage-service](../storage/common/storage-introduction.md#blob-storage) in azure Storage is een van de verschillende op Azure gebaseerde oorsprong die is geïntegreerd met Azure Content Delivery Network (CDN). Alle openbaar toegankelijke blob-inhoud kan in de cache worden opgeslagen in Azure CDN totdat de TTL (time-to-Live) is verstreken. De TTL wordt bepaald door de `Cache-Control` header in het HTTP-antwoord van de oorspronkelijke server. In dit artikel worden verschillende manieren beschreven waarop u de `Cache-Control` koptekst kunt instellen voor een BLOB in azure Storage.

U kunt ook de cache-instellingen van de Azure Portal beheren door de regels voor CDN-caching in te stellen. Als u een cache regel maakt en het cache gedrag instelt op het **overschrijven** of **overs Laan** van de cache, worden de door de bron opgegeven cache-instellingen die in dit artikel worden besproken genegeerd. Zie [How caching Works](cdn-how-caching-works.md)(Engelstalig) voor meer informatie over algemene cache concepten.

> [!TIP]
> U kunt ervoor kiezen om geen TTL in te stellen voor een blob. In dit geval past Azure CDN automatisch een standaard-TTL van zeven dagen toe, tenzij u in de Azure Portal cache regels hebt ingesteld. Deze standaard-TTL geldt alleen voor algemene optimalisaties van webleveringen. Voor grote bestands optimalisaties is de standaard-TTL één dag en voor optimalisaties van mediastreaming, is de standaard-TTL één jaar.
> 
> Zie [overzicht van Azure Content Delivery Network](cdn-overview.md)voor meer informatie over de werking van Azure CDN om de toegang tot blobs en andere bestanden sneller te maken.
> 
> Zie [Inleiding tot Blob Storage](../storage/blobs/storage-blobs-introduction.md)voor meer informatie over Azure Blob-opslag.
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Cache-Control headers instellen met behulp van CDN-cache regels
De voorkeurs methode voor het instellen van de koptekst van een BLOB `Cache-Control` is het gebruik van cache regels in de Azure Portal. Zie [beheer van Azure CDN caching met cache regels](cdn-caching-rules.md)voor meer informatie over de regels voor CDN-caching.

> [!NOTE] 
> Regels voorcaching zijn alleen beschikbaar voor de profielen **Azure CDN Standard van Verizon** en **Azure CDN Standard van Akamai**. Voor **Azure CDN Premium van Verizon** -profielen moet u de [engine Azure CDN Rules](./cdn-verizon-premium-rules-engine.md) in de portal **beheren** gebruiken voor vergelijk bare functionaliteit.

**Ga naar de pagina regels voor CDN-caching**:

1. Selecteer in de Azure Portal een CDN-profiel en selecteer vervolgens het eind punt voor de blob.

2. Selecteer in het linkerdeelvenster onder Instellingen de optie **Regels voor opslaan in cache**.

   ![Knop regels voor CDN-caching](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   De pagina **Regels voor opslaan in cache** wordt weergegeven.

   ![Pagina voor CDN-caching](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**De Cache-Control headers van een Blob Storage-service instellen met behulp van algemene regels voor opslaan in cache:**

1. Stel onder **algemene regels voor caching** het **in cache geheugen opslaan van de query reeks** in op het negeren van **query reeksen** en instellen dat **cache gedrag** wordt **overschreven**.
      
2. Voer 3600 in het vak **seconden** of 1 in het vak **uren** in voor de **verloop tijd** van de cache. 

   ![Voor beeld van globale CDN-cache regels](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Met deze algemene regel voor opslaan in cache wordt een cache duur van één uur ingesteld en is dit van invloed op alle aanvragen voor het eind punt. Het onderdrukt de `Cache-Control` of `Expires` http-headers die worden verzonden door de oorspronkelijke server die door het eind punt is opgegeven.   

3. Selecteer **Opslaan**.
 
**De Cache-Control headers van een blob-bestand instellen met behulp van aangepaste regels voor opslaan in cache:**

1. Maak onder **regels voor aangepaste caching** twee match-voor waarden:

     A. Voor de eerste match-voor waarde stelt u de voor **waarde match** in op **Path** en voert u in `/blobcontainer1/*` voor de **waarde match**. Stel **caching-gedrag** in op **overschrijven** en voer 4 in het vak **uren** in.

    B. Voor de tweede voor waarde match stelt u de voor waarde **match** in op **Path** en voert u in `/blobcontainer1/blob1.txt` voor de **waarde match**. Stel **caching-gedrag** in op **overschrijven** en voer 2 in het vak **uren** in.

    ![Voor beeld van regels voor aangepaste CDN-caching](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Met de eerste aangepaste regel voor opslaan in cache wordt een cache duur van vier uur ingesteld voor BLOB-bestanden in de `/blobcontainer1` map op de oorspronkelijke server die door het eind punt is opgegeven. Met de tweede regel wordt alleen de eerste regel voor het `blob1.txt` blobbestand overschreven en wordt er een cache duur van twee uur voor ingesteld.

2. Selecteer **Opslaan**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Cache-Control headers instellen met behulp van Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) is een van de snelste en krach tigste manieren om uw Azure-Services te beheren. Gebruik de `Get-AzStorageBlob` cmdlet om een verwijzing naar de BLOB op te halen en stel vervolgens de `.ICloudBlob.Properties.CacheControl` eigenschap in. 

Bijvoorbeeld:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> U kunt ook Power shell gebruiken voor [het beheren van uw CDN-profielen en-eind punten](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Cache-Control headers instellen met behulp van .NET
Als u de koptekst van een BLOB wilt opgeven met `Cache-Control` behulp van .net-code, gebruikt u de [Azure Storage-client bibliotheek voor .net](../storage/blobs/storage-quickstart-blobs-dotnet.md) om de eigenschap [CloudBlob. Properties. CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) in te stellen.

Bijvoorbeeld:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Er zijn meer .NET-code voorbeelden beschikbaar in [Azure Blob Storage-voor beelden voor .net](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Cache-Control headers instellen met behulp van andere methoden

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Met [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)kunt u uw Blob Storage-resources weer geven en bewerken, inclusief eigenschappen zoals de eigenschap *CacheControl* . 

De eigenschap *CacheControl* van een BLOB bijwerken met Azure Storage Explorer:
   1. Selecteer een BLOB en selecteer **Eigenschappen** in het context menu. 
   2. Schuif omlaag naar de eigenschap *CacheControl* .
   3. Voer een waarde in en selecteer vervolgens **Opslaan**.


![Azure Storage Explorer eigenschappen](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure-opdrachtregelinterface
Met de [azure Command-Line interface](/cli/azure) (CLI) kunt u Azure Blob-resources beheren vanaf de opdracht regel. Als u de cache-Control-header wilt instellen wanneer u een blob met de Azure CLI uploadt, stelt u de eigenschap *cacheControl* in met behulp van de `-p` Switch. In het volgende voor beeld ziet u hoe u de TTL instelt op één uur (3600 seconden):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure Storage-services REST API
U kunt de [Azure Storage-services rest API](/rest/api/storageservices/) om de eigenschap *x-MS-BLOB-cache-Control* expliciet in te stellen met behulp van de volgende bewerkingen op aanvraag:
  
   - [Blob plaatsen](/rest/api/storageservices/Put-Blob)
   - [Blokkerings lijst plaatsen](/rest/api/storageservices/Put-Block-List)
   - [BLOB-eigenschappen instellen](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>De Cache-Control-header testen
U kunt de TTL-instellingen van uw blobs eenvoudig controleren. Test met de [ontwikkel hulpprogramma's](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)van uw browser of uw BLOB de `Cache-Control` reactie header bevat. U kunt ook een hulp programma zoals [wget](https://www.gnu.org/software/wget/), [postman](https://www.getpostman.com/)of [Fiddler](https://www.telerik.com/fiddler) gebruiken om de antwoord headers te onderzoeken.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het beheren van de verval datum van Cloud service-inhoud in Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Meer informatie over de cache concepten](cdn-how-caching-works.md)