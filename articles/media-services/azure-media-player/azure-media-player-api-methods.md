---
title: Azure Media Player API-methoden
description: Met de Azure Media Player-API kunt u communiceren met de video via Java script, of de browser de video via HTML5-Video, Flash, Silverlight of andere ondersteunde afspeel technologieën speelt.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81727266"
---
# <a name="api"></a>API #

Met de Azure Media Player-API kunt u communiceren met de video via Java script, of de browser de video via HTML5-Video, Flash, Silverlight of andere ondersteunde afspeel technologieën speelt.

## <a name="referencing-the-player"></a>Verwijzen naar de speler ##

Als u de API-functies wilt gebruiken, moet u toegang hebben tot het Player-object. Gelukkig is eenvoudig te krijgen. U hoeft alleen maar te controleren of uw video code een ID heeft. De code voor het insluiten van voor beelden bevat een ID van `vid1` . Als u meerdere Video's op één pagina hebt, zorg er dan voor dat elke video code een unieke ID heeft.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Als de speler nog niet is geïnitialiseerd via het kenmerk voor de gegevens installatie of een andere methode, wordt de speler ook geïnitialiseerd.

## <a name="wait-until-the-player-is-ready"></a>Wacht tot de speler gereed is ##

De tijd die nodig is Azure Media Player voor het instellen van de video en API variëren, afhankelijk van de gebruikte afspeel technologie. HTML5 is vaak veel sneller te laden dan Flash of Silverlight. Daarom moet de functie Ready van de speler worden gebruikt voor het activeren van code waarvoor de API van de speler is vereist.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OF

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API-methoden ##

Nu u toegang hebt tot een kant-en-klare speler, kunt u de video beheren, waarden ophalen of reageren op video gebeurtenissen. De Azure Media Player-API-functie namen proberen de [HTML5-media-API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)te volgen. Het belangrijkste verschil is dat getter/setter-functies worden gebruikt voor video-eigenschappen.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registreren voor gebeurtenissen ##
Gebeurtenissen moeten rechtstreeks worden geregistreerd nadat de speler voor het eerst is geïnitialiseerd om ervoor te zorgen dat alle gebeurtenissen op de juiste manier worden gerapporteerd aan de toepassing en moeten worden uitgevoerd buiten de gebeurtenis ready.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Volgende stappen ##

<!---Some context for the following links goes here--->
- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)