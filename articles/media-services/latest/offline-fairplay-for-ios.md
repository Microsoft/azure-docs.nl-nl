---
title: Media Services v3 offline FairPlay streaming voor iOS
description: Dit onderwerp bevat een overzicht en laat zien hoe u Azure Media Services v3 kunt gebruiken om uw HTTP Live Streaming-inhoud (HLS) dynamisch te versleutelen met Apple FairPlay in de offline modus.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0412ef303a301873d437e03e91c58c0a2e8e7e15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640728"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline FairPlay streaming voor iOS met Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 Azure Media Services biedt een aantal goed ontworpen [Content Protection-Services](https://azure.microsoft.com/services/media-services/content-protection/) die betrekking hebben op:

- Micro soft PlayReady
- Google Widevine
    
    Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.
- Apple FairPlay
- AES-128-versleuteling

Digital Rights Management (DRM)/Advanced Encryption Standard (AES) versleuteling van inhoud wordt dynamisch uitgevoerd op aanvraag voor verschillende streaming-protocollen. De bezorgings services van DRM-licentie/AES-ontsleuteling worden ook geleverd door Media Services.

Naast het beveiligen van inhoud voor online streaming via verschillende streaming-protocollen, is de offline modus voor beveiligde inhoud ook een regel matig aangevraagde functie. Ondersteuning voor offline modus is vereist voor de volgende scenario's:

* Afspelen als Internet verbinding niet beschikbaar is, zoals tijdens het reizen.
* Sommige inhouds providers kunnen geen DRM-licentie levering toestaan buiten de grens van een land/regio. Als gebruikers inhoud willen bekijken terwijl ze zich buiten het land of de regio bevinden, is offline downloaden vereist.
* In sommige landen/regio's is Internet Beschik baarheid en/of band breedte nog steeds beperkt. Gebruikers kunnen ervoor kiezen om eerst te downloaden om inhoud te kunnen bekijken in een oplossing die hoog genoeg is voor een goede kijk ervaring. In dit geval is het probleem doorgaans niet de beschik baarheid van het netwerk, maar beperkte netwerk bandbreedte. Over-the-top (OTT)/online video platform (OVP)-providers vragen om ondersteuning van offline modus.

In dit artikel wordt Inge gaan op de ondersteuning voor offline modus van FairPlay streaming (FPS) die is gericht op apparaten met iOS 10 of hoger. Deze functie wordt niet ondersteund voor andere Apple-platformen, zoals watchOS, tvOS of Safari op macOS.

> [!NOTE]
> Offline DRM wordt alleen gefactureerd voor het maken van één aanvraag voor een licentie wanneer u de inhoud downloadt. Er worden geen fouten in rekening gebracht.

## <a name="prerequisites"></a>Vereisten

Voordat u offline DRM implementeert voor FairPlay op een iOS 10 +-apparaat:

* Online inhouds beveiliging controleren voor FairPlay: 

    - [Vereisten voor en configuratie van Apple FairPlay-licenties](fairplay-license-overview.md)
    - [De Digital Rights Management-service gebruiken voor dynamische versleuteling en licentielevering](protect-with-drm.md)
    - Een .NET-voor beeld met configuratie van online FPS streaming: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Haal de FPS-SDK op uit het Apple Developer Network. De FPS-SDK bevat twee onderdelen:

    - De server-SDK van FPS, die de sleutel Security module (KSM), client voorbeelden, een specificatie en een set test vectoren bevat.
    - Het FPS-implementatie pakket, dat de D-functie specificatie bevat, evenals instructies over het genereren van het FPS-certificaat, de klantspecifieke persoonlijke sleutel en de geheime sleutel van de toepassing. Apple geeft het FPS-implementatie pakket alleen voor de gelicentieerde inhouds providers.
* Kloon https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git . 

    U moet de code in [versleutelen met DRM wijzigen met behulp van .net](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) om Fairplay-configuraties toe te voegen.  

## <a name="configure-content-protection-in-azure-media-services"></a>Beveiliging van inhoud configureren in Azure Media Services

Ga als volgt te werk in de methode [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) :

Verwijder de opmerking over de code die de FairPlay-beleids optie configureert:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Verwijder ook de opmerking over de code waarmee CBCS ContentKeyPolicyOption wordt toegevoegd aan de lijst met ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Offline modus inschakelen

Als u de offline modus wilt inschakelen, maakt u een aangepaste StreamingPolicy en gebruikt u de naam bij het maken van een StreamingLocator in [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

Uw Media Services-account is nu geconfigureerd voor het leveren van offline FairPlay-licenties.

## <a name="sample-ios-player"></a>Voor beeld iOS-Player

Ondersteuning voor de offline modus van FPS is alleen beschikbaar op iOS 10 en hoger. De FPS Server SDK (versie 3,0 of hoger) bevat het document en het voor beeld voor de offline modus FPS. Met name de server-SDK (versie 3,0 of hoger) van FPS bevat de volgende twee items die betrekking hebben op de offline modus:

* Document: ' offline afspelen met FairPlay streaming en HTTP Live Streaming. ' Apple, 14 september 2016. In FPS Server SDK versie 4,0 wordt dit document samengevoegd in het hoofd-FPS-document.
* Voorbeeld code: HLSCatalog-voor beeld (onderdeel van de bps Server SDK van Apple) voor de offline modus van FPS in de \FairPlay streaming server SDK-versie 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. In de HLSCatalog-voor beeld-app worden de volgende code bestanden gebruikt om de functies van de offline modus te implementeren:

    - AssetPersistenceManager. Swift-code bestand: AssetPersistenceManager is de belangrijkste klasse in dit voor beeld waarin wordt getoond hoe:

        - Het downloaden van HLS-streams beheren, zoals de Api's die worden gebruikt om down loads te starten en te annuleren en om bestaande activa van apparaten te verwijderen.
        - De voortgang van de down load bewaken.
    - AssetListTableViewController. Swift en AssetListTableViewCell. Swift code files: AssetListTableViewController is de belangrijkste interface van dit voor beeld. Het bevat een lijst met assets die het voor beeld kan gebruiken voor het afspelen, downloaden, verwijderen of annuleren van een down load. 

Deze stappen laten zien hoe u een actieve iOS-speler kunt instellen. Als u begint met het HLSCatalog-voor beeld in FPS Server SDK version 4.0.1, moet u de volgende code wijzigingen aanbrengen:

Implementeer de-methode in HLSCatalog\Shared\Managers\ContentKeyDelegate.swift `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` met behulp van de volgende code. Stel ' drmUr ' een variabele toe die is toegewezen aan de HLS-URL.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Implementeer de-methode in HLSCatalog\Shared\Managers\ContentKeyDelegate.swift `requestApplicationCertificate()` . Deze implementatie is afhankelijk van het feit of u het certificaat (alleen open bare sleutel) insluit met het apparaat of als host van het certificaat op het web. De volgende implementatie maakt gebruik van het certificaat van de gehoste toepassing die wordt gebruikt in de test voorbeelden. Laat ' certUrl ' een variabele zijn die de URL van het toepassings certificaat bevat.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Voor de laatste geïntegreerde test worden zowel de video-URL als de URL van het toepassings certificaat weer gegeven in de sectie ' geïntegreerde test '.

Voeg in HLSCatalog\Shared\Resources\Streams.plist uw test video-URL toe. Gebruik voor de inhouds sleutel-ID de FairPlay-URL voor het verkrijgen van licenties met het SKD-protocol als unieke waarde.

![Offline FairPlay iOS-app-streams](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Gebruik uw eigen test video-URL, FairPlay licentie verwervings-URL en de URL van het toepassings certificaat als u deze hebt ingesteld. Of u kunt door gaan met de volgende sectie, die test voorbeelden bevat.

## <a name="integrated-test"></a>Geïntegreerde test

Drie test voorbeelden in Media Services hebben betrekking op de volgende drie scenario's:

* Met FPS beveiligde, met video, audio en alternatief audio spoor
* Beveiligt FPS, met video en audio, maar geen alternatief audio spoor
* Met FPS beveiligd, met alleen video en zonder audio

U kunt deze voor beelden vinden op [deze demo site](https://aka.ms/poc#22), met het bijbehorende toepassings certificaat dat wordt gehost in een Azure-web-app.
Met versie 3 of versie 4 van de FPS Server-SDK, als een Master-afspeel lijst alternatieve audio bevat, wordt tijdens de offline modus alleen audio afgespeeld. Daarom moet u de alternatieve audio verwijderen. Met andere woorden, de tweede en derde steek proef die eerder in de online-en offline modus werkt. Het voor beeld dat in de lijst wordt weer gegeven, speelt alleen geluid af tijdens de offline modus, terwijl online streaming goed werkt.

## <a name="offline-fairplay-questions"></a>Vragen over offline Fairplay

Zie [vragen over offline Fairplay](questions-collection.md#why-does-only-audio-play-but-not-video-during-offline-mode).
