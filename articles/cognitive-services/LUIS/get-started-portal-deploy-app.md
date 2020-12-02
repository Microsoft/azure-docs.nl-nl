---
title: 'Quickstart: Een app met de LUIS-portal implementeren'
description: In deze quickstart ziet u hoe u een app implementeert door een voorspellingseindpuntresource te maken, de resource toe te wijzen, training uit te voeren en de app te publiceren.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/06/2020
ms.openlocfilehash: b051f349716b5597adee4509cfe774978a8f61aa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95972505"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Quickstart: Een app in de LUIS-portal implementeren

Wanneer uw LUIS-app klaar is om uitingsvoorspellingen door een clienttoepassing (bijvoorbeeld een chatbot) te retourneren, moet u de app bij het voorspellingseindpunt implementeren.

In deze quickstart leert u een toepassing te implementeren. U maakt een voorspellingseindpuntresource, wijst de resource toe aan de app, traint de app en publiceert de app.

## <a name="prerequisites"></a>Vereisten

* Neem een [Azure-abonnement](https://azure.microsoft.com/free).
* Voltooi de [quickstart in de vorige portal](get-started-portal-build-app.md) of [download en importeer de app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json).
* Als u apps hebt die eerder zijn geïmplementeerd voordat de Azure-resource is geverifieerd, [migreert u deze naar een Azure-resource](luis-migration-authoring.md). Sommige portalpagina's zien er anders uit wanneer e-mailverificatie wordt toegepast.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>De resourcesleutel aan de LUIS-app toewijzen in de LUIS-portal

Telkens wanneer u een nieuwe ontwerp- of queryvoorspellingsresource voor LUIS maakt, moet u de resource aan de LUIS-app toewijzen. Nadat de resource is toegewezen, hoeft u deze stap niet nog eens uit te voeren, tenzij u een nieuwe resource maakt. Mogelijk moet u een nieuwe resource maken om de regio's van uw app uit te breiden of om ondersteuning te bieden voor een groter aantal voorspellingsquery's.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en kies de **myEnglishApp**-app uit de lijst met apps.

1. Selecteer **Beheren** in het menu rechtsboven en selecteer vervolgens **Azure-resources**.

1. Selecteer **Voorspellingsresource toevoegen** om de LUIS toe te voegen.

    ![Voorspellingsresource toevoegen selecteren om de LUIS-voorspellingsresource toe te voegen](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Selecteer uw tenant, abonnement en resourcenaam. Select **Assign resource**.

   > [!div class="mx-imgBorder"]
   > ![Een resource aan uw app toewijzen](./media/get-started-portal-deploy-app/assign-resource.png)

1. Voltooi dezelfde stappen om de ontwerpsleutel aan uw app toe te voegen.

1. Zoek de nieuwe rij in de tabel voor de nieuwe voorspellingsresource en kopieer de eindpunt-URL. De constructie is geschikt om een `HTTP GET`-aanvraag in te dienen bij de LUIS API-eindpunt voor een voorspelling.

> [!TIP]
> Als u van plan bent om actief leren te gebruiken om uw LUIS-app te verbeteren, selecteert u **Queryparameters wijzigen** en selecteert u **Logboeken opslaan**. Met deze actie wordt de voorbeeld-URL gewijzigd door de querytekenreeksparameter `log=true` toe te voegen. Kopieer en gebruik de gewijzigde voorbeeldquery-URL wanneer u voorspellingsquery's voor het runtime-eindpunt maakt.

## <a name="train-the-app"></a>De app trainen

[Train](get-started-portal-build-app.md) de app opnieuw als u deze hebt gewijzigd nadat u deze voor het laatst hebt getraind.

## <a name="publish-the-app-to-the-prediction-endpoint"></a>De app publiceren naar het voorspellingseindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Aanvraag voor voorspellingseindpunt

In de portal is `query=` aan het einde van de URL de locatie waar de uiting van de gebruiker aan de GET-aanvraag wordt toegevoegd. Voer na de `query=` dezelfde gebruikersuiting in als die is gebruikt aan het einde van de vorige quickstart:

```Is there a form named hrf-234098```

Controleer of de querytekensreeks de volgende paren bevat:

* `show-all-intents=true`
* `verbose=true`

In de browser wordt het antwoord weergegeven:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
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

Als u ditzelfde informatieniveau ook in het testdeelvenster wilt zien, moet u de app publiceren. Zodra de app is gepubliceerd, selecteert u **Vergelijken met gepubliceerd** in het testdeelvenster. Gebruik **JSON-weergave tonen** in het gepubliceerde testdeelvenster om dezelfde JSON als in de vorige stap te zien. Op deze manier kunt u wijzigingen met de huidige app waaraan u werkt, vergelijken met een app die op het eindpunt is gepubliceerd.

[![Een app die momenteel wordt bewerkt vergelijken met een gepubliceerde versie van de app](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, selecteert u **Mijn apps** in het bovenste navigatiemenu. Schakel het selectievakje van de app in de lijst in en selecteer **Verwijderen** in de contextwerkbalk boven in de lijst.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Algemene intenties en entiteiten identificeren](./tutorial-machine-learned-entity.md)