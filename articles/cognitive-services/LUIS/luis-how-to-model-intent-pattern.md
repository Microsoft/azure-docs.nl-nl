---
title: Nauw keurigheid van patronen toevoegen-LUIS
titleSuffix: Azure Cognitive Services
description: Voeg patroon sjablonen toe om de nauw keurigheid van de voor spellingen te verbeteren in Language Understanding-toepassingen (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5e1cc5cdda921e63315c2904d560b7ad3939776
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018851"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Patronen toevoegen om de nauw keurigheid van de voor spelling te verbeteren
Nadat een LUIS-app eind punt uitingen ontvangt, gebruikt u een [patroon](luis-concept-patterns.md) om de nauw keurigheid van de voor spelling te verbeteren voor uitingen die een patroon onthult in de woord volgorde en woord keuze. Patronen gebruiken specifieke [syntaxis](luis-concept-patterns.md#pattern-syntax) om de locatie van: [entiteiten](luis-concept-entity-types.md), entiteits [rollen](./luis-concept-entity-types.md)en optionele tekst aan te geven.

> [!CAUTION]
> Patronen zijn alleen van toepassing op machine learning-entiteit, niet voor subentiteiten.

## <a name="add-template-utterance-using-correct-syntax"></a>Sjabloon utterance toevoegen met de juiste syntaxis

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer **patronen** in het linkerpaneel onder Verbeter de **prestaties** van de app.

1. Selecteer de juiste intentie voor het patroon.

1. Typ in het tekstvak sjabloon de sjabloon utterance en selecteer ENTER. Wanneer u de naam van de entiteit wilt opgeven, gebruikt u de juiste syntaxis voor de patroon entiteit. Start de entiteits syntaxis met `{` . De lijst met entiteiten wordt weer gegeven. Selecteer de juiste entiteit.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van de entiteit voor het patroon](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Als uw entiteit een [rol](./luis-concept-entity-types.md)bevat, geeft u de rol op met één dubbele punt, `:` achter de naam van de entiteit, zoals `{Location:Origin}` . De lijst met rollen voor de entiteiten wordt weer gegeven in een lijst. Selecteer de rol en selecteer vervolgens ENTER.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van entiteit met rol](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Nadat u de juiste entiteit hebt geselecteerd, voltooit u de invoer van het patroon en selecteert u vervolgens ENTER. Wanneer u klaar bent met het invoeren van patronen, [traint](luis-how-to-train.md) u uw app.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van het ingevoerde patroon met beide typen entiteiten](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="create-a-patternany-entity"></a>Een patroon maken. elke entiteit

[Patroon. alle](luis-concept-entity-types.md) entiteiten zijn alleen geldig in [patronen](luis-how-to-model-intent-pattern.md), niet in de uitingen van voor beelden. Dit type entiteit helpt LUIS het einde van entiteiten met een verschillende lengte en woord keuze te vinden. Omdat deze entiteit in een patroon wordt gebruikt, weet LUIS waar het einde van de entiteit zich in de utterance-sjabloon bevindt.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer **+ maken**.

1. Voer in het dialoog venster **een entiteits type kiezen** de naam van de entiteit in het vak **naam** in en selecteer vervolgens **patroon. elk** **type** en selecteer vervolgens **maken**.

    Zodra u [een patroon utterance maakt](luis-how-to-model-intent-pattern.md) met behulp van deze entiteit, wordt de entiteit geëxtraheerd met een gecombineerd machine learning-en tekst vergelijkings algoritme.

## <a name="adding-example-utterances-as-pattern"></a>Voor beeld van uitingen als patroon toevoegen

Als u een patroon wilt toevoegen voor een entiteit, is de _eenvoudigste_ manier om het patroon te maken op de pagina Details van intentie. Dit zorgt ervoor dat uw syntaxis overeenkomt met het voor beeld utterance.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer op de pagina lijst met **doel** stellingen de doel naam van het voor beeld utterance u een sjabloon utterance wilt maken.
1. Selecteer op de pagina Details van intentie de rij voor het voor beeld-utterance dat u wilt gebruiken als de sjabloon utterance en selecteer **+ toevoegen als patroon** in de context werkbalk.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van het selecteren van voor beeld-utterance als sjabloon patroon op de detail pagina Details.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

    De utterance moet een entiteit bevatten om een patroon te maken op basis van de utterance.

1. Selecteer in het pop-upvenster **gereed** op de pagina **patronen bevestigen** . U hoeft de subentiteiten of onderdelen van de entiteiten niet te definiëren. U hoeft alleen de entiteit machine learning weer te geven.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van het bevestigende voor beeld-utterance als sjabloon patroon op de detail pagina van het doel.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Als u de sjabloon moet bewerken, zoals het selecteren van tekst als optioneel, met behulp van de vier `[]` Kante haakjes, moet u deze bewerking op de pagina **patronen** maken.

1. Selecteer in de navigatie balk **Train** om de app te trainen met het nieuwe patroon.

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uw app trainen na het wijzigen van het model met patronen
Nadat u een patroon hebt toegevoegd, bewerkt, verwijderd of opnieuw toewijst, [traint](luis-how-to-train.md) u uw app en [publiceert](luis-how-to-publish-app.md) u deze voor uw wijzigingen om eindpunt query's te beïnvloeden.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Contextuele werk balk gebruiken

Met de contextuele werk balk boven de lijst met patronen kunt u het volgende doen:

* Patronen zoeken
* Een patroon bewerken
* Afzonderlijk patroon toewijzen aan ander doel
* Verschillende patronen opnieuw toewijzen aan verschillende doel stellingen
* Delete-a-enkel patroon
* Verschillende patronen verwijderen
* Filter patroon lijst op entiteit
* Filter-pattern-list-by-intentie
* Entiteit of intentie filter verwijderen
* Patroon toevoegen vanuit bestaande utterance op de pagina intentie of entiteit

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [bouwen van een patroon](luis-tutorial-pattern.md) met een patroon. alle en rollen met een zelf studie.
* Meer informatie over het [trainen](luis-how-to-train.md) van uw app.
