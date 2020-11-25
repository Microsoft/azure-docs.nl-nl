---
title: Voor beeld van een label entiteit utterance
description: Meer informatie over het labelen van een machine learning-entiteit met subentiteiten in een voor beeld van een utterance op een pagina met details van de LUIS-Portal.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019732"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Label machine-learning-entiteit in een voor beeld-utterance

Het labelen van een entiteit in een voor beeld utterance geeft LUIS een voor beeld van wat de entiteit is en waar de entiteit kan worden weer gegeven in de utterance.

U kunt door machines geleerde entiteiten en subentiteiten labelen.

Omdat u geen reguliere expressie, lijst of vooraf gedefinieerde entiteiten kunt labelen, maakt u een entiteit of een subentiteit en voegt u deze entiteiten vervolgens toe als onderdelen, indien van toepassing, aan de entiteit of subentiteit.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Voor beeld van een label uitingen van de detail pagina van de intentie

Als u voor beelden wilt van entiteiten in de utterance, selecteert u de intentie van de utterance.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer de intentie met het voor beeld uitingen dat u wilt labelen voor extractie met een entiteit.
1. Selecteer de tekst die u wilt labelen en selecteer vervolgens de entiteit.

## <a name="two-techniques-to-label-entities"></a>Twee technieken voor het labelen van entiteiten

Er worden twee label technieken ondersteund op de detail pagina van het doel.
* Selecteer entiteit of subentiteit in het [palet entiteit](#label-with-the-entity-palette-visible) en selecteer vervolgens in voor beeld utterance tekst. Dit is de aanbevolen techniek, omdat u visueel kunt controleren of u met de juiste entiteit of subentiteit werkt, volgens het schema.
* Selecteer eerst de tekst van het voor beeld utterance. Wanneer u dit doet, wordt er een pop-upmenu met [label opties](#how-to-label-entity-from-in-place-menu) weer gegeven.

## <a name="label-with-the-entity-palette-visible"></a>Label met het entiteits palet zichtbaar

Wanneer u [uw schema met entiteiten hebt gepland](luis-how-plan-your-app.md), moet u het **entiteits palet** zichtbaar blijven tijdens het labelen. Het **entiteits palet** is een herinnering van de entiteiten die u wilt uitpakken.

Om toegang te krijgen tot het **entiteits palet**, selecteert u het **@** symbool in de contextuele werk balk boven de lijst voor beeld utterance.

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding van het deel venster entity op de pagina Details van intentie.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>Hoe kan ik een label voor entiteit uit het entiteits palet

Het entiteits palet biedt een alternatief voor de vorige label ervaring. Hiermee kunt u tekst vervormen om deze direct te labelen met een entiteit.

1. Open het palet entity door op het **@** symbool rechtsboven in de tabel utterance te klikken.

2. Selecteer de entiteit in het palet dat u wilt labelen. Deze actie wordt visueel aangeduid met een nieuwe cursor. De cursor volgt de muis tijdens het verplaatsen in de LUIS-Portal.

3. In het voor beeld utterance _tekent_ u de entiteit met de cursor.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont de entiteit die met de cursor is getekend.](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Entiteit toevoegen als een functie uit het entiteits palet

In het onderste gedeelte van het entiteits palet kunt u functies toevoegen aan de entiteit die momenteel is geselecteerd. U kunt kiezen uit alle bestaande entiteiten en woordgroepen lijsten of een nieuwe woordgroepen lijst maken.

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding van het entiteits palet met entiteit als onderdeel](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Entiteits rollen labelen

Entiteits rollen worden aangeduid met het **palet entity**.

1. Selecteer op de detail pagina Details het **entiteits palet** in de context werkbalk.
1. Nadat het entiteits palet wordt geopend, selecteert u de entiteit in de lijst met entiteiten.
1. Selecteer een bestaande rol onder de lijst met entiteiten.
1. In het voor beeld utterance tekst labelt u de tekst met de rol van entiteit.

## <a name="how-to-label-entity-from-in-place-menu"></a>De entiteit labelen vanuit een in-place menu

Met labelen in-place kunt u de tekst in de utterance snel selecteren en labelen. U kunt ook een machine learning entiteit of lijst entiteit maken op basis van de gelabelde tekst.

Bekijk het voor beeld utterance, `hi, please I want a cheese pizza in 20 minutes` .

Selecteer de meest linkse tekst en selecteer vervolgens de juiste tekst van de entiteit en kies in het menu in de locatie de entiteit waaraan u een label wilt plaatsen.

> [!div class="mx-imgBorder"]
> ![Label voor volledige machine learning-entiteit](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>Gelabelde tekst controleren

Bekijk na het labelen het voor beeld utterance en zorg ervoor dat de geselecteerde hoeveelheid tekst is onderstreept met de gekozen entiteit. De ononderbroken lijn geeft aan dat de tekst is gelabeld.

> [!div class="mx-imgBorder"]
> ![Gelabelde volledige machine learning-entiteit](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Voorspelde entiteit bevestigen

Als er een vak met een stippel lijn rond de hoeveelheid tekst bestaat, wordt aangegeven dat de tekst wordt voor speld, maar _nog niet is gelabeld_. Als u de voor spelling in een label wilt omzetten, selecteert u de rij utterance en selecteert u **entiteiten bevestigen** op de contextuele werk balk.

## <a name="relabeling-over-existing-entities"></a>Labeling over bestaande entiteiten opnieuw bepalen

Als u tekst opnieuw labelt die al een label heeft, kan LUIS bestaande labels splitsen of samen voegen.

## <a name="labeling-for-punctuation"></a>Labels voor interpunctie

U hoeft geen label voor interpunctie in te voeren. Gebruik [Toepassings instellingen](luis-reference-application-settings.md) om te bepalen hoe interpunctie invloed heeft op utterance voor spellingen.

## <a name="unlabel-entities"></a>Entiteiten onlabelen

> [!NOTE]
> Alleen door de machine geleerde entiteiten kunnen worden ontmerkt. U kunt gewone expressie-entiteiten, lijst entiteiten of vooraf gemaakte entiteiten niet labelen of onlabelen.

Als u het label van een entiteit wilt opheffen, selecteert u de entiteit en selecteert u **Label** in het menu in de locatie.

> [!div class="mx-imgBorder"]
> ![Scherm opname van entiteit met onlabeling](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Automatische labeling voor bovenliggende en onderliggende entiteiten

Als u een bovenliggende entiteit labelt, krijgt elke subentiteit die kan worden voor speld op basis van de momenteel getrainde versie, een label.

Als u labels voor een subentiteit labelt, wordt het bovenliggende item automatisch gelabeld.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Automatische labeling voor niet door de machine geleerde entiteiten

Niet-computer geleerde entiteiten bevatten vooraf gemaakte entiteiten, reguliere expressie-entiteiten, lijst entiteiten en het patroon. alle entiteiten. Deze worden automatisch gelabeld door LUIS, zodat ze niet hoeven hand matig te worden gelabeld door gebruikers.

## <a name="intent-prediction-errors"></a>Voorspellings fouten in intentie

Een intentie-Voorspellings fout geeft aan dat het voor beeld utterance, op basis van de huidige getrainde app, niet voor de intentie wordt voor speld.

Meer informatie over het [weer geven van deze fouten](luis-how-to-add-intents.md#intent-prediction-errors) op de detail pagina van de intentie.

## <a name="entity-prediction-errors"></a>Voorspellings fouten van entiteit

Voor de voor spelling van entiteits fouten wordt aangegeven dat de voorspelde entiteit niet overeenkomt met de entiteit met het label. Dit wordt gevisualiseerd met een waarschuwings indicator naast het utterance.

> [!div class="mx-imgBorder"]
> ![Het palet van de entiteit voor de machine learning-entiteit](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Volgende stappen

Gebruik het [dash board](luis-how-to-use-dashboard.md) en [Bekijk het eind punt uitingen](luis-how-to-review-endpoint-utterances.md) om de kwaliteit van de voor spelling van uw app te verbeteren.
