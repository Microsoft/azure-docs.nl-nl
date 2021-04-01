---
title: Entiteiten toevoegen-LUIS
description: Maak entiteiten voor het extra heren van belang rijke gegevens van gebruikers uitingen in apps van Language Understanding (LUIS). Geëxtraheerde entiteits gegevens worden door de client toepassing gebruikt voor fullfile-aanvragen van klanten.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91540962"
---
# <a name="add-entities-to-extract-data"></a>Entiteiten toevoegen om gegevens op te halen

Maak entiteiten voor het extra heren van belang rijke gegevens van gebruikers uitingen in apps van Language Understanding (LUIS). Geëxtraheerde entiteits gegevens worden door uw client toepassing gebruikt voor fullfile-aanvragen van klanten.

De entiteit vertegenwoordigt een woord of woord groep in de utterance die u wilt geëxtraheerden. Entiteiten beschrijven informatie die relevant is voor de bedoeling, en soms zijn ze essentieel voor uw app om de taak uit te voeren. U kunt entiteiten maken wanneer u een voor beeld-utterance toevoegt aan een intentie of van (vóór of na) het toevoegen van een voor beeld-utterance aan een intentie.

## <a name="plan-entities-then-create-and-label"></a>Entiteiten plannen, vervolgens maken en labelen

machine learning-entiteiten kunnen worden gemaakt op basis van de voorbeeld uitingen of worden gemaakt op basis van de pagina **entiteiten** .

Over het algemeen is een best practice tijd om de entiteiten te plannen voordat een machine learning-entiteit wordt gemaakt in de portal. Maak vervolgens de machine learning-entiteit uit het voor beeld utterance met zoveel details in de subentiteiten en functies die u op dat moment kent. Met de [zelf studie voor het samen stellen](tutorial-machine-learned-entity.md) van de entiteit wordt gedemonstreerd hoe u deze methode gebruikt.

Als onderdeel van het plannen van de entiteiten weet u misschien dat u tekst-overeenkomende entiteiten (zoals vooraf gemaakte entiteiten, reguliere expressie-entiteiten of lijst entiteiten) nodig hebt. U kunt deze maken op de pagina **entiteiten** voordat ze worden gelabeld in voor beeld uitingen.

Wanneer u labels aanmaakt, kunt u afzonderlijke entiteiten labelen en vervolgens een bovenliggende entiteit voor machine learning bouwen. U kunt ook beginnen met een bovenliggende machine learning-entiteit en de onderliggende entiteiten afbreken.

> [!TIP]
>Voorzie alle woorden die een entiteit kunnen aanduiden, zelfs als de woorden niet worden gebruikt als ze worden geëxtraheerd in de client toepassing.

## <a name="when-to-create-an-entity"></a>Wanneer u een entiteit maakt

Nadat u uw entiteiten hebt gepland, moet u uw machine learning-entiteiten en-subentiteiten maken. Hiervoor moet u mogelijk vooraf gemaakte entiteiten of tekst vergelijkings entiteiten toevoegen om functies te bieden voor uw machine learning-entiteiten. Deze moeten allemaal worden uitgevoerd voordat labels kunnen worden gelabeld.

Zodra u begint met het labelen van voor beeld uitingen, kunt u door de machine geleerde entiteiten maken of lijst entiteiten uitbreiden.

Gebruik de volgende tabel om te begrijpen waar u elk type entiteit kunt maken of toevoegen aan de app.

|Entiteitstype|Locatie voor het maken van een entiteit in de LUIS-Portal|
|--|--|
|machine learning-entiteit|Details van entiteiten of intentie|
|Lijstentiteit|Details van entiteiten of intentie|
|Een entiteit in de vorm van een reguliere expressie|Entiteiten|
|Pattern.any-entiteit|Entiteiten|
|Vooraf gebouwde entiteit|Entiteiten|
|Vooraf ontwikkelde domein entiteit|Entiteiten|

U kunt alle entiteiten maken op de pagina **entiteiten** , maar u kunt ook een aantal entiteiten maken als onderdeel van het labelen van de entiteit in het voor beeld utterance op de detail pagina van het **doel** . U kunt een entiteit alleen _labelen_ in een voor beeld-utterance van de detail pagina van het **doel** .



## <a name="how-to-create-a-new-custom-entity"></a>Een nieuwe aangepaste entiteit maken

Dit proces werkt voor door machines gewerkte entiteiten, lijst entiteiten en reguliere expressie-entiteiten.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer de pagina **entiteiten** .
1. Selecteer **+ maken** en selecteer vervolgens het entiteits type.
1. Ga verder met het configureren van de entiteit en selecteer **maken** wanneer u klaar bent.

## <a name="create-a-machine-learned-entity"></a>Een door een machine geleerde entiteit maken

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer **+ maken**.
1. Voer in het dialoog venster **een entiteits type maken** de naam van de entiteit in en selecteer door de **machine geleerd**. Als u subentiteiten wilt toevoegen, selecteert u **structuur toevoegen**. Selecteer **Maken**.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van het maken van een door de machine geleerde entiteit.](media/add-entities/machine-learned-entity-with-structure.png)

1. Voeg een subentiteit toe door in **subentiteiten toevoegen** de **+** rij bovenliggende entiteit te selecteren.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van het toevoegen van subentiteiten.](media/add-entities/machine-learned-entity-with-subentities.png)

1. Selecteer **maken** om het aanmaak proces te volt ooien.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Een functie toevoegen aan een door een machine geleerde entiteit

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer vervolgens de door de machine geleerde entiteit.
1. Voeg een functie toe door de **functie + toevoegen** te selecteren in de rij entiteit of subentiteit.
1. Selecteer in de lijsten bestaande entiteiten en zinsdelen.
1. Als de entiteit alleen moet worden geëxtraheerd als de functie is gevonden, selecteert u het sterretje `*` voor die functie.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van het toevoegen van een functie aan entiteit.](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Een entiteit in de vorm van een reguliere expressie maken

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer **+ maken**.

1. In het dialoog venster **een entiteits type maken** voert u de naam van de entiteit in en selecteert u **regex**, voert u de reguliere expressie in het veld **regex** in en selecteert u **maken**.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van het maken van een reguliere expressie-entiteit.](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Een lijst entiteit maken

Lijst entiteiten vertegenwoordigen een vaste, gesloten set verwante woorden. Als de auteur de lijst kan wijzigen, kan LUIS de lijst niet verg Roten of verkleinen. U kunt ook importeren naar een bestaande lijst entiteit met behulp van een [lijst entiteit. json-indeling](reference-entity-list.md#example-json-to-import-into-list-entity).

In de volgende lijst ziet u de canonieke naam en de synoniemen.

|Kleur-lijst item naam|Kleur-synoniemen|
|--|--|
|Red|Crimson, bloed, Apple, brand engine|
|Blue|lucht, kobalt|
|Green|Kelly, licht groen|

Gebruik de procedure om een lijst entiteit te maken. Zodra de lijst entiteit is gemaakt, hoeft u geen label voor beeld-uitingen in te richten. Lijst items en synoniemen worden vergeleken met de exacte tekst.
1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster en selecteer **+ maken**.

1. In het dialoog venster **een entiteits type maken** voert u de naam van de entiteit in, zoals `Colors` en **lijst** selecteren.
1. Voer in het dialoog venster **een lijst entiteit maken** in de lijst **nieuwe sublijst toevoegen....** de naam van het item in, bijvoorbeeld `Green` , en voeg synoniemen toe.

    > [!div class="mx-imgBorder"]
    > ![Maak een lijst met kleuren als een lijst entiteit op de pagina Details van entiteit.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. Wanneer u klaar bent met het toevoegen van lijst items en synoniemen, selecteert u **maken**.

    Wanneer u klaar bent met een groep wijzigingen in de app, vergeet dan niet om de app te **trainen** . Train de app niet na één wijziging.

    > [!NOTE]
    > Deze procedure illustreert het maken en labelen van een lijst entiteit van een voorbeeld utterance op de detail pagina van het **doel** . U kunt ook dezelfde entiteit maken op de pagina **entiteiten** .

## <a name="add-a-role-for-an-entity"></a>Een rol voor een entiteit toevoegen

Een rol is een benoemd subtype van een entiteit, op basis van de context.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Een rol toevoegen om verschillende contexten te onderscheiden

In de volgende utterance zijn er twee locaties en elke naam wordt semantisch door de woorden beschreven, zoals `to` en `from` :

`Pick up the package from Seattle and deliver to New York City.`

In deze procedure voegt `origin` `destination` u rollen toe aan een vooraf samengestelde geographyV2-entiteit.
1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer in de sectie **Build** de optie **entiteiten** in het linkerdeel venster.

1. Selecteer **+ vooraf samengestelde entiteit toevoegen**. Selecteer **geographyV2** en selecteer vervolgens **gereed**. Hiermee voegt u een vooraf samengestelde entiteit toe aan de app.

    Als u merkt dat het patroon, wanneer dit een Pattern.any bevat, entiteiten onjuist extraheert, gebruikt u een [expliciete lijst](reference-pattern-syntax.md#explicit-lists) om dit probleem te verhelpen.

1. Selecteer de zojuist toegevoegde, vooraf gemaakte geographyV2-entiteit uit de pagina lijst **entiteiten** van entiteiten.
1. Als u een nieuwe rol wilt toevoegen, selecteert u **+** volgende om **geen rollen** toe te voegen.
1. Voer in het tekstvak **type Role...** de naam van de rol in `Origin` en voer vervolgens in. Voeg een tweede rolnaam toe van `Destination` vervolgens ENTER.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van de functie origin toevoegen aan de locatie-entiteit](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    De rol wordt toegevoegd aan de vooraf samengestelde entiteit, maar wordt niet aan uitingen toegevoegd met behulp van die entiteit.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Label tekst met een rol in een voor beeld van een utterance

> [!TIP]
> Rollen kunnen worden vervangen door het labelen met subentiteiten van een machine learning-entiteit.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Ga naar de pagina Details van intentie, met bijvoorbeeld uitingen die gebruikmaken van de rol.
1. Als u een label met de rol wilt hebben, selecteert u het label van de entiteit (effen lijn onder tekst) in het voor beeld utterance en selecteert u vervolgens **weer geven in entiteit deel venster** in de vervolg keuzelijst.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont de menu optie weer gave in deel venster entiteit geselecteerd.](media/add-entities/view-in-entity-pane.png)

    Het palet entiteit wordt geopend aan de rechter kant.

1. Selecteer de entiteit, ga naar de onderkant van het palet en selecteer de rol.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding laat zien waar de rol moet worden geselecteerd.](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Een patroon maken. elke entiteit

Het **patroon. elke** entiteit is alleen beschikbaar met [patronen](luis-how-to-model-intent-pattern.md).


## <a name="do-not-change-entity-type"></a>Entiteits type niet wijzigen

LUIS staat niet toe dat u het type van de entiteit wijzigt omdat deze niet weet wat moet worden toegevoegd of verwijderd om die entiteit te bouwen. Als u het type wilt wijzigen, is het beter om een nieuwe entiteit van het juiste type te maken met een iets andere naam. Nadat de entiteit is gemaakt, verwijdert u in elke utterance de oude naam van de entiteit met het label en voegt u de nieuwe entiteits naam toe. Als alle uitingen opnieuw zijn gelabeld, verwijdert u de oude entiteit.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Vooraf gemaakte modellen gebruiken](howto-add-prebuilt-models.md)

Meer informatie over:
* [Train](luis-how-to-train.md)
* [Testen](luis-interactive-test.md)
* [Publiceren](luis-how-to-publish-app.md)
* Daarin
    * [Concepten](luis-concept-patterns.md)
    * [Syntaxis](reference-pattern-syntax.md)
* [Vooraf gemaakte entiteiten GitHub-opslag plaats](https://github.com/Microsoft/Recognizers-Text)
* [Concepten voor gegevens extractie](luis-concept-data-extraction.md)



