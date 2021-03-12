---
title: Leverings beleid voor assets configureren met .NET SDK | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u een ander beleid voor levering van assets configureert met Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 82e152f257e867dfdbf97c92b041ce57c5a1bdbc
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014261"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Leverings beleid voor assets configureren met .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Overzicht
Als u van plan bent om versleutelde assets te leveren, wordt een van de stappen in de werk stroom voor het leveren van inhoud van de Media Services het beleid voor de levering van assets geconfigureerd. Het beleid voor levering van activa vertelt Media Services hoe u wilt dat uw activa worden geleverd: in het streaming-protocol moet uw asset dynamisch worden verpakt (bijvoorbeeld MPEG DASH, HLS, Smooth Streaming of alle), ongeacht of u uw activa dynamisch wilt versleutelen en hoe (envelop of gemeen schappelijke versleuteling).

In dit artikel wordt beschreven waarom en hoe u beleid voor levering van assets maakt en configureert.

>[!NOTE]
>Wanneer uw AMS-account wordt gemaakt, wordt er een **standaard** streaming-eind punt toegevoegd aan uw account met de status **gestopt** . Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 
>
>Als u dynamische pakketten en dynamische versleuteling wilt gebruiken, moet uw asset bovendien een set adaptieve bitsnelheid Mp4's of een adaptieve bitrate Smooth Streaming-bestanden bevatten.

U kunt verschillende beleids regels Toep assen op hetzelfde activum. U kunt bijvoorbeeld PlayReady-versleuteling Toep assen op Smooth Streaming en AES-envelop versleuteling naar MPEG DASH en HLS. Alle protocollen die niet zijn gedefinieerd in een leveringsbeleid (u voegt bijvoorbeeld één beleid toe waarmee alleen HLS als protocol wordt opgegeven), worden voor streaming geblokkeerd. De uitzondering is als u helemaal geen leveringsbeleid voor assets hebt gedefinieerd. In dat geval is streaming voor alle protocollen toegestaan.

Als u een door opslag versleutelde Asset wilt leveren, moet u het leverings beleid van het activum configureren. Voordat uw asset kan worden gestreamd, verwijdert de streaming-server de opslag versleuteling en streamt uw inhoud met behulp van het opgegeven leverings beleid. Als u bijvoorbeeld uw asset versleuteld wilt leveren met de versleutelings sleutel voor de Advanced Encryption Standard (AES), stelt u het beleids type in op **DynamicEnvelopeEncryption**. Als u de opslag versleuteling wilt verwijderen en het activum in de heldere stroom wilt streamen, stelt u het beleids type in op **NoDynamicEncryption**. Voor beelden die laten zien hoe deze beleids typen moeten worden geconfigureerd, volgt.

Afhankelijk van hoe u het leverings beleid voor assets configureert, kunt u de volgende streaming-protocollen dynamisch verpakken, versleutelen en streamen: Smooth Streaming, HLS en MPEG DASH.

De volgende lijst bevat de indelingen die u gebruikt voor het streamen van vloeiende, HLS en streepje.

Smooth Streaming:

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest

HLS

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Overwegingen
* Voordat u de AssetDeliveryPolicy verwijdert, moet u alle streaming-Locators verwijderen die aan de Asset zijn gekoppeld. U kunt later, indien gewenst, nieuwe streaming-Locators maken met een nieuwe AssetDeliveryPolicy.
* Er kan geen streaming-Locator worden gemaakt op een door opslag versleutelde Asset wanneer er geen beleid voor levering van assets is ingesteld.  Als de Asset niet is versleuteld, kunt u met het systeem een Locator maken en de Asset streamen zonder activa leverings beleid.
* U kunt meerdere Asset Delivery policies koppelen aan één enkel activum, maar u kunt slechts één manier opgeven om een bepaalde AssetDeliveryProtocol te verwerken.  Dit betekent dat als u probeert twee leverings beleidsregels te koppelen die het AssetDeliveryProtocol. SmoothStreaming-protocol opgeven dat een fout veroorzaakt, omdat het systeem niet weet welke toepassing u wilt Toep assen wanneer een client een Smooth Streaming aanvraag doet.
* Als u een Asset met een bestaande streaming-Locator hebt, kunt u een nieuw beleid niet aan de Asset koppelen (u kunt een bestaand beleid ontkoppelen van de Asset of een bezorgings beleid bijwerken dat is gekoppeld aan de Asset).  Eerst moet u de streaming-Locator verwijderen, het beleid aanpassen en de streaming-Locator vervolgens opnieuw maken.  U kunt dezelfde locatorId gebruiken wanneer u de streaming-Locator opnieuw maakt, maar u moet er wel voor zorgen dat er geen problemen voor clients optreden omdat inhoud door de oorsprong of een downstream CDN kan worden opgeslagen in de cache.

## <a name="clear-asset-delivery-policy"></a>Leverings beleid voor assets wissen

Met de volgende **ConfigureClearAssetDeliveryPolicy** -methode wordt aangegeven dat u geen dynamische versleuteling wilt Toep assen en de stroom wilt leveren in een van de volgende protocollen: MPEG Dash, HLS en Smooth streaming protocollen. Mogelijk wilt u dit beleid Toep assen op uw door opslag versleutelde assets.

Voor informatie over welke waarden u kunt opgeven bij het maken van een AssetDeliveryPolicy, raadpleegt u de [typen die worden gebruikt bij het definiëren](#types) van de sectie AssetDeliveryPolicy.

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Leverings beleid voor DynamicCommonEncryption-activa

Met de volgende **CreateAssetDeliveryPolicy** -methode maakt u de **AssetDeliveryPolicy** die is geconfigureerd voor het Toep assen van dynamische common Encryption (**DynamicCommonEncryption**) op een Smooth streaming protocol (andere protocollen worden geblokkeerd voor streaming). De methode heeft twee para meters: **activa** (de Asset waarop u het leverings beleid wilt Toep assen) en **IContentKey** (de inhouds sleutel van het type **CommonEncryption** , zie: [een inhouds sleutel maken](media-services-dotnet-create-contentkey.md#common_contentkey)) voor meer informatie.

Voor informatie over welke waarden u kunt opgeven bij het maken van een AssetDeliveryPolicy, raadpleegt u de [typen die worden gebruikt bij het definiëren](#types) van de sectie AssetDeliveryPolicy.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Met Azure Media Services kunt u ook Widevine-versleuteling toevoegen. In het volgende voor beeld ziet u hoe PlayReady en Widevine worden toegevoegd aan het leverings beleid voor assets.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Bij het versleutelen met Widevine zou u alleen kunnen leveren met een streepje. Zorg ervoor dat u een streepje opgeeft in het protocol voor het leveren van activa.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Leverings beleid voor DynamicEnvelopeEncryption-activa
Met de volgende **CreateAssetDeliveryPolicy** -methode maakt u de **AssetDeliveryPolicy** die is geconfigureerd voor het Toep assen van dynamische envelop versleuteling (**DynamicEnvelopeEncryption**) op Smooth streaming-, HLS-en streepje-protocollen (als u geen protocollen opgeeft, worden ze geblokkeerd voor streaming). De methode heeft twee para meters: **activa** (de Asset waarop u het leverings beleid wilt Toep assen) en **IContentKey** (de inhouds sleutel van het type **EnvelopeEncryption** , zie: [een inhouds sleutel maken](media-services-dotnet-create-contentkey.md#envelope_contentkey)) voor meer informatie.

Voor informatie over welke waarden u kunt opgeven bij het maken van een AssetDeliveryPolicy, raadpleegt u de [typen die worden gebruikt bij het definiëren](#types) van de sectie AssetDeliveryPolicy.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typen die worden gebruikt bij het definiëren van AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

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
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

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
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

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
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

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

