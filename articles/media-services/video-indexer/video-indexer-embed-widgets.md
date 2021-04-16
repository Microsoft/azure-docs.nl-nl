---
title: Widgets Video Indexer insluiten in uw apps
titleSuffix: Azure Media Services
description: Meer informatie over het insluiten Video Indexer widgets in uw apps.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 822d50bca6bc1139e9b5f0554bcf9a56a8fcbd74
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532876"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Widgets Video Indexer insluiten in uw apps

In dit artikel wordt beschreven hoe u Video Indexer widgets in uw apps kunt insluiten. Video Indexer ondersteunt het insluiten van drie typen widgets in uw apps: *Cognitieve inzichten*, *Speler* en *Editor*.

Vanaf versie 2 bevat de basis-URL van de widget de regio van het opgegeven account. Een account in de regio VS - west genereert bijvoorbeeld: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Typen widget

### <a name="cognitive-insights-widget"></a>Widget Inzichten

De widget Inzichten bevat alle visuele inzichten die tijdens het indexeringsproces zijn opgehaald uit uw video. De widget Cognitive Insights ondersteunt de volgende optionele URL-parameters:

|Name|Definitie|Beschrijving|
|---|---|---|
|`widgets` | Tekenreeksen gescheiden door komma's | Hiermee kunt u de inzichten bepalen die u wilt renderen.<br/>Voorbeeld: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` geeft alleen inzichten over personen en trefwoorden weer in de gebruikersinterface.<br/>Beschikbare opties: people, animatedCharacters,keywords, labels, sentiments, emotions, topics, keyframes, transcript, ocr, speakers, scenes en namedEntities.|
|`controls`|Tekenreeksen gescheiden door komma's|Hiermee kunt u de besturingselementen bepalen die u wilt renderen.<br/>Voorbeeld: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` geeft alleen de zoekoptie en downloadknop weer.<br/>Beschikbare opties: zoeken, downloaden, voorinstellingen, taal.|
|`language`|Een korte taalcode (taalnaam)|Hiermee bepaalt u de inzichtentaal.<br/>Voorbeeld: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>of `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Een korte taalcode | Hiermee bepaalt u de taal van de gebruikersinterface. De standaardwaarde is `en`. <br/>Bijvoorbeeld: `locale=de`.|
|`tab` | Het standaard geselecteerde tabblad | Hiermee bepaalt **u het tabblad** Inzichten dat standaard wordt weergegeven. <br/>Voorbeeld: `tab=timeline` geeft de inzichten weer met het tabblad **Tijdlijn** geselecteerd.|
|`location` ||De `location` parameter moet worden opgenomen in de ingesloten koppelingen. Zie de naam van uw regio op te [halen.](regions.md) Als uw account zich in de preview-versie bevindt, `trial` moet worden gebruikt voor de locatiewaarde. `trial` is de standaardwaarde voor de `location` parameter .| 

### <a name="player-widget"></a>Widget Speler

U kunt de widget Speler gebruiken om video te streamen met behulp van adaptieve bitsnelheid. De widget Speler ondersteunt de volgende optionele URL-parameters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`t` | Seconden vanaf het begin | Zorgt ervoor dat de speler begint met afspelen vanaf het opgegeven tijdstip.<br/> Bijvoorbeeld: `t=60`. |
|`captions` | Een taalcode | Haalt het bijschrift op in de opgegeven taal tijdens  het laden van de widget om beschikbaar te zijn in het menu Bijschriften.<br/> Bijvoorbeeld: `captions=en-US`. |
|`showCaptions` | Een Booleaanse waarde | Zorgt ervoor dat speler wordt geladen met de ondertiteling al ingeschakeld.<br/> Bijvoorbeeld: `showCaptions=true`. |
|`type`| | Activeert de skin van een audiospeler (het videogedeelte wordt verwijderd).<br/> Bijvoorbeeld: `type=audio`. |
|`autoplay` | Een Booleaanse waarde | Geeft aan of de speler moet beginnen met het afspelen van de video wanneer deze wordt geladen. De standaardwaarde is `true`.<br/> Bijvoorbeeld: `autoplay=false`. |
|`language`/`locale` | Een taalcode | Hiermee bepaalt u de taal van de speler. De standaardwaarde is `en-US`.<br/>Bijvoorbeeld: `language=de-DE`.|
|`location` ||De `location` parameter moet worden opgenomen in de ingesloten koppelingen. Zie de naam van uw regio op te [halen.](regions.md) Als uw account zich in de preview-versie bevindt, `trial` moet worden gebruikt voor de locatiewaarde. `trial` is de standaardwaarde voor de `location` parameter .| 

### <a name="editor-widget"></a>Editorwidget

U kunt de widget Editor gebruiken om nieuwe projecten te maken en de inzichten van een video te beheren. De widget Editor ondersteunt de volgende optionele URL-parameters.

|Name|Definitie|Beschrijving|
|---|---|---|
|`accessToken`<sup>*</sup> | Tekenreeks | Biedt toegang tot video's die alleen in het account staan dat wordt gebruikt voor het insluiten van de widget.<br> Voor de widget Editor is de `accessToken` parameter vereist. |
|`language` | Een taalcode | Hiermee bepaalt u de taal van de speler. De standaardwaarde is `en-US`.<br/>Bijvoorbeeld: `language=de-DE`. |
|`locale` | Een korte taalcode | Hiermee bepaalt u de inzichtentaal. De standaardwaarde is `en`.<br/>Bijvoorbeeld: `language=de`. |
|`location` ||De `location` parameter moet worden opgenomen in de ingesloten koppelingen. Zie de naam van uw regio op te [halen.](regions.md) Als uw account zich in de preview-versie bevindt, `trial` moet worden gebruikt voor de locatiewaarde. `trial` is de standaardwaarde voor de `location` parameter .| 

<sup>*</sup>De eigenaar moet voorzichtig `accessToken` zijn.

## <a name="embed-videos"></a>Video's insluiten

In deze sectie wordt het insluiten van video's besproken met behulp van [de portal](#the-portal-experience) of door de URL handmatig samen [te stellen](#assemble-the-url-manually) in apps. 

De `location` parameter moet worden opgenomen in de ingesloten koppelingen. Zie de naam van uw regio op te [halen.](regions.md) Als uw account zich in de preview-versie bevindt, `trial` moet worden gebruikt voor de locatiewaarde. `trial` is de standaardwaarde voor de `location` parameter . Bijvoorbeeld: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

### <a name="the-portal-experience"></a>De portalervaring

Als u een video wilt insluiten, gebruikt u de portal zoals hieronder wordt beschreven:

1. Meld u [](https://www.videoindexer.ai/) aan bij de Video Indexer website.
1. Selecteer de video met wie u wilt werken en druk op **Afspelen.**
1. Selecteer het type widget dat u wilt (**Cognitive Insights,** **Player** of **Editor**).
1. Klik **&lt; / &gt; op Insluiten.**
5. Kopieer de insluitcode (wordt weergegeven in Het ingesloten **code** kopiëren in het **dialoogvenster & delen).**
6. Voeg de code toe aan uw app.

> [!NOTE]
> Het delen van een koppeling voor de **widget** **Speler** of Inzichten bevat het toegangsteken en verleent de alleen-lezen machtigingen voor uw account.

### <a name="assemble-the-url-manually"></a>De URL handmatig samenstellen

#### <a name="public-videos"></a>Openbare video's

U kunt openbare video's insluiten door de URL als volgt samen te stellen:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>Privévideo's

Als u een privévideo wilt insluiten, moet u een toegangs token doorgeven (gebruik [Toegangs token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) voor video's in het kenmerk `src` van het iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>Mogelijkheden voor het bewerken van inzichten bieden

Als u de mogelijkheden voor het bewerken van inzichten in uw ingesloten widget wilt bieden, moet u een toegangs token doorgeven dat bewerkingsmachtigingen bevat. Gebruik [Get Video Access Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) met `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interactie van widgets

De widget Cognitive Insights kan communiceren met een video in uw app. In deze sectie wordt beschreven hoe u deze interactie bereikt.

![Cognitive Insights widget Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Overzicht van Flow

Wanneer u de transcripties bewerkt, vindt de volgende stroom plaats:

1. U bewerkt de transcriptie in de tijdlijn.
1. Video Indexer ontvangt deze updates en slaat deze op in de [van transcriptbewerkingen](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) in het taalmodel.
1. De bijschriften worden bijgewerkt:

    * Als u de Video Indexer van uw apparaat gebruikt, wordt deze automatisch bijgewerkt.
    * Als u een externe speler gebruikt, krijgt u een nieuwe gebruiker van het bijschriftbestand de oproep **Videobijschriften** krijgen.

### <a name="cross-origin-communications"></a>Communicatie van verschillende bronnen

Als u de Video Indexer widgets wilt laten communiceren met andere onderdelen, gaat Video Indexer service:

- Maakt gebruik van de HTML5-methode voor cross-origin-communicatie. `postMessage`
- Het bericht wordt gevalideerd op basis van de bron VideoIndexer.ai.

Als u uw eigen spelercode implementeert en integreert met Cognitive Insights-widgets, is het uw verantwoordelijkheid om de oorsprong van het bericht te valideren dat afkomstig is van VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Widgets insluiten in uw app of blog (aanbevolen)

In deze sectie ziet u hoe u interactie tussen twee Video Indexer widgets bereikt, zodat de speler naar het relevante moment gaat wanneer een gebruiker het besturingselement inzicht in uw app selecteert.

1. Kopieer de invoegcode van de widget Speler.
2. Kopieer de invoegcode van de widget Inzichten.
3. Voeg het [Mediator-bestand](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) voor het afhandelen van de communicatie tussen de twee widgets toe:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Wanneer een gebruiker nu het besturingselement inzicht in uw app selecteert, gaat de speler naar het relevante moment.

Zie de demo voor Video Indexer [- Beide widgets insluiten voor meer informatie.](https://codepen.io/videoindexer/pen/NzJeOb)

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>De widget Inzichten insluiten en Azure Media Player gebruiken om de inhoud af te spelen

In deze sectie ziet u hoe u interactie tussen een Cognitive Insights-widget en een Azure Media Player-instantie bereikt met behulp van de [AMP-in plug-in](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Voeg een Video Indexer-invoeg voor de AMP-speler toe:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instantieer Azure Media Player met de Video Indexer-in plug-in.

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

Zie de demo Azure Media Player [+ VI Insights voor meer informatie.](https://codepen.io/videoindexer/pen/rYONrO)

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>De widget Video Indexer Cognitive Insights insluiten en een andere videospeler gebruiken

Als u een andere videospeler dan Azure Media Player, moet u de videospeler handmatig bewerken om de communicatie te bereiken.

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

Zie de demo Azure Media Player [+ VI Insights voor meer informatie.](https://codepen.io/videoindexer/pen/YEyPLd)

## <a name="adding-subtitles"></a>Ondertiteling toevoegen

Als u inzichten Video Indexer insluit met uw eigen [Azure Media Player,](https://aka.ms/azuremediaplayer)kunt u de methode gebruiken om `GetVttUrl` ondertiteling (ondertiteling) op te halen. U kunt ook een JavaScript-methode aanroepen vanuit Video Indexer AMP-in plug-in `getSubtitlesUrl` (zoals eerder weergegeven).

## <a name="customizing-embeddable-widgets"></a>Insluitbare widgets aanpassen

### <a name="cognitive-insights-widget"></a>Widget Inzichten

U kunt de typen inzichten kiezen die u wilt. U doet dit door ze op te geven als een waarde voor de volgende URL-parameter die wordt toegevoegd aan de insluitcode die u krijgt (van de API of van de web-app): `&widgets=<list of wanted widgets>` .

De mogelijke waarden zijn: `people` , , , , , , , , `animatedCharacters` , , `keywords` , , , `labels` en `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` `namedEntities` .

Als u bijvoorbeeld een widget wilt insluiten die alleen inzichten voor personen en trefwoorden bevat, ziet de insluit-URL van het iframe er als volgende uit:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

De titel van het iframe-venster kan ook worden aangepast door de URL van het `&title=<YourTitle>` iframe op te geven. (De HTML-waarde wordt `<title>` aangepast).
   
Als u uw iframe-venster bijvoorbeeld de titel 'MyInsights' wilt geven, ziet de URL er als volgende uit:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

U ziet dat deze optie alleen relevant is voor gevallen waarbij u de inzichten in een nieuw venster moet openen.

### <a name="player-widget"></a>Widget Speler

Bij het insluiten van een Video Indexer-speler kunt u de grootte van de speler kiezen door de grootte van de iframe op te geven.

Bijvoorbeeld:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Standaard heeft Video Indexer automatisch gegenereerde ondertiteling die zijn gebaseerd op de transcriptie van de video. De transcriptie wordt geëxtraheerd uit de video met de brontaal die is geselecteerd toen de video werd geüpload.

Als u wilt insluiten met een andere taal, kunt u toevoegen `&captions=<Language Code>` aan de URL van de insluitspeler. Als u wilt dat de bijschriften standaard worden weergegeven, kunt u &showCaptions=true.

De insluit-URL ziet er dan als volgende uit:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Automatisch afspelen

Standaard begint de speler met het afspelen van de video. U kunt ervoor kiezen om dit niet te doen door door `&autoplay=false` te geven aan de voorgaande insluit-URL.

## <a name="code-samples"></a>Codevoorbeelden

Zie [](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) de codevoorbeelden-repo die voorbeelden bevat voor Video Indexer API en Widgets:

| Bestand/map                       | Description                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Video-indexervideo laden in een aangepaste Azure Media Player.                        |
| `azure-media-player-vi-insights`  | VI Insights insluiten met een aangepaste Azure Media Player.                             |
| `control-vi-embedded-player`      | Sluit VI Player in en beheer deze van buiten.                                    |
| `custom-index-location`           | VI Insights insluiten vanaf een aangepaste externe locatie (kan een blob van de klant zijn).     |
| `embed-both-insights`             | Basisgebruik van VI Insights, zowel speler als inzichten.                            |
| `embed-insights-with-AMP`         | De widget VI Insights insluiten met een Azure Media Player.                      |
| `customize-the-widgets`           | Vi-widgets met aangepaste opties insluiten.                                     |
| `embed-both-widgets`              | Sluit VI Player en Insights in en communiceer ertussen.                      |
| `url-generator`                   | Genereert aangepaste insluit-URL voor widgets op basis van door de gebruiker opgegeven opties.             |
| `html5-player`                    | VI Insights insluiten met een standaard HTML5-videospeler.                           |

## <a name="supported-browsers"></a>Ondersteunde browsers

Zie ondersteunde [browsers voor meer informatie.](video-indexer-overview.md#supported-browsers)

## <a name="next-steps"></a>Volgende stappen

Zie View and edit Video Indexer insights (Inzichten weergeven Video Indexer bewerken) voor meer informatie over het weergeven en bewerken [Video Indexer inzichten.](video-indexer-view-edit.md)

Bekijk ook [Video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
