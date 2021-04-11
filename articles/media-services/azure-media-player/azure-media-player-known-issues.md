---
title: Bekende problemen Azure Media Player
description: De huidige versie heeft de volgende bekende problemen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 9d2289f1cec0d3679b0e9400f75c75cf9fece60b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448812"
---
# <a name="known-issues"></a>Bekende problemen #

De huidige versie heeft de volgende bekende problemen:

## <a name="azure-media-player"></a>Azure Media Player ##

- Onjuist geconfigureerde encoders kunnen problemen veroorzaken met afspelen
- Stromen met tijds tempels die groter zijn dan 2 ^ 53 kunnen afspeel problemen bevatten.
  - Risico beperking: gebruik 90-kHz video-en 44,1-kHz-tijd schalen
- Geen autoplay op mobiele apparaten zonder tussen komst van de gebruiker. Het wordt geblokkeerd door het platform.
- Het kan voor komen dat er problemen optreden bij het afspelen.
- Het downloaden van grote presentaties kan ertoe leiden dat de gebruikers interface Lockup.
- Kan niet automatisch dezelfde bron afspelen nadat de presentatie is beëindigd.
  - Als u een bron opnieuw wilt afspelen nadat deze is beëindigd, moet u de bron opnieuw instellen.
- Lege manifesten kunnen problemen met de speler veroorzaken.
  - Dit probleem kan optreden bij het starten van een live stream en er zijn onvoldoende segmenten gevonden in het manifest.
- Afspeel positie mogelijk buiten de gebruikers interface-SeekBar.
- De volg orde van gebeurtenissen is niet consistent op alle technische technici.
- De gebufferde eigenschap is niet consistent in de technische ondersteuning.
- nativeControlsForTouch moet zijn ingesteld op False (standaard) om te voor komen dat "object geen ondersteuning biedt voor eigenschap of methode" setControls ""
- Posters moeten nu absolute url's zijn
- Er kunnen kleine esthetische problemen optreden in de gebruikers interface wanneer de modus Hoog contrast van het apparaat wordt gebruikt
- Url's met '% ' of ' + ' in de volledig gedecodeerde teken reeks hebben mogelijk problemen met het instellen van de bron

## <a name="ad-insertion"></a>AD-invoeging ##

- Ads kan problemen ondervinden bij het invoegen (op aanvraag of Live) wanneer een ad-blocker in de browser is geïnstalleerd
- Op mobiele apparaten kunnen problemen optreden bij het afspelen van advertenties.
- MP4 midroll Ads wordt momenteel niet ondersteund door Azure Media Player.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- In het DVR-venster van live content, blijft de inhoud van de tijd lijn toenemen totdat het naar het gebied wordt gezocht of het einde van de presentatie wordt bereikt.
- Live presentaties in Firefox met MSE ingeschakeld hebben enkele problemen

- Activa die alleen audio zijn, worden niet afgespeeld via de AzureHtml5JS-techniek.
  - Als u activa zonder audio wilt afspelen, kunt u dit doen door lege audio in te voegen met behulp van de [Azure Media Services Explorer](https://aka.ms/amse)
  - Instructies voor het invoegen van Silent audio vindt u [hier](../previous/media-services-advanced-encoding-with-mes.md#silent_audio)

## <a name="flash"></a>Flits ##

- AES-inhoud wordt niet weer gegeven in Flash versie 30.0.0.134 vanwege een fout in de cache logica van Adobe. Adobe heeft het probleem opgelost en uitgebracht in 30.0.0.154
- Tech-en HTTP-fouten (zoals 404 time-outs in het netwerk), neemt de speler langer in beslag dan andere technische instellingen.
- Klik op video gebied met behulp van de knipperende tech die de speler niet kan afspelen/onderbreken.
- Als op de gebruiker Flash is geïnstalleerd, maar niet is gemachtigd om deze te laden op de site, kan er oneindig draaiend worden uitgevoerd. Dit komt omdat de speler denkt dat de invoeg toepassing is geïnstalleerd en beschikbaar is en dat de inhoud wordt uitgevoerd door de invoeg toepassing. Java script-code is verzonden, maar de browser instellingen hebben de uitvoering van de invoeg toepassing geblokkeerd totdat de gebruiker de prompt accepteert om de invoeg toepassing toe te staan. Dit kan in alle browsers optreden.  

## <a name="silverlight"></a>Silverlight ##

- Ontbrekende onderdelen
- Tech-en HTTP-fouten (zoals 404 time-outs in het netwerk), neemt de speler langer in beslag dan andere technische instellingen.
- Voor het afspelen van Safari en Firefox op Mac met Silverlight moet de bron expliciet worden gedefinieerd `"http://` `https://` .
- Als er voor deze tech een API ontbreekt, wordt in het algemeen Null geretourneerd.
- Als op de gebruiker Flash is geïnstalleerd, maar niet is gemachtigd om deze te laden op de site, kan er oneindig draaiend worden uitgevoerd. Dit komt omdat de speler denkt dat de invoeg toepassing is geïnstalleerd en beschikbaar is en dat de inhoud wordt uitgevoerd door de invoeg toepassing. Java script-code is verzonden, maar de browser instellingen hebben de uitvoering van de invoeg toepassing geblokkeerd totdat de gebruiker de prompt accepteert om de invoeg toepassing toe te staan. Dit kan in alle browsers optreden.  

## <a name="native-html5"></a>Systeem eigen HTML5 ##

- Html5-tech verzendt alleen de canplaythrough-gebeurtenis voor de eerste set-bron.
- Deze tech ondersteunt alleen de implementatie van de browser.  Sommige functies ontbreken mogelijk in deze technische stand.  
- Als er voor deze tech een API ontbreekt, wordt in het algemeen Null geretourneerd.
- Er zijn problemen met bijschriften en ondertiteling op deze technische zaken. Ze kunnen of zijn mogelijk niet beschikbaar of op deze technische weer gave worden weer gegeven.
- Een beperkte band breedte in HLS/Html5 tech scenario resulteert in audio afspelen zonder video.

### <a name="microsoft"></a>Microsoft ###

- Afspelen op IE8 werkt momenteel niet vanwege incompatibiliteit met ECMAScript 5
- In IE en sommige versies van Edge kan volledig scherm worden ingevoerd door met de tab-toets naar de knop te gaan en deze te selecteren of door de F/F-sneltoets te gebruiken.

## <a name="google"></a>Google ##

- Meerdere coderings profielen in hetzelfde manifest bevatten enkele afspeel problemen in Chrome en worden niet aanbevolen.
- Chrome kan niet met AzureHtml5JS worden afgespeeld. Volg de details van de [fout tracering](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- Vanaf Chrome v58 moet widevine-inhoud worden geladen/afgespeeld via het https://-protocol, anders mislukt het afspelen.

## <a name="mozilla"></a>Mozilla ##

- Voor de audio stream switch zijn audio stromen vereist om dezelfde codec-privé gegevens te hebben wanneer AzureHtml5JS wordt gebruikt. Voor het Firefox-platform is dit vereist.

## <a name="apple"></a>Apple ##

- In Safari op Mac wordt de energiebesparings modus vaak standaard ingeschakeld met de instelling ' invoeg toepassingen stoppen om energie te besparen ', waarmee invoeg toepassingen zoals Flash en Silverlight worden geblokkeerd wanneer ze van mening zijn dat deze niet van toepassing zijn op de gebruiker. Dit blok blokkeert de bestaande en alleen mogelijkheden van de invoeg toepassing niet. Dit kan problemen veroorzaken bij het afspelen van de standaard techOrder.
  - Risico beperking 1: als de video speler vooraan en gecentreerd is (binnen een 3000 x 3000 pixel grens in de linkerbovenhoek van het document), moet deze nog steeds worden afgespeeld.
  - Beperking 2: Wijzig techOrder voor Safari in ["azureHtml5JS", "HTML5"]. Deze beperking heeft gevolgen voor niet alle functies die beschikbaar zijn in de beschik baarheid van de technologie.
- Voor inhoud van PlayReady via Silverlight kunnen problemen optreden in Safari.
- AES-en beperkte token inhoud wordt niet afgespeeld met iOS-en oudere Android-apparaten.
  - Voor dit scenario moet er een proxy aan uw service worden toegevoegd.
- Standaard weergave voor iOS-telefoon wordt weer gegeven via.
- het afspelen van iPhone vindt altijd plaats in het volledige scherm van de speler.
  - Functies, waaronder bijschriften, zijn mogelijk niet behouden in deze niet-inline afspelen.
- Wanneer Live Presentation eindigt, worden de presentaties niet goed beëindigd door iOS-apparaten.
- iOS staat geen DVR-mogelijkheden toe.
- de schakel optie voor de iOS-audio stroom kan alleen worden uitgevoerd als de native gebruikers interface van iOS
- Oudere versies van Safari kunnen mogelijk problemen ondervinden bij het afspelen van live streams.

## <a name="older-android"></a>Oudere Android ##

- AES-en beperkte token inhoud wordt niet afgespeeld met iOS-en oudere Android-apparaten.
  - Voor dit scenario moet er een proxy aan uw service worden toegevoegd.

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
