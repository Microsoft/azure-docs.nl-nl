---
title: Concepten van gezichts detectie en kenmerken
titleSuffix: Azure Cognitive Services
description: Gezichts detectie is de actie van het vinden van menselijke gezichten in een afbeelding en optioneel verschillende soorten informatie retour neren.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 68d1e9744d937cf80327c3f41cc69f4af97d3400
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600185"
---
# <a name="face-detection-and-attributes"></a>Gezichts detectie en kenmerken

In dit artikel worden de concepten van gezichts detectie en face-kenmerk gegevens uitgelegd. Gezichts detectie is de actie van het vinden van menselijke gezichten in een afbeelding en optioneel verschillende soorten informatie retour neren.

U gebruikt de bewerking [Face-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) om gezichten in een installatie kopie te detecteren. Elk gedetecteerd gezicht komt ten minste overeen met een faceRectangle-veld in het antwoord. Deze set pixel coördinaten voor de linkerkant, boven, breedte en hoogte markeert het bevindde gezicht. Met deze coördinaten kunt u de locatie van het gezicht en de grootte ervan ophalen. In de API-reactie worden gezichten weer gegeven in de volg orde van grootste naar kleinste.

## <a name="face-id"></a>Face ID

De face ID is een unieke id-teken reeks voor elk gedetecteerd gezicht in een afbeelding. U kunt een gezichts-ID aanvragen bij uw [gezicht-detectie API-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) oproep.

## <a name="face-landmarks"></a>Gezichtsoriëntatiepunten

Gezichts bezienswaardigheden zijn een verzameling gemakkelijk te vinden punten op een gezicht, zoals de pupilsen of de tip van de neus. Standaard zijn er 27 oriëntatiepunten vooraf gedefinieerde punten. In de volgende afbeelding ziet u alle 27 punten:

![Een gezichts diagram met alle 27 oriëntatie punten](../Images/landmarks.1.jpg)

De coördinaten van de punten worden geretourneerd in eenheden van pixels.

## <a name="attributes"></a>Kenmerken

Kenmerken zijn een set functies die optioneel kunnen worden gedetecteerd door de [-detectie-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. De volgende kenmerken kunnen worden gedetecteerd:

* **Leeftijd**. De geschatte leeftijd in jaren van een bepaald gezicht.
* **Vervagen**. De blurriness van het gezicht in de afbeelding. Dit kenmerk retourneert een waarde tussen nul en een informele classificatie van laag, gemiddeld of hoog.
* **Emotion**. Een lijst met emoties met hun detectie betrouwbaarheids niveau voor het gegeven gezicht. Betrouw bare scores worden genormaliseerd en de scores voor alle emoties worden Maxi maal één opgeteld. De emoties is blij, verdriet, neutraal, boosheid, congeneigd, walging, verrassing en vrezen.
* **Bloot stelling**. De bloot stelling van het gezicht in de afbeelding. Dit kenmerk retourneert een waarde tussen nul en een informele classificatie van onderbelichting, goodExposure of overbelichting.
* Laat het **staan.** Het geschatte gezicht is de aanwezigheid en de lengte van het gegeven gezicht.
* **Geslacht**. Het geschatte geslacht van het opgegeven gezicht. Mogelijke waarden zijn mannelijk, vrouwelijk en geslacht.
* **Glazen**. Hiermee wordt aangegeven of het opgegeven gezicht bril heeft. Mogelijke waarden zijn glazen, ReadingGlasses, zonnebril en zwem brillen.
* **Haar**. Het type van het gezicht. Dit kenmerk toont of het haar zichtbaar is, of baldness wordt gedetecteerd en wat haar kleuren zijn gedetecteerd.
* **Hoofd pose**. De richting van het gezicht in de 3D-ruimte. Dit kenmerk wordt beschreven door de hoeken pitch, roll en yaw in graden. De waardebereiken zijn-90 graden tot 90 graden,-90 graden tot 90 graden en-90 graden tot 90 graden. Zie het volgende diagram voor hoek toewijzingen:

    ![Een kop met de assen pitch, roll en yaw label](../Images/headpose.1.jpg)
* **Makeup**. Hiermee wordt aangegeven of het gezicht Makeup heeft. Dit kenmerk retourneert een Booleaanse waarde voor eyeMakeup en lipMakeup.
* **Lawaai**. Het visuele geluid dat is gedetecteerd in de gezichts afbeelding. Dit kenmerk retourneert een waarde tussen nul en een informele classificatie van laag, gemiddeld of hoog.
* **Bedekking**. Hiermee wordt aangegeven of er objecten zijn die delen van het gezicht blok keren. Dit kenmerk retourneert een Booleaanse waarde voor eyeOccluded, foreheadOccluded en mouthOccluded.
* **Glim lach**. De glim lach-expressie van het opgegeven gezicht. Deze waarde ligt tussen nul en een voor een helder glim lach.

> [!IMPORTANT]
> Gezichts kenmerken worden voor speld door het gebruik van statistische algoritmen. Ze zijn mogelijk niet altijd nauw keurig. Wees voorzichtig bij het nemen van beslissingen op basis van kenmerk gegevens.

## <a name="input-data"></a>Invoergegevens

Gebruik de volgende tips om ervoor te zorgen dat uw invoer installatie kopieën de meest nauw keurige detectie resultaten bieden:

* De ondersteunde indelingen voor de invoer afbeeldingen zijn JPEG, PNG, GIF voor het eerste frame en BMP.
* De grootte van het afbeeldings bestand mag niet groter zijn dan 6 MB.
* De minimale detectie grootte is 36 x 36 pixels in een afbeelding die niet groter is dan 1920 x 1080 pixels. Afbeeldingen met meer dan 1920 x 1080 pixels hebben een proportioneel grotere minimale face-grootte. Het verminderen van de face-grootte kan ertoe leiden dat sommige gezichten niet worden gedetecteerd, zelfs als ze groter zijn dan de mini maal waarneem bare face-grootte.
* De Maxi maal detectie bare face-grootte is 4096 x 4096 pixels.
* Gezichten buiten het grootte bereik van 36 x 36 tot 4096 x 4096 pixels worden niet gedetecteerd.
* Sommige gezichten worden mogelijk niet gedetecteerd vanwege technische problemen. Extreme gezichts hoeken (Head pose) of gezichts bedekking (objecten zoals zonnebril of handen die deel van het gezicht blok keren) kunnen de detectie beïnvloeden. Front-en bijna-frontale gezichten bieden de beste resultaten.

Als u gezichten van een video feed detecteert, kunt u de prestaties mogelijk verbeteren door bepaalde instellingen op uw video camera aan te passen:

* **Vloeiend maken**: veel video camera's maken een gladd effect toe. Als dat het geval is, kunt u dit uitschakelen omdat er een vervaging tussen frames wordt gemaakt en de duidelijkheid wordt verminderd.
* **Sluiter snelheid**: een snellere sluiter snelheid vermindert de hoeveelheid beweging tussen frames en maakt elk frame duidelijker. U kunt het beste een sluiter snelheid van 1/60 seconden of sneller.
* **Sluit hoek**: sommige camera's geven een sluit hoek aan in plaats van de sluiter snelheid. Gebruik, indien mogelijk, een kleine sluit hoek. Dit leidt tot duidelijkere video frames.

    >[!NOTE]
    > Een camera met een lagere sluiter hoek krijgt minder licht in elk frame, waardoor de afbeelding donkerder wordt. U moet het juiste niveau bepalen dat u wilt gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met gezichts detectie concepten, leert u hoe u een script schrijft dat gezichten detecteert in een bepaalde afbeelding.

* [Gezichten in een afbeelding detecteren](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
