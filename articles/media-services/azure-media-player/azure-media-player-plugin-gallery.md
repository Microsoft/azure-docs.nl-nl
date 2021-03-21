---
title: Galerie met invoegtoepassingen voor Azure Media Player
description: In dit artikel vindt u een lijst van invoegtoepassingen die beschikbaar zijn voor Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 71fa79cb8847d16ac0890f9aba647cb8f5e2e444
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99089339"
---
# <a name="azure-media-player-plugin-gallery"></a>Galerie met invoegtoepassingen voor Azure Media Player #

## <a name="plugins"></a>Invoegtoepassingen ##

| Naam van invoegtoepassing                         | Demo-URL                    | Broncode                | Beschrijving    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Aanvullende functies                 | | | |
| **Nieuw!** AMP360Video                | [Demo](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Met deze invoegtoepassing kunt u 360 graden-video's in AMP visualiseren op uw desktop of VR-compatibele apparaten. De volledige documentatie kunt u [hier](https://doc.babylonjs.com/extensions/amp360video) raadplegen: |
|  Sprite voor aanwijzen                         | [Demo](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Invoegtoepassing voor Azure Media Player (AMP) voor een tijdlijnweergave van een sprite met videominiaturen die is gegenereerd door de Media Encoder Standard (MES) van Azure Media Services (AMS). |
| Overlay met diagnostische gegevens                 | [Demo](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Deze invoegtoepassing geeft het volgende weer: Alle belangrijke parameters, statistieken van de video, alle gebeurtenissen in de afspeellevenscyclus van de video en DRM-beschermingsgegevens, zoals de sleutel-id en URL's voor het verkrijgen van licenties, indien beschermd.                                                                                                                                                                      |
| Rekenmachine voor framesnelheid en tijdcode | Geen demo beschikbaar | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Deze invoegtoepassing berekent de framesnelheid van de video op basis van de MP4-vakken `tfhd`/`trun` van het eerste MPEG-DASH-videofragment, parseert de tijdschaalwaarde van het MPEG-DASH-clientmanifest en biedt een manier om de tijdcode te genereren voor een bepaalde absolute tijd in de speler (en geeft de absolute tijd in de speler op basis van een tijdcode). |
| <strike>Afspeelsnelheid</strike>                      | [Demo](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Met deze invoegtoepassing kunnen kijkers de afspeelsnelheid van de video bepalen. *Deze functionaliteit is automatisch beschikbaar in versie AMP v2.0.0+, maar is standaard uitgeschakeld.* Bekijk [hier](https://github.com/Azure-Samples/azure-media-player-samples) onze voorbeelden voor instructies om dit in te schakelen. |
| Tijdstip bij aanwijzen                      | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Hiermee wordt een tijdstip weergegeven boven de voortgangsbalk als de muisaanwijzer op de balk wordt geplaatst, zodat de kijker een nauwkeurige tijd kan aflezen. *Opmerking: Deze invoegtoepassing is al geïntegreerd in AMP* maar neem gerust een kijkje als u benieuwd bent hoe deze is geprogrammeerd.                                                                                                                 |
| Titel-overlay                       | [Demo](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Hiermee wordt de configureerbare videotitel als overlay op het scherm weergegeven. |
| Markeringen van tijdlijn                    | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Deze invoegtoepassing geeft op basis van een matrix met tijden een overlay met kleine markeringen weer boven de voortgangsbalk op de betreffende tijdstippen. |
| Analyse                           | | | |
| Application Insights                | [Blogpost](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Deze invoegtoepassing houdt metrische gegevens van uw speler bij en zet deze over naar Power BI voor een intuïtieve grafische weergave van de kijkerservaring voor uw speler. |
| Google Analytics                    | N.v.t.                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Google Analytics-invoegtoepassing voor Azure Media Player |
| Diagnostiek                         | | | |
| Diagnostiekuitvoer                  | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Deze invoegtoepassing geeft een matrix van diagnostische gegevens van uw speler als uitvoer. Klik op de demo-link en open uw JavaScript-console om te zien hoe deze invoegtoepassing werkt. |
| Toegankelijkheid                      | | | |
| Inzoomen                             | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Met deze invoegtoepassing wordt een inzoomschaal weergegeven op het scherm van de speler zodat kijkers op uw inhoud kunnen inzoomen door te slepen. |
| Live ondertiteling                       | [Azure-blogpost](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/), [SubPly-post](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | N.v.t. | *Bekijk de post voor meer informatie.* Invoegtoepassing voor Azure Media Player op basis van een end-to-end werkstroom voor live ondertiteling. Klik op de link hiernaast om naar de site van SubPly te gaan en meer informatie over de oplossing te lezen. |
| Sneltoetsen                            | <strike>[Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Met de invoegtoepassing voor sneltoetsen kunnen kijkers diverse aspecten van de speler besturen met algemene toetsencombinaties, zoals F voor weergave op volledig scherm, M voor dempen en de pijltoetsen voor de voortgangsbalk. *Opmerking: Deze invoegtoepassing is al geïntegreerd in AMP, maar gebruik hem gerust als bron.* |
| Sociaal                              | | | |
| Delen                               | [Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Met deze invoegtoepassing wordt een knop toegevoegd aan de balk met besturingselementen in de speler waardoor kijkers de video die ze bekijken met hun vrienden kunnen delen via Facebook, Twitter of LinkedIn. |

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
