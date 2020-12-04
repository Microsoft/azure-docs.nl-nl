---
title: 'Quickstart: Een nieuwe app maken in de LUIS-portal'
description: In deze quickstart maakt u de basisonderdelen van een app, intenties en entiteiten en voert u een test uit met een voorbeelduiting in de LUIS-portal.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/30/2020
ms.openlocfilehash: a5443dce9fc8bc028232f66f45b537a46858f35a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436671"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Quickstart: Een nieuwe app maken in de LUIS-portal

In deze quickstart maakt u een nieuwe app in de LUIS-portal. Eerst maakt u de basisonderdelen van een app, **intenties** en **entiteiten**. Test vervolgens de app door een voorbeeld van een gebruikersuiting in te voeren in het interactieve testpaneel om de voorspelde intentie te verkrijgen.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Een app maken

Als u een app wilt maken, selecteert u op **+ Nieuwe app**. 

Typ de volgende informatie in het venster dat daarna wordt weergegeven:

|Naam  |Beschrijving  |
|---------|---------|
|Naam     | Een naam voor uw app. Bijvoorbeeld 'home automation'. Vereist.        |
|Cultuur     | De taal die door uw app wordt begrepen en waarin deze communiceert. Vereist.   |
|Beschrijving | Een beschrijving voor uw app. Optioneel.
|Voorspellingsresource | De voorspellingsresource waarmee query's worden ontvangen. Optioneel. |

Selecteer **Gereed**.

>[!NOTE]
>De cultuur kan niet worden gewijzigd nadat de toepassing is gemaakt.


## <a name="create-intents"></a>Intents maken

Nadat de LUIS-app is gemaakt, moet u intenties maken. Intenties zijn een manier om teksten van gebruikers te classificeren. Zo zou een human resources-app bijvoorbeeld twee functies kunnen hebben. Om mensen te helpen:

 1. Banen te zoeken en ernaar te solliciteren
 1. Formulieren te zoeken om naar banen te solliciteren

De twee verschillende _intenties_ van de app sluiten aan bij de volgende intenties:

|Intentie|Voorbeeldtekst van gebruiker,<br>_uiting_ genoemd|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Voer de volgende stappen uit om intenties te maken:

1. Nadat de app is gemaakt, moet u controleren of u zich op de pagina **Intenties** van de sectie **Bouwen** bevindt. Selecteer **Maken**.

   [![Schermopname van het selecteren van 'Create' om een nieuwe intentie te maken](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Voer de naam van de nieuwe intentie, `FindForm`, in en selecteer **Done** (Gereed).

## <a name="add-an-example-utterance"></a>Een voorbeelduiting toevoegen

Nadat u intenties hebt gemaakt, voegt u voorbeelduitingen toe. Voorbeelduitingen zijn tekst die een gebruiker invoert in een chatbot of andere clienttoepassing. Ze stemmen de intentie van de gebruikerstekst af op een LUIS-intentie. Voor de `FindForm`-intentie van deze voorbeeldtoepassing bevatten voorbeelduitingen het formuliernummer. De clienttoepassing heeft het formuliernummer nodig om aan de vraag van de gebruiker te voldoen, dus het is belangrijk om het op te nemen in de uiting.

Voeg op de pagina **Intenties** voor `FindForm` het volgende utterances-voorbeeld toe onder **Voorbeelden van utterances**. 

|#|Voorbeelden van utterances|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Deze voorbeelduitingen zijn met opzet zo gemaakt dat ze op de volgende manieren variëren:

* lengte van de uiting
* [interpunctie](luis-reference-application-settings.md#punctuation-normalization)
* woordkeus
* werkwoordtijd (is, was, zal)
* woordvolgorde


## <a name="create-a-regular-expression-entity"></a>Een entiteit in de vorm van een reguliere expressie maken

Om het formuliernummer te retourneren in het voorspellingsantwoord van de runtime, moet het formuliernummer worden geëxtraheerd als een entiteit. Omdat de tekst van het formuliernummer zeer gestructureerd is, kunt u een reguliere expressie-entiteit gebruiken. Maak de reguliere expressie-entiteit met de volgende stappen:

1. Selecteer **Entities** (Entiteiten) in het menu aan de linkerkant.

1. Selecteer **Create** (Maken) op de pagina **Entities**.

1. Voer de naam `FormNumber` in en selecteer het entiteitstype **Regex**.

1. Voer de reguliere expressie `hrf-[0-9]{6}` in het veld **Regex** in. Deze vermelding komt overeen met de letterlijke tekens `hrf-`, gevolgd door precies zes cijfers. Selecteer vervolgens **Create** (Maken).

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het maken van een reguliere expressie-entiteit](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Deze entiteit extraheert alle tekst die overeenkomt met de reguliere expressie in de intenties.

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None

De intentie **None** (Geen) is de terugvalintentie, en moet niet leeg worden gelaten. Deze intentie moet één uiting bevatten voor elke 10 voorbeelduitingen die u hebt toegevoegd voor de andere intenties van de app.

De voorbeelduitingen voor de intentie **None** moeten buiten het toepassingsdomein van uw clienttoepassing liggen.

1. Selecteer **Intents** (Intenties) in het menu links en selecteer vervolgens **None** (Geen) in de lijst met intenties.

1. Voeg de volgende voorbeelduitingen toe aan de intentie:

   |Geen voorbeelduitingen intent|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Deze voorbeelduitingen liggen buiten het domein van deze app. Als uw domein dieren bevat, of de oceaan, moet u andere voorbeelden gebruiken voor de intentie **None**.

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>De reguliere expressie-entiteit in de voor beelduitingen bekijken

1. Controleer of de entiteit zich in de intentie **FindForm** bevindt door **Intents** (Intenties) te selecteren in het menu links. Selecteer de **FindForm**-intentie.

   De entiteit wordt gemarkeerd waar deze voorkomt in de voorbeelduitingen.

   > [!div class="mx-imgBorder"]
   > [![Schermopname van alle voorbeelduitingen gemarkeerd met entiteiten](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Uw nieuwe app testen met het interactieve testdeelvenster

Gebruik het interactieve **Test**-deelvenster in de LUIS-portal om te valideren dat de entiteit wordt geëxtraheerd van nieuwe uitingen die de app nog niet heeft gezien.

1. Selecteer **Test** in het menu rechtsboven.

1. Voeg een nieuwe uiting toe en druk op Enter:

   ```Is there a form named hrf-234098```

    Selecteer **Inspect** (Inspecteren) om de voorspellingen van de entiteit weer te geven.

   > [!div class="mx-imgBorder"]
   > ![Schermopname van het testen van een nieuwe uiting in het testdeelvenster](./media/get-started-portal-build-app/test-new-utterance.png)

   De hoogst scorende voorspelde intentie is inderdaad **FindForm**, met een betrouwbaarheid van 90% (0.977). De entiteit **FormNumber** wordt geëxtraheerd met de waarde hrf-234098.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart en niet wilt doorgaan met de volgende, selecteert u **Mijn apps** in het bovenste navigatiemenu. Schakel het linker selectievakje van de app in de lijst in en selecteer **Verwijderen** in de contextwerkbalk boven in de lijst.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [2. Een app implementeren](get-started-portal-deploy-app.md)
