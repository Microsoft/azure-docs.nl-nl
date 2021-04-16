---
title: Wat is optische tekenherkenning?
titleSuffix: Azure Cognitive Services
description: De OCR-service (Optical Character Recognition) extraheert zichtbare tekst in een afbeelding en retourneert deze als gestructureerde tekenreeksen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: da4ada8b505c747d24738e175a1701b5ea73b4e4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536742"
---
# <a name="what-is-optical-character-recognition"></a>Wat is optische tekenherkenning?

Met de OCR-service (Optical Character Recognition) kunt u gedrukte of handgeschreven tekst extraheren uit afbeeldingen, zoals foto's van straatborden en producten, evenals uit documenten facturen, facturen, financiële rapporten, artikelen en &mdash; meer. Het maakt gebruik van deep learning-modellen en werkt met tekst op verschillende oppervlakken en achtergronden.

De OCR-API's bieden ondersteuning voor het extraheren van gedrukte tekst in [verschillende talen.](./language-support.md) Volg een [snelstart](./quickstarts-sdk/client-library.md) om aan de slag te gaan.

![OCR-demo's](./Images/ocr-demo.gif)

Deze documentatie bevat de volgende typen artikelen:
* De [quickstarts](./quickstarts-sdk/client-library.md) zijn stapsgewijs instructies voor het aanroepen van de service en het in korte tijd krijgen van resultaten. 
* De [instructiegidsen bevatten](./Vision-API-How-to-Topics/call-read-api.md) instructies voor het gebruik van de service op specifiekere of aangepaste manieren.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Ondersteunde talen
De OCR-API's ondersteunen in totaal 73 talen voor tekst in afdrukstijl. Raadpleeg de volledige lijst met door [OCR ondersteunde talen.](./language-support.md#optical-character-recognition-ocr) OcR in handgeschreven stijl wordt uitsluitend ondersteund voor Engels.

## <a name="input-requirements"></a>Vereisten voor invoer

De **aanroep Lezen** gebruikt afbeeldingen en documenten als invoer. Ze hebben de volgende vereisten:

* Ondersteunde bestandsindelingen: JPEG, PNG, BMP, PDF en TIFF
* Voor PDF- en TIFF-bestanden worden maximaal 2000 pagina's (slechts de eerste twee pagina's voor de gratis laag) verwerkt.
* De bestandsgrootte moet kleiner zijn dan 50 MB (4 MB voor de gratis laag) en de afmetingen moeten ten minste 50 x 50 pixels en ten hoogste 10000 x 10000 pixels zijn. 

## <a name="read-api"></a>API lezen 

De Computer Vision [Read-API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) is de nieuwste OCR-technologie van Azure (learn[what's new)](./whats-new.md)die gedrukte tekst extraheert (in verschillende talen), handgeschreven tekst (alleen Engels), cijfers en valutasymbolen uit afbeeldingen en PDF-documenten met meerdere pagina's. Het is geoptimaliseerd om tekst te extraheren uit tekstzware afbeeldingen en PDF-documenten met meerdere pagina's met gemengde talen. Het ondersteunt het detecteren van zowel gedrukte als handgeschreven tekst in dezelfde afbeelding of hetzelfde document.

![Hoe OCR afbeeldingen en documenten converteert naar gestructureerde uitvoer met geëxtraheerde tekst](./Images/how-ocr-works.svg)

### <a name="key-features"></a>Belangrijke functies

De Read-API bevat de volgende functies. 

* Tekstextractie afdrukken in 73 talen
* Handgeschreven tekstextractie in het Engels
* Tekstregels en woorden met locatie- en betrouwbaarheidsscores
* Er is geen taalidentificatie vereist
* Ondersteuning voor gemengde talen, gemengde modus (afdrukken en handgeschreven)
* Pagina's en paginabereiken selecteren uit grote documenten met meerdere pagina's
* Natuurlijke leesorde voor tekstregels
* Handschriftclassificatie voor tekstregels
* Beschikbaar als Distroless Docker-container voor on-premises implementatie

Meer [informatie over het gebruik van de OCR-functies.](./vision-api-how-to-topics/call-read-api.md)

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>De cloud-API gebruiken of on-premises implementeren
De Read 3.x-cloud-API's zijn de voorkeursoptie voor de meeste klanten vanwege de eenvoudige integratie en snelle productiviteit. Azure en de Computer Vision verwerken de behoeften op het gebied van schaal, prestaties, gegevensbeveiliging en naleving, terwijl u zich richt op het voldoen aan de behoeften van uw klanten.

Voor een on-premises implementatie kunt u met de [Read Docker-container (preview)](./computer-vision-how-to-install-containers.md) de nieuwe OCR-mogelijkheden implementeren in uw eigen lokale omgeving. Containers zijn ideaal voor specifieke vereisten voor beveiliging en gegevensbeheer.

## <a name="ocr-api"></a>OCR-API

De verouderde [OCR-API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) maakt gebruik van een ouder herkenningsmodel, ondersteunt alleen afbeeldingen en wordt synchroon uitgevoerd en retourneert onmiddellijk de gedetecteerde tekst. Zie de kolom OCR met [ondersteunde talen](./language-support.md#optical-character-recognition-ocr) voor een lijst met ondersteunde talen.

> [!WARNING]
> De Computer Vision 2.0 RecognizeText-bewerkingen worden afgeschaft in plaats van de nieuwe [Read-API](#read-api) die in dit artikel wordt behandeld. Bestaande klanten moeten [overstappen op het gebruik van Leesbewerkingen.](upgrade-api-versions.md)

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle Cognitive Services, dienen ontwikkelaars die de Computer Vision-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met de snelstart voor [OCR (Lezen) REST API of clientbibliotheek.](./quickstarts-sdk/client-library.md)
- Meer informatie over [de Read 3.2-REST API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
