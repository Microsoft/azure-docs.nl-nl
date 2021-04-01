---
title: Intenties toevoegen-LUIS
titleSuffix: Azure Cognitive Services
description: Voeg intenties toe aan uw LUIS-app om groepen vragen of opdrachten te identificeren die dezelfde bedoelingen hebben.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: f2401c032f0fc90024e0049fad5f696b8a184544
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018936"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Intenties toevoegen om te bepalen wat de gebruikers intentie van uitingen zijn

Voeg [intenties](luis-concept-intent.md) toe aan uw Luis-app om groepen vragen of opdrachten te identificeren die hetzelfde doel hebben.

In de LUIS-Portal worden de intenties beheerd vanuit de **eerste sectie van de navigatie** balk en vervolgens vanuit de **intenties** van het linkerdeel venster.

## <a name="add-an-intent-to-your-app"></a>Een intentie toevoegen aan uw app

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer op de pagina **intenties** **+ maken**.
1. In het dialoog venster **nieuwe intentie maken** voert u de naam van de doel groep in, bijvoorbeeld `ModifyOrder` en selecteert u **gereed**.

    > [!div class="mx-imgBorder"]
    > ![Intentie toevoegen](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Het doel van de opzet heeft [voor beeld uitingen](luis-concept-utterance.md) nodig om uitingen te voors pellen bij het gepubliceerde prediction-eind punt.

## <a name="add-an-example-utterance"></a>Een voorbeelduiting toevoegen

Voor beelden van uitingen zijn tekst voorbeelden van gebruikers vragen of-opdrachten. Als u Language Understanding wilt leren (LUIS) wanneer u dit opzet wilt voors pellen, moet u bijvoorbeeld uitingen toevoegen aan een intentie. LUIS moet binnen het bereik van 15 tot 30 voor beeld uitingen zijn om te beginnen met de bedoeling. Voeg geen voor beeld-uitingen toe. Elke utterance moet zorgvuldig worden gekozen voor de manier waarop deze afwijkt van de voor beelden die al in de bedoeling zijn.

1. Voer op de pagina Details van intentie een relevante utterance in die u van uw gebruikers verwacht, zoals `Deliver a large cheese pizza` in het tekstvak onder de naam van de intentie en druk vervolgens op ENTER.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van de pagina met details van de intenties, met utterance gemarkeerd](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS converteert alle uitingen naar kleine letters en voegt spaties rondom [tokens](luis-language-support.md#tokenization) , zoals afbreek streepjes, toe.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Voorspellings fouten in intentie

Een intentie Voorspellings fout wordt bepaald wanneer de utterance niet wordt voor speld met de getrainde app voor het doel.

1. Als u utterance-Voorspellings fouten wilt vinden en deze wilt herstellen, gebruikt u de **filter** opties onjuist en onduidelijk.

    > [!div class="mx-imgBorder"]
    > ![Gebruik de filter optie om utterance Voorspellings fouten te vinden en deze op te lossen.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. Als u de waarde van de score wilt weer geven op de detail pagina Details, selecteert u details van de **intentie weer geven** in het menu **weergave** opties.

    Wanneer de filters en weer gave worden toegepast en er voorbeeld uitingen zijn met fouten, worden de uitingen en de problemen weer gegeven in de lijst voor beeld utterance.

Elke rij toont de huidige Voorspellings Score van de training voor het voor beeld utterance, het dichtstbijzijnde aantal punten, het verschil in deze twee scores.

### <a name="fixing-intents"></a>Intenties herstellen

Gebruik het [dash board samen vatting](luis-how-to-use-dashboard.md)voor meer informatie over het oplossen van problemen met het Voorspellings beleid. Het dash board samen vatting bevat een analyse voor de laatste training van de actieve versie en biedt de beste suggesties voor het oplossen van uw model.

## <a name="using-the-contextual-toolbar"></a>De contextuele werk balk gebruiken

De context werkbalk bevat andere acties:

* Voor beeld-utterance bewerken of verwijderen
* Voor beeld-utterance opnieuw toewijzen aan een ander doel
* Filters en weer gaven: alleen uitingen weer geven met gefilterde entiteiten of optionele details weer geven
* Doorzoek bijvoorbeeld uitingen

## <a name="train-your-app-after-changing-model-with-intents"></a>Uw app trainen nadat u een model met intenties hebt gewijzigd

Nadat u intenties hebt toegevoegd, bewerkt of verwijderd, [traint](luis-how-to-train.md) en [publiceert](luis-how-to-publish-app.md) u uw app zodat uw wijzigingen worden toegepast op eindpunt query's. Niet trainen na elke wijziging. Train na een groep wijzigingen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van [voor beeld-uitingen](./luis-how-to-add-entities.md) met entiteiten.