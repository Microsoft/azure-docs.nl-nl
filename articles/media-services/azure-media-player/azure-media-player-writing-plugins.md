---
title: Invoeg toepassingen schrijven voor Azure Media Player
description: Meer informatie over het schrijven van een invoeg toepassing met Azure Media Player met Java script
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7f14be29a33a885bfcef27fa9ef2775e72df16d9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449797"
---
# <a name="writing-plugins-for-azure-media-player"></a>Invoeg toepassingen schrijven voor Azure Media Player #

Een invoeg toepassing is Java script geschreven om de speler uit te breiden of te verbeteren. U kunt invoeg toepassingen schrijven die de weer gave van Azure Media Player, de functionaliteit ervan wijzigen of zelfs een IT-interface met andere services. U kunt dit doen in twee eenvoudige stappen:

## <a name="step-1"></a>Stap 1 ##

Schrijf uw Java script in een functie zoals:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

U kunt uw code rechtstreeks in uw HTML-pagina binnen `<script>` Tags of in een extern java script-bestand schrijven. Als u dit wel doet, moet u het Java script-bestand toevoegen aan de `<head>` van uw HTML-pagina *na* het amp-script.

Voorbeeld:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Stap 2 ##
Initialiseer de invoeg toepassing met Java script op een van de volgende twee manieren:

Methode 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Methode 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Opties voor de invoeg toepassing zijn niet vereist, met inbegrip van de ontwikkel aars die de invoeg toepassing gebruiken om het gedrag te configureren zonder de bron code te hoeven wijzigen.

Bekijk voor inspiratie en meer voor beelden over het maken van een invoeg toepassing de [Galerie](azure-media-player-plugin-gallery.md)

>[!NOTE]
> De code van de invoeg toepassing verandert dynamisch items in het DOM tijdens de levens duur van de speler van de viewer, maar maakt geen permanente wijzigingen in de bron code van de speler. Hier vindt u meer informatie over de ontwikkel hulpprogramma's van uw browser. Als u bijvoorbeeld het uiterlijk van een element in de speler wilt wijzigen, kunt u het HTML-element vinden op basis van de naam van de klasse en vervolgens kenmerken toevoegen of wijzigen. Hier vindt u een goede bron voor het [wijzigen van HTML-kenmerken.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Geïntegreerde invoeg toepassingen ###

 Er zijn momenteel twee geïntegreerde in AMP: de [tijd tips](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) en [sneltoetsen](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Deze invoeg toepassingen zijn oorspronkelijk ontwikkeld voor modulaire invoeg toepassingen voor de speler, maar zijn nu opgenomen in de bron code van de speler.

### <a name="plugin-gallery"></a>Galerie van invoeg toepassingen ###

De [Galerie met invoeg](https://aka.ms/ampplugins) toepassingen bevat verschillende invoeg toepassingen die de Community al heeft bijgedragen aan functies zoals tijd regel markeringen, zoomen, analyses en meer. De pagina biedt toegang tot de invoeg toepassingen en instructies over hoe u deze kunt instellen, evenals een demo waarin de invoeg toepassing in actie wordt weer gegeven. Als u een koele invoeg toepassing maakt die u in de galerie wilt opnemen, kunt u deze verzenden zodat we deze kunnen controleren.

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
