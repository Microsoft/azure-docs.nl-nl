---
title: Wat is Computer Vision?
titleSuffix: Azure Cognitive Services
description: De Computer Vision-service geeft u toegang tot geavanceerde algoritmen voor het verwerken van afbeeldingen en het retourneren van informatie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: computer vision, computer vision-toepassingen, computer vision-service
ms.openlocfilehash: 804dacc4351da9e04ac75b2484b4330901a69271
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488479"
---
# <a name="what-is-computer-vision"></a>Wat is Computer Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De Computer Vision-service van Azure geeft u toegang tot geavanceerde algoritmen waarmee afbeeldingen worden verwerkt en informatie wordt geretourneerd op basis van de visuele kenmerken waarin u geïnteresseerd bent. Met Computer Vision kunt u bijvoorbeeld bepalen of een afbeelding inhoud voor volwassenen bevat, specifieke merken of objecten zoeken, of gezichten van mensen zoeken.

U kunt Computer Vision-toepassingen maken met behulp van een [clientbibliotheek-SDK](./quickstarts-sdk/client-library.md) of door de [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) rechtstreeks aan te roepen. Deze pagina laat grosso modo zien wat u kunt doen met Computer Vision.

Deze documentatie bevat de volgende typen artikelen:
* In de [Quick](./quickstarts-sdk/client-library.md) starts vindt u stapsgewijze instructies voor het aanroepen van de service en het verkrijgen van resultaten in korte tijd. 
* De [hand leidingen](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) bevatten instructies voor het gebruik van de service op meer specifieke of aangepaste manieren.
* De [conceptuele artikelen](concept-recognizing-text.md) bevatten gedetailleerde uitleg over de functionaliteit en functies van de service.
* De [zelf studies](./tutorials/storage-lab-tutorial.md) zijn meer gidsen die laten zien hoe u deze service kunt gebruiken als onderdeel in bredere zakelijke oplossingen.

## <a name="optical-character-recognition-ocr"></a>Optische tekenherkenning (OCR)

Computer Vision bevat mogelijkheden voor [Optical Character Recognition (OCR)](concept-recognizing-text.md). U kunt de nieuwe Read-API gebruiken om gedrukte en handgeschreven tekst uit afbeeldingen en documenten te extraheren. Het maakt gebruik van diep gaande modellen en werkt met tekst op verschillende Opper vlakken en achtergronden. Dit zijn onder andere bedrijfs documenten, facturen, bevestigingen, posters, visite kaartjes, brieven en White boards. De OCR-Api's bieden ondersteuning voor het extra heren van gedrukte tekst in [verschillende talen](./language-support.md). Volg een [snelstart](./quickstarts-sdk/client-library.md) om aan de slag te gaan.

## <a name="computer-vision-for-digital-asset-management"></a>Computer Vision voor beheer van digitale assets

Met Computer Vision kunt u tal van DAM-scenario's (Digitaal Asset Management) ondersteunen. DAM is het bedrijfsproces voor het organiseren, opslaan en ophalen van geavanceerde media-assets en het beheren van digitale rechten en machtigingen. Het is bijvoorbeeld mogelijk dat een bedrijf afbeeldingen wil groeperen en identificeren op basis van zichtbare logo's, gezichten, objecten, kleuren, enzovoort. U kunt ook automatisch [bijschriften voor afbeeldingen genereren](./Tutorials/storage-lab-tutorial.md) en trefwoorden toevoegen zodat ze zoekbaar zijn. Zie de handleiding [Knowledge Mining Solution Accelerator](https://github.com/Azure-Samples/azure-search-knowledge-mining) (Oplossingsverbetering voor kennisanalyse) op GitHub voor een alles-in-eenoplossing met Cognitive Services, Azure Cognitive Search en intelligente rapportage. Zie de opslagplaats voor [Computer Vision Solution Templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) (Oplossingssjablonen van Computer Vision) voor andere DAM-voorbeelden.

## <a name="analyze-images-for-insight"></a>Analyseren van afbeeldingen voor inzicht

U kunt afbeeldingen analyseren om inzicht te krijgen in de visuele kenmerken en eigenschappen van die afbeeldingen. Alle functies in de onderstaande tabel worden geleverd door de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API. Volg een [snelstart](./quickstarts-sdk/client-library.md) om aan de slag te gaan.


### <a name="tag-visual-features"></a>Visuele kenmerken taggen

Identificeer en tag visuele kenmerken in een afbeelding op basis van een set met duizenden herkenbare objecten, levende wezens, landschappen en acties. Als de tags dubbelzinnig of niet algemeen bekend zijn, worden via de API-reactie tips gegeven om de context van de tag te verduidelijken. U kunt tagging niet alleen gebruiken voor het hoofdonderwerp, zoals een persoon op de voorgrond, maar ook voor de omgeving (binnen of buiten), meubels, gereedschap, planten, dieren, accessoires, gadgets en enzovoort. [Visuele kenmerken taggen](concept-tagging-images.md)

### <a name="detect-objects"></a>Objecten detecteren

Objectdetectie is vergelijkbaar met het gebruik van tags, maar de API retourneert de coördinaten van de omsluitende box voor elke tag die wordt toegepast. Als een afbeelding bijvoorbeeld een hond, kat en persoon bevat, zal de detectiebewerking deze objecten vermelden, samen met hun coördinaten in de afbeelding. U kunt deze functie gebruiken om verdere relaties tussen de objecten in een afbeelding te verwerken. Ook weet u daardoor wanneer er meerdere exemplaren van dezelfde tag in een afbeelding voorkomen. [Objecten detecteren](concept-object-detection.md)

### <a name="detect-brands"></a>Merken detecteren

Identificeer commerciële merken in afbeeldingen of video's met behulp van een database met duizenden logo's. U kunt deze functie bijvoorbeeld gebruiken om te ontdekken welke merken het populairst zijn op sociale media of het meest voorkomen in productplaatsing in de media. [Merken detecteren](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Een afbeelding categoriseren

Identificeer en categoriseer een volledige afbeelding met behulp van een [categorietaxonomie](Category-Taxonomy.md) met bovenliggende/onderliggende erfelijke hiërarchieën. Categorieën kunnen zelfstandig worden gebruikt of met onze nieuwe tagmodellen.<br/>Engels is momenteel de enige ondersteunde taal voor het taggen en categoriseren van afbeeldingen. [Een afbeelding categoriseren](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Een afbeelding beschrijven

Genereer een beschrijving van een volledige afbeelding in leesbare taal met behulp van volledige zinnen. Met de algoritmen van Computer Vision worden verschillende beschrijvingen gegenereerd op basis van de objecten die zijn geïdentificeerd in de afbeelding. De beschrijvingen worden geëvalueerd en hiervoor wordt een betrouwbaarheidsscore gegenereerd. Vervolgens wordt er een lijst geretourneerd die is geordend van de hoogste naar de laagste betrouwbaarheidsscore. [Een afbeelding beschrijven](concept-describing-images.md)

### <a name="detect-faces"></a>Gezichten detecteren

Detecteer gezichten in een afbeelding en geef informatie op over elk gedetecteerd gezicht. Met Computer Vision worden de coördinaten, de rechthoek, het geslacht en de leeftijd van elk gedetecteerd gezicht geretourneerd.<br/>Computer Vision biedt een subset van de servicefunctionaliteit [Face](../face/index.yml). U kunt de Face-service gebruiken voor een gedetailleerdere analyse, zoals gezichtsidentificatie en posedetectie. [Gezichten detecteren](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Afbeeldingstypen detecteren

Detecteer kenmerken van een afbeelding, bijvoorbeeld of een afbeelding een lijntekening of een illustratie is. [Afbeeldingstypen detecteren](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Domeinspecifieke inhoud detecteren

Gebruik domeinmodellen om domeinspecifieke inhoud in een afbeelding te detecteren en te identificeren, zoals beroemdheden en oriëntatiepunten. Als een afbeelding bijvoorbeeld mensen bevat, kan Computer Vision een domeinmodel gebruiken voor beroemdheden om te bepalen of de personen die in de afbeelding worden gedetecteerd, overeenkomen met bekende beroemdheden. [Domeinspecifieke inhoud detecteren](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Het kleurenschema detecteren

Analyseer het kleurgebruik in een afbeelding. Met Computer Vision kan worden bepaald of een afbeelding zwart-wit of kleur is en voor kleurenafbeeldingen kunnen de dominante kleuren en accentkleuren worden geïdentificeerd. [Het kleurenschema detecteren](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Een miniatuur genereren

Analyseer de inhoud van een afbeelding om een geschikte miniatuur voor deze afbeelding te genereren. Computer Vision genereert eerst een miniatuur van hoge kwaliteit, waarna de objecten in de afbeelding worden geanalyseerd om het *interessegebied* te bepalen. Computer Vision past de afbeelding vervolgens aan de vereisten van het interessegebied aan. De gegenereerde miniatuur kan worden weergegeven met een andere hoogte-breedteverhouding dan wordt gebruikt in de oorspronkelijke afbeelding, afhankelijk van uw behoeften. [Een miniatuur genereren](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Interessegebied ophalen

Analyseer de inhoud van een afbeelding om de coördinaten van het *interessegebied* te retourneren. In plaats van de miniatuur bij te snijden en te genereren, retourneert Computer Vision de coördinaten van het begrenzingsvak van het gebied, zodat de aanroepende toepassing de oorspronkelijke afbeelding naar wens kan wijzigen. [Interessegebied ophalen](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Beheren van inhoud in afbeeldingen

U kunt Computer Vision gebruiken om [erotische inhoud te detecteren](concept-detecting-adult-content.md) in een afbeelding en vertrouwensscores voor verschillende classificaties te retourneren. De drempel voor het markeren van inhoud kan worden ingesteld met een glijdende schaal om uw voorkeuren aan te geven.

## <a name="deploy-on-premises-using-docker-containers"></a>On-premises implementeren met behulp van Docker-containers

Gebruik Computer Vision-containers om API-functies on-premises te implementeren. Deze Docker-containers stellen u in staat om de service dichter bij uw gegevens te brengen voor naleving, beveiliging en andere operationele redenen. Computer Vision biedt de volgende containers:

* Met de [Computer Vision-lees-OCR-container (preview)](computer-vision-how-to-install-containers.md) kunt u gedrukte en handgeschreven tekst in afbeeldingen herkennen.
* Met de [container voor ruimtelijke analyse van Computer Vision (preview)](spatial-analysis-container.md) kunt u realtime streaming-video analyseren om inzicht te krijgen in de ruimtelijke relaties tussen mensen en hun verplaatsing door fysieke omgevingen.

## <a name="image-requirements"></a>Vereisten voor installatiekopieën

Met Computer Vision kunt u afbeeldingen analyseren die voldoen aan de volgende vereisten:

- De afbeelding moet worden weergegeven in de JPEG-, PNG-, GIF- of BMP-indeling
- De afbeelding moet kleiner zijn dan 4 megabyte (MB)
- De afmetingen van de afbeelding moet groter zijn dan 50 x 50 pixels
  - Voor de Read-API moet de afbeelding tussen de 50 x 50 en 10000 x 10000 pixels groot zijn.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle Cognitive Services, dienen ontwikkelaars die de Computer Vision-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met Computer Vision door de quickstart-gids te volgen in de ontwikkelingstaal van uw voorkeur:

- [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md)
