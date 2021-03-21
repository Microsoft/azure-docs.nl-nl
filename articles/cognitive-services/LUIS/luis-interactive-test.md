---
title: App testen in de LUIS-Portal
description: Gebruik Language Understanding (LUIS) om voortdurend aan uw toepassing te werken om deze te verfijnen en de taal inzichten te verbeteren.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 31885eba16d59e2e48a08f84c56271b84e6c565f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790915"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Uw LUIS-app testen in de LUIS-Portal

Het [testen](luis-concept-test.md) van een app is een iteratief proces. Nadat u uw LUIS-app hebt getraind, test u deze met voor beeld-uitingen om te zien of de intenties en entiteiten goed zijn herkend. Als dat niet het geval is, moet u updates voor de LUIS-app, Train en test opnieuw uitvoeren.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Trainen voor testen

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Als u wilt testen op de meest recente versie van de actieve app, selecteert u **trainen** in het bovenste menu voordat u gaat testen.

## <a name="test-an-utterance"></a>Een utterance testen

De test utterance mag niet exact hetzelfde zijn als een voor beeld van een uitingen in de app. De test utterance moet de woord keuze, de woord lengte en het entiteits gebruik bevatten die u verwacht voor een gebruiker.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.

1. Als u toegang wilt krijgen tot het **toetsen paneel testen** , selecteert u **testen** in het bovenste paneel van de toepassing.

    > [!div class="mx-imgBorder"]
    > ![App-pagina voor Train &-test](./media/luis-how-to-interactive-test/test.png)

1. Voer een utterance in het tekstvak in en selecteer ENTER. U kunt net zoveel test uitingen als u wilt in de **test** typen, maar slechts één utterance tegelijk.

1. De utterance, de belangrijkste intentie en de score worden toegevoegd aan de lijst met uitingen onder het tekstvak.

    > [!div class="mx-imgBorder"]
    > ![Interactieve tests duiden het verkeerde doel aan](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>De voor spelling controleren

U kunt de details van het test resultaat bekijken in het deel venster **inspecteren** .

1. **Selecteer in** het deel venster voor het **testen** van de schuif regelaar naar een utterance die u wilt vergelijken.

    > [!div class="mx-imgBorder"]
    > ![Selecteer de knop controleren om meer informatie over de test resultaten weer te geven](./media/luis-how-to-interactive-test/inspect.png)

1. Het deel venster **inspectie** wordt weer gegeven. Het paneel bevat de belangrijkste Score intentie en alle geïdentificeerde entiteiten. In het deel venster wordt de voor spelling van de geselecteerde utterance weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Gedeeltelijke scherm opname van het controle paneel voor testen](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>Toevoegen aan voor beeld uitingen

In het deel venster inspectie kunt u de test utterance toevoegen aan een intentie door **toevoegen aan voor beeld uitingen** te selecteren.

## <a name="disable-required-features"></a>Vereiste onderdelen uitschakelen

Met deze wissel knop kunt u bepalen of de getrainde app op de juiste wijze uw entiteiten kan voors pellen op basis van de vereiste onderdelen. De standaard instelling is het Toep assen van de functie zoals vereist tijdens de voor spelling. Selecteer deze wissel knop om te zien wat de voor spelling zou zijn als de functie van de subentiteit niet is vereist.

### <a name="when-to-disable-required-features"></a>Wanneer u de vereiste onderdelen wilt uitschakelen

De getrainde app kan een door de machine geleerde entiteit op basis van een van de volgende voor spelden:
* Onjuiste labeling van bijvoorbeeld uitingen.
* De vereiste functie komt niet overeen met de tekst.

Een voor beeld is een door een machine geleerde entiteit met een subentiteit van de naam van een persoon.

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="Scherm opname van door de LUIS Portal machine geleerde entiteits schema met vereiste functie":::

Een voor beeld van een utterance voor deze entiteit is: `Assign Bob Jones to work on the new security feature` .

De extractie moet worden beschouwd `security feature` als de beschrijving van het ticket en `Bob Jones` als de engineer, twee subentiteiten `Assign ticket` .

Om ervoor te zorgen dat de subentiteit kan voors pellen, voegt u de vooraf opgebouwde entiteit [persoonnaam](luis-reference-prebuilt-person.md) AA een functie toe aan de `engineer` subentiteit. Als u de functie verplicht maakt, betekent dit dat de subentiteit alleen wordt geëxtraheerd als de vooraf samengestelde entiteit voor de persoon voor de tekst wordt voor speld. Dit betekent dat een wille keurige naam in de tekst die geen voor spelde subentiteit heeft, als een gelabelde subentiteit wordt geretourneerd `engineer` .

Wanneer u het interactieve test venster gebruikt en een subentiteit ziet, met een vereiste functie, niet voor te voors pellen, schakelt u deze instelling uit om te zien of de subentiteit zou worden voor speld zonder dat de functie vereist is. Het kan zijn dat de subentiteit op de juiste wijze kan worden voor speld zonder de functie als vereist vanwege het corrigeren van het labelen van bijvoorbeeld uitingen.

## <a name="view-sentiment-results"></a>Sentiment-resultaten weer geven

Als **sentiment analyse** is geconfigureerd op de pagina **[publiceren](luis-how-to-publish-app.md#enable-sentiment-analysis)** , bevatten de test resultaten de sentiment die zijn gevonden in de utterance.

## <a name="correct-matched-patterns-intent"></a>Juist overeenkomend patroon intentie

Als u [patronen](luis-concept-patterns.md) gebruikt en de utterance overeenkomt met een patroon, maar de verkeerde intentie is voor speld, selecteert u de koppeling **bewerken** op basis van het patroon en selecteert u vervolgens de juiste intentie.

## <a name="compare-with-published-version"></a>Vergelijken met gepubliceerde versie

U kunt de actieve versie van uw app testen met de gepubliceerde versie van het [eind punt](luis-glossary.md#endpoint) . Selecteer in het deel venster **inspecteren** de optie **compare with published**. Elk testen op basis van het gepubliceerde model wordt in mindering gebracht op het quotum saldo van uw Azure-abonnement.

> [!div class="mx-imgBorder"]
> ![Vergelijken met gepubliceerde](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>JSON van eind punt weer geven in het test paneel
U kunt de eindpunt-JSON weer geven die is geretourneerd voor de vergelijking door de **weer gave JSON** weer geven te selecteren.

> [!div class="mx-imgBorder"]
> ![Gepubliceerd JSON-antwoord](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Aanvullende instellingen in het test paneel

### <a name="luis-endpoint"></a>LUIS-eind punt

Als u meerdere LUIS-eind punten hebt, gebruikt u de koppeling **extra instellingen** in het gepubliceerde deel venster van de test om het eind punt te wijzigen dat wordt gebruikt voor het testen. Als u niet zeker weet welk eind punt u moet gebruiken, selecteert u de standaard **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![Test paneel met de koppeling extra instellingen gemarkeerd](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Batchgewijs testen
Zie [concepten](./luis-how-to-batch-test.md) voor batch tests en leer [hoe u](luis-how-to-batch-test.md) een batch-uitingen kunt testen.

## <a name="next-steps"></a>Volgende stappen

Als met testen wordt aangegeven dat uw LUIS-app de juiste intenties en entiteiten niet herkent, kunt u werken om de nauw keurigheid van de LUIS-app te verbeteren door meer uitingen te labelen of functies toe te voegen.

* [Aanbevolen uitingen label met LUIS](luis-how-to-review-endpoint-utterances.md)
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md)