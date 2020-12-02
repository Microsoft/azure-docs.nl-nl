---
title: Migreren van Azure Media Services v2 naar v3
description: In dit artikel worden wijzigingen beschreven die in Azure Media Services v3 zijn geïntroduceerd en verschillen tussen twee versies worden weer gegeven.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, uitzenden, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 14544f58bcda56a55cef33de8fe0a70d5859b589
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510944"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2 versus v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel worden wijzigingen beschreven die in Azure Media Services v3 zijn geïntroduceerd en verschillen tussen twee versies worden weer gegeven.

## <a name="general-changes-from-v2"></a>Algemene wijzigingen van v2

* Zie de sectie [activa-specifieke wijzigingen](#asset-specific-changes) voor activa die gerelateerd zijn.
* De V3 Sdk's zijn nu losgekoppeld van de opslag-SDK, waarmee u meer controle hebt over de versie van de opslag-SDK die u wilt gebruiken en voor komt versie problemen. 
* In de V3-Api's bevinden alle coderings bitsnelheden zich in bits per seconde. Dit wijkt af van de v2-Media Encoder Standard voor instellingen. De bitrate in v2 wordt bijvoorbeeld opgegeven als 128 (kbps), maar in v3 zou 128000 (bits/seconde) zijn. 
* Entiteiten AssetFiles, AccessPolicies en IngestManifests bestaan niet in v3.
* ContentKeys is geen entiteit meer, het is nu een eigenschap van de streaming-Locator.
* Event Grid-ondersteuning vervangt NotificationEndpoints.
* De naam van de volgende entiteiten is gewijzigd:

   * V3 JobOutput vervangt v2 taak en maakt nu deel uit van een taak. Invoer en uitvoer zijn nu op taak niveau. Zie [een taak invoer maken op basis van een lokaal bestand](job-input-from-local-file-how-to.md)voor meer informatie. 

       Als u de geschiedenis van de voortgang van de taak wilt weer geven, luistert u naar de EventGrid-gebeurtenissen. Zie [Handling Event grid Events](reacting-to-media-services-events.md)(Engelstalig) voor meer informatie.
    * De streaming-Locator vervangt Locator.
    * Live gebeurtenis vervangt kanaal.<br/>Facturering van Live-gebeurtenissen is gebaseerd op Live Channel meters. Zie [facturering](live-event-states-billing.md) en [prijzen](https://azure.microsoft.com/pricing/details/media-services/)voor meer informatie.
    * Live uitvoer vervangt programma.
* Live-uitvoer starten zodra ze zijn gemaakt en stoppen wanneer ze worden verwijderd. Program ma's hebben in de v2-Api's anders gewerkt, maar ze moesten worden gestart na het maken.
* Als u informatie over een taak wilt ontvangen, moet u de naam van de trans formatie weten waaronder de taak is gemaakt. 
* In v2 worden XML- [invoer](../previous/media-services-input-metadata-schema.md) -en [uitvoer](../previous/media-services-output-metadata-schema.md) -meta gegevensbestanden gegenereerd als resultaat van een coderings taak. In v3 wordt de meta gegevens indeling gewijzigd van XML naar JSON. 
* In Media Services v2 kunt u de initialisatie vector (IV) opgeven. In Media Services v3 kan de FairPlay IV niet worden opgegeven. Hoewel dit geen invloed heeft op klanten die Media Services gebruiken voor zowel verpakking als licentie levering, kan het een probleem zijn bij het gebruik van een DRM-systeem van derden voor het leveren van de FairPlay-licenties (hybride modus). In dat geval is het belang rijk te weten dat de FairPlay IV is afgeleid van de cbcs-sleutel-ID en kan worden opgehaald met behulp van de volgende formule:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    wordt uitgevoerd met

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Zie voor meer informatie de [Azure functions C#-code voor Media Services v3 in de hybride modus voor zowel live als VOD-bewerkingen](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Bekijk de naamgevings regels die worden toegepast op [Media Services v3-resources](media-services-apis-overview.md#naming-conventions). Bekijk ook [naamgevings-blobs](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Functieproblemen met v2-API's

De V3 API heeft de volgende functie hiaten ten opzichte van de v2 API. Het sluiten van de hiaten is het onderhanden werk.

* De [Premium encoder](../previous/media-services-encode-asset.md) en de verouderde [Media Analytics-processors](../previous/legacy-components.md) (Azure Media Services indexer 2, Face Redactor, enzovoort) zijn niet toegankelijk via v3.<br/>Klanten die vanaf de preview-versie van Media Indexer 1 of 2 willen migreren, kunnen onmiddellijk de voor instelling AudioAnalyzer in de V3 API gebruiken.  Deze nieuwe definitie bevat meer functies dan de oudere Media Indexer 1 of 2. 
* Veel van de [geavanceerde functies van de Media Encoder Standard in v2](../previous/media-services-advanced-encoding-with-mes.md) -api's zijn momenteel niet beschikbaar in v3, zoals:
  
    * Activa bijvoegen
    * Overlays
    * Bijsnijd
    * Een Silent audio-track invoegen wanneer de invoer geen audio heeft
    * Een video track invoegen wanneer de invoer geen video bevat
* Live-gebeurtenissen met transcode ring bieden momenteel geen ondersteuning voor de toevoeging van een mid-Stream en het invoegen van AD-markeringen via API-aanroepen. 
* Raadpleeg de `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` voorbeeld code voor aanbevolen procedures en patronen met betrekking tot het gebruik van V2 rest API op. NetCore-SDK.

## <a name="asset-specific-changes"></a>Activa-specifieke wijzigingen

* Voor assets die zijn gemaakt met v3, Media Services alleen de [Azure storage opslag versleuteling aan de server zijde](../../storage/common/storage-service-encryption.md)ondersteunt.
    * U kunt v3-Api's gebruiken met assets die zijn gemaakt met v2-Api's met de [opslag versleuteling](../previous/media-services-rest-storage-encryption.md) (AES 256) van Media Services.
    * U kunt geen nieuwe assets maken met de verouderde AES 256- [opslag versleuteling](../previous/media-services-rest-storage-encryption.md) met behulp van v3-api's.
* De eigenschappen van het [activum](assets-concept.md)in v3 verschillen van v2, Zie [hoe de eigenschappen worden toegewezen](#map-v3-asset-properties-to-v2).
* De eigenschap IAsset. ParentAssets bestaat niet in v3.

### <a name="map-v3-asset-properties-to-v2"></a>V3-Asset-eigenschappen toewijzen aan v2

In de volgende tabel ziet u hoe de eigenschappen van het [element](/rest/api/media/assets/createorupdate#asset)in v3 worden toegewezen aan de eigenschappen van het element in v2.

|V3-eigenschappen|v2-eigenschappen|
|---|---|
|`id` -(uniek) het volledige Azure Resource Manager pad, zie voor beelden in [Asset](/rest/api/media/assets/createorupdate)||
|`name` -(uniek) Zie [naamgevings conventies](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` -(unieke) waarde begint met het `nb:cid:UUID:` voor voegsel.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opties voor maken)|
|`type`||

### <a name="storage-side-encryption"></a>Versleuteling van opslag side

Ter bescherming van uw assets op rest moeten de activa worden versleuteld door de versleuteling van de opslag side. In de volgende tabel ziet u hoe de opslag side-versleuteling werkt in Media Services:

|Versleutelings optie|Beschrijving|Media Services v2|Media Services v3|
|---|---|---|---|
|Opslag versleuteling Media Services|AES-256-versleuteling, sleutel beheerd door Media Services.|Ondersteund<sup>(1)</sup>|Niet ondersteund<sup>(2)</sup>|
|[Storage Service Encryption voor Data-at-rest](../../storage/common/storage-service-encryption.md)|Versleuteling aan de server zijde die wordt aangeboden door Azure Storage, sleutel die wordt beheerd door Azure of door de klant.|Ondersteund|Ondersteund|
|[Versleuteling van opslag Client-Side](../../storage/common/storage-client-side-encryption.md)|Versleuteling aan de client zijde die wordt aangeboden door Azure Storage, sleutel die wordt beheerd door de klant in Key Vault.|Niet ondersteund|Niet ondersteund|

<sup>1</sup> hoewel Media Services de verwerking van inhoud in de Clear/zonder vorm van versleuteling ondersteunt, wordt dit niet aanbevolen.

<sup>2</sup> in Media Services V3 wordt opslag VERSLEUTELING (AES-256-versleuteling) alleen ondersteund voor achterwaartse compatibiliteit wanneer uw assets zijn gemaakt met Media Services v2. Dit betekent dat v3 werkt met bestaande, versleutelde opslag assets, maar geen nieuwe apparaten mag maken.

## <a name="code-differences"></a>Code verschillen

In de volgende tabel ziet u de code verschillen tussen v2 en v3 voor algemene scenario's.

|Scenario|v2-API|V3-API|
|---|---|---|
|Een Asset maken en een bestand uploaden |[v2 .NET-voor beeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET-voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Een taak indienen|[v2 .NET-voor beeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET-voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Laat zien hoe u eerst een trans formatie maakt en vervolgens een taak verzendt.|
|Een Asset met AES-versleuteling publiceren |1. ContentKeyAuthorizationPolicyOption maken<br/>2. ContentKeyAuthorizationPolicy maken<br/>3. AssetDeliveryPolicy maken<br/>4. activa maken en inhoud uploaden of taak verzenden en uitvoer activa gebruiken<br/>5. AssetDeliveryPolicy koppelen aan Asset<br/>6. ContentKey maken<br/>7. Koppel ContentKey aan Asset<br/>8. AccessPolicy maken<br/>9. Locator maken<br/><br/>[v2 .NET-voor beeld](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. inhouds sleutel beleid maken<br/>2. Asset maken<br/>3. inhoud uploaden of activa gebruiken als JobOutput<br/>4. streaming-Locator maken<br/><br/>[v3 .NET-voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Taak Details ophalen en taken beheren |[Taken beheren met v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Taken beheren met v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Geef een blad wijzer op voor dit artikel en blijf controleren op updates.

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

[Migratie richtlijnen voor het overstappen van Media Services versie 2 naar v3](migrate-from-v2-to-v3.md)