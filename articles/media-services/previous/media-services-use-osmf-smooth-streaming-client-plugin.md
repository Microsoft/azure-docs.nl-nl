---
title: Smooth Streaming-invoeg toepassing voor het open source-media Framework
description: Meer informatie over het gebruik van de Azure Media Services Smooth Streaming-invoeg toepassing voor het Adobe Open source-media Framework.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 70042a5c4c27ec5d163e5f05089ae50b92140c28
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011252"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>De micro soft Smooth Streaming-invoeg toepassing voor het Adobe Open source-media Framework gebruiken

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht
De micro soft Smooth Streaming-invoeg toepassing voor open source media Framework 2,0 (SS voor OSMF) breidt de standaard mogelijkheden van OSMF uit en voegt micro soft Smooth Streaming inhoud af te spelen op nieuwe en bestaande OSMF-spelers. De invoeg toepassing voegt ook Smooth Streaming afspeel mogelijkheden toe aan het afspelen van een stroboscopisch licht (SMP-media).

SS voor OSMF bevat twee versies van de invoeg toepassing:

* Statische Smooth Streaming-invoeg toepassing voor OSMF (. SWC)
* Dynamische Smooth Streaming-invoeg toepassing voor OSMF (. swf)

In dit document wordt ervan uitgegaan dat de lezer een algemene werk ervaring heeft met OSMF-en OSMF-invoeg toepassingen. Raadpleeg de documentatie op de [officiële OSMF-site](http://osmf.org/)voor meer informatie over OSMF.

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming-invoeg toepassing voor OSMF 2,0
De invoeg toepassing ondersteunt het laden en afspelen van Smooth Streaming inhoud op aanvraag met de volgende functies:

* Smooth Streaming afspelen op aanvraag (afspelen, onderbreken, zoeken, stoppen)
* Live Smooth Streaming afspelen (afspelen)
* Live DVR functions (pauzeren, zoeken, DVR afspelen, go-to-Live)
* Ondersteuning voor video-codecs-H. 264
* Ondersteuning voor audio-codecs-AAC
* Meerdere audio taal wisseling met OSMF ingebouwde Api's
* Maximale selectie van afspeel kwaliteit met ingebouwde Api's van OSMF
* Ondertiteling closed captioning with OSMF Captions-invoeg toepassing
* Adobe &reg; Flash &reg; Player 11,4 of hoger.
* Deze versie ondersteunt alleen OSMF 2,0.

## <a name="supported-features-and-known-issues"></a>Ondersteunde functies en bekende problemen
Raadpleeg [dit document](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/)voor een volledige lijst met ondersteunde functies, niet-ondersteunde functies en bekende problemen.

## <a name="loading-the-plugin"></a>De invoeg toepassing laden
OSMF-invoeg toepassingen kunnen statisch worden geladen (tijdens het compileren) of dynamisch (tijdens runtime). De Smooth Streaming-invoeg toepassing voor OSMF-down loads bevat zowel dynamische als statische versies.

* Statisch laden: om statisch te laden, is een bestand met een statische bibliotheek (SWC) vereist. Statische invoeg toepassingen worden toegevoegd als een verwijzing naar de projecten en samengevoegd in het uiteindelijke uitvoer bestand tijdens de compilatie.
* Dynamisch laden: om dynamisch te laden, is een vooraf gecompileerd (SWF-) bestand vereist. Dynamische invoeg toepassingen worden in de runtime geladen en niet opgenomen in de project uitvoer. (Gecompileerde uitvoer) Dynamische invoeg toepassingen kunnen worden geladen met behulp van HTTP-en BESTANDS protocollen.

Zie de [pagina officiële OSMF-invoeg toepassing](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)voor meer informatie over statische en dynamische belasting.

### <a name="ss-for-osmf-static-loading"></a>SS voor het statische laden van OSMF
Het onderstaande code fragment laat zien hoe u de SS-invoeg toepassing voor OSMF statisch kunt laden en een eenvoudige video kunt afspelen met behulp van de OSMF MediaFactory-klasse. Voordat u de OSMF-code voor SS insluit, moet u ervoor zorgen dat de project verwijzing de statische invoeg toepassing MSAdaptiveStreamingPlugin-v 1.0.3-osmf 2.0. SWC bevat.

```csharp
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS voor het dynamisch laden van OSMF
Het onderstaande code fragment laat zien hoe u de SS-invoeg toepassing voor OSMF dynamisch kunt laden en een eenvoudige video kunt afspelen met behulp van de OSMF MediaFactory-klasse. Voordat u de OSMF-code voor SS inneemt, kopieert u de dynamische invoeg toepassing MSAdaptiveStreamingPlugin-v 1.0.3-osmf 2.0. swf uit naar de projectmap als u het BESTANDS protocol wilt laden of kopiëren onder een webserver voor HTTP-belasting. Het is niet nodig om "MSAdaptiveStreamingPlugin-v 1.0.3-osmf 2.0. swc" op te voegen in de project verwijzingen.

```csharp
package 
{

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Afspelen van een stroboscopisch-media met de dynamische invoeg toepassing SS ODMF
De Smooth Streaming voor de dynamische invoeg toepassing OSMF is compatibel met het afspelen van een [stroboscopisch-media (SMP)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/). U kunt de OSMF-invoeg toepassing voor de SS gebruiken om Smooth Streaming inhoud af te spelen op SMP. Om dit te doen, kopieert u "MSAdaptiveStreamingPlugin-v 1.0.3-osmf 2.0. swf" onder een webserver voor HTTP-belasting door de volgende stappen uit te voeren:

1. Blader door de [pagina afspelen van de modus van stroboscopisch media](http://osmf.org/dev/2.0gm/setup.html). 
2. De src instellen op een Smooth Streaming bron, (bijvoorbeeld http: \/ /devplatem.vo.msecnd.net/sintel/Sintel_H264.ISM/manifest) 
3. Breng de gewenste wijzigingen aan in de configuratie en klik op voor beeld en bijwerken.
   
   **Opmerking** De webserver voor inhoud moet een geldig crossdomain.xml hebben. 
4. Kopieer en plak de code naar een eenvoudige HTML-pagina met behulp van uw favoriete tekst editor, zoals in het volgende voor beeld:

    ```html
    <html>
    <body>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
    </embed>
    </object>
    </body>
    </html>
    ```


1. Voeg Smooth Streaming OSMF-invoeg toepassing toe aan de insluit code en sla deze op.
   
    ```html
    <html>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
    </embed>
    </object>
    </html>
    ```

2. Sla uw HTML-pagina op en publiceer deze op een webserver. Blader naar de gepubliceerde webpagina met behulp van uw favoriete Flash &reg; Player-Internet browser (Internet Explorer, Chrome, Firefox, enzovoort).
3. Profiteer van Smooth Streaming inhoud in Adobe &reg; Flash &reg; Player.

Raadpleeg de officiële [OSMF-ontwikkelings pagina](http://osmf.org/resources.html)voor meer informatie over de ontwikkeling van algemene OSMF.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Micro soft Adaptive streaming-invoeg toepassing voor OSMF-update](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

