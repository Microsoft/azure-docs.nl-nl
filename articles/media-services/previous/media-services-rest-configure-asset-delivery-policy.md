---
title: Leverings beleid voor assets configureren met behulp van Media Services REST API | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u verschillende beleids regels voor levering van assets configureert met behulp van Media Services REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 65a7b9479dae1826f478ca5e038ef5feb1caefb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017321"
---
# <a name="configuring-asset-delivery-policies"></a>Leverings beleid voor assets configureren

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Als u van plan bent om dynamisch versleutelde assets te leveren, is een van de stappen in de Media Services werk stroom voor inhouds levering de configuratie van het leverings beleid voor assets. Het beleid voor levering van activa vertelt Media Services hoe u wilt dat uw activa worden geleverd: in het streaming-protocol moet uw asset dynamisch worden verpakt (bijvoorbeeld MPEG DASH, HLS, Smooth Streaming of alle), ongeacht of u uw activa dynamisch wilt versleutelen en hoe (envelop of gemeen schappelijke versleuteling).

In dit onderwerp wordt beschreven waarom en hoe u beleid voor levering van assets maakt en configureert.

> [!NOTE]
> Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 
>
> Als u dynamische pakketten en dynamische versleuteling wilt gebruiken, moet uw asset bovendien een set adaptieve bitsnelheid Mp4's of een adaptieve bitrate Smooth Streaming-bestanden bevatten.

U kunt verschillende beleids regels Toep assen op hetzelfde activum. U kunt bijvoorbeeld PlayReady-versleuteling Toep assen op Smooth Streaming en AES-envelop versleuteling naar MPEG DASH en HLS. Alle protocollen die niet zijn gedefinieerd in een leveringsbeleid (u voegt bijvoorbeeld één beleid toe waarmee alleen HLS als protocol wordt opgegeven), worden voor streaming geblokkeerd. De uitzondering hierop is als u helemaal geen leveringsbeleid voor assets hebt gedefinieerd. In dat geval is streaming voor alle protocollen toegestaan.

Als u een door opslag versleutelde Asset wilt leveren, moet u het leverings beleid van het activum configureren. Voordat uw asset kan worden gestreamd, verwijdert de streaming-server de opslag versleuteling en streamt uw inhoud met behulp van het opgegeven leverings beleid. Als u bijvoorbeeld uw asset versleuteld wilt leveren met de versleutelings sleutel voor de Advanced Encryption Standard (AES), stelt u het beleids type in op **DynamicEnvelopeEncryption**. Als u de opslag versleuteling wilt verwijderen en het activum in de heldere stroom wilt streamen, stelt u het beleids type in op **NoDynamicEncryption**. Voor beelden die laten zien hoe deze beleids typen moeten worden geconfigureerd, volgt.

Afhankelijk van hoe u het beleid voor de levering van assets configureert, kunt u de volgende streaming-protocollen dynamisch verpakken, dynamisch versleutelen en streamen: Smooth Streaming, HLS, MPEG DASH streams.

De volgende lijst bevat de indelingen die u gebruikt voor het streamen van vloeiende, HLS, streepje.

Smooth Streaming:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest

HLS

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=mpd-time-csf)


Zie [Build a streaming URL](media-services-deliver-streaming-content.md) (Een streaming-URL bouwen) voor instructies over het publiceren van een asset en bouwen van een streaming-URL.

## <a name="considerations"></a>Overwegingen
* U kunt een AssetDeliveryPolicy die is gekoppeld aan een Asset niet verwijderen terwijl er een OnDemand-Locator (streaming) bestaat voor die Asset. De aanbeveling is het beleid uit de Asset te verwijderen voordat u het beleid verwijdert.
* Er kan geen streaming-Locator worden gemaakt op een door opslag versleutelde Asset wanneer er geen beleid voor levering van assets is ingesteld.  Als de Asset niet is versleuteld, kunt u met het systeem een Locator maken en de Asset streamen zonder activa leverings beleid.
* U kunt meerdere Asset Delivery policies koppelen aan één enkel activum, maar u kunt slechts één manier opgeven om een bepaalde AssetDeliveryProtocol te verwerken.  Dit betekent dat als u probeert twee leverings beleidsregels te koppelen die het AssetDeliveryProtocol. SmoothStreaming-protocol opgeven dat een fout veroorzaakt, omdat het systeem niet weet welke toepassing u wilt Toep assen wanneer een client een Smooth Streaming aanvraag doet.
* Als u een Asset met een bestaande streaming-Locator hebt, kunt u geen nieuw beleid koppelen aan de Asset, een bestaand beleid ontkoppelen van de Asset of een leverings beleid bijwerken dat is gekoppeld aan de Asset.  Eerst moet u de streaming-Locator verwijderen, het beleid aanpassen en de streaming-Locator vervolgens opnieuw maken.  U kunt dezelfde locatorId gebruiken wanneer u de streaming-Locator opnieuw maakt, maar u moet er wel voor zorgen dat er geen problemen voor clients optreden omdat inhoud door de oorsprong of een downstream CDN kan worden opgeslagen in de cache.

> [!NOTE]
> 
> Wanneer u entiteiten in Media Services opent, moet u specifieke header-velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 

## <a name="clear-asset-delivery-policy"></a>Leverings beleid voor assets wissen
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Leverings beleid voor assets maken
Met de volgende HTTP-aanvraag wordt een leverings beleid voor assets gemaakt waarmee wordt aangegeven dat er geen dynamische versleuteling moet worden toegepast en om de stroom te leveren in een van de volgende protocollen: MPEG DASH, HLS en Smooth Streaming protocollen. 

Voor informatie over welke waarden u kunt opgeven bij het maken van een AssetDeliveryPolicy, raadpleegt u de [typen die worden gebruikt bij het definiëren](#types) van de sectie AssetDeliveryPolicy.   

Aanvraag:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
Host: media.windows.net

{"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null}
```

Reactie:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 363
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 06:21:27 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null,
"Created":"2015-02-08T06:21:27.6908329Z",
"LastModified":"2015-02-08T06:21:27.6908329Z"}
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Asset koppelen met het leverings beleid voor assets
Met de volgende HTTP-aanvraag wordt het opgegeven activum gekoppeld aan het leverings beleid voor assets.

Aanvraag:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
Host: media.windows.net

{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}
```

Reactie:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Leverings beleid voor DynamicEnvelopeEncryption-activa
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Maak een inhouds sleutel van het type EnvelopeEncryption en koppel deze aan de Asset
Wanneer u DynamicEnvelopeEncryption-leverings beleid opgeeft, moet u ervoor zorgen dat u uw asset koppelt aan een inhouds sleutel van het type EnvelopeEncryption. Zie voor meer informatie: [een inhouds sleutel maken](media-services-rest-create-contentkey.md).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Bezorgings-URL ophalen
Haal de bezorgings-URL op voor de opgegeven leverings methode van de inhouds sleutel die u in de vorige stap hebt gemaakt. Een client gebruikt de geretourneerde URL om een AES-sleutel of een PlayReady-licentie aan te vragen om de beveiligde inhoud af te spelen.

Geef het type URL op dat moet worden opgehaald in de hoofd tekst van de HTTP-aanvraag. Als u uw inhoud beveiligt met PlayReady, vraagt u een Media Services PlayReady-URL voor het verkrijgen van een licentie met behulp van 1 voor de keyDeliveryType: {"keyDeliveryType": 1}. Als u uw inhoud beveiligt met de envelop versleuteling, vraagt u een URL voor sleutel aanschaf op door 2 op te geven voor keyDeliveryType: {"keyDeliveryType": 2}.

Aanvraag:

```console
POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
Content-Type: application/json
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
Host: media.windows.net
Content-Length: 21

{"keyDeliveryType":2}
```

Reactie:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 198
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
request-id: d26f65d2-fe65-4136-8fcf-31545be68377
x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 21:42:30 GMT

{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}
```


### <a name="create-asset-delivery-policy"></a>Leverings beleid voor assets maken
Met de volgende HTTP-aanvraag wordt de **AssetDeliveryPolicy** gemaakt die is geconfigureerd voor het Toep assen van dynamische envelop versleuteling (**DynamicEnvelopeEncryption**) op het **HLS** -Protocol (in dit voor beeld worden andere protocollen geblokkeerd voor streaming). 

Voor informatie over welke waarden u kunt opgeven bij het maken van een AssetDeliveryPolicy, raadpleegt u de [typen die worden gebruikt bij het definiëren](#types) van de sectie AssetDeliveryPolicy.   

Aanvraag:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}
```


Reactie:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 460
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 09 Feb 2015 05:24:38 GMT

{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}
```


### <a name="link-asset-with-asset-delivery-policy"></a>Asset koppelen met het leverings beleid voor assets
Zie [activa koppelen met leverings beleid voor assets](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Leverings beleid voor DynamicCommonEncryption-activa
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Maak een inhouds sleutel van het type CommonEncryption en koppel deze aan de Asset
Wanneer u DynamicCommonEncryption-leverings beleid opgeeft, moet u ervoor zorgen dat u uw asset koppelt aan een inhouds sleutel van het type CommonEncryption. Zie voor meer informatie: [een inhouds sleutel maken](media-services-rest-create-contentkey.md).

### <a name="get-delivery-url"></a>Bezorgings-URL ophalen
Haal de bezorgings-URL op voor de bezorgings methode PlayReady van de inhouds sleutel die u in de vorige stap hebt gemaakt. Een client gebruikt de geretourneerde URL om een PlayReady-licentie aan te vragen om de beveiligde inhoud af te spelen. Zie [Get Delivery URL](#get_delivery_url)voor meer informatie.

### <a name="create-asset-delivery-policy"></a>Leverings beleid voor assets maken
Met de volgende HTTP-aanvraag wordt de **AssetDeliveryPolicy** gemaakt die is geconfigureerd voor het Toep assen van dynamische common Encryption (**DynamicCommonEncryption**) op het **Smooth streaming** -Protocol (in dit voor beeld worden andere protocollen geblokkeerd voor streaming). 

Voor informatie over welke waarden u kunt opgeven bij het maken van een AssetDeliveryPolicy, raadpleegt u de [typen die worden gebruikt bij het definiëren](#types) van de sectie AssetDeliveryPolicy.   

Aanvraag:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}
```


Als u uw inhoud wilt beveiligen met behulp van Widevine DRM, werkt u de AssetDeliveryConfiguration-waarden bij om WidevineLicenseAcquisitionUrl (met de waarde 7) te gebruiken en geeft u de URL op van een licentie leverings service. U kunt de volgende AMS-partners gebruiken om Widevine-licenties te leveren: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [cast Labs](https://castlabs.com/company/partners/azure/).

Bijvoorbeeld: 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> Bij het versleutelen met Widevine zou u alleen kunnen leveren met een streepje. Zorg ervoor dat u het streepje (2) opgeeft in het protocol voor het leveren van assets.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Asset koppelen met het leverings beleid voor assets
Zie [activa koppelen met leverings beleid voor assets](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typen die worden gebruikt bij het definiëren van AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

In de volgende opsomming worden waarden beschreven die u kunt instellen voor het protocol voor het leveren van activa.

```csharp
[Flags]
public enum AssetDeliveryProtocol
{
    /// <summary>
    /// No protocols.
    /// </summary>
    None = 0x0,

    /// <summary>
    /// Smooth streaming protocol.
    /// </summary>
    SmoothStreaming = 0x1,

    /// <summary>
    /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
    /// </summary>
    Dash = 0x2,

    /// <summary>
    /// Apple HTTP Live Streaming protocol.
    /// </summary>
    HLS = 0x4,

    ProgressiveDownload = 0x10, 

    /// <summary>
    /// Include all protocols.
    /// </summary>
    All = 0xFFFF
}
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

In de volgende opsomming worden waarden beschreven die u kunt instellen voor het type Asset Delivery Policy.  

```csharp
public enum AssetDeliveryPolicyType
{
    /// <summary>
    /// Delivery Policy Type not set.  An invalid value.
    /// </summary>
    None,

    /// <summary>
    /// The Asset should not be delivered via this AssetDeliveryProtocol. 
    /// </summary>
    Blocked, 

    /// <summary>
    /// Do not apply dynamic encryption to the asset.
    /// </summary>
    /// 
    NoDynamicEncryption,  

    /// <summary>
    /// Apply Dynamic Envelope encryption.
    /// </summary>
    DynamicEnvelopeEncryption,

    /// <summary>
    /// Apply Dynamic Common encryption.
    /// </summary>
    DynamicCommonEncryption
    }
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

In de volgende opsomming worden waarden beschreven die u kunt gebruiken om de leverings methode van de inhouds sleutel te configureren voor de client.
    
```csharp
public enum ContentKeyDeliveryType
{
    /// <summary>
    /// None.
    ///
    </summary>
    None = 0,

    /// <summary>
    /// Use PlayReady License acquisition protocol
    ///
    </summary>
    PlayReadyLicense = 1,

    /// <summary>
    /// Use MPEG Baseline HTTP key protocol.
    ///
    </summary>
    BaselineHttp = 2,

    /// <summary>
    /// Use Widevine License acquisition protocol
    ///
    </summary>
    Widevine = 3

}
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

In de volgende opsomming worden waarden beschreven die u kunt instellen voor het configureren van sleutels die worden gebruikt voor het ophalen van specifieke configuratie voor een leverings beleid voor activa.

```csharp
public enum AssetDeliveryPolicyConfigurationKey
{
    /// <summary>
    /// No policies.
    /// </summary>
    None,

    /// <summary>
    /// Exact Envelope key URL.
    /// </summary>
    EnvelopeKeyAcquisitionUrl,

    /// <summary>
    /// Base key url that will have KID=<Guid> appended for Envelope.
    /// </summary>
    EnvelopeBaseKeyAcquisitionUrl,

    /// <summary>
    /// The initialization vector to use for envelope encryption in Base64 format.
    /// </summary>
    EnvelopeEncryptionIVAsBase64,

    /// <summary>
    /// The PlayReady License Acquisition Url to use for common encryption.
    /// </summary>
    PlayReadyLicenseAcquisitionUrl,

    /// <summary>
    /// The PlayReady Custom Attributes to add to the PlayReady Content Header
    /// </summary>
    PlayReadyCustomAttributes,

    /// <summary>
    /// The initialization vector to use for envelope encryption.
    /// </summary>
    EnvelopeEncryptionIV,

    /// <summary>
    /// Widevine DRM acquisition url
    /// </summary>
    WidevineLicenseAcquisitionUrl
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

