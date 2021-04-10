---
title: Azure Media Player fout codes
description: Een verwijzing naar een fout code voor Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 89f063ab50cfd880552e80a77ba798e4d06de5b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709577"
---
# <a name="error-codes"></a>Foutcodes #

Wanneer het afspelen niet kan worden gestart of gestopt, wordt een fout gebeurtenis geactiveerd en `error()` retourneert de functie een code en een optioneel bericht om de app-ontwikkelaar te helpen meer details te krijgen. `error().message` het bericht wordt niet weer gegeven voor de gebruiker.  Het bericht dat wordt weer gegeven aan de gebruiker is gebaseerd op `error().code` bits 27-20. Zie de tabel hieronder.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Fout codes, bits [31-28] (4 bits) ##

Beschrijf het gebied van de fout.

- 0 - Onbekend
- 1-AMP
- 2-AzureHtml5JS
- 3-knipperend
- 4-Silverlight
- 5-Html5
- 6-Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Fout codes, bits [27-0] (28 bits) ##

Geef een beschrijving van de fout, bits 27-20 biedt een hoog niveau, bits 19-0 meer details, indien beschikbaar.


| amp. error code. naam | Codes, bits [27-0] (28 bits) | Description |
|---|---:|---|
| **MEDIA_ERR_ABORTED-fouten bereik (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Algemene Afbrekings fout |
| abortedErrNotImplemented | 0x0100001 | Fout bij afbreken, niet geïmplementeerd |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Fout afbreken, gemengde inhoud wordt geblokkeerd-doorgaans gebeurt bij het laden `http://` van een stroom van een `https://` pagina |
| **MEDIA_ERR_NETWORK-fouten begin waarde (0x0200000-0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Algemene netwerk fout |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400-fout bericht |
| networkErrHttpUserAuthRequired | 0x0200191 | HTTP 401-fout bericht |
| networkErrHttpUserForbidden | 0x0200193 | HTTP 403-fout bericht |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404-fout bericht |
| networkErrHttpNotAllowed | 0x0200195 | Http 405-fout bericht |
| networkErrHttpGone | 0x020019A | Http 410-fout bericht |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412-fout bericht |
| networkErrHttpInternalServerFailure | 0x02001F4 | HTTP 500-fout bericht
| networkErrHttpBadGateway | 0x02001F6 | Http 502-fout bericht |
| networkErrHttpServiceUnavailable | 0x02001F7 | HTTP 503-fout bericht |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504-fout bericht |
| networkErrTimeout | 0x0200258 | Time-out voor netwerk
| networkErrErr | 0x0200259 | Fout bericht netwerk verbinding |
| **MEDIA_ERR_DECODE fouten (0x0300000-0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Algemene decodeer fout |
| **MEDIA_ERR_SRC_NOT_SUPPORTED fouten (0x0400000-0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Fout bericht algemene bron niet ondersteund |
| srcErrParsePresentation | 0x0400001 | Fout bij parseren van presentatie |
| srcErrParseSegment | 0x0400002 | Fout bij segment parser |
| srcErrUnsupportedPresentation | 0x0400003 | Presentatie niet ondersteund |
| srcErrInvalidSegment | 0x0400004 | Ongeldig segment |
| **MEDIA_ERR_ENCRYPTED-fouten begin waarde (0x0500000-0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Algemene versleutelde fout | 
| encryptErrDecrypterNotFound | 0x0500001 | Decrypter niet gevonden |
| encryptErrDecrypterInit | 0x0500002 | Decrypter-initialisatie fout |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter wordt niet ondersteund |
| encryptErrKeyAcquire | 0x0500004 | Ophalen van sleutel is mislukt |
| encryptErrDecryption | 0x0500005 | Ontsleuteling van segment is mislukt |
| encryptErrLicenseAcquire | 0x0500006 | Kan de licentie niet ophalen |
| **SRC_PLAYER_MISMATCH-fouten begin waarde (0x0600000-0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Algemeen geen overeenkomende tech Player voor het afspelen van de bron |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |De Flash-invoeg toepassing is niet geïnstalleerd. als deze is geïnstalleerd, kan de bron worden afgespeeld. *Of* Flash 30 is geïnstalleerd en het afspelen van AES-inhoud.  Als dit het geval is, probeert u een andere browser. Flash 30 wordt vandaag niet ondersteund vanaf 7 van juni. Zie [bekende problemen](azure-media-player-known-issues.md) voor meer informatie. Opmerking: als 0x00600003, worden Flash en Silverlight niet geïnstalleerd, indien opgegeven in de techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | De Silverlight-invoeg toepassing is niet geïnstalleerd. als de bron is geïnstalleerd, kan deze worden afgespeeld. Opmerking: als 0x00600003, worden Flash en Silverlight niet geïnstalleerd, indien opgegeven in de techOrder. |
| | 0x00600003 | Flash en Silverlight zijn niet geïnstalleerd als deze zijn opgegeven in de techOrder. |
| **Onbekende fouten (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Onbekende fouten |

## <a name="user-error-messages"></a>Fout berichten voor gebruikers ##

Het bericht van de gebruiker wordt weer gegeven op basis van de fout code bits 27-20.

- MEDIA_ERR_ABORTED (1)-"u hebt het afspelen van de video afgebroken"
- MEDIA_ERR_NETWORK (2)-' er is een netwerk fout opgetreden tijdens het downloaden van de video. '
- MEDIA_ERR_DECODE (3)-' het afspelen van de video is afgebroken vanwege een beschadigd probleem of omdat de video gebruikte functies uw browser niet ondersteunen. '
- MEDIA_ERR_SRC_NOT_SUPPORTED (4): de video kan niet worden geladen omdat de server of het netwerk is mislukt of omdat de indeling niet wordt ondersteund.
- MEDIA_ERR_ENCRYPTED (5)-' de video is versleuteld en we hebben niet de sleutel om deze te ontsleutelen. '
- SRC_PLAYER_MISMATCH (6)-er is geen compatibele bron gevonden voor deze video.
- MEDIA_ERR_UNKNOWN (0xFF): er is een onbekende fout opgetreden.

## <a name="examples"></a>Voorbeelden ##

### <a name="0x10600001"></a>0x10600001 ##

Er is geen compatibele bron gevonden voor deze video. wordt weer gegeven voor de eind gebruiker.

Er is geen technische speler die de aangevraagde bronnen kan afspelen, maar als Flash-invoeg toepassing is geïnstalleerd, is het waarschijnlijk dat een bron kan worden afgespeeld.  

### <a name="0x20200194"></a>0x20200194 ###

' Er is een netwerk fout opgetreden waardoor het downloaden van de video is mislukt. ' wordt weer gegeven voor de eind gebruiker.

AzureHtml5JS kan niet afspelen vanuit een HTTP 404-antwoord.

### <a name="categorizing-errors"></a>Fouten categoriseren ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Een specifieke fout opvangen ###

Met de volgende code worden slechts 404 fouten onderschept:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)