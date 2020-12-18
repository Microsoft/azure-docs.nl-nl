---
title: Gebruik AES-128 Dynamic Encryption en de key delivery service | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u dynamisch versleutelen met AES-128 en de key delivery-service gebruikt.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a0e2f785bb3c097b7d2918198457a3f7baf6a39c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96023445"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Dynamische AES-128-versleuteling en de sleutelleveringsservice gebruiken

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

U kunt Media Services gebruiken om HTTP Live Streaming (HLS) te leveren en Smooth Streaming versleuteld met de AES door gebruik te maken van 128-bits coderings sleutels. Media Services biedt ook de sleutelleveringsservice die versleutelingssleutels levert aan gemachtigde gebruikers. Als u wilt dat Media Services een Asset versleutelt, koppelt u een versleutelings sleutel aan de Asset en configureert u ook autorisatie beleid voor de sleutel. Wanneer een stroom wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met behulp van AES-versleuteling. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. Om te bepalen of de gebruiker gemachtigd is om de sleutel op te halen, evalueert de service het autorisatie beleid dat u hebt opgegeven voor de sleutel.

Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutels aanvragen. Het autorisatiebeleid voor inhoudssleutels kan een of meer autorisatiebeperkingen hebben: beperking voor openen of tokenbeperkingen. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de indelingen [Simple Web Tokens](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) en [JSON Web Token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT). Zie [Het autorisatiebeleid voor inhoudssleutels configureren](media-services-protect-with-aes128.md#configure_key_auth_policy) voor meer informatie.

Als u dynamische versleuteling wilt gebruiken, moet u een asset hebben die een set multi-bitrate MP4-bestanden of multi-bitrate Smooth Streaming-bronbestanden bevat. U moet ook het leverings beleid voor de Asset configureren (verderop in dit artikel wordt beschreven). Vervolgens zorgt de server voor streaming on demand er op basis van de indeling die is opgegeven in de streaming-URL voor dat de stream wordt geleverd in het protocol dat u hebt geselecteerd. Als gevolg hiervan moet u alleen voor de bestanden in één opslag indeling opslaan en betalen. Media Services bouwt en verwerkt het juiste antwoord op basis van aanvragen van een client.

Dit artikel is nuttig voor ontwikkel aars die werken aan toepassingen die beveiligde media leveren. In dit artikel leest u hoe u de service voor sleutel levering configureert met autorisatie beleid zodat alleen geautoriseerde clients versleutelings sleutels kunnen ontvangen. Ook wordt uitgelegd hoe u dynamische versleuteling kunt gebruiken.

Zie [dit blog bericht](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)voor meer informatie over het versleutelen van inhoud met de Advanced Encryption Standard (AES) voor levering aan Safari op macOS.
Zie [deze video](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)voor een overzicht van het beveiligen van uw media-inhoud met AES-versleuteling.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128-werk stroom voor dynamische versleuteling en key delivery service

Voer de volgende algemene stappen uit wanneer u uw assets versleutelt met AES door gebruik te maken van de Media Services key delivery service en ook met behulp van dynamische versleuteling:

1. [Maak een Asset en upload bestanden in de Asset](media-services-protect-with-aes128.md#create_asset).

2. [Codeer de Asset die het bestand bevat naar de Adaptive bitrate MP4-set](media-services-protect-with-aes128.md#encode_asset).

3. [Maak een inhouds sleutel en koppel deze aan de gecodeerde Asset](media-services-protect-with-aes128.md#create_contentkey). De inhoudssleutel bevat in Media Services de versleutelingssleutel van de asset.

4. [Configureer het autorisatie beleid voor de inhouds sleutel](media-services-protect-with-aes128.md#configure_key_auth_policy). U moet het autorisatiebeleid voor de inhoudssleutel configureren. De client moet voldoen aan het beleid voordat de inhoudssleutel aan de client wordt geleverd.

5. [Configureer het leverings beleid voor een Asset](media-services-protect-with-aes128.md#configure_asset_delivery_policy). De configuratie van het leverings beleid bevat de URL voor sleutel aanschaf en een initialisatie vector (IV). (AES-128 vereist dezelfde IV voor versleuteling en ontsleuteling.) De configuratie omvat ook het leverings Protocol (bijvoorbeeld MPEG-DASH, HLS, Smooth Streaming of alle) en het type dynamische versleuteling (bijvoorbeeld een envelop of geen dynamische versleuteling).

    U kunt voor dezelfde asset een ander beleid voor elk protocol toepassen. U kunt bijvoorbeeld PlayReady-versleuteling toepassen op Smooth/DASH en een AES Envelope op HLS. Alle protocollen die niet zijn gedefinieerd in een leverings beleid, worden geblokkeerd voor het streamen van gegevens. (Een voor beeld hiervan is als u één beleid toevoegt waarmee alleen HLS als protocol worden opgegeven.) De uitzonde ring hierop is als u geen leverings beleid voor assets hebt gedefinieerd. In dat geval is streaming voor alle protocollen toegestaan.

6. [Maak een OnDemand-Locator](media-services-protect-with-aes128.md#create_locator) om een streaming-URL op te halen.

In dit artikel wordt ook [beschreven hoe een client toepassing een sleutel kan aanvragen van de key delivery service](media-services-protect-with-aes128.md#client_request).

Aan het einde van het artikel vindt u een volledig [.net-voor beeld](media-services-protect-with-aes128.md#example) .

De volgende afbeelding geeft een illustratie van de eerder beschreven werkstroom. Hier wordt de token voor verificatie gebruikt.

![Beveiligen met AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

De rest van dit artikel bevat uitleg, code voorbeelden en koppelingen naar onderwerpen waarin wordt uitgelegd hoe u de eerder beschreven taken kunt uitvoeren.

## <a name="current-limitations"></a>Huidige beperkingen
Als u het leveringsbeleid voor uw asset toevoegt of bijwerkt, moet u een eventuele locator verwijderen en een nieuwe locator maken.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Een Asset maken en bestanden uploaden naar de Asset
Als u uw video's wilt beheren, coderen en streamen, moet u eerst uw inhoud uploaden naar Media Services. Wanneer uw inhoud is geüpload, wordt deze veilig opgeslagen in de cloud voor verdere verwerking en streaming. 

Zie [Upload Files into a Media Services account](media-services-dotnet-upload-files.md) (Bestanden uploaden naar een Media Services-account) voor gedetailleerde informatie.

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>De asset die het bestand bevat coderen naar de Adaptive Bitrate MP4-set
Bij dynamische versleuteling maakt u een asset die een set multi-bitrate MP4-bestanden of multi-bitrate Smooth Streaming-bronbestanden bevat. Op basis van de opgegeven indeling in het manifest of de fragment aanvraag zorgt de server voor streaming op aanvraag ervoor dat u de stream ontvangt in het protocol dat u hebt geselecteerd. Vervolgens hoeft u de bestanden niet in één opslag indeling op te slaan en te betalen. Media Services bouwt en verwerkt het juiste antwoord op basis van aanvragen van een client. Zie [Dynamic packaging overview](media-services-dynamic-packaging-overview.md) (Overzicht van dynamische pakketten).

>[!NOTE]
>Wanneer uw Media Services-account is gemaakt, wordt er een standaard streaming-eindpunt met de status Gestopt aan uw account toegevoegd. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status Wordt uitgevoerd hebben. 
>
>Als u dynamische pakketten en dynamische versleuteling wilt gebruiken, moet uw asset bovendien een set adaptieve bitsnelheid Mp4's of een adaptieve bitrate Smooth Streaming-bestanden bevatten.

Zie [Encode an asset by using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Een asset coderen met Media Encoder Standard) voor instructies voor het coderen.

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Een inhouds sleutel maken en deze koppelen aan de gecodeerde Asset
In Media Services bevat de inhoudssleutel de sleutel waarmee u een asset wilt coderen.

Zie [Create content key](media-services-dotnet-create-contentkey.md) (Inhoudssleutels maken) voor meer informatie.

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Het autorisatie beleid voor de inhouds sleutel configureren
Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutels aanvragen. U moet het autorisatiebeleid voor de inhoudssleutel configureren. De-client (Player) moet voldoen aan het beleid voordat de sleutel aan de client kan worden geleverd. Het autorisatie beleid voor inhouds sleutels kan een of meer autorisatie beperkingen hebben: open, token beperking of IP-beperking.

Zie [Een autorisatiebeleid voor inhoudssleutels configureren](media-services-dotnet-configure-content-key-auth-policy.md) voor meer informatie.

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Een leveringsbeleid voor assets configureren
Configureer het leveringsbeleid voor uw asset. De configuratie van het leveringsbeleid voor assets omvat onder andere het volgende:

* De URL van de sleutel aanschaft. 
* De initialisatie vector (IV) die moet worden gebruikt voor de versleuteling van enveloppen. Voor AES-128 is dezelfde IV vereist voor versleuteling en ontsleuteling. 
* Het protocol voor het leveren van assets (bijvoorbeeld MPEG-DASH, HLS, Smooth Streaming of alle).
* Het type dynamische versleuteling (bijvoorbeeld AES-envelop) of geen dynamische versleuteling. 

Zie [Een leveringsbeleid voor assets configureren](media-services-dotnet-configure-asset-delivery-policy.md) voor meer informatie.

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Een OnDemand-streaminglocator maken om een streaming-URL te verkrijgen
U moet de gebruiker voorzien van de streaming-URL voor Smooth Streaming, DASH of HLS.

> [!NOTE]
> Als u het leveringsbeleid voor uw asset toevoegt of bijwerkt, moet u een eventuele locator verwijderen en een nieuwe locator maken.
> 
> 

Zie [Build a streaming URL](media-services-deliver-streaming-content.md) (Een streaming-URL bouwen) voor instructies over het publiceren van een asset en bouwen van een streaming-URL.

## <a name="get-a-test-token"></a>Een test-token ophalen
Haal op basis van de tokenbeperking een test-token op die is gebruikt voor het sleutelautorisatiebeleid.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

U kunt de [Azure Media Services Player](https://aka.ms/azuremediaplayer) gebruiken om uw stream te testen.

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Hoe kan uw client een sleutel aanvragen bij de key delivery service?
In de vorige stap hebt u de URL die naar een manifest bestand verwijst, samengesteld. Uw client moet de benodigde gegevens uit de streaming-manifest bestanden ophalen om een aanvraag voor de key delivery-service te maken.

### <a name="manifest-files"></a>Manifest bestanden
De client moet de URL (die ook inhoud sleutel-ID [Kid] bevat) uit het manifest bestand extra heren. De client probeert vervolgens de versleutelings sleutel op te halen van de key delivery service. De client moet ook de waarde voor de IV extra heren en gebruiken om de stroom te ontsleutelen. Het volgende code fragment toont het `<Protection>` element van het Smooth streaming-manifest:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

In het geval van HLS wordt het basis manifest onderverdeeld in segment bestanden. 

Het basis manifest is bijvoorbeeld: http://test001.Origin.mediaservices.Windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL). Het bevat een lijst met de bestands namen van het segment.

```text
. . . 
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
…
```

Als u een van de segment bestanden in een tekst editor opent (bijvoorbeeld http://test001.Origin.mediaservices.Windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ISM/QualityLevels (514369)/manifest (video, Format = M3U8-AAPL), bevat deze een #EXT-X-sleutel. Dit geeft aan dat het bestand is versleuteld.

```text
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:9
#EXT-X-KEY:METHOD=AES-128,
URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
        kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
        IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:8.425708,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXT-X-ENDLIST
```

>[!NOTE] 
>Als u van plan bent een AES-versleutelde HLS te spelen in Safari, raadpleegt u [deze blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>De sleutel van de key delivery-service aanvragen

De volgende code laat zien hoe u een aanvraag verzendt naar de Media Services key delivery service met behulp van een key delivery-URI (die is geëxtraheerd uit het manifest) en een token. (In dit artikel wordt niet uitgelegd hoe u SWTs van een STS kunt ophalen.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Bescherm uw inhoud met AES-128 met behulp van .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

1. Stel uw ontwikkelomgeving in en vul in het bestand app.config de verbindingsinformatie in, zoals beschreven in [Media Services ontwikkelen met .NET](media-services-dotnet-how-to-use.md).

2. Voeg de volgende elementen toe aan appSettings, zoals gedefinieerd in uw app.config-bestand:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Voorbeeld

Overschrijf de code in uw Program.cs-bestand met de code die wordt weergegeven in deze sectie.
 
>[!NOTE]
>Er geldt een limiet van 1.000.000 beleids regels voor verschillende Media Services-beleids regels (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-ID als u altijd dezelfde dagen/toegangs machtigingen gebruikt. Een voorbeeld is een beleid voor locators die zijn bedoeld om aanwezig te blijven gedurende een lange periode (niet-uploadbeleidsregels). Zie de sectie toegangs beleid beperken in [assets en gerelateerde entiteiten beheren met de Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies)voor meer informatie.

Zorg ervoor dat variabelen zo worden bijgewerkt dat ze verwijzen naar de mappen waar uw invoerbestanden zich bevinden.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
