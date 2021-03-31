---
title: Wijzigingenlogboek van Azure Media Player
description: Wijzigingenlogboek van Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: c63a8efc4bbcf6e5a124d439e6f9f91397e2fa53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370573"
---
# <a name="changelog"></a>Wijzigingenlogboek

## <a name="236-official-update-september-21-2020"></a>2.3.6 (Officiële update 21 september 2020)

### <a name="features-236"></a>Functies 2.3.6

Toegevoegde ondersteuning voor alleen-audio voor de azureHtml5JS Tech (DASH) Ondersteuning van een latere start van Livetranscriptie Ondersteuning van een taalwijziging in Livetranscriptie

### <a name="bug-fixes-236"></a>Opgeloste fouten 2.3.6

Bij het gebruik van playsinline in bij het afspelen van HLS op Apple-apparaten, wordt de video opnieuw gestart als u op de knop LIVE klikt De AMP-posterafbeelding veroorzaakt soms een uitzondering De volumeknop ontbrak bij het afspelen van HLS FairPlay [Toegankelijkheid] Knopinfo niet gedefinieerd voor knoppen bij gebruik van het toetsenbord [Toegankelijkheid] De helderheidsverhouding is minder dan 1.3:1 voor de voortgangsbalk [Toegankelijkheid] De focus van het toetsenbord gaat soms niet terug naar de videokwaliteitsknop [Toegankelijkheid] Besturingselementen zijn niet zichtbaar in het scherm Video, waardoor Verteller ze niet kan vinden

### <a name="changes-236"></a>Wijzigingen 2.3.6

Relevante KeyDelivery-fouten retourneren voor het aanroepen van toepassingen

## <a name="235-official-update-june-1-2020"></a>2.3.5 (officiële update 1 juni 2020)

### <a name="bug-fixes-235"></a>Opgeloste fouten 2.3.5

- [Toegankelijkheid] ESC-toetslistener in deelvenster Opties is gekoppeld aan document
- [Toegankelijkheid] Voorkomen dat de gebruikersinterface van de speler wordt weergegeven als de besturingsbalk of het menu opties focus bevat
- De controlebalk toont een onjuiste kloktijd wanneer de Instellingen voor de Weergave van de Kloktijd zijn ingeschakeld

### <a name="changes-235"></a>Wijzigingen 2.3.5

- Er is een foutbericht toegevoegd voor de foutcode 0x00400005 en gedocumenteerd

## <a name="234-official-update-march-4-2020"></a>2.3.4 (officiële update 4 maart 2020)

### <a name="bug-fixes-234"></a>Opgeloste fouten 2.3.4

- Kan PlayReady overrideLicenseAcquistionUrl niet instellen
- Kan bepaalde inhoud met discontinuïteiten niet afspelen
- [Toegankelijkheid] De waarde van het kenmerk ID voor schermlezerswaarschuwing moet uniek zijn
- [Toegankelijkheid] Tijdens het navigeren in het dialoogvenster Instellingen voor Ondertiteling wordt de focus verplaatst van het dialoogvenster

### <a name="changes-234"></a>Wijzigingen 2.3.4

- Logboekinhoud: lengte na een succesvolle download om te helpen bij het analyseren van ontsleutelingsfouten 2.3.3 (officiële update 12 november 2019)

### <a name="features-234"></a>Functies 2.3.4

- Er is ondersteuning toegevoegd voor het weergeven van de kloktijd van een video als overlay, en op de besturingsbalk

### <a name="bug-fixes-234"></a>Opgeloste fouten 2.3.4

- Het schakelen tussen audionummers werkt, maar er is een fout opgetreden in de uitvoer van IE11 en Windows7 'Het object ondersteunt voor de eigenschap of methode niet 'ingeschakeld''
- Schakelen tussen audionummers mislukt wanneer de buffer volledig is geladen
- Het schakelen tussen audionummers mislukt wanneer de gebruiker de video pauzeert en zeer snel tussen audionummers schakelt
- [Toegankelijkheid] Knopinfo niet gedefinieerd voor video-besturingselement onder Videospeler
- Er ontbreken volumeknoppen op Html5, afhankelijk van wanneer 'loadstart' is ontvangen
- [Toegankelijkheid] Geen manier om de alternatieve tekst voor de posterafbeelding in te stellen
- [Toegankelijkheid] De toepassingsfocus gaat verloren nadat u 'Gereed' in het dialoogvenster instellingen voor ondertiteling hebt geselecteerd
- [Toegankelijkheid] Er zijn onjuiste ARIA-kenmerken gedefinieerd voor 'video' onder 'preview van segmenten'

### <a name="changes-234"></a>Wijzigingen 2.3.4

- Leeg ondertitelingslabel/nummer verwijderd bij het afspelen van HLS op iOS en macOS Safari
- Het aantal 412s voor IMSC1-ondertitels is verminderd
- Uitvoerwaarschuwing in de console voor 10 opeenvolgende lege IMSC1-ondertitels voor het oplossen van problemen met live foutopsporing

## <a name="232-official-update-october-9-2019"></a>2.3.2 (officiële update 9 oktober 2019)

### <a name="features-232"></a>Kenmerken 2.3.2

\- Toegevoegde PlayReady-ondersteuning voor het afspelen van DASH voor de Chromium Edge-browser

### <a name="bug-fixes-232"></a>Opgeloste fouten 2.3.2

- De huidige afspeelsnelheid wordt niet visueel weergegeven in het menu afspeelsnelheid, tenzij de gebruiker deze handmatig instelt
- [Toegankelijkheid] Het deelvenster 'Instellingen' wordt niet samengevouwen met 'Esc'-toets
- [Toegankelijkheid] AMP-sneltoets 'M' werkt niet als Verteller is ingeschakeld

### <a name="changes-232"></a>Wijzigingen 2.3.2

- Voor browsers die geen ondersteuning bieden voor E-AC3 audio-codec, worden E-AC3-audionummers verborgen in het menu audionummer
- Voor browsers die ondersteuning bieden voor E-AC3 audio-codec, is standaard een E-AC3-audionummer geselecteerd
- Voor browsers die geen ondersteuning bieden voor audio-codecs, worden audionummers met een andere codec dan het geselecteerde nummer verborgen in het menu audionummer

## <a name="231-official-update-august-12-2019"></a>2.3.1 (officiële update 12 augustus 2019)

### <a name="features-231"></a>Functies 2.3.1

- Een gebeurtenis laten zien wanneer er emsg-vakken worden ontvangen in DASH-afspelen -Toegevoegde ondersteuning voor het weergeven van EC-3-audionummers in het audiomenu van browsers die EC-3 ondersteunen en het omschakelen van audionummer van AAC naar EC3 en vice versa alleen in de Chromimum-gebaseerde Edge-browser

### <a name="bug-fixes-231"></a>Opgeloste fouten 2.3.1

- Het menu audionummers is beschadigd nadat u de EC-3-nummers hebt verwijderd
- De huidige tijd kan groter zijn dan de duur van de video
- Het instellen van de afspeelsnelheid via initialSpeed werkt niet
- Soms lijkt de speler vast te lopen
- Wanneer u op Microsoft Edge en Internet Explorer op een aanraakscherm drukt, wordt het juiste segment van de video niet nauwkeurig weergegeven wanneer u inzoomt op een pagina
- [Toegankelijkheid] Het label Aria voor afspelen/onderbreken beschrijft niet videospeler. Live segment niet gevonden fout voor flashSS wordt niet aan de correcte amp-fout toegewezen
- [Toegankelijkheid] Aria-rollen die worden gebruikt voor afspelen/onderbreken moeten voldoen aan geldige waarden (. vjs-text-track-display)
- [Toegankelijkheid] Bepaalde ARIA-rollen moeten zijn opgenomen in bepaalde bovenliggende items
- [Toegankelijkheid] Er is geen knopinfo gedefinieerd voor de knop afspelen/pauzeren van de IMSC1-ondertitels van de videospeler kunnen verdwijnen na het zoeken in de huidige video/audio-buffer

### <a name="changes-231"></a>Wijzigingen 2.3.1

- Bij het ophalen van een segmentDecryptError en als de speler zich al op de live edge bevindt, vernieuwt de speler nu het manifest in plaats van het volgende segment te proberen
- Meer logboekregistratie toegevoegd voor diagnose
- Bijgewerkte documentatie met FairPlay-ondersteuning voor iOS Safari
- Er is een voorbeeld toegevoegd voor de optie 'srclang' van IMSC1
- Opvulling, textPadding, boxShadow onderdrukkingen voor tekstnummer toegevoegd.
- Er is een foutcode (0x0020025B) toegevoegd om te differentiëren dat het segment kan worden gedownload vanwege gebrek aan internet in plaats van alleen 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (officiële release 30 april 2019)

### <a name="features-230"></a>Functies 2.3.0

- Ondersteuning toegevoegd voor IMSC1-ondertitels voor DASH
- Ondersteuning toegevoegd voor alleen video-assets voor DASH
- API-presentatie TimeOffsetInSec toegevoegd

### <a name="bug-fixes-230"></a>Opgeloste fouten 2.3.0

- Het AMP-profiel van de LowLatency-heuristiek kan problemen veroorzaken bij afspeelfuncties 'dempen' en 'dempen opheffen' van iOS-video en sommige talen hebben onjuiste vertalingen
- De aria-valuenow-waarde van de voortgangsbalk-schuifrelegaar is soms onjuist
- De waarde van de ariarol van tekstnummerweergave is onjuist

### <a name="changes-230"></a>Wijzigingen 2.3.0

- De logboeken bevatten nu de grootte van de gedownloade mediafragmenten
- De ondersteuning voor IE 9 en IE 10 is verwijderd
- Bijgewerkt CEA708-voorbeeld om links uitgelijnde ondertiteling weer te geven
- MediaError.message toegevoegd aan logboeken voor afspeelfouten

## <a name="224-official-update-february-22-2019"></a>2.2.4 (officiële update 22 februari 2019) ##

### <a name="bug-fixes-224"></a>Opgeloste fouten 2.2.4 ###

- [Opgeloste fout][AMP][Toegankelijkheid] Er is een bereikbaar fantoomtabblad verwijderd wanneer het foutscherm wordt weergegeven
- [Opgeloste fout][AMP] De sneltoets M voor IE11 en Microsoft Edge is hersteld
- [Opgeloste fout][AMP] Een uitzondering voor CEA708-bijschriften is opgelost
- [Opgeloste fout][AMP] Een probleem met het blokkeren van video voor de Edge-browser is opgelost

### <a name="changes-224"></a>Wijzigingen in 2.2.4 ###

- [Wijziging][AMP] Wanneer er een fout optreedt bij het decoderen van een fragment, probeert de speler de huidige en diverse fragmenten opnieuw af te spelen om het afspelen te herstellen
- [Wijziging][AMP] AMP is toleranter gemaakt voor overlappende video- of audiofragmenten

## <a name="223-official-update-january-9-2019"></a>2.2.3 (officiële update 9 januari 2019) ##

### <a name="features-223"></a>Functies 2.2.3 ###

- [Functie][HLS] Het audionummermenu is toegevoegd voor Safari HLS-afspelen

### <a name="bug-fixes-223"></a>Opgeloste fouten 2.2.3 ###

- [Opgeloste fout][AMP][Toegankelijkheid] Tijdens het afspelen van live-uitzendingen kan de knop live niet worden geselecteerd met behulp van het toetsenbord
- [Opgeloste fout][AMP] Fout-positieve 0x0400003-fouten vanwege een mislukte MSE-test zijn opgelost
- [Opgeloste fout][AMP] Er is een probleem opgelost waarbij de video kon worden geblokkeerd bij het starten van een livestream

### <a name="changes-223"></a>Wijzigingen 2.2.3 ###

- [Wijziging][AMP] Er is meer informatie toegevoegd aan het logboek om betere diagnostische gegevens mogelijk te maken
- [Wijziging][AMP] Als er meer dan één bitrate beschikbaar is op dezelfde schermresolutie, zijn alle bitrates beschikbaar voor selectie

## <a name="222-official-update"></a>2.2.2 (officiële update) ##

### <a name="bug-fixes-222"></a>Opgeloste fouten 2.2.2 ###

- [Opgeloste fout][AMP] Wanneer de speler een tijdelijke netwerkstoring tegenkomt, wordt direct gestopt met afspelen
- [Opgeloste fout][AMP][Toegankelijkheid] Het foutdialoogvenster is niet toegankelijk via het toetsenbord
- [Opgeloste fout][AMP] Er wordt een oneindig kringveld weergegeven wanneer een asset met alleen audio wordt afgespeeld in plaats van niet-ondersteunde fout

### <a name="changes-222"></a>Wijzigingen 2.2.2 ###

- [Wijziging][AMP] Gelokaliseerde tekenreeksen toegevoegd voor de aankondigings-UI

## <a name="221-official-update"></a>2.2.1 (officiële update) ##

### <a name="features-221"></a>Functies 2.2.1 ###

- [Functie][CMAF] Er is ondersteuning toegevoegd voor HLS CMAF

### <a name="bug-fixes"></a>Opgeloste fouten ###

- [Opgeloste fout][AMP] niet-gewiste timers in de logica genereren fouten bij afspelen
- [Opgeloste fout][AMP][Firefox] beëindigde gebeurtenis wordt niet geactiveerd op Firefox en Chrome wanneer het live-programma wordt gestopt
- [Opgeloste fout][AMP]Besturingselementen worden weergegeven na setsource, zelfs wanneer besturingselementen zijn ingesteld op False in de opties voor de speler

### <a name="changes"></a>Wijzigingen ###

- [Wijziging][Live-ondertiteling] API-naam voor CEA-bijschriften gewijzigd van 608 in 708. Zie [CEA708 Captions Settings](/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)--> (Engelstalig) voor meer informatie

## <a name="220-official-release"></a>2.2.0 (officiële versie) ##

### <a name="features-220"></a>Functies 2.2.0 ###

- [Functie][Azurehtml5JS][Flash][LiveCaptions] Ondersteuning voor CEA 708-ondertiteling in Azurehtml5JS en FlashSS-tech voor duidelijke en AES-inhoud.

### <a name="bug-fixes-220"></a>Opgeloste fouten 2.2.0 ###

- [Opgeloste fout] Flash-versiedetectie werkt niet in Chrome/Microsoft Edge

### <a name="changes-220"></a>Wijzigingen 2.2.0 ###

- [Wijziging][AMP][Heuristiek] De naam van het heuristische profiel is gewijzigd van Quick Start in LowLatency
- [Wijziging][Flash] Wijziging in Flash-speler zodat versiedetectie het afspelen van AES-inhoud met de nieuwe Adobe Flash-update kan inschakelen.

## <a name="219-official-hotfix"></a>2.1.9 (officiële hotfix) ##

### <a name="bug-fixes-219"></a>Opgeloste fouten 2.1.9 ###

- [Opgeloste fout][Live] Er treedt een uitzondering op wanneer livestreams overgaan naar video-on-demand/live-archieven

### <a name="changes-219"></a>Wijzigingen 2.1.9 ###

- [Wijziging][Flash][AES] Flash-techlogica gewijzigd om sharedbytearrays niet te gebruiken voor AES-ontsleuteling, omdat Adobe het gebruik heeft geblokkeerd vanaf Flash 30. Denk erom dat afspelen alleen werkt wanneer Adobe een nieuwe versie van Flash implementeert vanwege een fout in V30. Raadpleeg [bekende problemen](azure-media-player-known-issues.md) voor meer informatie

## <a name="218-official-update"></a>2.1.8 (officiële update) ##

### <a name="bug-fixes-218"></a>Opgeloste fouten 2.1.8 ###

- [Opgeloste fout] Kringveld geeft af en toe post-zoeken en pre-afspelen niet weer
- [Opgeloste fout] Speler begint niet gedempt wanneer de optie voor dempen is ingeschakeld
- [Opgeloste fout] Volumeschuifregelaar wordt weergegeven wanneer besturingselementen zijn ingesteld op false
- [Opgeloste fout] Afspelen wordt af en toe herhaald wanneer gebruiker naar de live edge gaat
- [Opgeloste fout][Firefox] Speler genereert af en toe JavaScript-uitzondering bij laden
- [Opgeloste fout][Toegankelijkheid] Knop Afspelen/Onderbreken/Volume verliest focusomtrek wanneer deze wordt geselecteerd met toetsenbord
- [Opgeloste fout] Opgeloste geheugenlekkage op speler is verwijderd
- [Opgeloste fout] Door src() aan te roepen na fout in de speler, wordt de bron niet opnieuw ingesteld
- [Opgeloste fout][Live] AMP bevindt zich in een constante laadstatus wanneer de gebruiker op de knop Live klikt nadat de uitzending is beëindigd
- [Opgeloste fout][Chrome] De speler loopt vast en het afspelen mislukt wanneer de browser op de achtergrond is geminimaliseerd.

### <a name="changes-218"></a>Wijzigingen 2.1.8 ###

- [Wijziging] Fout 0x0600001 bijgewerkt om weer te geven wanneer AES-inhoud wordt afgespeeld met Flash 30, omdat dit op dit moment niet wordt ondersteund. Raadpleeg [bekende problemen](azure-media-player-known-issues.md) voor meer informatie
- [Wijziging] Er zijn nieuwe pogingen toegevoegd voor Live-scenario's wanneer het manifest verzoekt om 404 of lege manifesten retourneert.

## <a name="217-official-update"></a>2.1.7 (officiële update) ##

### <a name="features-217"></a>Functies 2.1.7 ###

- [Functie][AzureHtml5JS] Configuratie-optie toegevoegd voor het leegmaken van verouderde gegevens in de mediabronbuffer

### <a name="bug-fixes-217"></a>Opgeloste fouten 2.1.7 ###

- [Opgeloste fout][Toegankelijkheid][Schermlezer] De lege koptekst die is opgenomen in de speler wanneer de titel niet is ingesteld is verwijderd
- [Opgeloste fout][UWA] AMP genereert uitzondering bij afspelen in universele Windows-app
- [Opgeloste fout][OSX] setActiveTextTrack() werkt niet in Safari op OSx
- [Opgeloste fout][Live] Klikken op de live edge na het weglaten en opnieuw initialiseren van de speler genereert uitzondering
- [Opgeloste fout][Skin] Huidige tijd wordt afgekapt voor bepaalde assets
- [Opgeloste fout][DRM] fix opgenomen om ondersteuning te bieden voor afspelen in browsers die ondersteuning bieden voor meerdere CENC DRM

### <a name="changes-217"></a>Wijzigingen 2.1.7 ###

- [Wijziging][Voorbeelden][Toegankelijkheid] Er is een taalcodetag toegevoegd aan alle voorbeelden

## <a name="216-official-update"></a>2.1.6 (officiële update) ##

### <a name="bug-fixes-216"></a>Opgeloste fouten 2.1.6 ###

- [Opgeloste fout] AMP geeft de onjuiste duur weer voor specifieke asset
- [Opgeloste fout][FairPlay-HLS] Fairplay-fouten worden niet doorgegeven aan de gebruikersinterface
- [Opgeloste fout] Aangepaste heuristische eigenschappen worden genegeerd in AMP 2.1.5.

### <a name="changes-216"></a>Wijzigingen 2.1.6 ###

- [Wijziging][FairPlayDRM] De time-out voor zowel certificaataanvraag als licentie-aanvraag voor FairPlay is verwijderd om pariteit met PlayReady- en Widevine-implementaties te behouden
- [Wijziging] Diverse heuristische verbeteringen om wazige inhoud te bestrijden

### <a name="features-216"></a>Functies 2.16 ###

- [Functie] Ondersteuning toegevoegd voor indeling mpd-time-cmaf

## <a name="215-official-hotfix"></a>2.1.5 (officiële hotfix) ##

### <a name="bug-fixes-215"></a>Opgeloste fouten 2.1.5 ###

- [Opgeloste fout][Bijschriften] VTT-opmaak wordt niet correct weergegeven door de speler
- [Opgeloste fout][Toegankelijkheid] De Live-knop heeft geen aria-label

## <a name="214-official-update"></a>2.1.4 (officiële update) ##

### <a name="bug-fixes-214"></a>Opgeloste fouten 2.1.4 ###

- [Opgeloste fout][Toegankelijkheid][Focus] Gebruikers kunnen niet met behulp van de tabtoets focussen op aangepaste knoppen die aan de rechterkant van de knop Volledig scherm op de systeembalk zijn toegevoegd
- [Opgeloste fout][IE11][Volumebalk] Wanneer met de tabtoets naar de volumepop-up wordt gegaan, gaat het hele scherm in IE11 knipperen in de modus volledig scherm
- [Opgeloste fout][Skin|Leegmaken] Er wordt ruimte weergegeven tussen de systeembalk en de pop-up van de volumebalk
- [Opgeloste fout][AMP][Bijschriften] Oude ingesloten sporen worden niet gewist wanneer de bron wordt gewijzigd op een bestaande speler
- [Opgeloste fout][Toegankelijkheid][Narrator] Schermlezer leest volumeregeling onjuist
- [Opgeloste fout][FlashSS] Afspelen van een gebeurtenis kan af en toe niet worden gestart vanuit Flash-tech
- [Opgeloste fout][AMP][Focus] Voor afspelen/pauzeren zijn twee klikken vereist wanneer de speler de focus heeft en in de modus volledig scherm wordt weergegeven
- [Opgeloste fout][AMP][Skin] Er wordt een onjuiste duur weergegeven op de voortgangsbalk voor een specifieke asset
- [Opgeloste fout][Advertenties][Ad Butler] De parser VAST verwerkt geen VAST-bestand dat geen voortgangsgebeurtenis heeft
- [Opgeloste fout][SDN][AMP 2.1.1] Er is een probleem opgelost voor ondersteuning van Hive SDN-invoegtoepassing
- [Opgeloste fout][Toegankelijkheid] Verteller leest Midnight Mute Button wanneer gebruiker de focus op volumeknop heeft

### <a name="changes-214"></a>Wijzigingen 2.1.4 ###

- [Wijziging][Toegankelijkheid][Ondersteunende technologie] Knoppen hebben nu de eigenschap aria-live om de ervaring met ondersteunende technologie te verbeteren
- [Wijziging][Toegankelijkheid][Volumeknop|Verteller] Toegankelijkheid van de volumeknop is verbeterd door de tabfunctionaliteit en het gedrag van de schuifregelaar te wijzigen. Door deze wijzigingen kunnen toetsenbordgebruikers eenvoudiger het volume van de speler wijzigen
- [Wijziging] De time-out voor inactiviteit van het contextmenu is verhoogd van 3 naar 5 seconden
- [Wijziging][Toegankelijkheid][Helderheid] De helderheid van de contrastverhouding in vervolgkeuzemenu's in instellingen voor bijschriften is verbeterd

## <a name="213-official-update"></a>2.1.3 (officiële update) ##

### <a name="bug-fixes-213"></a>Opgeloste fouten 2.1.3 ###

- [Opgeloste fout][Invoegtoepassingen|Titel-overlay] De invoegtoepassing voor titel-overlay genereert JS-uitzonderingen met AMP v2.X+
- [Opgeloste fout] Gebeurtenis Source Set wordt verzonden naar JavaScript-console, zelfs wanneer logboekregistratie is uitgeschakeld
- [Opgeloste fout][Skin] Tijdtips van de speler worden buiten de context van de speler weergegeven wanneer met de muisaanwijzer een van de balken voor de eindduur wordt aangewezen
- [Opgeloste fout][Toegankelijkheid][Schermlezer] Verteller leest 'regio-oriëntatiepunt' of 'regio-oriëntatiepunt videospeler' wanneer de focus van de viewer is gericht op speler
- [Opgeloste fout][AMP] Kan de speleromtrek niet uitschakelen via CSS
- [Opgeloste fout][Toegankelijkheid] Kan tabtoets niet gebruiken om te focussen op de hele speler wanneer gebruiker in de modus volledig scherm is
- [Opgeloste fout][Skin][Live] Skin reageert niet op gelokaliseerde LIVE-tekst in het Japans
- [Opgeloste fout][Skin] Duur en huidige tijd worden afgekapt wanneer stream > 60 min -[Opgeloste fout][iPhone|Live] de speler geeft tekst weer voor de huidige tijd/duur in systeembalk
- [Opgeloste fout][AMP] Aanroepen van heuristieke API's van speler leidt tot JavaScript-uitzonderingen
- [Opgeloste fout][Systeemeigen Html5|iOS] Videotag-eigenschap 'playsinline' wordt niet doorgegeven aan speler
- [Opgeloste fout][iOS|iframe] De speler kan het volledige scherm op iPhone niet weergeven als de speler is geladen in een iframe
- [Opgeloste fout][AMP][Heuristiek] AMP werkt altijd met hybride profiel, ongeacht de opties voor de speler
- [Opgeloste fout][AMP|Win 8.1]wordt gegenereerd wanneer gehost in een win 8.1-app met een webweergave

### <a name="changes-213"></a>Wijzigingen 2.1.3 ###

- [Wijziging][AMP] Er is CDN-eindpuntinformatie toegevoegd aan de gebeurtenis FragmentDownloadComplete
- [Wijziging][AMP][Live] De latentie voor live streamen is verbeterd en geoptimaliseerd

## <a name="212-official-hotfix"></a>2.1.2 (officiële hotfix) ##

### <a name="bug-fixes-212"></a>Opgeloste fouten 2.1.2 ####

- [Opgeloste fout][Toegankelijkheids][Windows Verteller] Verteller leest 'Progress midnight' wanneer de gebruiker context van de voortgangsbalk heeft en de huidige tijd 0:00 is
- [Opgeloste fout][Skin] de logogrootte is vastgelegd in JavaScript-code
- [Toegankelijkheid][Sneltoetsen] Sneltoetsen niet ingeschakeld wanneer op de speler wordt geklikt.

### <a name="changes-212"></a>Wijzigingen 2.1.2 ####

- [Wijziging][Logboekregistratie] Manifest-URL registreren wanneer de speler het manifest niet kan laden

### <a name="features-212"></a>Functies 2.1.2 ###

- [Wijziging][Prestatie][Optimalisatie] De spelerbelasting en de opstarttijden zijn verbeterd

## <a name="211-official-update"></a>2.1.1 (officiële update) ##

### <a name="bug-fixes-211"></a>Opgeloste fouten 2.1.1 ####

- [Opgeloste fout][iOS] Wanneer Autoplay wordt ingesteld op False leidt dat tot een oneindig kringveld in Safari voor iOS
- [Opgeloste fout] Het zoeken naar een tijd die groter is dan de duur van de inhoud resulteert in een oneindig kringveld
- [Opgeloste fout] Sneltoetsen vereisen meerdere toetsenbordtabs om de context van de speler te laten werken
- [Opgeloste fout] Video wordt gedurende enkele seconden geblokkeerd na het wijzigen van de grootte van de speler in bepaalde assets
- [Opgeloste fout] Oneindig kringveld (nadat de zoekactie is voltooid) wanneer de gebruiker meerdere keren snel zoekt
- [Opgeloste fout] Systeembalk wordt niet verborgen tijdens inactiviteit
- [Opgeloste fout] Het openen van webapp die als host fungeert voor AMP kan ertoe leiden dat de webpagina tweemaal wordt geladen
- [Opgeloste fout] Oneindig tijdens het afspelen van inhoud van bepaalde activa via Flash Tech
- [Opgeloste fout] Het menu Meer opties wordt niet weergegeven met invoegtoepassingen van derden
- [Opgeloste fout][Skin|Tube][Live] Er worden twee live-pictogrammen weergegeven wanneer speler op de live edge van een programma is
- [Opgeloste fout][Skin] Logo kan niet worden uitgeschakeld
- [Opgeloste fout][DD + Inhoud] Doorlopend kringveld wordt weergegeven voor de assets met Dolby Digital-audiotrack
- [Opgeloste fout] De nieuwste AMP wordt geblokkeerd wanneer tijdens de livestream van audiotaalspoor wordt gewisseld
- [Opgeloste fout] het verdwijnen op achtergrond voor het kringveld is opgelost
- [Opgeloste fout] Oneindig kringveld in AES Flash-Token statische voor beelden van problemen met oplossingen

### <a name="changes-211"></a>Wijzigingen 2.1.1 ####

- [Wijziging] Er is foutcode toegevoegd voor https-vereiste van Widevine: vanaf Chrome v58 moet widevine-inhoud worden geladen/afgespeeld via het protocol `https://`, anders mislukt het afspelen.
- [Wijziging] Er is een aria-label toegevoegd om het kringveld te laden, zodat ondersteunende technologie 'video laden' kan lezen wanneer de inhoud wordt geladen  

## <a name="210-official-release"></a>2.1.0 (officiële versie) ##

### <a name="features-210"></a>Functies 2.1.0 ###

- [Functie][AzureHtml5JS] VOD Ad-ondersteuning voor rolls pre, mid en post
- [Functie][Bèta][AzureHtml5JS] Live Ad-ondersteuning voor rolls pre, mid en post
- [Functie] Er is een nieuwe skin-optie toegevoegd - AMP-leegmaken
- [Functie] Er zijn verbeterde aria-labels toegevoegd voor betere integratie met schermlezers/ondersteunende technologie
- [Functie][Skin] Skin toont nu alle pictogrammen en knoppen duidelijk in de modus Hoog contrast

### <a name="bug-fixes-210"></a>Opgeloste fouten 2.1.0 ###

- [Opgeloste fout] Aantal toegankelijkheids- en UI-correcties
- [Opgeloste fout] AMP wordt niet correct geladen in IE9

### <a name="changes-210"></a>Wijzigingen 2.1.0 ###

- [Wijziging] DOM-elementen in de speler zijn opnieuw gestructureerd voor advertentiewerk
- [Wijziging] Overgeschakeld van CSS naar SCSS voor skin-ontwikkeling
- [Wijziging][Voorbeelden] Er is een voorbeeld toegevoegd voor VOD-advertenties
- [Wijziging][Voorbeelden] Er is een voorbeeld toegevoegd voor afspeelsnelheid
- [Wijziging][Voorbeelden] Er is een voorbeeld toegevoegd voor Flush Skin

## <a name="200-beta-release"></a>2.0.0 (bètaversie) ##

- [Wijziging] bijgewerkt naar VJS5
- [Functie] Nieuwe vloeiende API toegevoegd voor vloeiende reacties van speler
- [Functie] Afspeelsnelheid
- [Wijziging] Overgeschakeld van CSS naar SCSS voor skin

## <a name="183-official-hotfix-update"></a>1.8.3 (officiële hotfix-update) ##

### <a name="bug-fixes-183"></a>Opgeloste fouten 1.8.3 ###

- [Opgeloste fout][AzureHtml5JS] Bepaalde activa met negatieve DTS worden niet afgespeeld in Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (officiële hotfix-update) ##

### <a name="bug-fixes-182"></a>Opgeloste fouten 1.8.2 ###

- [Opgeloste fout][AzureHtml5JS] Hogere audiobitrates worden niet afgespeeld via AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (officiële update) ##

### <a name="bug-fixes-181"></a>Opgeloste fouten 1.8.1 ###

- [Opgeloste fout][iOS] Bijschriften/ondertitels worden niet weergegeven in systeemeigen speler
- [Opgeloste fout][AMP] Door CDN-ondersteunde streaming-URL's worden toegevoegd met verificatietokens die niet worden afgespeeld
- [Opgeloste fout][Fairplay] FairPlay-foutcode mist Tech-ID (bits [31-28] van de foutcode) raadpleeg Foutcodes voor meer informatie
- [Opgeloste fout][Safari][PlayReady] PlayReady-inhoud in Safari genereert oneindig kringveld

### <a name="changes-181"></a>Wijzigingen 1.8.1 ###

- [Wijziging][Html5] Systeemeigen html5-technische uitgebreide logboeken wijzigen om gebeurtenissen van VideoTag op te nemen

## <a name="180-official-update"></a>1.8.0 (officiële update) ##

### <a name="features-180"></a>Functies 1.8.0 ###

- [Functies][DRM] Er is ondersteuning toegevoegd voor FairPlay (zie [Beveiligde inhoud](azure-media-player-protected-content.md) voor meer informatie)

### <a name="bug-fixes-180"></a>Opgeloste fouten 1.8.0 ###

- [Opgeloste fout][AMP] Zoekactie van gebruiker activeert geen wachtgebeurtenis wanneer het netwerk wordt beperkt
- [Opgeloste fout][FlashSS] Bij het selecteren van kwaliteit in flash-tech wordt een uitzondering gegenereerd
- [Opgeloste fout][AMP] Dynamisch selecteren van kwaliteit wordt weergegeven in contextmenu
- [Opgeloste fout][Skin] Het is moeilijk om het laatste menu-item van contextmenu's te selecteren

### <a name="changes-180"></a>Wijzigingen 1.8.0 ###

- [Wijziging] De speler is bijgewerkt naar de huidige Chrome EME-vereisten
- [Wijziging] De standaard techOrder is gewijzigd voor nieuwe tech-html5FairPlayHLS (zie [Beveiligde inhoud](azure-media-player-protected-content.md) voor meer informatie)
- [Wijziging][AzureHtml5JS] MPEG-Dash afspelen is ingeschakeld in Safari
- [Wijziging][Voorbeelden] Multi-DRM-voorbeelden gewijzigd voor FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (officiële hotfix-update) ##

### <a name="bug-fixes-174"></a>Opgeloste fouten 1.7.4 ###

- [Opgeloste fout][Chrome] Er wordt een blauwe contour weergegeven om de zoekgreep wanneer de gebruiker de context van de speler heeft
- [Opgeloste fout][IE9] Er wordt een JavaScript-uitzondering gegenereerd wanneer de speler wordt geladen in IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (officiële hotfix-update) ##

### <a name="bug-fixes-173"></a>Opgeloste fouten 1.7.3 ###

- [Opgeloste fout][AzureHtml5JS] Er treedt een time-out op van de speler in beperkte netwerken

### <a name="changes-173"></a>Wijzigingen 1.7.3 ###

- [Wijziging] Webcrypto inschakelen voor Microsoft Edge voor het ontsleutelen van AES-inhoud
- [Wijziging] AMP-heuristiek optimaliseren voor segmenten in de cache
- [Wijziging][AzureHtml5JS] Optimaliseer heuristiek door latentie van bandbreedteschatting te verlagen

## <a name="172-official-hotfix-update"></a>1.7.2 (officiële hotfix-update) ##

### <a name="features-172"></a>Functies 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [Functie][AzureHtml5JS|Firefox] Widevine-afspelen inschakelen met EME voor Firefox 47+
- [Functie] Gebeurtenis toevoegen voor afstoten van de speler
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Opgeloste fouten 1.7.2 ###

- [Opgeloste fout] De Akamai CDN URL-queryparameters zijn niet juist gedecodeerd
- [Opgeloste fout] Er is een uitzondering opgetreden in manifestPlayableWindowLength()
- [Opgeloste fout] De kijker kan niet altijd klikken op afspelen op de video nadat de video is gestopt om opnieuw te kijken
- [Opgeloste fout] Responsief vergroten/verkleinen voldoet niet aan snelle veranderingen van venstergrootte
- [Opgeloste fout][EDGE|IE] Responsief vergroten/verkleinen niet meegenomen bij laden van pagina's voor breedte=x, hoogte=auto
- [Opgeloste fout][Android|Chrome] Chrome vraagt om machtigingen voor het afspelen van DRM-inhoud wanneer de inhoud niet is versleuteld
- [Opgeloste fout][Toegankelijkheid][Microsoft Edge] Besturingselementen voor het toetsenbord selecteren contextmenu-items niet correct
- [Opgeloste fout][Toegankelijkheid] Ontbrekende weergegeven rand in de modus Hoog contrast
- [Opgeloste fout][FlashSS] Gebeurtenis-listener voor muis omhoog is niet verwijderd nadat door het verwijderen van de speler een uitzondering is veroorzaakt
- [Opgeloste fout][FlashSS] Probleem met parseren van manifest-URL met gecodeerde ruimten
- [Opgeloste fout][iOS] Fout type bij evalueren van tech.featuresVolumeControl

### <a name="changes-172"></a>Wijzigingen 1.7.2 ###

- [Wijziging][DRM] DRM-controles verplaatst na de ingestelde bron naar alleen controleren wanneer inhoud is versleuteld
- [Wijziging][AES] Niet-gedefinieerde hoofdtekst van het type/onbewerkt is verwijderd uit sleutelleveringsverzoek
- [Wijziging][Toegankelijkheid] Windows Verteller leest nu 'Media Player' wanneer de context op de speler is in plaats van op eigenschappen

## <a name="171-official-hotfix-update"></a>1.7.1 (officiële hotfix-update) ##

### <a name="features-171"></a>Functies 1.7.1 ###

- [Functie] Er is een optie toegevoegd voor hybride heuristisch profiel (dit profiel is standaard ingesteld)

### <a name="bug-fixes-171"></a>Opgeloste fouten 1.7.1 ###

- [Opgeloste fout] Responsief ontwerp werkt niet volgens HTML5-standaard (breedte=100%, hoogte=automatisch)
- [Opgeloste fout] Percentagewaarden voor breedte en hoogte werken niet zoals verwacht in v1.7.0

## <a name="170-official-update"></a>1.7.0 (officiële update) ##

### <a name="features-170"></a>Functies 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [Functie][AzureHtml5JS][FlashSS] currentMediaTime() toegevoegd om de encodermediatijd van de huidige tijd in seconden op te halen
- [Functie][FlashSS] Downloaden van telemetrie-API's geïmplementeerd met videoBufferData() en audioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [Functie][FlashSS] Gebeurtenis downloadbitratechanged toegevoegd
- [Functie] Laadtijd verbeterd vergeleken met oudere versies van speler
- [Functie] Fouten worden geregistreerd in JavaScript-console

### <a name="bug-fixes-170"></a>Opgeloste fouten 1.7.0 ###

- [Opgeloste fout] Gecodeerde poster-URL met querytekenreeksparameters worden niet weergegeven in speler
- [Opgeloste fout] Er wordt een uitzondering gegenereerd wanneer geen tech is geladen en API amp.Player.poster() wordt aangeroepen
- [Opgeloste fout] Er treedt een uitzondering op wanneer functies speler proberen te benaderen na verwijdering
- [Opgeloste fout][Toegankelijkheid] Overzicht ontbreekt bij focus op voortgangsbalk
- [Opgeloste fout][Toegankelijkheid] Contextmenu's hebben een schaduw in de modus Hoog contrast
- [Opgeloste fout][iOS] afspelen van bijschriften van systeemeigen speler WebVTT werkt niet
- [Opgeloste fout][AzureHtml5JS] Fout 0x0100002 moet worden weergegeven bij het afspelen van een HTTP-stroom op een HTTPS-site die in plaats daarvan oneindig kringveld genereert als resultaat van gemengde inhoud
- [Opgeloste fout][AzureHtml5JS] Ontbrekend eindsegment die een fout met de status van een lus veroorzaakt geeft een waargenomen oneindige bufferstatus weer
- [Opgeloste fout][AzureHtml5JS] Onjuiste naam voor audiospoor in het menu wanneer useManifestForLabel=false en taalcodes van drie letters worden gebruikt
- [Opgeloste fout][AzureHtml5JS|Chrome] Er is een oneindige bufferstatus waargenomen aan het einde van de inhoud die wordt veroorzaakt door drijvende-komma onnauwkeurigheid in de duur van JavaScript in Chrome
- [Opgeloste fout][FlashSS] Niet-fatale terugkerende fout wordt tijdelijk weergegeven wanneer Flash Player wordt gemaakt
- [Opgeloste fout][FlashSS] Afspelen mislukt wanneer video- en audiostromen verschillende tijdschalen gebruiken als gevolg van afrondingsonnauwkeurigheid die mislukken met Fragment url (...) kan FLVTags niet genereren
- [Opgeloste fout][FlashSS] Problemen met parseren van manifest-URL's met gecodeerde ruimten
- [Opgeloste fout][FlashSS] Er ontbreekt een controle om te bepalen of de Flash Player-versie >= 11,4 een fout bij afspelen veroorzaakt in plaats van terug te vallen op de volgende tech in de techOrder
- [Opgeloste fout][FlashSS][AES] Problemen met het accepteren van AES-tokens met onderstrepingstekens erin
- [Opgeloste fout][SilverlightSS|OSX] // als voorvoegsel bij een manifest in plaats van het protocol (HTTP of HTTPS) wordt herkend als een lokaal bestand dat een oneindig kringveld oplevert

### <a name="changes-170"></a>Wijzigingen 1.7.0 ###

- [Wijziging][FlashSS] SWF-scripts (MSAdaptiveStreamingPlugin-osmf 2.0.swf en StrobeMediaPlayback.2.0.swf) zijn samengevoegd in één SWF met de naam StrobeMediaPlayback.2.0.swf
- [Wijziging][FlashSS] De foutcodedoorgifte is bijgewerkt voor preciezere foutcodes (bijvoorbeeld 404's resulteren nu in 0x30200194 in plaats van algemene fout 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (officiële hotfix-update) ##

### <a name="bug-fixes-163"></a>Opgeloste fouten 1.6.3 ###

- [Opgeloste fout] JavaScript runtime-uitzondering wanneer de gebeurtenis-handler voor sneltoetsen wordt uitgevoerd na het verwijderen van de speler
- [Opgeloste fout][Android][AzureHtml5JS] Er wordt niet afgespeeld op mobiel apparaat met mobiel netwerk
- [Opgeloste fout] Forge is bijgewerkt om te worden uitgevoerd als webworker om gebruikersinterface vrij te maken

## <a name="162-official-hotfix-update"></a>1.6.2 (officiële hotfix-update) ##

### <a name="features-162"></a>Functies 1.6.2 ###

- [Functie] Er zijn extra talen toegevoegd voor lokalisatie (zie de documentatie voor meer informatie)

### <a name="bug-fixes-162"></a>Opgeloste fouten 1.6.2 ###

- [Opgeloste fout][IE9-10] Klikken op gebieden rond de speler minimaliseerde browservenster vanwege een IE9/IE10-fout die minimaliseert bij window.blur()
- [Opgeloste fout][FlashSS][AES] AES-tokens met onderstrepingstekens worden niet geaccepteerd

## <a name="161-official-hotfix-update"></a>1.6.1 (officiële hotfix-update) ##

### <a name="bug-fixes-161"></a>Opgeloste fouten 1.6.1 ###

- [Opgeloste fout][FlashSS|Edge,IE][SilverlightsSS|IE] Kan geen focus krijgen op andere elementen van de gebruikersinterface voor invoer of iets anders in IE/Microsoft Edge
- [Opgeloste fout] AES-afspelen mislukt wanneer forge niet is gedefinieerd
- [Opgeloste fout][Android][AzureHtml5JS|Chrome] Doorlopend kringveld speelt geen inhoud af wanneer in statuscontrolelus
- [Opgeloste fout][IE9] console.log () wordt niet ondersteund door IE 9, waardoor een uitzondering wordt veroorzaakt

## <a name="160-official-update"></a>1.6.0 (officiële update) ##

### <a name="features-160"></a>Functies 1.6.0 ###

- [Functie] Groottereductie van 33% van azuremediaplayer.min.js
- [Functie][AzureHtml5JS|Microsoft Edge][Niet-getest] Ondersteuning voor DD + audiostreams in Microsoft Edge (geen codec-switches na de eerste keuze). De app moet op dit moment de juiste audiostroom selecteren.
- [Functie] Besturingselementen voor sneltoetsen (zie docs voor meer informatie)
- [Functie] Tip voor voortgangstijd voor tijdnauwkeurig zoeken
- [Functie] Asynchrone detectie van invoegtoepassingen toestaan als de methode setupDone in de invoegtoepassing bestaat

### <a name="bug-fixes-160"></a>Opgeloste fouten 1.6.0 ###

- [Opgeloste fout] Het geheugenlogboek wordt niet leeggemaakt op getMemoryLog(true)
- [Opgeloste fout] Het selectievakje voor bitrate wordt opnieuw ingesteld bij het verplaatsen van de muis, wat een probleem veroorzaakt bij het selecteren van lagere bitrates middels muisbesturing
- [Opgeloste fout] Mac Office in de app loopt vast bij het uitvoeren van DRM-controle
- [Opgeloste fout] CSS-klassen kunnen eenvoudig per ongeluk worden overschreven
- [Opgeloste fout][Chrome] Het bijwerken van de id van de tekenreeksbrowser van de gebruikersagent is Microsoft Edge
- [Opgeloste fout][AzureHtml5JS] De knop Bijschriften wordt niet weergegeven in de werkbalk in Microsoft Edge (Win10) of Chrome (Mac)
- [Opgeloste fout][Android][AzureHtml5JS|Chrome] InvalidStateError-uitzondering op endOfStream()-aanroep op korte video's
- [Opgeloste fout][Firefox] Verwijderen van DRM-waarschuwing wordt veroorzaakt door Firefox bij het controleren van browsermogelijkheden
- [Opgeloste fout][Html5] Ondertitel/bijschriften worden niet weergegeven bij progressieve MP4-inhoud
- [Opgeloste fout][FlashSS] Berichten met overeenkomende tijdstempels zijn in omgekeerde volgorde geregistreerd
- [Opgeloste fout][Toegankelijkheid][Chrome|Firefox] Tab- en selectiebesturingselementen selecteren automatisch eerste menu-item
- [Opgeloste fout][Toegankelijkheids] Tab om de volumeknop te besturen

### <a name="changes-160"></a>Wijzigingen 1.6.0 ###

- [Wijziging] AES-ontsleutelingstijd gebruiken bij selectie van kwaliteitsniveau
- [Wijziging] URL ReWriter bijwerken om HLS v4 te gebruiken voor HLS v3 voor multi-audiostreams
- [Wijziging] nativeControlsForTouch instellen op false als standaard (moet false zijn om correct te werken)

## <a name="150-official-update"></a>1.5.0 (officiële update) ##

### <a name="features-150"></a>Functies 1.5.0 ###

- [Functie] Verbeteringen voor algemene webbeveiliging (ter voorkoming van injectie, XSS, enzovoort)
- [Functie] Integratiehooks van de SDN-invoegtoepassing voor de gebeurtenis sourceset en options.sdn
- [Functie] Robuuste verwerking van 5XX- en 4XX-fouten tijdens afspelen

### <a name="bug-fixes-150"></a>Opgeloste fouten 1.5.0 ###

- [Opgeloste fout] CSS-minificatie bijwerken om lettertypecodes van HTML-entiteit te gebruiken voor knoppen in plaats van Unicode
- [Opgeloste fout][AzureHtml5JS] Multi-DRM-inhoud selecteert altijd het token van het eerste element van protectionInfo waardoor de tweede DRM niet kan worden uitgevoerd
- [Opgeloste fout][AzureHtml5JS] Zoekbewerkingen worden nooit voltooid wanneer wordt gezocht in een gebied met ontbrekende segmenten.
- [Opgeloste fout][AzureHtml5JS|Microsoft Edge] Prefixed EME inschakelen in Microsoft Edge-update voor PlayReady-afspelen
- [Opgeloste fout][AzureHtml5JS|Firefox] EME-controle bijwerken zodat Firefox v42+ (met MSE) kan terugvallen op Silverlight voor beveiligde inhoud
- [Opgeloste fout][FlashSS] error.message bijwerken van nummer naar gedetailleerde tekenreeks

### <a name="changes-150"></a>Wijzigingen 1.5.0 ###

- [Wijziging] Posters werken momenteel alleen als absolute URL's.

## <a name="140-official-update"></a>1.4.0 (officiële update) ##

### <a name="features-140"></a>Functies 1.4.0 ###

- [Functie][AzureHtml5JS|Chrome] Eenvoudige Widevine DRM-ondersteuning
- [Functie][AzureHtml5JS] Robuuste verwerking van 404/412-fouten tijdens afspelen

### <a name="bug-fixes-140"></a>Opgeloste fouten 1.4.0 ###

- [Opgeloste fout][FlashSS] Uitbreiding voor parametervalidatie

## <a name="130-official-update"></a>1.3.0 (officiële update) ##

### <a name="features-130"></a>Functies 1.3.0 ###

- [Functie][AzureHtml5JS][FlashSS] Audioswitching van dezelfde codec multi-audio-inhoud

### <a name="bug-fixes-130"></a>Opgeloste fouten 1.3.0 ###

- [Opgeloste fout][AzureHtml5JS|Chrome] Onregelmatig oneindig kringveld
- [Opgeloste fout][AzureHtml5JS|IE][Windows Phone] Uitzondering waardoor Windows Phone afspeelproblemen ondervindt
- [Opgeloste fout][FlashSS] AutoPlay is ingesteld op False voor extra instanties
- [Opgeloste fout] Problemen met de grootte van UI-menu's

## <a name="120-official-update"></a>1.2.0 (officiële update) ##

### <a name="features-120"></a>Functies 1.2.0 ###

- [Functie][AzureHtml5JS|Firefox] Ondersteuning wanneer MSE is ingeschakeld
- [Functie] Het is niet langer nodig dat de app paden opgeeft voor binaire technische terugvalbestanden (SWF, XAP). Het pad is relatief ten opzichte van het Azure Media Player-script.

### <a name="bug-fixes-120"></a>Opgeloste fouten 1.2.0 ###

- [Opgeloste fout][AzureHtml5JS|Chrome] De speler wordt achter live edge geplaatst wanneer de speler op de achtergrond is
- [Opgeloste fout][AzureHtml5JS|Microsoft Edge] Volledig scherm werkt niet
- [Opgeloste fout][AzureHtml5JS] Logboekregistratie was niet correct ingeschakeld bij het instellen in opties
- [Opgeloste fout][Flash] Zowel buffering als het pictogram voor bufferen worden weergegeven tijdens wachtgebeurtenis
- [Opgeloste fout] Afspelen toestaan om door te gaan als de eerste bandbreedteaanvraag mislukt
- [Opgeloste fout] De speler kan niet worden geladen wanneer deze is geïnitialiseerd met niet-gedefinieerde opties
- [Opgeloste fout] Wanneer wordt geprobeerd de speler te verwijderen nadat deze al is verwijderd, treedt een vdata-uitzondering op
- [Opgeloste fout] De pictogrammen van de kwaliteitsbalk zijn onjuist toegewezen

## <a name="111-official-hotfix-update"></a>1.1.1 (officiële hotfix-update) ##

### <a name="bug-fixes-111"></a>Opgeloste fouten 1.1.1 ###

- [Opgeloste fout] Probleem met volledig scherm met oudere IE
- [Opgeloste fout] Invoegtoepassingen worden niet meer overschreven

## <a name="110-official-update"></a>1.1.0 (officiële update) ##

### <a name="features-110"></a>Functies 1.1.0 ###

- [Functie] Lokalisatietekenreeksen van gebruikersinterface bijwerken

### <a name="bug-fixes-110"></a>Opgeloste fouten 1.1.0 ###

- [Opgeloste fout] De grote afspeelknop heeft onvoldoende contrast
- [Opgeloste fout] Focusindicator van visueel element
- [Opgeloste fout] Selecteer het menu bitrate nu met de juiste resolutiegegevens
- [Opgeloste fout] De afmeting van het menu Meer opties wordt nu dynamisch aangepast
- [Opgeloste fout] Diverse problemen met de gebruikersinterface

## <a name="100-official-release"></a>1.0.0 (officiële versie) ##

### <a name="features-100"></a>Functies 1.0.0 ###

- [Functie] Basistoegankelijkheidstests voor tabbesturing, focusbeheer, schermlezer, gebruikersinterface met hoog contrast
- [Functie] Bijgewerkte gebruikersinterface
- [Functie] Dev-logboekregistratie
- [Functie] API voor het dynamisch instellen van bijschrift-/ondertitelsporen
- [Functie] Basisfuncties voor lokalisatie
- [Functie] Foutcodeconsolidatie in techs
- [Functie] Nieuwe foutcode voor wanneer invoegtoepassingen (zoals Flash of Silverlight) niet zijn geïnstalleerd
- [Functie][AzureHtml5JS] Diagnostische basisgebeurtenissen geïmplementeerd

### <a name="bug-fixes-100"></a>Opgeloste fouten 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Opgeloste fout][AzureHtml5JS] Live afspelen loopt vast bij MPD-updates wanneer er kleine onnauwkeurigheden in de tijdstempel zijn
- [Opgeloste fout][AzureHtml5JS] Meerdere problemen met live afspelen zijn opgelost
- [Opgeloste fout][AzureHtml5JS] Buffers leegmaken wanneer de heuristiek van de venstergrootte is ingeschakeld en naar een scherm met een hogere resolutie gaat
- [Opgeloste fout][AzureHtml5JS] Chrome toont nu op de juiste manier de beëindigde gebeurtenis. Gekoppeld aan het vorige bekende probleem van *Chrome verzendt geen beëindigde gebeurtenis wanneer AzureHtml5JS wordt gebruikt. Er is een probleem in de onderliggende browser.*
- [Opgeloste fout][AzureHtml5JS] Safari is voor deze tech uitgeschakeld ter oplossing van *Afspeelprobleem met OSX Yosemite met AzureHtml5JS-tech. Er zijn problemen met de implementatie van MSE. Tijdelijke correctie: flashSS, SilverlightSS afdwingen als tech order van deze gebruikersagents in Silverlight*
- [Opgeloste fout][FlashSS] loadstart gestart nadat de fout is opgetreden

## <a name="020-beta"></a>0.2.0 (bètaversie) ##

### <a name="features-020"></a>Functies 0.2.0 ###

- [Functie] Tests voor PlayReady en AES zijn voltooid voor on-demand en live - zie compatibiliteitsmatrix
- [Functie] Verwerkingen afhandelen
- [Functie] Ondersteuning voor tijdstempels die groter zijn dan 2^53
- [Functie] URL-queryparameter blijft in de manifest-aanvraag
- [Functie][Niet getest] Ondersteuning voor de heuristische profielen `QuickStart` en `HighQuality`
- [Functie][Niet getest] Informatie over videogegevensstroom weergeven voor de bitrates, breedte en hoogte van AzureHtml5JS en FlashSS
- [Functie][Niet getest] Selecteer de bitrate op AzureHtml5JS en FlashSS (zie API-documentatie)

### <a name="bug-fixes-020"></a>Opgeloste fouten 0.2.0 ###

- [Opgeloste fout] de grote afspeelknop kan nu worden weergegeven op WP 8.1
- [Opgeloste fout] er zijn meerdere problemen met live-afspelen opgelost
- [Opgeloste fout] knop om dempen op te heffen, werkt nu in de UI
- [Opgeloste fout] ervaring met laden van UI bijgewerkt voor de modus voor automatisch afspelen
- [Opgeloste fout] Probleem met AMD-loader en methodeconflicten definiëren
- [Opgeloste fout] Probleem met het laden van WP 8.1 Cordova-app
- [Opgeloste fout] Beveiligd platform/tech voor inhoudsquery's platform/tech ondersteunde beveiligingstype om de juiste technologie te selecteren voor afspelen.  Hiermee wordt het eerder bekende probleem opgelost van _PlayReady-inhoud op Chrome (Desktop)/Safari 8 (op OSX Yosemite) ondersteunt momenteel teruggevallen op Silverlight-speler niet_
- [Opgeloste fout] niet-onderschepte uitzondering op WinServer 2012 R2, omdat Media Foundation standaard niet op die machine is geïnstalleerd.  Probeert API's voor HTML-videotags te gebruiken, die niet zijn geïmplementeerd, waardoor er een fout optreedt. De huidige correctie is om deze fout te ondervangen en false te retourneren in plaats van de fout te genereren.
- [Opgeloste fout] het init-segment altijd ophalen na een zoek-of HTTP-fout om fouten tijdens het afspelen te voorkomen
- [Opgeloste fout] het bijhouden van gesimuleerde voortgang en timeupdates uitschakelen wanneer er een fout is opgetreden.
- [Opgeloste fout] Rechtsklikmenu verwijderen
- [Opgeloste fout][AzureHtml5JS] foutbericht wordt niet weergegeven wanneer ongeldig token is ingesteld voor PlayReady-inhoud
- [Opgeloste fout][AzureHtml5JS] tijdens het afspelen op volledig scherm werd geen rekening gehouden met heuristiek van venstergrootte
- [Opgeloste fout][FlashSS] Verwijderde stroboscopisch Media Player gaf berichten weer, zodat alleen Azure Media Player-berichten worden weergegeven
- [Opgeloste fout][SilverlightSS] de gezochte gebeurtenis niet ophalen wanneer de duur van de zoekopdracht minder dan 0 is

## <a name="010-beta-release"></a>0.1.0 (bètaversie) ##

Oorspronkelijke voorlopige versie

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
