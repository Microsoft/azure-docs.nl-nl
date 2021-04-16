---
title: Azure Media Services Video Indexer opmerkingen bij de release | Microsoft Docs
description: Om op de hoogte te blijven van de nieuwste ontwikkelingen, vindt u in dit artikel de nieuwste updates over Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.custom: references_regions
ms.date: 03/30/2021
ms.author: juliako
ms.openlocfilehash: b3602d421718cbd1de3509751491ec6db65b1b01
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532893"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer opmerkingen bij de release

>Ontvang een melding wanneer u deze pagina opnieuw moet bezoeken voor updates door deze URL te kopiëren en in uw `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` RSS-feedlezer te kopiëren en te kopiëren.

Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

* De nieuwste releases
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functionaliteit

## <a name="march-2021"></a>Maart 2021

### <a name="audio-analysis"></a>Audioanalyse 

Audio-analyse is nu beschikbaar in een extra nieuwe bundel audiofuncties voor verschillende prijs. De nieuwe **standaardinstelling voor standaard** audioanalyse biedt een voordelige optie om alleen spraaktranscriptie, vertaling en indeling van uitvoerbijschriften en ondertiteling te extraheren. De **standaardinstelling Basic Audio** produceert twee afzonderlijke meters op uw factuur, waaronder een regel voor transcriptie en een afzonderlijke regel voor het opmaken van bijschriften en ondertiteling. Zie de pagina met prijzen Media Services meer [informatie over de](https://azure.microsoft.com/pricing/details/media-services/) prijzen.

De zojuist toegevoegde bundel is beschikbaar bij het indexeren of opnieuw indexeren van uw bestand door de geavanceerde optie Standaard audio vooraf te kiezen (onder de vervolgkeuzevak  ->   Video **+ audio** indexeren).

### <a name="new-developer-portal"></a>Nieuwe ontwikkelaarsportal 

Video Indexer heeft een nieuwe [](https://api-portal.videoindexer.ai/)ontwikkelaarsportal, probeert de nieuwe Video Indexer-API's uit en vindt alle relevante resources op één plek: [GitHub-opslagplaats,](https://github.com/Azure-Samples/media-services-video-indexer) [Stack overflow,](https://stackoverflow.com/questions/tagged/video-indexer) [Video Indexer tech-community](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) met relevante blogberichten, [Video Indexer](faq.md)Veelgestelde vragen, [User Voice](https://feedback.azure.com/forums/932041-cognitive-services?category_id=399016) om uw feedback te geven en functies voor te stellen, en ['CodePen'-koppeling](https://codepen.io/videoindexer) met codevoorbeelden voor widgets. 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Geavanceerde aanpassingsmogelijkheden voor inzichtwidget 

SDK is nu beschikbaar voor het insluiten Video Indexer widget inzichten in uw eigen service en om de stijl en gegevens aan te passen. De SDK ondersteunt de standaard widget Video Indexer inzichten en een volledig aanpasbare inzichtenwidget. Codevoorbeeld is beschikbaar in [Video Indexer GitHub-opslagplaats](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization). Met deze geavanceerde aanpassingsmogelijkheden kan de ontwikkelaar van oplossingen aangepaste stijlen toepassen en de eigen AI-gegevens van de klant gebruiken en deze presenteren in de inzichtwidget (met of zonder Video Indexer inzichten). 

### <a name="video-indexer-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Indexer geïmplementeerd in US - noord-centraal, US - west en Canada - centraal 

U kunt nu een betaald Video Indexer maken in de regio's US - noord-centraal, US - west en Canada - centraal regio's
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Ondersteuning voor nieuwe brontalen voor spraak-naar-tekst (STT), vertaling en zoeken 

Video Indexer biedt nu ondersteuning voor STT, vertaling en zoeken in Deens ('da-DK'), Noors('nb-NO'), Zweeds('sv-SE'), Fins('fi-FI'), Frans ('fr-CA'), Thai('th-TH'), Arabisch ('ar-WANT', 'ar-EG', 'ar-IQ', 'ar-JO', 'ar-KW', 'ar-LB', 'ar-OM', 'ar-QA', 'ar-S' en 'ar-SY' en Turks('tr-TR'). Deze talen zijn beschikbaar op zowel de API- als Video Indexer website. 
 
### <a name="search-by-topic-in-video-indexer-website"></a>Zoeken op onderwerp in Video Indexer website 

U kunt nu de zoekfunctie bovenaan de pagina Video Indexer [gebruiken](https://www.videoindexer.ai/account/login) om te zoeken naar video's met specifieke onderwerpen. 

## <a name="february-2021"></a>Februari 2021

### <a name="multiple-account-owners"></a>Meerdere accounteigenaren 

De rol accounteigenaar is toegevoegd aan Video Indexer. U kunt gebruikers toevoegen, wijzigen en verwijderen; hun rol te wijzigen. Zie Invite users (Gebruikers uitnodigen) voor meer informatie over het [delen van een account.](invite-users.md)

### <a name="audio-event-detection-public-preview"></a>Detectie van audiogebeurtenissen (openbare preview)

> [!NOTE]
> Deze functie is alleen beschikbaar in proefaccounts. 

Video Indexer detecteert nu de volgende audio-effecten in de niet-spraaksegmenten van de inhoud: gunshot, glassurken, alarm, siren, explosion, dog dog dog, booing, crowd reactions (herkenning, clapping en booing) en Stilte. 

De zojuist toegevoegde functie voor audio-invloeden is beschikbaar bij het indexeren van uw bestand door de geavanceerde optie Geavanceerde audiovoorinstelling te kiezen  ->   (onder Video en audio indexeren). Standaardindexering omvat alleen stilte **en** **reacties van de massa.** 

Het **gebeurtenistype voor clapping** dat is opgenomen in het vorige model voor audio-effecten, is nu geëxtraheerd als onderdeel van het gebeurtenistype **crowdreactie.**

Wanneer u ervoor kiest om **Inzichten van** uw video weer te geven op de Video Indexer website, worden de audio-effecten weergegeven op de pagina. [](https://www.videoindexer.ai/)

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Detectie van audiogebeurtenissen":::

### <a name="named-entities-enhancement"></a>Verbeterde benoemde entiteiten  

De uitgepakte lijst met personen en locatie is uitgebreid en in het algemeen bijgewerkt. 

Daarnaast bevat het model nu personen en locaties in de context die niet beroemde zijn, zoals een 'Sam' of 'Home' in de video. 

## <a name="january-2021"></a>Januari 2021

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer is geïmplementeerd in de cloud van de Amerikaanse overheid 

U kunt nu een betaald Video Indexer account maken in de cloud van de Amerikaanse overheid in de regio's Virginia en Arizona. Video Indexer gratis proefversie is niet beschikbaar in de genoemde regio. Ga voor meer informatie naar Video Indexer Documentatie. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Video Indexer geïmplementeerd in de regio India - centraal 

U kunt nu een betaald Video Indexer maken in de regio India - centraal. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Nieuwe donkere modus voor de Video Indexer website-ervaring

De Video Indexer website is nu beschikbaar in de donkere modus. Als u de donkere modus wilt inschakelen, opent u het instellingenvenster en schakelt u de **optie Donkere** modus in. 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Instelling voor donkere modus":::

## <a name="december-2020"></a>December 2020

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Indexer geïmplementeerd in de Zwitserland - west en Zwitserland - noord

U kunt nu een betaald Video Indexer maken in de Zwitserland - west en Zwitserland - noord regio's.

## <a name="october-2020"></a>Oktober 2020

### <a name="animated-character-identification-improvements"></a>Verbeteringen in de identificatie van animaties  

Video Indexer ondersteunt detectie, groepering en herkenning van tekens in animaties via integratie met Cognitive Services Custom Vision. We hebben een belangrijke verbetering toegevoegd aan dit AI-algoritme in de detectie en de herkenning van tekens, omdat de nauwkeurigheid van het inzicht en geïdentificeerde tekens aanzienlijk worden verbeterd.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>Wijzigingen Video Indexer de authenticatie van geplande websites

Vanaf 1 maart 2021 kunt u zich niet meer registreren en aanmelden bij de ontwikkelaarsportal van [de Video Indexer-website](https://www.videoindexer.ai/) [](video-indexer-use-apis.md) via Facebook of LinkedIn.

U kunt zich registreren en aanmelden met een van deze providers: Azure AD, Microsoft en Google.

> [!NOTE]
> De Video Indexer-accounts die zijn verbonden met LinkedIn en Facebook, zijn niet meer toegankelijk na 1 maart 2021. 
> 
> U moet [een](invite-users.md) e-mail van Azure AD, Microsoft of Google uitnodigen die u hebt voor het Video Indexer-account, zodat u nog steeds toegang hebt. U kunt een extra eigenaar van ondersteunde providers toevoegen, zoals beschreven in [Uitnodigen.](invite-users.md) <br/>
> U kunt ook een betaald account maken en de gegevens migreren.

## <a name="august-2020"></a>Augustus 2020

### <a name="mobile-design-for-the-video-indexer-website"></a>Mobiel ontwerp voor de Video Indexer website

De Video Indexer website ondersteunt nu mobiele apparaten. De gebruikerservaring is responsief om zich aan te passen aan de grootte van uw mobiele scherm (met uitzondering van aanpassings-API's). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Toegankelijkheidsverbeteringen en oplossingen voor fouten 

Als onderdeel van WCAG (Richtlijnen voor toegankelijkheid van webinhoud) is de Video Indexer website-ervaring afgestemd op klasse C, als onderdeel van microsoft-toegankelijkheidsstandaarden. Er zijn verschillende fouten en verbeteringen met betrekking tot toetsenbordnavigatie, programmatische toegang en schermlezer opgelost. 

## <a name="july-2020"></a>Juli 2020

### <a name="ga-for-multi-language-identification"></a>GA voor identificatie in meerdere talen

Identificatie in meerdere talen is verplaatst van de preview-versie naar de ga-versie en klaar voor productief gebruik.

Er zijn geen gevolgen voor de prijzen met betrekking tot de overgang 'Preview naar GA'.

### <a name="video-indexer-website-improvements"></a>Video Indexer website verbeteren

#### <a name="adjustments-in-the-video-gallery"></a>Aanpassingen in de videogalerie

Er is een nieuwe zoekbalk toegevoegd voor zoeken in deep insights met aanvullende filtermogelijkheden. Zoekresultaten zijn ook verbeterd.

Nieuwe lijstweergave met de mogelijkheid om videoarchieven met meerdere bestanden te sorteren en te beheren.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Nieuw deelvenster voor eenvoudige selectie en configuratie

Zijpaneel voor eenvoudige selectie en gebruikersconfiguratie is toegevoegd, zodat u eenvoudig en snel een account kunt maken en delen en de configuratie kunt instellen.

Zijpaneel wordt ook gebruikt voor gebruikersvoorkeuren en help.

## <a name="june-2020"></a>Juni 2020

### <a name="search-by-topics"></a>Zoeken op onderwerpen

U kunt nu de zoek-API gebruiken om te zoeken naar video's met specifieke onderwerpen (alleen API).

Onderwerpen worden toegevoegd als onderdeel van de `textScope` (optionele parameter). Zie [API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos) voor meer informatie.  

### <a name="labels-enhancement"></a>Labels verbeteren

De labeltagger is bijgewerkt en bevat nu meer visuele labels die kunnen worden geïdentificeerd.

## <a name="may-2020"></a>Mei 2020

### <a name="video-indexer-deployed-in-the-east-us"></a>Video Indexer geïmplementeerd in VS - oost

U kunt nu een betaald Video Indexer maken in de regio VS - oost.
 
### <a name="video-indexer-url"></a>Video Indexer URL

Video Indexer regionale eindpunten zijn samengevoegd om alleen met www te beginnen. Er is geen actie-item vereist.

Vanaf nu kunt u zien www.videoindexer.ai het is voor het insluiten van widgets of het aanmelden bij Video Indexer webtoepassingen.

Ook wus.videoindexer.ai worden omgeleid naar www. Meer informatie is beschikbaar in [Insluiten Video Indexer widgets in uw apps.](video-indexer-embed-widgets.md)

## <a name="april-2020"></a>April 2020

### <a name="new-widget-parameters-capabilities"></a>Mogelijkheden voor nieuwe widgetparameters

De **widget Inzichten** bevat nieuwe parameters: en `language` `control` .

De **widget Speler** heeft een nieuwe `locale` parameter. De `locale` parameters en bepalen de taal van de `language` speler.

Zie de sectie [widgettypen voor meer](video-indexer-embed-widgets.md#widget-types) informatie. 

### <a name="new-player-skin"></a>Nieuwe speler skin

Er wordt een nieuwe speler-skin gelanceerd met bijgewerkt ontwerp.

### <a name="prepare-for-upcoming-changes"></a>Voorbereiden op toekomstige wijzigingen

* Vandaag retourneren de volgende API's een accountobject:

    * [Betaald account maken](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Get-Accounts-with-Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    Het object Account heeft een `Url` veld dat verwijst naar de locatie van de [Video Indexer website](https://www.videoindexer.ai/).
Voor betaalde accounts `Url` verwijst het veld momenteel naar een interne URL in plaats van de openbare website.
In de komende weken wijzigen we deze en retourneren we de URL [Video Indexer website](https://www.videoindexer.ai/) voor alle accounts (proefversie en betaald).

    Gebruik niet de interne URL's. Gebruik de Video Indexer [API's](https://api-portal.videoindexer.ai/).
* Als u Video Indexer-URL's insluit in uw toepassingen en de URL's niet naar de [Video Indexer-website of](https://www.videoindexer.ai/) het Video Indexer API-eindpunt ( ) maar naar een regionaal eindpunt (bijvoorbeeld ) wijzen, worden de URL's opnieuw `https://api.videoindexer.ai` `https://wus2.videoindexer.ai` gemaakt.

   U kunt dit doen door:

    * Vervang de URL door een URL die verwijst naar Video Indexer widget-API's (bijvoorbeeld de [widget inzichten](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget))
    * Gebruik de Video Indexer om een nieuwe ingesloten URL te genereren:
         
         Druk **op Afspelen** om naar de pagina van uw video te gaan. Klik > op de knop **&lt; / &gt; Insluiten** om > URL naar uw toepassing te kopiëren:
   
    De regionale URL's worden niet ondersteund en worden de komende weken geblokkeerd.

## <a name="january-2020"></a>Januari 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Ondersteuning voor aangepaste talen voor aanvullende talen

Video Indexer ondersteunt nu aangepaste taalmodellen voor `ar-SY` `en-UK` , en `en-AU` (alleen API).
 
### <a name="delete-account-timeframe-action-update"></a>Actie-update voor het tijdsbestek van het account verwijderen

Met de actie Account verwijderen wordt het account nu binnen 90 dagen verwijderd in plaats van 48 uur.
 
### <a name="new-video-indexer-github-repository"></a>Nieuwe Video Indexer GitHub-opslagplaats

Een nieuwe Video Indexer GitHub met verschillende projecten, aan de slag-handleidingen en codevoorbeelden is nu beschikbaar: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger-update

Video Indexer **verificaties en** bewerkingen **in** één enkele [Video Indexer OpenAPI-specificatie (swagger) .](https://api-portal.videoindexer.ai/api-details#api=Operations&operation) Ontwikkelaars kunnen de API's vinden in [Video Indexer Developer Portal.](https://api-portal.videoindexer.ai/)

## <a name="december-2019"></a>December 2019

### <a name="update-transcript-with-the-new-api"></a>Transcriptie bijwerken met de nieuwe API

Werk een specifieke sectie in de transcriptie bij met [behulp van de API Update-Video-Index.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Accountconfiguratie herstellen vanuit de Video Indexer portal

U kunt nu de Media Services bijwerken om zelfhulp te bieden bij problemen zoals: 

* onjuiste Azure Media Services resource
* wachtwoordwijzigingen
* Media Services zijn verplaatst tussen abonnementen  

Als u de accountconfiguratie wilt herstellen, gaat u in Video Indexer portal naar > tabblad Instellingen en account (als eigenaar).

### <a name="configure-the-custom-vision-account"></a>Het Custom Vision-account configureren

Configureer het Custom Vision-account voor betaalde accounts met behulp van Video Indexer portal (voorheen werd dit alleen ondersteund door de API). Als u dit wilt doen, meld u zich aan bij Video Indexer portal, kiest u Modelaanpassing > animaties > Configureren. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scènes, opnamen en keyframes, nu in één inzichtvenster

Scènes, opnamen en keyframes worden nu samengevoegd in één inzicht voor eenvoudiger gebruik en eenvoudigere navigatie. Wanneer u de gewenste scène selecteert, kunt u zien uit welke opnamen en keyframes deze bestaat. 

### <a name="notification-about-a-long-video-name"></a>Melding over een lange videonaam

Wanneer de naam van een video langer is dan 80 tekens, Video Indexer bij het uploaden een beschrijvende fout weergegeven.

### <a name="streaming-endpoint-is-disabled-notification"></a>Melding dat het streaming-eindpunt is uitgeschakeld

Wanneer het streaming-eindpunt is uitgeschakeld, Video Indexer een beschrijvende fout weergegeven op de spelerpagina.

### <a name="error-handling-improvement"></a>Verbetering van foutafhandeling

Statuscode 409 wordt nu geretourneerd door de API's Video opnieuw [indexeren](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) en [Video Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) bijwerken voor het geval een video actief wordt geïndexeerd, om te voorkomen dat de huidige wijzigingen in de index per ongeluk worden overschrijven.

## <a name="november-2019"></a>November 2019
 
* Ondersteuning voor aangepaste taalmodellen koreaans

    Video indexer ondersteunt nu aangepaste taalmodellen in koreaans ( `ko-KR` ) in zowel de API als de portal. 
* Nieuwe talen die worden ondersteund voor spraak-naar-tekst (STT)

    Video Indexer-API's bieden nu ondersteuning voor STT in de Arabische Levantine (ar-SY), engels ENGELS ENGELS dialect (en-GB) en Engels Australisch dialect (en-AU).
    
    Voor het uploaden van video hebben we zh-HANS vervangen door zh-CN. Beide worden ondersteund, maar zh-CN wordt aanbevolen en nauwkeuriger.
    
## <a name="october-2019"></a>Oktober 2019
 
* Zoeken naar animaties in de galerie

    Wanneer u animaties indexeert, kunt u deze nu zoeken in de videogalley van het account. Zie Herkenning van [animaties voor meer informatie.](animated-characters-recognition.md)

## <a name="september-2019"></a>September 2019
 
Meerdere verbeteringen aangekondigd op IBC 2019:
 
* Herkenning van animaties (openbare preview)

    De mogelijkheid om tekens in animaties van groeps-ad-herkennen te detecteren via integratie met Custom Vision. Zie Detectie van animaties [voor meer informatie.](animated-characters-recognition.md)
* Identificatie met meerdere talen (openbare preview)

    Segmenten in meerdere talen in het audiospoor detecteren en op basis daarvan een meertalige transcriptie maken. Eerste ondersteuning: Engels, Spaans, Duits en Frans. Zie [Inhoud in meerdere talen automatisch identificeren en transcriberen](multi-language-identification-transcription.md) voor meer informatie.
* Extractie van benoemde entiteiten voor Personen en Locatie

    Extraheert merken, locaties en personen uit spraak- en visuele tekst via NLP (Natural Language Processing).
* Classificatie van het type in een hoofdartikel

    Het taggen van opnamen met redactionele typen, zoals close-up, gemiddelde opname, twee keer binnen, buiten, enzovoort. Zie Voor meer informatie De detectie [van het type hoofdschermafbeelding.](scenes-shots-keyframes.md#editorial-shot-type-detection)
* Uitbreiding van onderwerpdeferencing - nu niveau 2
    
    Het model voor onderwerpdeductie ondersteunt nu een diepere granulariteit van de IPTC-taxonomie. Lees de volledige details [op Azure Media Services nieuwe innovatie op basis van AI.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>Augustus 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer geïmplementeerd in VK - zuid

U kunt nu een betaald account Video Indexer in de regio VK - zuid.

### <a name="new-editorial-shot-type-insights-available"></a>Er zijn nieuwe inzichten voor het type hoofdartikels beschikbaar

Nieuwe tags die aan video-opnamen zijn toegevoegd, bieden een aantal hoofdinhoudstypen om ze te identificeren met veelgebruikte woordgroepen die worden gebruikt in de werkstroom voor het maken van inhoud, zoals: extreme close-up, close-up, breed, gemiddeld, twee keer buiten, binnen, linker- en rechtergezicht (beschikbaar in de JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extractie van nieuwe personen- en locaties-entiteiten beschikbaar

Video Indexer identificeert benoemde locaties en personen via natuurlijke taalverwerking (NLP) van OCR en transcriptie van de video. Video Indexer maakt gebruik van machine learning algoritme om te herkennen wanneer specifieke locaties (bijvoorbeeld de Tower Tower) of personen (bijvoorbeeld John Doe) worden opgeroepen in een video.

### <a name="keyframes-extraction-in-native-resolution"></a>Extractie van keyframes in native resolutie

Keyframes die zijn geëxtraheerd Video Indexer zijn beschikbaar in de oorspronkelijke resolutie van de video.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA voor het trainen van aangepaste gezichtsmodellen van afbeeldingen

Trainingsgezichten van afbeeldingen zijn verplaatst van de Preview-modus naar ga (beschikbaar via API en in de portal).

> [!NOTE]
> Er zijn geen gevolgen voor de prijzen met betrekking tot de overgang 'Preview naar GA'.

### <a name="hide-gallery-toggle-option"></a>De optie Galerie-schakelaar verbergen

De gebruiker kan ervoor kiezen om het tabblad Galerie te verbergen in de portal (vergelijkbaar met het verbergen van het tabblad Voorbeelden).
 
### <a name="maximum-url-size-increased"></a>Maximale URL-grootte verhoogd

Ondersteuning voor URL-queryreeks van 4096 (in plaats van 2048) bij het indexeren van een video.
 
### <a name="support-for-multi-lingual-projects"></a>Ondersteuning voor meertalige projecten

Projecten kunnen nu worden gemaakt op basis van video's die in verschillende talen zijn geïndexeerd (alleen API).

## <a name="july-2019"></a>Juli 2019

### <a name="editor-as-a-widget"></a>Editor als widget

De Video Indexer AI-editor is nu beschikbaar als een widget die kan worden ingesloten in klanttoepassingen.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aangepast taalmodel bijwerken vanuit een ondertitelingsbestand vanuit de portal

Klanten kunnen VTT-, SRT- en TTML-bestandsindelingen leveren als invoer voor taalmodellen op de aanpassingspagina van de portal.

## <a name="june-2019"></a>Juni 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer geïmplementeerd in Japan - oost

U kunt nu een betaald Video Indexer maken in de regio Japan - oost.

### <a name="create-and-repair-account-api-preview"></a>Account-API maken en herstellen (preview)

Er is een nieuwe API toegevoegd waarmee u het eindpunt of de sleutel van de [Azure Media Service-verbinding kunt bijwerken.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)

### <a name="improve-error-handling-on-upload"></a>Foutafhandeling bij uploaden verbeteren 

Er wordt een beschrijvend bericht geretourneerd in het geval van een onjuiste configuratie van het onderliggende Azure Media Services account.

### <a name="player-timeline-keyframes-preview"></a>Preview van keyframes op de tijdlijn van de speler 

U ziet nu een voorbeeld van een afbeelding voor elke keer op de tijdlijn van de speler.

### <a name="editor-semi-select"></a>Editor semi-select

U kunt nu een voorbeeld bekijken van alle inzichten die zijn geselecteerd als gevolg van het kiezen van een specifiek inzicht in de editor.

## <a name="may-2019"></a>Mei 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aangepast taalmodel bijwerken vanuit ondertitelingsbestand

[Api's voor aangepast taalmodel](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) maken en Aangepaste taalmodellen bijwerken ondersteunen nu VTT-, SRT- en TTML-bestandsindelingen als invoer voor taalmodellen. [](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model)

Bij het [aanroepen van de API voor het bijwerken van videotranscripties](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)wordt de transcriptie automatisch toegevoegd. Het trainingsmodel dat aan de video is gekoppeld, wordt ook automatisch bijgewerkt. Zie Een taalmodel aanpassen met een Video Indexer voor meer informatie over het aanpassen en trainen [van uw taalmodellen.](customize-language-model-overview.md)

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nieuwe transcriptindelingen downloaden - TXT en CSV

Naast de ondertitelingsindeling die al wordt ondersteund (SRT, VTT en TTML), ondersteunt Video Indexer nu het downloaden van de transcriptie in TXT- en CSV-indelingen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](video-indexer-overview.md)
