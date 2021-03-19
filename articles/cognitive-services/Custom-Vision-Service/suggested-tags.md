---
title: Afbeeldingen sneller labelen met Smart Labeler
titleSuffix: Azure Cognitive Services
description: In deze hand leiding leert u hoe u Smart Labeler kunt gebruiken om voorgestelde Tags voor installatie kopieën te genereren. Zo kunt u sneller een groot aantal afbeeldingen labelen bij het trainen van een Custom Vision-model.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "73647755"
---
# <a name="label-images-faster-with-smart-labeler"></a>Afbeeldingen sneller labelen met Smart Labeler

In deze hand leiding leert u hoe u Smart Labeler kunt gebruiken om voorgestelde Tags voor installatie kopieën te genereren. Zo kunt u sneller een groot aantal afbeeldingen labelen bij het trainen van een Custom Vision-model.

Wanneer u afbeeldingen labelt voor een Custom Vision model, gebruikt de service de laatste getrainde iteratie van het model om de labels van niet-gelabelde afbeeldingen te voors pellen. Vervolgens worden deze voor spellingen als voorgestelde Tags weer gegeven op basis van de geselecteerde betrouwbaarheids drempel en Voorspellings onzekerheid. U kunt de suggesties vervolgens bevestigen of wijzigen en het proces van het hand matig coderen van de afbeeldingen voor training versnellen.

## <a name="when-to-use-smart-labeler"></a>Wanneer Smart Labeler gebruiken

Houd de volgende beperkingen in acht:

* U moet alleen voorgestelde Tags aanvragen voor installatie kopieën waarvan de inhoud eenmaal is getraind. U krijgt geen suggesties voor een nieuwe tag die u alleen aan het beginnen bent.

> [!IMPORTANT]
> De functie Smart Labeler maakt gebruik van hetzelfde [prijs model](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) als normale voor spellingen. De eerste keer dat u voorgestelde labels voor een set met installatie kopieën triggert, worden de kosten voor Voorspellings aanroepen op dezelfde plaats in rekening gebracht. Daarna slaat de service gedurende 30 dagen de resultaten voor de geselecteerde installatie kopieën op in een Data Base. u kunt ze op elk gewenst moment binnen die periode openen. Na 30 dagen worden er kosten in rekening gebracht als u de voorgestelde Tags opnieuw aanvraagt.

## <a name="smart-labeler-workflow"></a>Smart Labeler werk stroom

De volgende stappen laten zien hoe u Smart Labeler gebruikt:

1. Upload al uw trainings afbeeldingen naar uw Custom Vision-project.
1. Label deel van uw gegevensset, waarbij u een gelijk aantal installatie kopieën voor elke tag kiest.
    > [!TIP]
    > Zorg ervoor dat u alle tags gebruikt waarvoor u later suggesties wilt.
1. Start het trainings proces.
1. Wanneer de training is voltooid, gaat u naar de weer gave zonder **Tags** en selecteert u de knop **voorgestelde labels ophalen** in het linkerdeel venster.
    > [!div class="mx-imgBorder"]
    > ![De knop voorgestelde labels wordt weer gegeven onder het tabblad niet-gecodeerde afbeeldingen.](./media/suggested-tags/suggested-tags-button.png)
1. In het pop-upvenster dat wordt weer gegeven, stelt u het aantal installatie kopieën in waarvoor u suggesties wilt. U moet alleen initiële label suggesties voor een deel van de niet-gecodeerde afbeeldingen ophalen. U krijgt betere label suggesties wanneer u dit proces doorloopt.
1. Bevestig de voorgestelde Tags en los ze op.
    > [!TIP]
    > Afbeeldingen met voorgestelde Tags worden gesorteerd op de onzekerheid van de voor spelling (lagere waarden geven een hogere betrouw baarheid aan). U kunt de sorteer volgorde wijzigen met de optie **sorteren op onzekere** . Als u de volg orde op **hoog** instelt, kunt u de voor spellingen met hoge onzekerheid eerst corrigeren en vervolgens snel de onzekerheid bevestigen.
    * In afbeeldings classificatie projecten kunt u tags in batches selecteren en bevestigen. Filter de weer gave op een bepaalde voorgestelde tag, schakel de selectie van installatie kopieën die onjuist zijn gelabeld uit en bevestig de rest in een batch.
        > [!div class="mx-imgBorder"]
        > ![Voorgestelde labels worden in batch modus voor IC met filters weer gegeven.](./media/suggested-tags/ic-batch-mode.png)

        U kunt ook aanbevolen tags in de modus individuele afbeelding gebruiken door een afbeelding in de galerie te selecteren.

        ![Voorgestelde Tags worden weer gegeven in de modus voor afzonderlijke afbeeldingen voor IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * In object detectie projecten worden batch bevestigingen niet ondersteund, maar u kunt nog steeds filteren en sorteren op voorgestelde Tags voor een meer gestructureerde label ervaring. Miniaturen van uw niet-gelabelde afbeeldingen tonen een overlay van selectie kaders die de locaties van voorgestelde Tags aangeven. Als u geen voorgesteld label filter selecteert, worden al uw niet-gelabelde afbeeldingen weer gegeven zonder dat de selectie vakjes worden bedekt.
        > [!div class="mx-imgBorder"]
        > ![Voorgestelde labels worden weer gegeven in de batch modus voor OD met filters.](./media/suggested-tags/od-batch-mode.png)

        Als u de tags voor object detectie wilt bevestigen, moet u deze Toep assen op elke afzonderlijke afbeelding in de galerie.

        ![Voorgestelde Tags worden weer gegeven in de afzonderlijke afbeeldings modus voor OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Start het trainings proces opnieuw.
1. Herhaal de voor gaande stappen totdat u tevreden bent met de kwaliteit van de suggestie.

## <a name="next-steps"></a>Volgende stappen

Volg een Snelstartgids om aan de slag te gaan met het maken en trainen van een Custom Vision project.

* [Een classificatie maken](getting-started-build-a-classifier.md)
* [Een objectdetector maken](get-started-build-detector.md)