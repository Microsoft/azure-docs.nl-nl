---
title: Wat is Azure Media Services Video Indexer?
titleSuffix: Azure Media Services
description: Dit artikel bevat een overzicht van de Azure Media Services Video Indexer-service.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/11/2020
ms.author: juliako
ms.openlocfilehash: 06f5e19718445f44dd2302faf280f083cce0774f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98783798"
---
# <a name="what-is-azure-media-services-video-indexer"></a>Wat is Azure Media Services Video Indexer?

[!INCLUDE [regulation](./includes/regulation.md)]

Video Indexer (VI) is de Azure Media Services AI-oplossing en deel van het Cognitive Services merk van Azure. Video Indexer biedt de mogelijkheid om diepere inzichten te verkrijgen (zonder gegevens analyse of codeer vaardig heden) met behulp van machine learning modellen op basis van meerdere kanalen (spraak, vocals, Visual). U kunt de modellen verder aanpassen en trainen. Met de service kunnen uitgebreide zoek opdrachten worden uitgevoerd, worden operationele kosten verminderd, worden nieuwe verdiensten maximaliseren-mogelijkheden geboden en worden nieuwe gebruikers ervaringen gemaakt op grote archieven van Video's (met een lage vermelding).

Als u inzichten wilt gaan extra heren met Video Indexer, moet u een account maken en Video's uploaden. Wanneer u uw Video's uploadt naar Video Indexer, analyseert deze zowel visuele elementen als audio door verschillende AI-modellen uit te voeren. Als Video Indexer analyseert u uw video, de inzichten die worden geëxtraheerd door de AI-modellen.

Wanneer u een Video Indexer account maakt en verbindt met Media Services, worden de media-en meta gegevensbestanden opgeslagen in het Azure-opslag account dat is gekoppeld aan dat Media Services-account. Zie [een video indexer-account maken dat is verbonden met Azure](connect-to-azure.md)voor meer informatie.

Het volgende diagram is een illustratie en is geen technische uitleg over de werking van Video Indexer in de back-end.

![Video Indexer stroom diagram Azure Media Services](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Als belang rijke herinnering moet u zich houden aan alle toepasselijke wetgeving bij het gebruik van Video Indexer en mag u Video Indexer of een Azure-service niet gebruiken op een manier die de rechten van anderen schendt of die schadelijk voor anderen kunnen zijn.

Voordat u een video/afbeelding naar Video Indexer uploadt, moet u over alle juiste rechten beschikken voor het gebruik van de video/afbeelding, inclusief, indien vereist door de wet, alle vereiste mede werkers (indien van toepassing) in de video/afbeelding, voor gebruik, verwerking en opslag van hun gegevens in Video Indexer en Azure. Sommige jurisdicties kunnen speciale wettelijke vereisten opleggen voor het verzamelen, online verwerken en opslaan van bepaalde typen gegevens, zoals biometrische gegevens. Voordat u Video Indexer en Azure gebruikt voor het verwerken en opslaan van gegevens die onder bijzondere wettelijke vereisten vallen, moet u ervoor zorgen dat u voldoet aan de wettelijke vereisten die voor u van toepassing kunnen zijn.

Ga naar het [vertrouwens centrum](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)van micro soft voor meer informatie over naleving, privacy en beveiliging in video indexer. Als u meer wilt weten over de privacyverplichtingen en procedures voor gegevensverwerking en -retentie die Microsoft hanteert ten aanzien van uw gegevens, inclusief het verwijderen van uw gegevens, kunt u de [Privacyverklaring](https://privacy.microsoft.com/PrivacyStatement) van Microsoft, de [Voorwaarden voor Online Diensten](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ('OST') en het [Addendum met betrekking tot gegevensverwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ('DPA') raadplegen. Door Video Indexer te gebruiken, gaat u ermee akkoord dat u bent gebonden aan de OST, DPA en de privacyverklaring.

## <a name="what-can-i-do-with-video-indexer"></a>Wat kan ik doen met Video Indexer?

De inzichten van Video Indexer kunnen worden toegepast op verschillende scenario's, waaronder:

* *Uitgebreide zoek opdracht*: gebruik de inzichten die zijn geëxtraheerd uit de video om de zoek ervaring in een video bibliotheek te verbeteren. Als u bijvoorbeeld gesp roken woorden en gezichten indexeert, kan de zoek ervaring van het vinden van momenten in een video waarbij een persoon op bepaalde woorden is gespokeeerd of wanneer twee personen samen worden weer gegeven. Zoeken op basis van deze inzichten van Video's is van toepassing op de nieuws instanties, onderwijs instituten, omroep organisaties, eigen aren van entertainment, zakelijke LOB-apps, en in het algemeen voor elke branche met een video bibliotheek die gebruikers nodig hebben om te zoeken.
* *Maken van inhoud*: Maak Trailers, Markeer trommels, sociale media-inhoud of Nieuws clips op basis van de inzichten video indexer extracten van uw inhoud. Met keyframes, scènes en tijds tempels voor de vormgeving van personen en labels kan het maken van het proces veel vloeiender en eenvoudiger worden en kunt u de onderdelen van de video ophalen die u nodig hebt voor de inhoud die u wilt maken.
* *Toegankelijkheid*: of u uw inhoud beschikbaar wilt maken voor mensen met een handicap of als u wilt dat uw inhoud naar verschillende regio's wordt gedistribueerd met behulp van verschillende talen, kunt u de transcriptie en vertaling van de video indexer in meerdere talen gebruiken.
* *Verdiensten maximaliseren*: video indexer kunt u helpen de waarde van Video's te verhogen. Zo kunnen branches die gebruikmaken van de inkomsten van de advertentie (nieuws media, sociale media, enzovoort), relevante advertenties leveren door gebruik te maken van de uitgepakte inzichten als extra signalen voor de ad-server.
* *Toezicht op inhoud*: gebruik tekstuele en visuele toezicht modellen voor inhoud om uw gebruikers veilig te houden van ongepaste inhoud en te controleren of de inhoud die u publiceert, overeenkomt met de waarden van uw organisatie. U kunt bepaalde Video's automatisch blok keren of uw gebruikers waarschuwen over de inhoud.
* *Aanbevelingen*: video Insights kan worden gebruikt om de gebruikers betrokkenheid te verbeteren door de relevante video te markeren voor gebruikers. Als u elke video met aanvullende meta gegevens codeert, kunt u gebruikers aanbevelen de meest relevante Video's te plaatsen en de onderdelen van de video te markeren die overeenkomen met hun behoeften.

## <a name="features"></a>Functies

In de volgende lijst ziet u de inzichten die u uit uw Video's kunt ophalen met Video Indexer video-en audio modellen:

### <a name="video-insights"></a>Video inzichten

* **Gezichtsdetectie**: detecteert en groepeert gezichten die worden weergegeven in de video.
* **Beroemdheden-id**: video indexer identificeert automatisch meer dan 1.000.000 beroemdheden, zoals wereld leiders, actoren, Actresses, atleten, onderzoekers, zakelijke en technologische leiders over de hele wereld. De gegevens over deze beroemdheden kunnen ook worden gevonden op verschillende websites (IMDB, Wikipedia, enzovoort).
* **Gezichtsidentificatie op basis van account**: Video Indexer traint een model voor een specifiek account. Vervolgens worden de gezichten herkend in de video op basis van het getrainde model. Zie [een persoons model aanpassen van de video indexer website](customize-person-model-with-website.md) en [een persoonlijk model aanpassen met de video indexer-API](customize-person-model-with-api.md)voor meer informatie.
* **Miniatuur extractie voor gezichten** ("beste gezicht"): identificeert automatisch het beste vastgelegde gezicht in elke groep gezichten (op basis van kwaliteit, grootte en frontale positie) en extraheert dit als een afbeeldings element.
* **Visuele tekst herkenning** (OCR): extraheert tekst die visueel wordt weer gegeven in de video.
* **Visueel inhoudstoezicht**: detecteert inhoud voor volwassenen en/of ongepaste visuele elementen.
* **Identificatie van labels**: identificeert visuele objecten en acties die worden weergegeven.
* **Scène segmentering**: Hiermee wordt bepaald wanneer een scène in video wordt gewijzigd op basis van visuele hints. In een scène wordt één gebeurtenis weer gegeven en deze is samengesteld op basis van een reeks opeenvolgende opnamen, die semantisch verwant zijn.
* **Opname detectie**: bepaalt wanneer een foto in video wordt gewijzigd op basis van visuele hints. Een foto is een reeks frames uit dezelfde camera voor animatie. Zie [scènes, afbeeldingen en keyframes](scenes-shots-keyframes.md)voor meer informatie.
* **Detectie van zwarte frames**: identificeert zwarte frames in de video.
* **Extractie van sleutelframes**: detecteert stabiele sleutelframes in een video.
* **Rollend tegoed**: identificeert het begin en het einde van de roulerende tegoeden aan het einde van TV-Program ma's en films.
* **Detectie van animatie tekens** (preview): detectie, groepering en herkenning van tekens in inhoud met animatie via integratie met [Cognitive Services aangepast gezichts vermogen](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Zie voor meer informatie [tekst detectie met animatie](animated-characters-recognition.md).
* **Redactionele afbeeldings type detectie**: Tags maken op basis van het type (zoals grote opname, gemiddelde opname, close-up, uiterst dicht op, twee Foto's, meerdere mensen, buiten en binnen, enzovoort). Zie [redactionele shot type Detection](scenes-shots-keyframes.md#editorial-shot-type-detection)(Engelstalig) voor meer informatie.

### <a name="audio-insights"></a>Audio inzichten

* **Audio-transcriptie**: converteert spraak naar tekst in 12 talen en maakt extensies mogelijk. De volgende talen worden ondersteund: Engels, Spaans, Frans, Duits, Italiaans, Chinees (Mandarijn), Japans, Arabisch, Russisch, Portugees, Hindi en Koreaans.
* **Automatische taaldetectie**: identificeert automatisch de meest gesproken taal. De volgende talen worden ondersteund: Engels, Spaans, Frans, Duits, Italiaans, Chinees (Mandarijn), Japans, Arabisch, Russisch en Portugees. Als de taal niet met vertrouwen kan worden geïdentificeerd, neemt Video Indexer aan dat de gesproken taal Engels is. Zie [Taalidentificatiemodel](language-identification-model.md) voor meer informatie.
* **Multi-Language Speech Identification and transcriptie**: identificeert automatisch de gesp roken taal in verschillende segmenten van audio. Elke segment van het mediabestand wordt verzonden voor een transcriptie en deze transcripties worden vervolgens gecombineerd in één uniforme transcriptie. Zie [Inhoud in meerdere talen automatisch identificeren en transcriberen](multi-language-identification-transcription.md) voor meer informatie.
* **Ondertiteling**: hiermee maakt u ondertiteling in drie indelingen: VTT, TTML, SRT.
* **Twee kanaal verwerking**: automatisch detecteert afzonderlijke transcripten en samen voegingen op één tijd lijn.
* **Ruis reductie**: Hiermee wist u de audio of ruis opnamen van telefonie (op basis van Skype-filters).
* **Transcript aanpassing** (cri's): traint aangepaste spraak naar tekst modellen om branchespecifieke transcripten te maken. Zie [een taal model aanpassen van de video indexer website](customize-language-model-with-website.md) en [een taal model aanpassen met de video indexer-api's](customize-language-model-with-api.md)voor meer informatie.
* **Opsomming van de luid sprekers**: kaarten en begrijpen welke sprekers Spaak zijn die woorden en wanneer. Zestien luid sprekers kunnen worden gedetecteerd in één audio bestand.
* **Sprekers statistieken**: biedt statistieken voor de spraak verhoudingen van de luid sprekers.
* **Tekstueel inhoudsbeheer**: detecteert expliciete tekst in het audiotranscript.
* **Audio-effecten**: identificeert audio-effecten zoals hand claps, spraak en stilte.
* **Detectie van Emotion**: identificeert emoties op basis van de spraak (wat wordt gezegd) en de gesp roken Toon (hoe dit wordt genoemd). De EMOTION kan Joy, verdriet, boosheid of vrezen zijn.
* **Vertaling**: maakt vertalingen van het audiotranscript in 54 verschillende talen.

### <a name="audio-and-video-insights-multi-channels"></a>Audio-en video inzichten (multi kanalen)

Bij het indexeren door één kanaal zijn gedeeltelijke resultaten voor deze modellen beschikbaar.

* **Extractie van tref woorden**: extraheert tref woorden uit spraak en visuele tekst.
* **Extractie van benoemde entiteiten**: pakt merken, locaties en mensen uit vanuit spraak en visuele tekst via natuurlijke taal verwerking (NLP).
* **Onderwerpdeductie**: maakt een deductie van de belangrijkste onderwerpen uit de transcripten. De IPTC-taxonomie op 2de niveau is inbegrepen.
* **Artefacten**: extraheert een grote verscheidenheid aan 'extra gedetailleerde' artefacten voor elk van de modellen.
* **Gevoelsanalyse**: identificeert positieve, negatieve en neutrale gevoelens uit visuele tekst en gesproken woorden.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hoe kan ik aan de slag met Video Indexer?

U kunt op drie manieren toegang krijgen tot Video Indexer mogelijkheden:

* Video Indexer portal: een eenvoudig te gebruiken oplossing waarmee u het product kunt evalueren, het account kunt beheren en modellen aanpassen.

    Zie [aan de slag met de video indexer-website](video-indexer-get-started.md)voor meer informatie over de portal.  

* API-integratie: alle mogelijkheden van Video Indexer zijn beschikbaar via een REST API, waarmee u de oplossing kunt integreren in uw apps en infra structuur.

    Zie [Video Indexer rest API gebruiken](video-indexer-use-apis.md)om aan de slag te gaan als ontwikkelaar.

* Inge sloten widget: Hiermee kunt u de Video Indexer Insights-, speler-en editor-ervaring in uw app insluiten.

    Zie [visuele objecten insluiten in uw toepassing](video-indexer-embed-widgets.md)voor meer informatie.

Als u de website gebruikt, worden de inzichten toegevoegd als meta gegevens en worden ze weer gegeven in de portal. Als u Api's gebruikt, zijn de inzichten beschikbaar als JSON-bestand.

## <a name="supported-browsers"></a>Ondersteunde browsers

De volgende lijst bevat de ondersteunde browsers die u kunt gebruiken voor de Video Indexer website en voor uw apps die de widgets insluiten. In de lijst wordt ook de mini maal ondersteunde browser versie weer gegeven:

- Rand, versie: 16
- Firefox, versie: 54
- Chrome, versie: 58
- Safari, versie: 11
- Opera, versie: 44
- Opera Mobile, versie: 59
- Android-browser, versie: 81
- Samsung-browser, versie: 7
- Chrome voor Android, versie: 87
- Firefox voor Android, versie: 83

## <a name="next-steps"></a>Volgende stappen

U kunt aan de slag met Video Indexer. Raadpleeg voor meer informatie de volgende artikelen:

- [Ga aan de slag met de video indexer-website](video-indexer-get-started.md).
- [Inhoud verwerken met Video Indexer rest API](video-indexer-use-apis.md).
- [Sluit visuele objecten in uw toepassing in](video-indexer-embed-widgets.md).
