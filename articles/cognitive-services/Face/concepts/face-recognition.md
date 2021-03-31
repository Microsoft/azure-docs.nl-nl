---
title: Gezichtsherkenningsconcepten
titleSuffix: Azure Cognitive Services
description: In dit artikel worden de concepten uitgelegd van de berekenings-en onderhouds bewerkingen voor het verifiëren, vergelijken, groeperen en identificeren van het gezicht en de onderliggende gegevens structuren.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92518803"
---
# <a name="face-recognition-concepts"></a>Gezichtsherkenningsconcepten

In dit artikel worden de concepten uitgelegd van de berekenings-en onderhouds bewerkingen voor het verifiëren, vergelijken, groeperen en identificeren van het gezicht en de onderliggende gegevens structuren. In het algemeen wordt met herkenning het werk van het vergelijken van twee verschillende gezichten beschreven om te bepalen of ze vergelijkbaar zijn of horen bij dezelfde persoon.

## <a name="recognition-related-data-structures"></a>Gegevens structuren met betrekking tot herkenning

De herkennings bewerkingen gebruiken voornamelijk de volgende gegevens structuren. Deze objecten worden opgeslagen in de Cloud en hiernaar kan worden verwezen met de ID-teken reeksen. ID-teken reeksen zijn altijd uniek binnen een abonnement. Naam velden kunnen worden gedupliceerd.

|Naam|Beschrijving|
|:--|:--|
|DetectedFace| Deze enkelvoudige gezichts weergave wordt opgehaald door de [gezichts detectie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) bewerking. De ID verloopt 24 uur nadat deze is gemaakt.|
|PersistedFace| Wanneer DetectedFace-objecten worden toegevoegd aan een groep, zoals FaceList of persoon, worden ze PersistedFace-objecten. Ze kunnen op elk gewenst moment worden [opgehaald](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) en verloopt niet.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) of [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Deze gegevens structuur is een geassorteerde lijst met PersistedFace-objecten. Een FaceList heeft een unieke ID, een naam reeks en optioneel een gebruikers gegevens reeks.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Deze gegevens structuur is een lijst met PersistedFace-objecten die deel uitmaken van dezelfde persoon. Het heeft een unieke ID, een naam reeks en optioneel een gebruikers gegevens reeks.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) of [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Deze gegevens structuur is een geassorteerde lijst met objecten van personen. Het heeft een unieke ID, een naam reeks en optioneel een gebruikers gegevens reeks. Een PersonGroup moet worden [getraind](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) voordat deze kan worden gebruikt in herkennings bewerkingen.|

## <a name="recognition-operations"></a>Herkennings bewerkingen

In deze sectie wordt uitgelegd hoe de vier herkennings bewerkingen gebruikmaken van de eerder beschreven gegevens structuren. Zie [overzicht](../Overview.md)voor een uitgebreide beschrijving van elke herkennings bewerking.

### <a name="verify"></a>Verifiëren

Bij de [controle](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) bewerking wordt een gezichts-id van DetectedFace of PersistedFace en een ander gezichts-id of persoons object gebruikt en wordt bepaald of deze bij dezelfde persoon horen. Als u een persoons object doorgeeft, kunt u eventueel een PersonGroup door geven waartoe deze persoon behoort om de prestaties te verbeteren.

### <a name="find-similar"></a>Zoek vergelijk bare

De [Zoek vergelijk bare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) bewerking neemt een gezichts-id van DetectedFace of PersistedFace en een FaceList of een matrix met andere gezichts-id's. Met een FaceList wordt een kleiner FaceList geretourneerd van gezichten die vergelijkbaar zijn met het opgegeven gezicht. Met een matrix met gezichts-Id's, wordt er ook een kleinere matrix geretourneerd.

### <a name="group"></a>Groep

De [groeps](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) bewerking heeft een matrix van geassorteerde gezichts-Id's van DetectedFace of PersistedFace en retourneert dezelfde id's die zijn gegroepeerd in verschillende kleinere matrices. Elke matrix ' groups ' bevat gezichts-Id's die vergelijkbaar zijn. Eén messyGroup-matrix bevat gezichts-Id's waarvoor geen overeenkomsten zijn gevonden.

### <a name="identify"></a>Vinden

Bij de [identificerende](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) bewerking worden een of meer gezichts-Id's van DetectedFace of PersistedFace en een PersonGroup opgehaald en wordt een lijst met objecten geretourneerd waarop elk gezicht kan worden aangesloten. Geretourneerde persoons objecten worden verpakt als objecten van de kandidaat, die een Voorspellings betrouwbaarheids waarde hebben.

## <a name="input-data"></a>Invoergegevens

Gebruik de volgende tips om ervoor te zorgen dat uw invoer installatie kopieën de meest nauw keurige herkennings resultaten geven:

* De ondersteunde indelingen voor de invoer afbeeldingen zijn JPEG, PNG, GIF (het eerste frame), BMP.
* De grootte van het afbeeldings bestand mag niet groter zijn dan 6 MB.
* Wanneer u persoons objecten maakt, gebruikt u Foto's die verschillende soorten hoeken en verlichting hebben.
* Sommige gezichten worden mogelijk niet herkend vanwege technische problemen, zoals:
  * Afbeeldingen met extreme verlichting, bijvoorbeeld ernstig achtergrond verlichting.
  * Obstakels waarmee één of beide ogen worden geblokkeerd.
  * Verschillen in haar aard of gezicht.
  * Wijzigingen in gezichts vormgeving vanwege leeftijd.
  * Extreme gezichts uitdrukkingen.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de concepten van gezichts herkenning, schrijft u een script dat gezichten identificeert op basis van een getrainde PersonGroup.

* [Quickstart voor de Face-clientbibliotheek](../Quickstarts/client-libraries.md)