---
title: Een model Custom Vision Service testen en opnieuw trainen
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u een installatie kopie kunt testen en deze vervolgens gebruikt om uw model in de Custom Vision-service te trainen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 736d135323e2a2df38a13dc46189e2a243fbe381
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097531"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Een model testen en opnieuw trainen met Custom Vision Service

Nadat u uw model hebt getraind, kunt u het snel testen met een lokaal opgeslagen installatie kopie of een online-installatie kopie. De test maakt gebruik van de meest recent getrainde iteratie.

## <a name="test-your-model"></a>Uw model testen

1. Selecteer uw project op de [webpagina Custom Vision](https://customvision.ai). Selecteer **snelle test** aan de rechter kant van de bovenste menu balk. Met deze actie wordt een venster met de naam **snelle test** geopend.

    ![De knop voor snelle test wordt weer gegeven in de rechter bovenhoek van het venster.](./media/test-your-model/quick-test-button.png)

2. Klik in het venster **snelle test** op het veld **installatie kopie verzenden** en voer de URL in van de afbeelding die u voor de test wilt gebruiken. Als u in plaats daarvan een lokaal opgeslagen installatie kopie wilt gebruiken, klikt u op de knop **Bladeren door lokale bestanden** en selecteert u een lokaal installatie kopie bestand.

    ![Afbeelding van de pagina afbeelding verzenden](./media/test-your-model/submit-image.png)

De afbeelding die u selecteert, wordt in het midden van de pagina weer gegeven. Vervolgens worden de resultaten weer gegeven onder de afbeelding in de vorm van een tabel met twee kolommen, **labels** met labels en **vertrouwen**. Nadat u de resultaten hebt bekeken, kunt u het venster voor **snelle test** sluiten.

U kunt deze test installatie kopie nu toevoegen aan uw model en vervolgens uw model opnieuw trainen.

## <a name="use-the-predicted-image-for-training"></a>De voorspelde afbeelding voor training gebruiken

Gebruik de volgende stappen om de installatie kopie te gebruiken die u eerder voor de training hebt ingediend:

1. Als u afbeeldingen wilt weer geven die zijn verzonden naar de classificatie, opent u de [webpagina Custom Vision](https://customvision.ai) en selecteert u het tabblad voor __spellingen__ .

    ![Afbeelding van het tabblad voor spellingen](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > In de standaard weergave worden afbeeldingen van de huidige herhaling weer gegeven. U kunt het veld vervolg keuzelijst __iteratie__ gebruiken om afbeeldingen weer te geven die zijn verzonden tijdens vorige iteraties.

2. Beweeg de muis aanwijzer over een afbeelding om de labels te zien die zijn voor speld door de classificatie.

    > [!TIP]
    > Installatie kopieën worden gerangschikt, zodat de installatie kopieën die de meeste winst naar de classificatie kunnen halen bovenaan staan. Als u een andere sorteer volgorde wilt selecteren, gebruikt u de sectie __sorteren__ .

    Als u een afbeelding wilt toevoegen aan uw trainings gegevens, selecteert u de afbeelding, selecteert u het label en selecteert u vervolgens __opslaan en sluiten__. De afbeelding wordt verwijderd uit de voor __spellingen__ en toegevoegd aan de trainings afbeeldingen. U kunt deze weer geven door het tabblad __trainings afbeeldingen__ te selecteren.

    ![Afbeelding van de pagina labelen](./media/test-your-model/tag-image.png)

3. Gebruik de knop __trainen__ om de classificatie opnieuw te trainen.

## <a name="next-steps"></a>Volgende stappen

[Uw model verbeteren](getting-started-improving-your-classifier.md)
