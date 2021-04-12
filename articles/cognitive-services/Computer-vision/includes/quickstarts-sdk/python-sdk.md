---
title: 'Snelstartgids: client bibliotheek voor optische teken herkenning voor python'
description: Ga aan de slag met de optische teken herkenning-client bibliotheek voor python met deze Snelstartgids.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: d0b2a854391097cc7d95c4286ba581f3660d397e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284760"
---
<a name="HOLTop"></a>

Gebruik de client bibliotheek voor optische teken herkenning om gedrukte en handgeschreven tekst te lezen met de Lees-API.

[Referentiedocumentatie](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [Package (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [Voorbeelden](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * De python-installatie moet [PIP](https://pip.pypa.io/en/stable/)bevatten. U kunt controleren of u PIP hebt geïnstalleerd door uit te voeren `pip --version` op de opdracht regel. Ontvang PIP door de meest recente versie van python te installeren.
* Wanneer u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" maakt u een computer vision resource Maak "  target="_blank"> een computer vision resource </a> in de Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.

    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

U kunt de clientbibliotheek installeren met:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Installeer ook de Pillow-bibliotheek.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak bijvoorbeeld een nieuw Python-bestand&mdash;*quickstart-file.py*. Open het vervolgens in uw voorkeurs editor of IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>De abonnements sleutel en het eind punt zoeken

Ga naar Azure Portal. Als de Computer Vision-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt de abonnements sleutel en het eind punt op de pagina **sleutel en eind punt** van de resource, onder **resource beheer**. 

Maak variabelen voor de sleutel en het eind punt van uw Computer Vision-abonnement. Plak uw abonnements sleutel en het eind punt in de volgende code, indien aangegeven. Het Computer Vision-eind punt heeft het formulier `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Vergeet niet om de abonnements sleutel uit uw code te verwijderen wanneer u klaar bent en deze nooit openbaar te plaatsen. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Bijvoorbeeld [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Ik heb de client ingesteld](?success=set-up-client#object-model) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de OCR python SDK.

|Naam|Beschrijving|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Met deze klasse worden alle afbeeldingsbewerkingen, zoals het analyseren van afbeeldingen, detecteren van tekst en genereren van miniaturen, direct afgehandeld.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken. Deze implementeert **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Deze opsomming definieert de verschillende typen afbeeldingsanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set **VisualFeatureTypes**-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de OCR-client bibliotheek voor python:

* [De client verifiëren](#authenticate-the-client)
* [Afgedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer een client met uw eindpunt en sleutel. Maak een [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials)-object met uw sleutel en gebruik het met uw eindpunt om een [ComputerVisionClient-](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient)-object te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Ik heb de client geverifieerd](?success=authenticate-client#read-printed-and-handwritten-text) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Afgedrukte en handgeschreven tekst lezen

De OCR-service kan zicht bare tekst in een afbeelding lezen en deze converteren naar een teken stroom. Dit doet u in twee delen.

### <a name="call-the-read-api"></a>De Read-API aanroepen

Gebruik eerst de volgende code om de methode **read** aan te roepen voor de gegeven afbeelding. Hiermee wordt een bewerkings-id geretourneerd en wordt een asynchroon proces gestart om de inhoud van de afbeelding te lezen.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> U kunt ook tekst lezen uit een lokale afbeelding. Zie de [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)-methoden, bijvoorbeeld **read_in_stream**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="get-read-results"></a>Leesresultaten ophalen

Vervolgens haalt u de bewerkings-id op die wordt geretourneerd vanaf de **read**-aanroep en gebruikt u deze om de service te doorzoeken op de resultaten van de bewerking. Met de volgende code wordt de bewerking gecontroleerd met een interval van één seconde totdat de resultaten worden geretourneerd. Vervolgens worden de geëxtraheerde tekstgegevens afgedrukt naar de console.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [Ik heb tekst gelezen](?success=read-printed-handwritten-text#run-the-application) [Er is een fout opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `python` in uw quickstart-bestand.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [Ik heb de toepassing uitgevoerd](?success=run-the-application#clean-up-resources) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ik heb resources opgeschoond](?success=clean-up-resources#next-steps) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de OCR-bibliotheek voor python kunt gebruiken om basis taken uit te voeren. Bestudeer daarna het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
>[OCR API-verwijzing (python)](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Overzicht van OCR](../../overview-ocr.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
