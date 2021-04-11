---
title: Het insluiten van een MPEG-DASH Adaptive streaming video in een HTML5-toepassing met DASH.js | Microsoft Docs
description: In dit onderwerp ziet u hoe u een MPEG-DASH Adaptive streaming video kunt insluiten in een HTML5-toepassing met DASH.js.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 820c10a2f5bb43ff931027954b7ecf10c5b4c59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013564"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Een adaptieve MPEG-DASH-videostream insluiten in een HTML5-toepassing met DASH.js

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

## <a name="overview"></a>Overzicht
MPEG-DASH is een ISO-standaard voor het adaptief streamen van video-inhoud, die grote voor delen biedt voor ontwikkel aars die een adaptieve video-streaming van hoge kwaliteit willen leveren. Met MPEG-DASH wordt de video stroom automatisch aangepast naar een lagere definitie wanneer het netwerk overbelast raakt. Dit verkleint de kans dat de viewer een ' onderbroken ' video ziet wanneer de speler de volgende paar seconden downloadt om af te spelen (dat wil zeggen, het vermindert de kans op buffering). Naarmate netwerk congestie vermindert, wordt de video speler weer omgezet in een stroom met hogere kwaliteit. Deze mogelijkheid om de vereiste band breedte aan te passen, resulteert ook in een snellere start tijd voor video. Dit betekent dat de eerste paar seconden kan worden afgespeeld in een snel-naar-down load-segment met lagere kwaliteit en vervolgens tot een hogere kwaliteit worden gebufferd wanneer voldoende inhoud is gefactureerd.

Dash.js is een open-source MPEG-DASH video speler geschreven in Java script. Het doel is om een robuuste, platformoverschrijdende speler te bieden die vrij kan worden hergebruikt in toepassingen waarvoor het afspelen van video vereist is. Het biedt MPEG-DASH Play in elke browser die ondersteuning biedt voor de MSE (Media Source Extensions) van W3C, dat nu Chrome, micro soft Edge en IE11 (andere browsers hebben aangegeven dat hun intentie ondersteuning biedt voor MSE). Voor meer informatie over DASH.js, JS raadpleegt u de opslag plaats GitHub dash.js.

## <a name="creating-a-browser-based-streaming-video-player"></a>Een op een browser gebaseerde video speler voor streaming maken
Als u een eenvoudige webpagina wilt maken waarin een video speler wordt weer gegeven met de verwachte besturings elementen zoals afspelen, onderbreken, terugspoelen enzovoort, moet u:

1. Een HTML-pagina maken
2. De video code toevoegen
3. De dash.js speler toevoegen
4. De speler initialiseren
5. Een bepaalde CSS-stijl toevoegen
6. De resultaten weer geven in een browser die de MSE implementeert

Het initialiseren van de speler kan in slechts een aantal regels java script-code worden uitgevoerd. Met dash.js is het heel eenvoudig om MPEG-DASH-video in te sluiten in uw op browser gebaseerde toepassingen.

## <a name="creating-the-html-page"></a>De HTML-pagina maken
De eerste stap is het maken van een standaard-HTML-pagina met het **video** -element, sla dit bestand op als basicPlayer.html, zoals in het volgende voor beeld wordt getoond:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>De DASH.js speler toevoegen
Als u de dash.js-referentie-implementatie wilt toevoegen aan de toepassing, moet u het dash.all.js-bestand uit de nieuwste versie van dash.js project halen. Dit moet worden opgeslagen in de Java script-map van uw toepassing. Dit bestand is een handig bestand dat alle benodigde dash.js code ophaalt in één bestand. Als u een kijkje hebt over de dash.js opslag plaats, vindt u de afzonderlijke bestanden, test code en nog veel meer, maar als u wilt dat alles dash.js gebruikt, is het dash.all.js bestand wat u nodig hebt.

Als u de dash.js speler wilt toevoegen aan uw toepassingen, voegt u een script code toe aan de sectie Head van basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Maak vervolgens een functie voor het initialiseren van de speler wanneer de pagina wordt geladen. Voeg het volgende script toe na de regel waarin u dash.all.js laadt:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Met deze functie maakt u eerst een DashContext. Dit wordt gebruikt om de toepassing te configureren voor een specifieke runtime-omgeving. Vanuit het oogpunt van technische controle definieert het de klassen die het afhankelijkheids injectie raamwerk moet gebruiken bij het maken van de toepassing. In de meeste gevallen gebruikt u streepje. di. DashContext.

Vervolgens maakt u een instantie van de primaire klasse van het dash.js Framework, Media Player. Deze klasse bevat de kern methoden zoals Play en PAUSE, beheert de relatie met het video-element en beheert ook de interpretatie van het bestand met de media Presentation Description (MPD), waarin de video wordt beschreven die moet worden afgespeeld.

De functie Startup () van de klasse Media Player wordt aangeroepen om ervoor te zorgen dat de speler klaar is voor het afspelen van video. De functie zorgt er onder andere voor dat alle benodigde klassen (zoals gedefinieerd door de context) zijn geladen. Als de speler klaar is, kunt u het video-element aan het object koppelen met behulp van de functie attachView (). Met de functie startup kan de Media Player de video stroom in het-element injecteren en zo nodig ook het afspelen regelen.

Geef de URL van het MPD-bestand door aan het Media Player, zodat het weet wat de video is die naar verwachting wordt afgespeeld. De functie setupVideo () die u zojuist hebt gemaakt, moet worden uitgevoerd zodra de pagina volledig is geladen. Dit doet u door de gebeurtenis OnLoad van het element body te gebruiken. Wijzig uw `<body>` element in:

```html
    <body onload="setupVideo()">
```

Stel tot slot de grootte van het video-element in met behulp van CSS. In een adaptieve streaming-omgeving is dit met name belang rijk, omdat de grootte van de video die wordt afgespeeld kan veranderen naarmate het afspelen wordt aangepast aan veranderende netwerk omstandigheden. In deze eenvoudige demo dwingt u het video-element gewoon te 80% van het beschik bare browser venster door de volgende CSS toe te voegen aan het hoofd gedeelte van de pagina:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Een video afspelen
Als u een video wilt afspelen, wijst u uw browser aan bij het bestand basicPlayback.html en klikt u op afspelen op de video speler die wordt weer gegeven.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook

[GitHub dash.js-opslag plaats](https://github.com/Dash-Industry-Forum/dash.js) 

