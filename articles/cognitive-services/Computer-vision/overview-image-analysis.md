---
title: Wat is afbeeldingsanalyse?
titleSuffix: Azure Cognitive Services
description: De Service voor afbeeldingsanalyse maakt gebruik van vooraf getrainde AI-modellen om veel verschillende visuele kenmerken uit afbeeldingen te extraheren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: computer vision, computer vision-toepassingen, computer vision-service
ms.openlocfilehash: 0258eb7c57bc0734b5c0a67644cbaa4f62a34537
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766911"
---
# <a name="what-is-image-analysis"></a>Wat is afbeeldingsanalyse?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De Computer Vision Image Analysis-service kan een groot aantal verschillende visuele kenmerken uit uw afbeeldingen extraheren. Het kan bijvoorbeeld bepalen of een afbeelding inhoud voor volwassenen bevat, specifieke merken of objecten zoeken of menselijke gezichten vinden.

U kunt Afbeeldingsanalyse gebruiken via een clientbibliotheek-SDK of door de REST API [aan](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v2-ga/operations/5d986960601faab4bf452005) te roepen. Volg de [quickstart om](quickstarts-sdk/image-analysis-client-library.md) aan de slag te gaan.

Deze documentatie bevat de volgende typen artikelen:
* De [quickstarts](./quickstarts-sdk/image-analysis-client-library.md) zijn stapsgewijs instructies voor het aanroepen van de service en het in korte tijd krijgen van resultaten. 
* De [instructiegidsen bevatten](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) instructies voor het gebruik van de service op specifiekere of aangepaste manieren.
* De [conceptuele artikelen](concept-tagging-images.md) bevatten uitgebreide uitleg over de functionaliteit en functies van de service.
* De [zelfstudies zijn](./tutorials/storage-lab-tutorial.md) langere handleidingen die laten zien hoe u deze service kunt gebruiken als onderdeel van bredere bedrijfsoplossingen.

## <a name="image-analysis-features"></a>Functies voor afbeeldingsanalyse

U kunt afbeeldingen analyseren om inzicht te krijgen in de visuele kenmerken en eigenschappen van die afbeeldingen. Alle functies in de onderstaande lijst worden geleverd door de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Volg een [snelstart](./quickstarts-sdk/image-analysis-client-library.md) om aan de slag te gaan.


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

## <a name="image-requirements"></a>Vereisten voor installatiekopieën

Afbeeldingsanalyse werkt op afbeeldingen die voldoen aan de volgende vereisten:

- De afbeelding moet worden weergegeven in de JPEG-, PNG-, GIF- of BMP-indeling
- De afbeelding moet kleiner zijn dan 4 megabyte (MB)
- De afmetingen van de afbeelding moet groter zijn dan 50 x 50 pixels

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle Cognitive Services, dienen ontwikkelaars die de Computer Vision-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met afbeeldingsanalyse door de snelstartgids te volgen in de ontwikkeltaal van uw voorkeur:

- [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/image-analysis-client-library.md)
