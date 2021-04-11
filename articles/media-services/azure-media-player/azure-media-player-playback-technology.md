---
title: Afspeeltechnologie van Azure Media Player
description: Meer informatie over de afspeeltechnologie die wordt gebruikt om de video of audio af te spelen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 0fadb679260582a788eb6408ff3761f00c2ceb1a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448821"
---
# <a name="playback-technology-tech"></a>Afspeeltechnologie ('tech') #

Afspeeltechnologie is de specifieke technologie in de browser of invoegtoepassing die wordt gebruikt om de video of audio af te spelen.

- **azureHtml5JS**: maakt gebruik van MSE- en EME-standaarden in combinatie met het video-element voor het invoegtoepassingsloos afspelen van inhoud met ondersteuning voor AES-128 bits met envelop versleutelde inhoud of algemene met DRM versleutelde inhoud (via PlayReady en Widevine als de browser dat ondersteunt) van Azure Media Services
- **flashSS**: maakt gebruik van Flash Player-technologie voor het afspelen van Smooth-inhoud met ondersteuning voor AES-128 bits envelop-ontsleuteling vanuit Azure Media Services: hiervoor is Flash versie 11.4 of hoger vereist
- **html5FairPlayHLS**: maakt gebruik van Safari-specifieke afspeeltechnologie in de browser via HLS met het video-element. Deze technologie is vereist voor het afspelen van met FairPlay beschermde inhoud van Azure Media Services en is toegevoegd aan de tech-order per 19 oktober 2016
- **Silverlight**: maakt gebruik van Silverlight-technologie om vloeiende inhoud af te spelen met ondersteuning voor met PlayReady beveiligde inhoud van Azure Media Services.
- **html5**: maakt gebruik van afspeeltechnologie in de browser met het video-element.  Op een Apple iOS- of Android-apparaat is het met deze technologie mogelijk om HLS-stromen af te spelen, met basisondersteuning voor AES-128 bits envelop-versleuteling of DRM-inhoud (via FairPlay als de browser dat ondersteunt).

## <a name="tech-order"></a>Tech-order ##

Om ervoor te zorgen dat uw asset kan worden afgespeeld op een groot aantal verschillende apparaten, wordt de volgende tech-order aanbevolen. Deze is de standaardwaarde als: `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` en kan rechtstreeks worden ingesteld op de `<video>` of programmatisch in de opties:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

of

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Compatibiliteitsmatrix ##

Gezien de aanbevolen tech-order met streaming-inhoud van Azure Media Services, wordt de volgende compatibiliteitsmatrix verwacht

| Browser        | OS                                                       | Verwachte technologie (Clear)  | Verwachte technologie (AES)  | Verwachte technologie (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10, Windows 8.1, Windows Phone 10<sup>1</sup>               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11          | Windows 7, Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | Silverlight (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| Edge           | Xbox One<sup>1</sup> (update van november 2015)                   | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| Chrome 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | Silverlight (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashSS                | flashSS              | Silverlight (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | html5 (no token)3    | niet ondersteund                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | flashSS                | flashSS              | Silverlight (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5 (no token)<sup>3</sup>    | niet ondersteund                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | niet ondersteund                |
| IE 8, IE 9, IE 10  | Windows                                                  | niet ondersteund          | niet ondersteund        | niet ondersteund                |

<sup>1</sup> Configuratie niet ondersteund of getest; weergegeven als referentie voor voltooiing.

<sup>2</sup> Het afspelen van video op Android-apparaten vereist een combinatie van apparaatmogelijkheden, grafische ondersteuning, codec-rendering, ondersteuning voor het besturingssysteem en meer. Omdat Android een open-source platform is dat telefoonfabrikanten de mogelijkheid biedt om het Vanilla Android-besturingssysteem te wijzigen dat door Google is geleverd, is er enige fragmentatie ontstaan in de Android-ruimte. Hierdoor is het mogelijk dat sommige apparaten niet worden ondersteund vanwege ontbrekende functies. Ook bieden niet alle Android-apparaten ondersteuning voor alle codecs.  

<sup>3</sup> In de gevallen waarin tokens niet wordt ondersteund, kan een proxy worden gebruikt om deze functionaliteit toe te voegen. Bekijk deze [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) voor meer informatie over deze oplossing.

> [!NOTE]
> Als de verwachte gekozen technologie vereist dat een invoegtoepassing wordt geïnstalleerd, zoals Flash, en die niet is geïnstalleerd op de computer van de gebruiker, zal AMP de mogelijkheden van de volgende technologie, in combinatie met brontypen en beveiligingsgegevens, in de lijst met technologieën doorlopend controleren. Als bijvoorbeeld wordt geprobeerd een niet-beveiligde stroom op aanvraag weer te geven in Safari 8 op OS X Yosemite, en Flash en Silverlight niet zijn geïnstalleerd, selecteert AMP de systeemeigen Html5-technologie voor het afspelen.<br/><br/>Er komen dagelijks nieuwe browsertechnologieën bij die van invloed kunnen zijn op deze matrix.

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)