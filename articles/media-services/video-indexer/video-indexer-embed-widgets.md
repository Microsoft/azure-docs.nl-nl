---
title: Video Indexer widgets insluiten in uw apps
titleSuffix: Azure Media Services
description: Meer informatie over het insluiten van Video Indexer widgets in uw apps.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: b13086e11e1181bba91a3255e68e9f8a32e78450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98797777"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Video Indexer widgets insluiten in uw apps

In dit artikel wordt uitgelegd hoe u Video Indexer widgets kunt insluiten in uw apps. Video Indexer ondersteunt het insluiten van drie typen widgets in uw apps: *Cognitieve inzichten*, *Speler* en *Editor*.

Vanaf versie 2 bevat de basis-URL van de widget de regio van het opgegeven account. Een account in de regio vs-West genereert bijvoorbeeld het volgende: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Typen widget

### <a name="cognitive-insights-widget"></a>Widget Inzichten

De widget Inzichten bevat alle visuele inzichten die tijdens het indexeringsproces zijn opgehaald uit uw video. De cognitieve Insights-widget ondersteunt de volgende optionele URL-para meters:

|Name|Definitie|Beschrijving|
|---|---|---|
|`widgets` | Tekenreeksen gescheiden door komma's | Hiermee kunt u de inzichten bepalen die u wilt weer geven.<br/>Voor beeld: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` alleen personen en tref woorden weer geven UI Insights.<br/>Beschik bare opties: personen, animatedCharacters, tref woorden, labels, gevoel, emoties, onderwerpen, keyframes, transcripten, OCR, sprekers, scènes en namedEntities.|
|`controls`|Tekenreeksen gescheiden door komma's|Hiermee kunt u de besturings elementen beheren die u wilt weer geven.<br/>Voor beeld: Hiermee wordt `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` alleen de zoek optie en de download knop weer gegeven.<br/>Beschik bare opties: zoeken, downloaden, voor instellingen en taal.|
|`language`|Een korte taal code (taal naam)|Programmeer taal voor besturings elementen.<br/>Voorbeeld: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>of `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Een korte taal code | Hiermee bepaalt u de taal van de gebruikers interface. De standaardwaarde is `en`. <br/>Bijvoorbeeld: `locale=de`.|
|`tab` | Het standaard geselecteerde tabblad | Hiermee bepaalt u het tabblad **inzichten** dat standaard wordt weer gegeven. <br/>Voor beeld: Hiermee wordt `tab=timeline` de inzichten weer gegeven op het tabblad **tijd lijn** geselecteerd.|
|`location` ||De `location` para meter moet worden opgenomen in de Inge sloten koppelingen. Zie [hoe u de naam van uw regio ophaalt](regions.md). Als uw account in preview is, `trial` moet het worden gebruikt voor de locatie waarde. `trial` is de standaard waarde voor de `location` para meter.| 

### <a name="player-widget"></a>Widget Speler

U kunt de Player-widget gebruiken om video te streamen met behulp van adaptieve bitsnelheid. De Player-widget ondersteunt de volgende optionele URL-para meters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`t` | Seconden vanaf het begin | Hiermee wordt het afspelen van de speler vanaf het opgegeven tijd punt gestart.<br/> Bijvoorbeeld: `t=60`. |
|`captions` | Een taal code | Haalt het bijschrift in de opgegeven taal op tijdens het laden van de widget zodat deze beschikbaar is in het menu **bijschriften** .<br/> Bijvoorbeeld: `captions=en-US`. |
|`showCaptions` | Een Booleaanse waarde | Zorgt ervoor dat speler wordt geladen met de ondertiteling al ingeschakeld.<br/> Bijvoorbeeld: `showCaptions=true`. |
|`type`| | Hiermee wordt een audio speler-weer gave geactiveerd (het video onderdeel wordt verwijderd).<br/> Bijvoorbeeld: `type=audio`. |
|`autoplay` | Een Booleaanse waarde | Hiermee wordt aangegeven of de speler de video moet starten wanneer deze wordt geladen. De standaardwaarde is `true`.<br/> Bijvoorbeeld: `autoplay=false`. |
|`language`/`locale` | Een taal code | Hiermee bepaalt u de taal van de speler. De standaardwaarde is `en-US`.<br/>Bijvoorbeeld: `language=de-DE`.|
|`location` ||De `location` para meter moet worden opgenomen in de Inge sloten koppelingen. Zie [hoe u de naam van uw regio ophaalt](regions.md). Als uw account in preview is, `trial` moet het worden gebruikt voor de locatie waarde. `trial` is de standaard waarde voor de `location` para meter.| 

### <a name="editor-widget"></a>Editor-widget

U kunt de editor-widget gebruiken om nieuwe projecten te maken en de inzichten van een video te beheren. De editor-widget ondersteunt de volgende optionele URL-para meters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`accessToken`<sup>*</sup> | Tekenreeks | Biedt toegang tot Video's die alleen voor komen in het account dat wordt gebruikt om de widget in te sluiten.<br> De editor-widget vereist de `accessToken` para meter. |
|`language` | Een taal code | Hiermee bepaalt u de taal van de speler. De standaardwaarde is `en-US`.<br/>Bijvoorbeeld: `language=de-DE`. |
|`locale` | Een korte taal code | Hiermee bepaalt u de inzichten taal. De standaardwaarde is `en`.<br/>Bijvoorbeeld: `language=de`. |
|`location` ||De `location` para meter moet worden opgenomen in de Inge sloten koppelingen. Zie [hoe u de naam van uw regio ophaalt](regions.md). Als uw account in preview is, `trial` moet het worden gebruikt voor de locatie waarde. `trial` is de standaard waarde voor de `location` para meter.| 

<sup>*</sup>De eigenaar moet `accessToken` voorzichtig zijn.

## <a name="embedding-videos"></a>Video's insluiten

In deze sectie wordt beschreven hoe u de open bare en persoonlijke inhoud insluit in apps.

De `location` para meter moet worden opgenomen in de Inge sloten koppelingen. Zie [hoe u de naam van uw regio ophaalt](regions.md). Als uw account in preview is, `trial` moet het worden gebruikt voor de locatie waarde. `trial` is de standaard waarde voor de `location` para meter. Bijvoorbeeld: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

> [!IMPORTANT]
> Het delen van een koppeling voor de **Media Player** of **Insights** -widget bevat het toegangs token en verleen de alleen-lezen machtigingen voor uw account.

### <a name="public-content"></a>Open bare inhoud

1. Meld u aan bij de [video indexer](https://www.videoindexer.ai/) -website.
1. Selecteer de video waarmee u wilt werken en druk op **Play**.
1. Selecteer het gewenste type widget (**cognitieve inzichten**, **speler** of **Redacteur**).
1. Klik op **&lt; / &gt; insluiten**.
5. Kopieer de insluit code (wordt weer gegeven in **de Inge sloten code kopiëren** in het dialoog venster **share & insluiten** ).
6. Voeg de code toe aan uw app.

### <a name="private-content"></a>Persoonlijke inhoud

Als u een privé video wilt insluiten, moet u een toegangs token in het `src` kenmerk van het iframe door geven:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Gebruik een van de volgende methoden om de inhoud van de cognitieve Insights-widget op te halen:

- De API voor het [verkrijgen van inzichten-widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- Het [token voor video toegang ophalen](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Voeg het als een query parameter toe aan de URL. Geef deze URL op als de `src` waarde voor de iframe, zoals eerder is weer gegeven.

Als u de mogelijkheden voor het bewerken van inzichten in uw Inge sloten object wilt bieden, moet u een toegangs token door geven dat bewerkings machtigingen bevat. Gebruik het [object Insights ophalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) of het [token video toegang ophalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) met `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interactie van widgets

De widget cognitieve Insights kan communiceren met een video in uw app. In deze sectie wordt beschreven hoe u deze interactie bereikt.

![Video Indexer van cognitieve Insights-widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Stroom overzicht

Wanneer u de transcripten bewerkt, treedt de volgende stroom op:

1. U bewerkt de transcriptie in de tijd lijn.
1. Video Indexer haalt deze updates op en slaat ze op in de [afschriften van transcripten](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) in het taal model.
1. De bijschriften worden bijgewerkt:

    * Als u de Windows Media Player-widget Video Indexer gebruikt, wordt deze automatisch bijgewerkt.
    * Als u een externe speler gebruikt, ontvangt u een nieuwe bijschriften bestand gebruiker de **ondertitelings gesprek video ophalen** .

### <a name="cross-origin-communications"></a>Communicatie van verschillende bronnen

Om Video Indexer widgets te laten communiceren met andere onderdelen, de Video Indexer-service:

- Maakt gebruik van de cross-Origin communicatie HTML5-methode `postMessage` .
- Het bericht wordt gevalideerd op basis van de bron VideoIndexer.ai.

Als u uw eigen Player-code implementeert en integreert met cognitieve Insights-widgets, is het uw verantwoordelijkheid om de oorsprong van het bericht te valideren dat afkomstig is van VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Widgets insluiten in uw app of blog (aanbevolen)

In deze sectie wordt uitgelegd hoe u de interactie tussen twee Video Indexer widgets kunt beheersen, zodat wanneer een gebruiker het Insight-besturings element in uw app selecteert, de speler naar het relevante moment springt.

1. Kopieer de invoegcode van de widget Speler.
2. Kopieer de invoegcode van de widget Inzichten.
3. Voeg het [Mediator-bestand](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) voor het afhandelen van de communicatie tussen de twee widgets toe:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Wanneer een gebruiker het Insight-besturings element in uw app selecteert, springt de speler naar het gewenste moment.

Zie voor meer informatie de [video indexer-beide widgets-demo insluiten](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>De widget Inzichten insluiten en Azure Media Player gebruiken om de inhoud af te spelen

In deze sectie wordt uitgelegd hoe u de interactie tussen een cognitieve Insights-widget en een Azure Media Player-exemplaar kunt verkrijgen met behulp van de [amp-invoeg toepassing](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Voeg een Video Indexer-invoeg toepassing toe voor de AMP-speler:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Exemplaar Azure Media Player met de Video Indexer-invoeg toepassing.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Kopieer de invoegcode van de widget Inzichten.

U kunt nu communiceren met Azure Media Player.

Zie de [demo over Azure Media Player en VI](https://codepen.io/videoindexer/pen/rYONrO)voor meer informatie.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>De Video Indexer cognitieve Insights-widget insluiten en een andere video speler gebruiken

Als u een andere video speler gebruikt dan Azure Media Player, moet u de video speler hand matig bewerken om de communicatie te verzorgen.

1. Voeg uw videospeler in.

    Bijvoorbeeld een standaard HTML5-speler:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Sluit de widget Inzichten in.
3. Implementeer communicatie voor de speler door te luisteren naar de gebeurtenis 'message'. Bijvoorbeeld:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Zie de [demo over Azure Media Player en VI](https://codepen.io/videoindexer/pen/YEyPLd)voor meer informatie.

## <a name="adding-subtitles"></a>Ondertiteling toevoegen

Als u Video Indexer Insights insluit met uw eigen [Azure Media Player](https://aka.ms/azuremediaplayer), kunt u de methode gebruiken om ondertiteling `GetVttUrl` (ondertiteling) te verkrijgen. U kunt ook een Java script-methode aanroepen vanuit de Video Indexer AMP-invoeg toepassing `getSubtitlesUrl` (zoals eerder weer gegeven).

## <a name="customizing-embeddable-widgets"></a>Insluitbare widgets aanpassen

### <a name="cognitive-insights-widget"></a>Widget Inzichten

U kunt kiezen welke typen inzichten u wilt. Als u dit wilt doen, geeft u deze op als waarde voor de volgende URL-para meter die wordt toegevoegd aan de Inge sloten code die u ontvangt (van de API of van de web-app): `&widgets=<list of wanted widgets>` .

De mogelijke waarden zijn: `people` , `animatedCharacters` , `keywords` , `labels` , `sentiments` , `emotions` , `topics` , `keyframes` , `transcript` ,,, `ocr` `speakers` `scenes` en `namedEntities` .

Als u bijvoorbeeld een widget wilt insluiten die alleen de inzichten van personen en tref woorden bevat, ziet de iframe-Inge sloten URL er als volgt uit:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

De titel van het iframe-venster kan ook worden aangepast door `&title=<YourTitle>` de URL van de iframe op te geven. (De HTML-waarde wordt aangepast `<title>` ).
   
Als u bijvoorbeeld het iframe-venster de titel ' MyInsights ' wilt geven, ziet de URL er als volgt uit:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

U ziet dat deze optie alleen relevant is voor gevallen waarbij u de inzichten in een nieuw venster moet openen.

### <a name="player-widget"></a>Widget Speler

Bij het insluiten van een Video Indexer-speler kunt u de grootte van de speler kiezen door de grootte van de iframe op te geven.

Bijvoorbeeld:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Video Indexer Player heeft standaard ondertiteling automatisch gegenereerd die is gebaseerd op de transcriptie van de video. De transcriptie wordt geëxtraheerd uit de video met de bron taal die is geselecteerd toen de video werd geüpload.

Als u wilt insluiten met een andere taal, kunt u toevoegen `&captions=<Language Code>` aan de URL van de insluitings speler. Als u wilt dat de bijschriften standaard worden weer gegeven, kunt u &showCaptions = True door geven.

De Inge sloten URL ziet er dan als volgt uit:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Zorgen

Standaard wordt de video afgespeeld met de speler. u kunt ervoor kiezen om niet door `&autoplay=false` te geven aan de voor gaande insluit-URL.

## <a name="code-samples"></a>Codevoorbeelden

Zie de [code voorbeelden](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) opslag plaats die voor beelden bevatten voor video INDEXER-API en widgets:

| Bestand/map                       | Beschrijving                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Video indexer video laden in een aangepaste Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Sluit VI Insights in met een aangepaste Azure Media Player.                             |
| `control-vi-embedded-player`      | Sluit VI Player en beheer deze van buiten.                                    |
| `custom-index-location`           | U kunt VI Insights insluiten vanaf een aangepaste externe locatie (dit kan een klant van een BLOB zijn).     |
| `embed-both-insights`             | Basis gebruik van VI Insights voor zowel spelers als inzichten.                            |
| `embed-insights-with-AMP`         | Sluit VI Insights-widget in met een aangepaste Azure Media Player.                      |
| `customize-the-widgets`           | Sluit VI-objecten met aangepaste opties.                                     |
| `embed-both-widgets`              | Sluit VI Player en inzichten toe en communiceer onderling.                      |
| `url-generator`                   | Hiermee wordt een aangepaste insluit-URL voor widgets gegenereerd op basis van door de gebruiker opgegeven opties.             |
| `html5-player`                    | Sluit VI Insights in met een standaard HTML5-Video speler.                           |

## <a name="supported-browsers"></a>Ondersteunde browsers

Zie [ondersteunde browsers](video-indexer-overview.md#supported-browsers)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [video indexer Insights weer geven en bewerken](video-indexer-view-edit.md)voor meer informatie over het weer geven en bewerken van video indexer inzichten.

Bekijk ook de [video indexer-CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
