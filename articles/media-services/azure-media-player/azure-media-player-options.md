---
title: Azure Media Player opties
description: De Azure Media Player insluit code is gewoon een HTML5-Video code. voor veel van de opties kunt u de standaard label kenmerken gebruiken om de opties in te stellen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 392ecff2ab120e713a07d130493a4a8339dae5ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366867"
---
# <a name="options"></a>Opties #

## <a name="setting-options"></a>Instellingsopties ##

De Azure Media Player insluit code is gewoon een HTML5-Video code. voor veel van de opties kunt u de standaard label kenmerken gebruiken om de opties in te stellen.

`<video controls autoplay ...>`

U kunt ook het data-setup-kenmerk gebruiken om opties te bieden in de [JSON](http://json.org/example.html) -indeling. Dit is ook de manier waarop u opties instelt die niet standaard zijn voor de video-tag.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Ten slotte, als u niet het data-setup-kenmerk gebruikt voor het activeren van de speler Setup, kunt u een object met de opties van de speler door geven als het tweede argument in de Java script-installatie functie.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> De opties in de constructor worden alleen ingesteld bij de eerste initialisatie voordat de bron is ingesteld.  Als u de opties voor hetzelfde geinitialiseerde Azure Media Player element wilt wijzigen, moet u de opties bijwerken voordat u de bron wijzigt. U kunt de opties in Java script bijwerken met behulp van `myPlayer.options({/*updated options*/});` . Houd er rekening mee dat alleen gewijzigde opties worden beïnvloed. alle andere eerder ingestelde opties blijven behouden.

## <a name="individual-options"></a>Afzonderlijke opties ##

> [!NOTE]
>Kenmerken van video Tags kunnen alleen waar of ONWAAR (Booleaans) zijn, u hoeft alleen het kenmerk op te nemen (geen gelijkteken) om het in te scha kelen, of het uit te sluiten om het uit te scha kelen. Als u bijvoorbeeld de besturings elementen wilt inschakelen: onjuist `<video controls="true" ...>` recht op `<video controls ...>` het grootste probleem dat gebruikers uitvoeren in, wordt geprobeerd deze waarden in te stellen op False met ONWAAR als de waarde (bijvoorbeeld Controls = "false") die werkelijk het tegenovergestelde doet en de waarde ingesteld op True, omdat het kenmerk nog steeds is opgenomen.

### <a name="controls"></a>besturingselementen ###

Met de optie besturings elementen wordt bepaald of de speler besturings elementen heeft waarmee de gebruiker kan werken. Zonder besturings elementen de enige manier om het afspelen van de video te starten, is het kenmerk AutoPlay of via de API.

`<video controls ...>` of `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Als AutoPlay is ingesteld op True, wordt de video afgespeeld zodra de pagina wordt geladen (zonder tussen komst van de gebruiker).

> [!NOTE]
> Deze optie wordt niet ondersteund door mobiele apparaten zoals Windows Phone, Apple iOS en Android. Mobiele apparaten blok keren de functionaliteit voor automatisch afspelen om te voor komen dat het gebruik van de maandelijkse gegevens plannen van de consument (vaak duur). Een gebruiker/klik is vereist voor het starten van de video in dit geval.

`<video autoplay ...>`of `{ "autoplay": true }`

### <a name="poster"></a>poster ###
Het poster kenmerk stelt de afbeelding in die wordt weer gegeven voordat de video wordt afgespeeld. Dit is vaak een frame van de video of een aangepaste titel scherm. Zodra de gebruiker op afspelen klikt, wordt de afbeelding verwijderd.

`<video poster="myPoster.jpg" ...>` of `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>breedte ###

Met het kenmerk Width wordt de weergave breedte van de video ingesteld.

`<video width="640" ...>` of `{ "width": 640 }`

### <a name="height"></a>hoogte ###

Met het kenmerk height wordt de weergave hoogte van de video ingesteld.

`<video height="480" ...>` of `{ "height": 480 }`

### <a name="plugins"></a>invoeg ###

De JSON plugins bepaalt welke invoeg toepassingen worden geladen met dit exemplaar van AMP kunt u opties configureren die door de invoeg toepassing kunnen worden geconfigureerd.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Zie [invoeg toepassingen schrijven](azure-media-player-writing-plugins.md) voor meer informatie over het ontwikkelen en gebruiken van de invoeg toepassing

### <a name="other-options"></a>andere opties ###

Andere opties kunnen worden ingesteld voor de `<video>` tag met behulp van de `data-setup` para meter die een JSON gebruikt.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Dit is expliciet ingesteld op ONWAAR. Als de instelling is ingesteld op ONWAAR, kan de Azure Media Player Skin hetzelfde op verschillende platforms worden weer gegeven.  Daarnaast is er nog steeds, in tegens telling tot de naam, aanraken ingeschakeld.

### <a name="fluid"></a>vloeistoffen ###

Als u deze optie instelt op True video-element krijgt de volledige breedte van de bovenliggende container en wordt de hoogte aangepast aan een video met een standaard verhouding van 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid` optie overschrijft expliciete `width` en `height` instellingen. Deze optie is alleen beschikbaar in Azure Media Player-versie `2.0.0` en hoger.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` Option Controls playbackSpeed Control en set met instellingen voor de afspeel snelheid die beschikbaar zijn voor de gebruiker. `playbackSpeed` neemt een object. Als u het besturings element voor de afspeel snelheid wilt inschakelen op de werk balk, `enabled` moet de eigenschap van het object worden ingesteld op True. Een voor beeld van het inschakelen van afspeel snelheid in Markeringen:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Andere eigenschappen van de `playbackSpeed` instelling worden gegeven door het object [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) .

Voor beeld van het instellen van opties voor afspeel snelheid in Java script:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Deze optie is alleen beschikbaar in Azure Media Player versie 2.0.0 en hoger.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

De `staleDataTimeLimitInSec` optie is een optimalisatie waarmee u kunt configureren hoeveel seconden verouderde gegevens u in de mediaSource-buffers wilt houden. Deze optie is standaard uitgeschakeld.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

Als u deze optie instelt op True, kunt u de ondertiteling van Live CEA in uw live streams en live-archieven weer geven. Het kenmerk label is niet vereist als de speler niet is opgenomen in een standaard label.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Deze optie is alleen beschikbaar in Azure Media Player versie 2.1.1 en hoger.

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
