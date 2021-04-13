---
title: Wat is optische teken herkenning?
titleSuffix: Azure Cognitive Services
description: De service voor optische teken herkenning (OCR) extraheert zicht bare tekst in een afbeelding en retourneert deze als gestructureerde teken reeksen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 41b3552a633c9cebce1138fa042dbd154eee0cb5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314113"
---
# <a name="what-is-optical-character-recognition"></a>Wat is optische teken herkenning?

Met de service voor optische teken herkenning (OCR) kunt u gedrukte of handgeschreven tekst uit afbeeldingen extra heren, zoals foto's van straat borden en producten, en van documenten &mdash; facturen, facturen, financiële rapporten, artikelen en meer. Het maakt gebruik van diep gaande modellen en werkt met tekst op verschillende Opper vlakken en achtergronden.

De OCR-Api's bieden ondersteuning voor het extra heren van gedrukte tekst in [verschillende talen](./language-support.md). Volg een [snelstart](./quickstarts-sdk/client-library.md) om aan de slag te gaan.

![OCR-demo's](./Images/ocr-demo.gif)

Deze documentatie bevat de volgende typen artikelen:
* In de [Quick](./quickstarts-sdk/client-library.md) starts vindt u stapsgewijze instructies voor het aanroepen van de service en het verkrijgen van resultaten in korte tijd. 
* De [hand leidingen](./Vision-API-How-to-Topics/call-read-api.md) bevatten instructies voor het gebruik van de service op meer specifieke of aangepaste manieren.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Ondersteunde talen
De OCR-Api's ondersteunen in totaal 73 talen voor de tekst van de afdruk stijl. Raadpleeg de volledige lijst met door [OCR ondersteunde talen](./language-support.md#optical-character-recognition-ocr). De handgeschreven OCR wordt alleen ondersteund voor Engels.

## <a name="input-requirements"></a>Vereisten voor invoer

De **Lees** aanroep neemt afbeeldingen en documenten op als invoer. Ze hebben de volgende vereisten:

* Ondersteunde bestands indelingen: JPEG, PNG, BMP, PDF en TIFF
* Voor PDF-en TIFF-bestanden worden Maxi maal 2000 pagina's (alleen de eerste twee pagina's voor de gratis laag) verwerkt.
* De bestands grootte moet kleiner zijn dan 50 MB (4 MB voor de gratis laag) en afmetingen ten minste 50 x 50 pixels en Maxi maal 10000 x 10000 pixels. 
* De PDF-dimensies mogen Maxi maal 17 x 17 inch zijn, overeenkomen met de papier formaten Legal of a3 en kleiner.

## <a name="read-api"></a>API lezen 

De Computer Vision [Lees-API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) is de nieuwste OCR-technologie van Azure ([informatie over wat er nieuw](./whats-new.md)is) waarmee gedrukte tekst (in verschillende talen), handgeschreven tekst (alleen Engels), cijfers en valuta symbolen van afbeeldingen en PDF-documenten met meerdere pagina's worden geëxtraheerd. Het is geoptimaliseerd voor het extra heren van tekst uit tekst-zware afbeeldingen en PDF-documenten met meerdere pagina's met gemengde talen. Het biedt ondersteuning voor het detecteren van gedrukte en handgeschreven tekst in dezelfde afbeelding of hetzelfde document.

![Hoe OCR afbeeldingen en documenten converteert naar gestructureerde uitvoer met geëxtraheerde tekst](./Images/how-ocr-works.svg)


## <a name="use-the-cloud-api-or-deploy-on-premise"></a>De Cloud-API gebruiken of on-premises implementeren
De lezen 3. x-Cloud-Api's zijn de voorkeurs optie voor de meeste klanten vanwege het gemak van integratie en snelle productiviteit. Azure en de Computer Vision service-afhandelings schaal, prestaties, gegevens beveiliging en nalevings behoeften terwijl u zich richt op het voldoen aan de behoeften van uw klanten.

Voor een on-premises implementatie kunt u met de [Lees docker-container (preview)](./computer-vision-how-to-install-containers.md) de nieuwe OCR-mogelijkheden in uw eigen lokale omgeving implementeren. Containers zijn ideaal voor specifieke vereisten voor beveiliging en gegevensbeheer.

## <a name="ocr-api"></a>OCR-API

De verouderde [OCR-API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f20d) maakt gebruik van een ouder herkennings model, ondersteunt alleen installatie kopieën en voert synchroon uit, wat direct met de gedetecteerde tekst wordt geretourneerd. Zie de OCR-kolom met [ondersteunde talen](./language-support.md#optical-character-recognition-ocr) voor een lijst met ondersteunde talen.

> [!WARNING]
> De RecognizeText-bewerkingen van Computer Vision 2,0 worden vervangen door de nieuwe [Lees-API](#read-api) die in dit artikel wordt besproken. Bestaande klanten moeten [overstappen op het gebruik van Lees bewerkingen](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle Cognitive Services, dienen ontwikkelaars die de Computer Vision-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met de [OCR (lezen) rest API of Quick](./quickstarts-sdk/client-library.md)starts van de client bibliotheek.
- Meer informatie over de [lees 3,2-rest API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
