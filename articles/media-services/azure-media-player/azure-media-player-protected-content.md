---
title: Beveiligde Azure Media Player-inhoud
description: Azure Media Player ondersteunt momenteel AES-128 bits envelop-versleuteling en normaal versleutelde inhoud.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 8c04a3847ce1005b530042926efb8fbbe75f9cfe
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448710"
---
# <a name="protected-content"></a>Beveiligde inhoud #

Azure Media Player ondersteunt momenteel AES-128 bits envelop-versleuteling en normaal versleutelde inhoud (va PlayReady en Widevine), of versleutelde inhoud via FairPlay. Als u beveiligde inhoud op de juiste manier wilt afspelen, moet u de `protectionInfo` opgeven in Azure Media Player. Deze informatie bestaat per bron en kan rechtstreeks aan het `<source>`-label worden toegevoegd via de `data-setup`.  U kunt de `protectionInfo` ook rechtstreeks als parameter toevoegen als u de bron dynamisch instelt.

`protectionInfo` accepteert een JSON-object en omvat:

- `type`: `AES` of `PlayReady` of `Widevine` of `FairPlay`
- `certificateUrl`: dit moet een directe koppeling zijn naar het gehoste FairPlay-certificaat

- `authenticationToken`: dit is een optieveld waarin u een niet-gecodeerd verificatietoken kunt toevoegen

> [!IMPORTANT]
> Het **certificateUrl**-object is alleen nodig voor FairPlay DRM.***
>[!NOTE]
> De standaard techOrder is specifiek voor de nieuwe tech-`html5FairPlayHLS` die specifiek is bedoeld voor het afspelen van FairPlay-inhoud op browsers die dit ondersteunen (Safari op OSX 8+). Als u FairPlay-inhoud wilt afspelen **EN** de standaard techOrder hebt gewijzigd in een aangepaste versie van uw toepassing, moet u deze nieuwe tech toevoegen aan uw techOrder-object. Het wordt aanbevolen deze op te nemen vóór silverlightSS, zodat uw inhoud niet wordt afgespeeld via PlayReady.

## <a name="code-sample"></a>Codevoorbeeld ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

of, met meervoudig DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Niet via alle browsers/platformen kan beveiligde inhoud worden afgespeeld. Zie de sectie [Afspeeltechnologie](azure-media-player-playback-technology.md) voor meer informatie over wat wordt ondersteund.
> [!IMPORTANT]
> Het token dat is doorgegeven aan de speler is bedoeld voor beveiligde inhoud en wordt alleen gebruikt voor geverifieerde gebruikers. Er wordt van uitgegaan dat voor de toepassing gebruik wordt gemaakt van SSL of een andere beveiligingsmaatregel. Tevens wordt ervan uitgegaan dat erop kan worden vertrouwd dat de eindgebruiker het token niet misbruikt. Als dat niet het geval is, moet u uw beveiligingsexperts betrekken.

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)