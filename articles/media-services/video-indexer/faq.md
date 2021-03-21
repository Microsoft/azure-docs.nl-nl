---
title: Veelgestelde vragen over Video Indexer-Azure
titleSuffix: Azure Media Services
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/12/2020
ms.author: juliako
ms.openlocfilehash: 0fc28a1f808eeb2977b1dcca5046ed29933b8aa8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028791"
---
# <a name="video-indexer-frequently-asked-questions"></a>Veelgestelde vragen over Video Indexer

In dit artikel vindt u antwoorden op veelgestelde vragen over Video Indexer.

## <a name="general-questions"></a>Algemene vragen

### <a name="what-is-video-indexer"></a>Wat is Video Indexer?

Video Indexer is een kunst matige intelligentie service die deel uitmaakt van Microsoft Azure Media Services. Video Indexer biedt een indeling van meerdere machine learning modellen waarmee u gemakkelijk diep inzicht kunt verkrijgen op basis van een video. Video Indexer maakt gebruik van meerdere kanalen van de video om geavanceerde en nauw keurige inzichten te bieden: Audio, spraak en visuele elementen. De inzichten van Video Indexer kunnen op verschillende manieren worden gebruikt, zoals het verbeteren van de detectie van inhoud en toegankelijkheid, het maken van nieuwe verdiensten maximaliseren-mogelijkheden of het bouwen van nieuwe ervaringen die gebruikmaken van de inzichten. Video Indexer biedt een webinterface voor het testen, configureren en aanpassen van modellen in uw account. Ontwikkel aars kunnen een op REST gebaseerde API gebruiken om Video Indexer te integreren in het productie systeem. 

### <a name="what-can-i-do-with-video-indexer"></a>Wat kan ik doen met Video Indexer?

Enkele van de bewerkingen die Video Indexer op media bestanden kunnen uitvoeren, zijn:

* Het identificeren en uitpakken van spraak en het identificeren van luid sprekers.
* Het identificeren en uitpakken van tekst op het scherm in een video.
* Objecten in een video bestand detecteren.
* Identificeren van merken (bijvoorbeeld micro soft) van audio tracks en tekst op het scherm in een video.
* Het detecteren en herkennen van gezichten van een Data Base met beroemdheden en een door de gebruiker gedefinieerde data base van gezichten.
* Het uitpakken van onderwerpen die worden besproken, maar niet noodzakelijkerwijs vermeld in audio-en video-inhoud.
* Ondertiteling of ondertiteling maken op basis van het audio spoor.

Zie [overzicht](video-indexer-overview.md)voor meer informatie en meer video indexer functies.

### <a name="how-do-i-get-started-with-video-indexer"></a>Hoe kan ik aan de slag met Video Indexer?

Video Indexer bevat een gratis proef versie van 600 minuten in de web-interface en 2.400 minuten via de API. U kunt u [Aanmelden bij de video indexer-webinterface](https://www.videoindexer.ai/) en deze zelf uitproberen met een webidentiteit en zonder dat u een Azure-abonnement hoeft in te stellen. Volg [Dit eenvoudige Lab](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) voor een betere indruk van het gebruik van video indexer.

Als u Video's en audio op schaal wilt indexeren, kunt u Video Indexer verbinden met een betaald Microsoft Azure-abonnement. Op de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) vindt u meer informatie over de prijzen.

Meer informatie vindt u [in aan de slag.](video-indexer-get-started.md)

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Heb ik codeer vaardigheden nodig om Video Indexer te gebruiken?

U kunt de webinterface van Video Indexer gebruiken om uw account te evalueren, te configureren en te beheren zonder dat hiervoor **code ring vereist** is.  Wanneer u klaar bent voor het ontwikkelen van complexere toepassingen, kunt u de [video indexer-API](https://api-portal.videoindexer.ai/) gebruiken om video indexer te integreren in uw eigen toepassingen, websites of [aangepaste werk stromen met behulp van serverloze technologieën zoals Azure Logic apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) of Azure functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Heb ik machine learning vaardig heden nodig om Video Indexer te gebruiken?

Nee, Video Indexer biedt de integratie van meerdere machine learning modellen in één pijp lijn. Als u een video-of audio bestand indexeert via Video Indexer, wordt een volledige set inzichten opgehaald die op één gedeelde tijd lijn is geëxtraheerd zonder enige machine learning vaardig heden of kennis over algoritmen die nodig zijn voor het onderdeel van de klant.

### <a name="what-media-formats-does-video-indexer-support"></a>Welke media-indelingen ondersteunt Video Indexer?

Video Indexer ondersteunt de meeste gang bare media-indelingen. Raadpleeg de lijst met [Azure Media Encoder standaard indelingen](../latest/media-encoder-standard-formats.md) voor meer informatie.

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>Hoe kan ik een media bestand uploaden naar Video Indexer en wat zijn de beperkingen?

In de Video Indexer-webportal kunt u een media bestand uploaden met behulp van het dialoog venster voor het uploaden van bestanden of door een URL aan te wijzen die het bron bestand rechtstreeks host (Zie [voor beeld](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Een URL die als host fungeert voor de media-inhoud die gebruikmaakt van een iFrame of invoeg code, werkt niet (Zie [voor beeld](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). 

Lees voor meer informatie deze [hand leiding](./upload-index-videos.md).

#### <a name="limitations"></a>Beperkingen

* De naam van de video mag niet langer zijn dan 80 tekens.
* Als u een video uploadt met behulp van een byte matrix, is de video grootte beperkt tot 2 GB (en 30 GB bij het gebruik van URL). 

Zie [overwegingen en beperkingen uploaden](upload-index-videos.md#uploading-considerations-and-limitations)voor de uitgebreide lijst.

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Hoe lang duurt het Video Indexer om inzichten te extra heren van media?

De hoeveelheid tijd die nodig is om een video-of audio bestand te indexeren, zowel met de Video Indexer-API als met de Video Indexer-webinterface, is afhankelijk van meerdere para meters, zoals de bestands lengte en de kwaliteit, het aantal inzichten in het bestand, het aantal beschik bare [gereserveerde eenheden](../previous/media-services-scale-media-processing-overview.md) en of het [streaming-eind punt](../previous/media-services-streaming-endpoints-overview.md) is ingeschakeld of niet. We raden u aan een paar test bestanden met uw eigen inhoud uit te voeren en zo een beter idee te krijgen.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Kan ik aangepaste werk stromen maken voor het automatiseren van processen met Video Indexer?

Ja, u kunt Video Indexer integreren in serverloze technologieën zoals Logic Apps, flow en [Azure functions](https://azure.microsoft.com/services/functions/). U kunt [hier](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)meer informatie vinden over de [logische app](https://azure.microsoft.com/services/logic-apps/) en [flow](https://flow.microsoft.com/en-us/) connectors voor video indexer. U kunt een aantal automatiserings projecten zien die door partners worden uitgevoerd in de [video indexer samples](https://github.com/Azure-Samples/media-services-video-indexer) opslag plaats.

### <a name="in-which-azure-regions-is-video-indexer-available"></a>In welke Azure-regio's is video indexer beschikbaar?

Op de pagina [regio's](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) kunt u zien welke Azure-regio's video indexer beschikbaar zijn.

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Kan ik Video Indexer modellen voor mijn specifieke gebruiks Case aanpassen? 

Ja. In Video Indexer kunt u enkele van de beschik bare modellen aanpassen zodat deze beter aansluiten bij uw behoeften. 

Ons persoons model ondersteunt bijvoorbeeld out-of-the-box 1.000.000-gezichten van beroemdheden-herkenning, maar u kunt deze ook trainen om andere gezichten te herkennen die zich niet in die data base bevinden. 

Zie artikelen over het aanpassen van [person](customize-person-model-overview.md), [Brands](customize-brands-model-overview.md)en [taal](customize-language-model-overview.md) modellen voor meer informatie. 

###  <a name="can-i-edit-the-videos-in-my-library"></a>Kan ik de Video's in mijn bibliotheek bewerken?

Ja. Druk op de knop **video bewerken** in de weer gave van de bibliotheek of de knop **openen in editor** van de weer gave van Media Player om naar het tabblad **projecten** te gaan. U kunt een nieuw project maken en meer Video's uit uw bibliotheek toevoegen om ze samen te bewerken. Als u klaar bent, kunt u uw video weer geven en downloaden. 

Als u inzicht wilt krijgen in uw nieuwe video, indexeert u deze met Video Indexer en wordt het weer gegeven in uw bibliotheek met de inzichten.

### <a name="can-i-index-multiple-audio-streams-or-channels"></a>Kan ik meerdere audiostreams of kanalen indexeren?

Als er meerdere audiostreams zijn, neemt Video Indexer de eerste problemen op en zal deze stroom alleen verwerken. In een audio stroom Video Indexer processen worden de verschillende kanalen gebruikt (indien aanwezig) en verwerkt deze samen als mono. Voor het bewerken van streams/kanalen kunt u ffmpeg-opdrachten gebruiken voor het bestand voordat u het indexeert.

### <a name="what-is-the-sla-for-video-indexer"></a>Wat is de SLA voor Video Indexer?

De SLA van Azure media service behandelt Video Indexer en is te vinden op de [Sla](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) -pagina. De SLA is alleen van toepassing op Video Indexer betaalde accounts en is niet van toepassing op de gratis proef versie.

## <a name="privacy-questions"></a>Privacy-vragen

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Worden video-en audio bestanden geïndexeerd door Video Indexer opgeslagen?

Ja, tenzij u het bestand verwijdert uit Video Indexer, met behulp van de Video Indexer website of API, worden uw video-en audio bestanden opgeslagen. Voor de gratis proef versie worden de video-en audio bestanden die u indexeert, opgeslagen in de Azure-regio VS-Oost. Anders worden uw video-en audio bestanden opgeslagen in het opslag account van uw Azure-abonnement.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Kan ik mijn bestanden verwijderen die zijn opgeslagen in Video Indexer Portal?

Ja, u kunt altijd uw video-en audio bestanden verwijderen, evenals alle meta gegevens en inzichten die door Video Indexer worden geëxtraheerd. Wanneer u een bestand verwijdert uit Video Indexer, worden het bestand en de bijbehorende meta gegevens en inzichten permanent verwijderd uit Video Indexer. Als u echter uw eigen back-upoplossing hebt geïmplementeerd in azure Storage, blijft het bestand in uw Azure-opslag.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Kan ik de gebruikers toegang tot mijn Video Indexer account beheren?

Ja, alleen account beheerders kunnen personen uitnodigen en uitnodigen voor hun account, maar ook toewijzen met bewerkings rechten en met alleen-lezen toegang.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Wie heeft toegang tot mijn video-en audio bestanden die zijn geïndexeerd en/of opgeslagen door Video Indexer en de door u uitgepakte meta gegevens en inzichten?

Uw video-of audio-inhoud die openbaar is als privacy-instelling, is toegankelijk voor iedereen met de koppeling naar uw video-of audio-inhoud en inzichten. Uw video-of audio-inhoud die als privacyinstelling is ingesteld, kan alleen worden gebruikt door gebruikers die zijn uitgenodigd voor het account van de video-of audio-inhoud. De privacyinstelling van uw inhoud is ook van toepassing op de meta gegevens en inzichten die Video Indexer uittreksels. U wijst de privacyinstelling toe wanneer u uw video-of audio bestand uploadt. U kunt de privacyinstelling ook na het indexeren wijzigen.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Welke toegang heeft micro soft voor mijn video-of audio bestanden die zijn geïndexeerd en/of opgeslagen door Video Indexer en de door u uitgepakte meta gegevens en inzichten?

Op basis van de [Azure Online Services-voor waarden](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) hebt u volledig eigenaar van uw inhoud. micro soft heeft alleen toegang tot uw inhoud en de meta gegevens en inzichten die video indexer worden geëxtraheerd uit uw inhoud volgens de OST en de privacyverklaring van micro soft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Zijn de aangepaste modellen die ik in mijn Video Indexer-account heb gemaakt, beschikbaar voor andere accounts?

 Nee, de aangepaste modellen die u in uw account maakt, zijn niet beschikbaar voor een ander account. Met Video Indexer kunt u op dit moment aangepaste [merken](customize-brands-model-overview.md), [talen](customize-language-model-overview.md)en [persoons](customize-person-model-overview.md) modellen bouwen in uw account. Deze modellen zijn alleen beschikbaar in het account waarin u de modellen hebt gemaakt.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Wordt de inhoud geïndexeerd door Video Indexer gehouden in de Azure-regio waar ik Video Indexer gebruik?

Ja, de inhoud en de inzichten worden bewaard in de Azure-regio, tenzij u een hand matige configuratie in uw Azure-abonnement hebt die gebruikmaakt van meerdere Azure-regio's. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Wat is het privacybeleid voor Video Indexer?

Video Indexer valt onder de [privacyverklaring van micro soft](https://privacy.microsoft.com/privacystatement). De privacyverklaring bevat een uitleg van de persoonlijke gegevens van micro soft-processen, de manier waarop micro soft deze verwerkt en voor welke doelen micro soft dit verwerkt. Ga naar het [vertrouwens centrum van micro soft](https://www.microsoft.com/trustcenter)voor meer informatie over privacy.

### <a name="what-certifications-does-video-indexer-have"></a>Welke certificeringen heeft Video Indexer?

Video Indexer heeft momenteel de SOC-certificering. Raadpleeg het [vertrouwens centrum van micro soft](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure)om de certificering van video indexer te controleren.

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Wat is het verschil tussen privé-en open bare Video's? 

Wanneer Video's naar Video Indexer worden geüpload, kunt u kiezen uit twee privacy-instellingen: privé en openbaar. Open bare Video's zijn toegankelijk voor iedereen, waaronder anonieme en niet-geïdentificeerde gebruikers. Persoonlijke items zijn alleen beperkt tot de account leden. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Ik heb geprobeerd een video als openbaar te uploaden en deze is gemarkeerd voor ongepaste of aanstootgevende inhoud. wat betekent dit? 

Wanneer u een video uploadt naar Video Indexer, wordt een automatische inhouds analyse uitgevoerd door de algoritmen en modellen om ervoor te zorgen dat er geen ongeschikte inhoud openbaar wordt weer gegeven. Als een video zo verdacht is dat deze expliciete inhoud bevat, is het niet mogelijk om deze als openbaar in te stellen. De account leden kunnen echter nog steeds toegang krijgen tot het bestand als een persoonlijke video (Bekijk het, down load de inzichten en geëxtraheerde artefacten en voer andere bewerkingen uit die beschikbaar zijn voor account leden).   

Als u de video voor open bare toegang wilt instellen, kunt u het volgende doen: 

* Bouw uw eigen interface-laag (zoals app of website) en gebruik deze om te communiceren met de Video Indexer-service. Op die manier blijft de video privé in onze portal en kunnen uw gebruikers via uw interface communiceren. U kunt bijvoorbeeld nog steeds inzichten krijgen of de video weer geven in uw eigen interface. 
* Vraag een menselijke beoordeling van de inhoud aan, wat zou leiden tot het verwijderen van de beperking, ervan uitgaande dat de inhoud niet expliciet is. 

    Deze optie kan worden bekeken als de Video Indexer website rechtstreeks door uw gebruikers wordt gebruikt als de interface laag en voor open bare (niet-geverifieerde) weer gave. 

## <a name="api-questions"></a>API-vragen

### <a name="what-apis-does-video-indexer-offer"></a>Welke Api's biedt Video Indexer?

Met de Api's van Video Indexer kan indexering worden uitgebreid, worden meta gegevens, het beheer van activa, de vertaling, het insluiten, het aanpassen van modellen en meer uitgepakt. Raadpleeg de [video indexer ontwikkelaars Portal](https://api-portal.videoindexer.ai/)voor meer gedetailleerde informatie over het gebruik van de video INDEXER-API.

### <a name="what-client-sdks-does-video-indexer-offer"></a>Welke client-Sdk's Video Indexer aanbieding?

Er zijn momenteel geen client-Sdk's beschikbaar. Het Video Indexer-team werkt aan de Sdk's en is van plan om ze binnenkort te leveren.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hoe kan ik aan de slag met de Video Indexer-API?

Volg [de zelf studie: aan de slag met de video indexer-API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Wat is het verschil tussen de Video Indexer API en de Azure Media Service V3 API?

Er zijn momenteel enkele overlap pingen in functies die worden aangeboden door de Video Indexer-API en de Azure Media Service V3 API. U kunt [hier](compare-video-indexer-with-media-services-presets.md)meer informatie vinden over het vergelijken van beide services.

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Wat is een API-toegangs token en waarom heb ik dit nodig?

De Video Indexer-API bevat een autorisatie-API en een operations API. De autorisaties-API bevat aanroepen waarmee u een toegangs token kunt opgeven. Elke aanroep naar de Operations-API moet worden gekoppeld aan een toegangstoken dat overeenkomt met het autorisatiebereik van de aanroep.

Toegangs tokens zijn nodig voor het gebruik van de Video Indexer-Api's om veiligheids redenen. Dit zorgt ervoor dat alle aanroepen afkomstig zijn van u of gebruikers met toegangs machtigingen voor uw account. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Wat is het verschil tussen account toegangs token, toegangs token voor gebruikers en video toegangs token?

* Accountniveau: met toegangstokens op accountniveau kunt u bewerkingen uitvoeren op het accountniveau of het videoniveau. U kunt bijvoorbeeld een video uploaden, alle Video's weer geven en video inzichten ophalen.
* Gebruikers niveau: toegangs tokens op gebruikers niveau bieden u de mogelijkheid om bewerkingen op het gebruikers niveau uit te voeren. Hiermee kunt u bijvoorbeeld gekoppelde accounts ophalen.
* Videoniveau: met toegangstokens op videoniveau kunt u bewerkingen uitvoeren op een specifieke video. Hiermee kunt u bijvoorbeeld inzichten in een video krijgen, ondertiteling downloaden, widgets ophalen, enzovoort.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Hoe vaak moet ik een nieuw toegangs token verkrijgen? Wanneer verlopen toegangs tokens?

Toegangs tokens verlopen elk uur, dus u moet elk uur een nieuw toegangs token genereren. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Wat zijn de aanmeldings opties voor Video Indexer ontwikkelaars Portal?

Bekijk een release opmerking over [aanmeldings gegevens](release-notes.md#october-2020).

Wanneer u uw e-mail account met een id-provider hebt geregistreerd, kunt u dit e-mail account niet gebruiken met een andere ID-provider.

## <a name="billing-questions"></a>Vragen over facturering

### <a name="how-much-does-video-indexer-cost"></a>Hoeveel kost het Video Indexer?

Video Indexer maakt gebruik van een eenvoudig prijs model voor betalen per gebruik op basis van de duur van de inhouds invoer die u indexeert. Er kunnen extra kosten in rekening worden gebracht voor het coderen, streamen, opslaan, netwerk gebruik en gereserveerde media-eenheden. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) voor meer informatie.

### <a name="when-am-i-billed-for-using-video-indexer"></a>Wanneer ben ik gefactureerd voor het gebruik van Video Indexer?

Bij het verzenden van een video die moet worden geïndexeerd, definieert de gebruiker de indexering voor video analyse, analyse van audio of beide. Hiermee wordt bepaald welke Sku's er in rekening worden gebracht. Als er een fout optreedt tijdens het verwerken van een kritiek niveau, wordt een fout code geretourneerd als antwoord. In zo'n geval vindt er geen facturering plaats.  Een kritieke fout kan worden veroorzaakt door een bug in onze code of een kritieke fout in een interne afhankelijkheid van de service. Fouten zoals een onjuiste identificatie of inzichtelijke extractie worden niet als kritiek beschouwd en er wordt een antwoord geretourneerd. In alle gevallen waarin een geldig antwoord (niet-fout code) wordt geretourneerd, wordt de facturering uitgevoerd.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Biedt Video Indexer een gratis proef versie?

Ja, Video Indexer biedt een gratis proef versie die volledige service-en API-functionaliteit biedt. Er is een quotum van 600 minuten aan Video's voor web-interface gebruikers en 2.400 minuten voor API-gebruikers. 

## <a name="next-steps"></a>Volgende stappen

* [Overzicht](video-indexer-overview.md)
* [Stack Overflow](https://stackoverflow.com/search?q=video-indexer)
