---
title: 'Quickstart: een LUIS-app maken'
description: Deze quickstart laat zien hoe u een LUIS-app maakt die gebruikmaakt van het vooraf gemaakte domein `HomeAutomation` voor het in- en uitschakelen van lichten en apparaten. Dit vooraf gemaakte domein biedt u intenties, entiteiten voorbeelden van utterances. Wanneer u klaar bent, hebt u een LUIS-eindpunt in de cloud.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/13/2020
ms.openlocfilehash: 2048079a9b1eb5234d03c6fe57073228bd892d53
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019157"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snelstart: Een vooraf gemaakte app voor huisautomatisering gebruiken

In deze snelstart maakt u een LUIS-app die gebruikmaakt van het vooraf gemaakte domein `HomeAutomation` voor het in- en uitschakelen van lichten en apparaten. Dit vooraf gemaakte domein biedt u intenties, entiteiten voorbeelden van utterances. Wanneer u klaar bent, hebt u een LUIS-eindpunt in de cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Een nieuwe app maken
U kunt uw toepassingen maken en beheren in **Mijn apps**.

### <a name="create-an-application"></a>Een app maken

Als u een app wilt maken, klikt u op  **+ Nieuwe app**. 

Typ de volgende informatie in het venster dat daarna wordt weergegeven:

|Naam  |Beschrijving  |
|---------|---------|
|Een naam     | Een naam voor uw app. Bijvoorbeeld 'home automation'.        |
|Cultuur     | De taal die door uw app wordt begrepen en waarin deze communiceert.   |
|Beschrijving | Een beschrijving voor uw app.
|Voorspellingsresource | De voorspellingsresource waarmee query's worden ontvangen. |

Selecteer **Gereed**.

>[!NOTE]
>De cultuur kan niet worden gewijzigd nadat de toepassing is gemaakt.

## <a name="add-prebuilt-domain"></a>Vooraf gemaakt domein toevoegen

1. Selecteer **Vooraf gemaakte domeinen** in het navigatiedeelvenster links.
1. Zoek naar **HomeAutomation**.
1. Selecteer **Domein toevoegen** op de kaart HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Selecteer 'Vooraf gemaakte domeinen' en zoek vervolgens naar 'HomeAutomation'. Selecteer 'Domein toevoegen' op de kaart HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

    Wanneer het vooraf gemaakte domein is toegevoegd, wordt in het vak met vooraf gemaakte domeinen de knop **Remove domain** weergegeven.

## <a name="intents-and-entities"></a>Intenties en entiteiten

1. Selecteer **Intenties** in het navigatiedeelvenster aan de linkerkant om de intenties van het domein 'HomeAutomation' weer te geven. Het bevat voorbeelden van utterances, zoals `HomeAutomation.QueryState` en `HomeAutomation.SetDevice`.

    > [!NOTE]
    > **None** is een intentie die door alle LUIS-apps wordt geboden. U kunt deze gebruiken voor het afhandelen van utterances die niet overeenkomen met de functionaliteit die uw app biedt.

1. Selecteer de intentie **HomeAutomation.TurnOff**. De intentie bevat een lijst met voorbeelduitingen die zijn gelabeld met entiteiten.

    > [!div class="mx-imgBorder"]
    > [![Schermopname van de intentie HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Schermopname van de intentie HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Als u de entiteiten voor de app wilt weergeven, selecteert u **Entiteiten**. Als u op een van de entiteiten klikt, zoals **HomeAutomation.DeviceName** ziet u een lijst met waarden die eraan zijn gekoppeld. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Afbeelding met alternatieve tekst" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>LUIS-app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Uw app testen
Nadat u uw app hebt getraind, kunt u deze testen.

1. Selecteer **Testen** in de navigatiebalk rechtsboven.

1. Typ een test-uiting, zoals `Turn off the lights` in het interactieve testdeelvenster en druk op Enter. Bijvoorbeeld: *de lichten uitschakelen*.

    In dit voorbeeld wordt `Turn off the lights` correct geïdentificeerd als de best scorende intentie van **HomeAutomation.TurnOff**.

    ![Schermafbeelding van deelvenster Test met utterance gemarkeerd](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Selecteer **Inspecteren** om meer informatie over de voorspelling weer te geven.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het testvenster met inspectie-informatie](media/luis-quickstart-new-app/test.png)

1. Sluit het testvenster.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>App publiceren om eindpunt-URL op te vragen

Om een voorspelling van LUIS te ontvangen in een chatbot of een andere clienttoepassing, moet u de app publiceren naar het voorspellingseindpunt.

1. Selecteer **Publiceren** in de rechterbovenhoek van het venster.

1. Selecteer de **Productiesite** en selecteer vervolgens **Gereed**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van publiceren naar eindpunt vanuit LUIS](media/howto-publish/publish-app-popup.png)

1. Selecteer de koppeling **Uw eindpunt-URL's openen** in de melding om naar de pagina **Azure-resources** te gaan. De eindpunt-URL's worden onderaan vermeld als de **Voorbeeldquery**.

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Een query uitvoeren op het V3 API-voorspellingseindpunt

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Zorg ervoor dat in de adresbalk van de browser voor de queryreeks de volgende naam- en waardebalken in de URL staan. Als ze niet voorkomen in de queryreeks, voegt u deze toe:

    |Naam/waarde-paar|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Ga in de adresbalk van de browser naar het einde van de URL en voer `turn off the living room light` in als waarde voor de _query_ en druk vervolgens op Enter.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt het eindpunt aanroepen vanuit code:

> [!div class="nextstepaction"]
> [Een LUIS-eindpunt aanroepen met behulp van code](./luis-get-started-get-intent-from-rest.md)