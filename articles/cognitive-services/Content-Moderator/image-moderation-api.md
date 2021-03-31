---
title: Afbeeldings toezicht-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik Content Moderator de door de machine gesteunde afbeeldings controle en het hulp programma voor het inchecken van de werk nemer naar gematige installatie kopieën voor inhoud voor volwassenen en ongepaste.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: fe76e32bfd9b1734f3c84a400f897b7af7e3168b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800992"
---
# <a name="learn-image-moderation-concepts"></a>Concepten van afbeeldings toezicht leren

Gebruik Content Moderator door de machine gestuurde afbeeldings toezicht en het [controle programma](Review-Tool-User-Guide/human-in-the-loop.md) voor gematige installatie kopieën voor inhoud voor volwassenen en ongepaste. Afbeeldingen scannen voor tekst inhoud en deze tekst uitpakken en gezichten detecteren. U kunt afbeeldingen afstemmen op aangepaste lijsten en verdere actie ondernemen.

## <a name="evaluating-for-adult-and-racy-content"></a>Beoordeling voor inhoud voor volwassenen en ongepaste

Met de bewerking **evalueren** wordt een betrouwbaarheids Score geretourneerd tussen 0 en 1. Ook worden Boole-gegevens geretourneerd die gelijk zijn aan True of false. Met deze waarden wordt voor speld dat de afbeelding potentiële inhoud voor volwassenen of ongepaste bevat. Wanneer u de API aanroept met uw installatie kopie (bestand of URL), bevat het geretourneerde antwoord de volgende informatie:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - `isImageAdultClassified` geeft de mogelijke aanwezigheid weer van afbeeldingen die in bepaalde situaties als seksueel expliciet of erotisch kunnen worden beschouwd.
> - `isImageRacyClassified` geeft de mogelijke aanwezigheid weer van afbeeldingen die in bepaalde situaties als suggestief of voor volwassenen kunnen worden beschouwd.
> - De scores liggen tussen 0 en 1. Hoe hoger de score, hoe hoger het model is om te voors pellen dat de categorie van toepassing kan zijn. Dit voor beeld is afhankelijk van een statistisch model in plaats van hand matige gecodeerde resultaten. We raden u aan om te testen met uw eigen inhoud om te bepalen hoe elke categorie wordt uitgelijnd op uw vereisten.
> - De Booleaanse waarden zijn waar of onwaar, afhankelijk van de drempel waarden van de interne Score. Klanten moeten beoordelen of u deze waarde moet gebruiken of besluiten over aangepaste drempel waarden op basis van hun inhouds beleid.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Tekst detecteren met optische teken herkenning (OCR)

De **optische teken herkenning (OCR)-** bewerking voor spelt de aanwezigheid van tekst inhoud in een afbeelding en extraheert deze voor tekst toezicht, onder andere gebruikt. U kunt de taal opgeven. Als u geen taal opgeeft, wordt de detectie standaard ingesteld op Engels.

Het antwoord bevat de volgende informatie:
- De oorspronkelijke tekst.
- De gedetecteerde tekst elementen met hun betrouwbaarheids scores.

Voor beeld extractie:

```json
"TextDetection": {
    "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
    },
    .........
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
},
```

## <a name="detecting-faces"></a>Gezichten detecteren

Het detecteren van gezichten helpt bij het detecteren van persoons gegevens, zoals gezichten in de installatie kopieën. U detecteert mogelijke gezichten en het aantal mogelijke gezichten in elke afbeelding.

Een antwoord bevat de volgende informatie:

- Aantal gezichten
- Lijst met locaties van gezichten gedetecteerd

Voor beeld extractie:

```json
"FaceDetection": {
    ......
    "result": true,
    "count": 2,
    "advancedInfo": [
        .....
    ],
    "faces": [
        {
            "bottom": 598,
            "left": 44,
            "right": 268,
            "top": 374
        },
        {
            "bottom": 620,
            "left": 308,
            "right": 532,
            "top": 396
        }
    ]
}
```

## <a name="creating-and-managing-custom-lists"></a>Aangepaste lijsten maken en beheren

Na het uploaden van een installatie kopie of ander type inhoud, kunnen aanstootgevende items in veel online community's meerdere keren worden gedeeld gedurende de volgende dagen, weken en maanden. De kosten voor het herhaaldelijk scannen en filteren van dezelfde afbeelding of zelfs gewijzigde versies van de afbeelding vanaf meerdere locaties kunnen duur en fout gevoelig zijn.

In plaats van dezelfde installatie kopie meermaals te gebruiken, voegt u de aanstootgevende installatie kopieën toe aan uw aangepaste lijst met geblokkeerde inhoud. Op die manier vergelijkt uw systeem voor content toezicht inkomende installatie kopieën met uw aangepaste lijsten en stopt verdere verwerking.

> [!NOTE]
> Er is een maximumlimiet van **5 afbeeldingslijsten** waarbij elke lijst **niet meer dan 10.000 afbeeldingen mag bevatten**.
>

De Content Moderator biedt een volledige [lijst beheer-API voor installatie kopieën](try-image-list-api.md) met bewerkingen voor het beheren van lijsten met aangepaste installatie kopieën. Begin met de [lijst met installatie kopieën](try-image-list-api.md) van de API-console en gebruik de rest API code voorbeelden. Bekijk ook de [lijst met installatie kopieën .net Quick](image-lists-quickstart-dotnet.md) start als u bekend bent met Visual Studio en C#.

## <a name="matching-against-your-custom-lists"></a>Vergelijken met uw aangepaste lijsten

Met de bewerking match kunt u het gebruik van de bewerkingen in de lijst opwaarderen voor een wille keurige, door u gemaakte en beheerde installatie kopieën.

Als er een overeenkomst wordt gevonden, retourneert de bewerking de id en de moderator Tags van de overeenkomende afbeelding. Het antwoord bevat de volgende informatie:

- Overeenkomende Score (tussen 0 en 1)
- Overeenkomende afbeelding
- Afbeeldings Tags (toegewezen tijdens vorige toezicht)
- Afbeeldings labels

Voor beeld extractie:

```json
{
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
}
```

## <a name="review-tool"></a>Beoordelingsprogramma

Gebruik het [hulp programma content moderator beoordeling](Review-Tool-User-Guide/human-in-the-loop.md) en de bijbehorende API om de controle resultaten en-inhoud in de beoordeling voor uw menselijke moderators te laten weer geven. Ze bekijken de door de machine toegewezen tags en bevestigen hun definitieve beslissingen.

![Beoordeling van afbeelding voor menselijke beoordelaars](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Volgende stappen

Test de [API-console voor Image toezicht](try-image-api.md) en gebruik de rest API code voorbeelden. Zie ook [Recensies, werk stromen en taken](./review-api.md) voor meer informatie over het instellen van Human Beoordelingen.
