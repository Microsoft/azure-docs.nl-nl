---
title: 'Quick Start: client bibliotheek voor installatie kopie analyse voor python'
description: Ga aan de slag met de client bibliotheek voor installatie kopie analyse voor python met deze Snelstartgids.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: aae780ad42c39789c0c4448a90a12737074e4176
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073682"
---
<a name="HOLTop"></a>

Gebruik de client bibliotheek voor afbeeldings analyse om een afbeelding te analyseren voor Tags, tekst beschrijving, gezichten, inhoud voor volwassenen en meer.

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

Maak bijvoorbeeld een nieuw Python-bestand&mdash;*quickstart-file.py*. Open vervolgens het bestand in uw voorkeurseditor of IDE en importeer de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak dan variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Computer Vision-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Bijvoorbeeld [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Ik heb de client ingesteld](?success=set-up-client#object-model) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Image Analysis python SDK.

|Naam|Beschrijving|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Met deze klasse worden alle afbeeldingsbewerkingen, zoals het analyseren van afbeeldingen, detecteren van tekst en genereren van miniaturen, direct afgehandeld.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken. Deze implementeert **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Deze opsomming definieert de verschillende typen afbeeldingsanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set **VisualFeatureTypes**-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de client bibliotheek voor installatie kopie analyse voor python:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer een client met uw eindpunt en sleutel. Maak een [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials)-object met uw sleutel en gebruik het met uw eindpunt om een [ComputerVisionClient-](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient)-object te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Ik heb de client geverifieerd](?success=authenticate-client#analyze-an-image) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="analyze-an-image"></a>Een afbeelding analyseren

Gebruik uw clientobject om de visuele kenmerken van een externe afbeelding te analyseren. Sla eerst een verwijzing op naar de URL van een afbeelding die u wilt analyseren.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

> [!TIP]
> U kunt ook een lokale afbeelding analyseren. Zie de [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)-methoden, bijvoorbeeld **analyze_image_in_stream**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

Met de volgende code wordt de lijst met gegenereerde bijschriften voor de afbeelding opgehaald. Zie [Afbeeldingen beschrijven](../../concept-describing-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Categorie van de afbeelding ophalen

Met de volgende code wordt de gedetecteerde categorie van de afbeelding opgehaald. Zie [Afbeeldingen categoriseren](../../concept-categorizing-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Afbeeldingstags ophalen

Met de volgende code wordt de set met gedetecteerde tags in de afbeelding opgehaald. Zie [Inhoudstags](../../concept-tagging-images.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

Met de volgende code worden algemene objecten in de afbeelding gedetecteerd en worden deze in de console afgedrukt. Zie [Objectdetectie](../../concept-object-detection.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]        

### <a name="detect-brands"></a>Merken detecteren

Met de volgende code worden bedrijfsmerken en -logo's in de afbeelding gedetecteerd en worden deze in de console afgedrukt. Zie [Merkdetectie](../../concept-brand-detection.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding met hun rechthoekcoördinaten als resultaat gegeven en worden gezichtskenmerken geselecteerd. Zie [Gezichtsdetectie](../../concept-detecting-faces.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Inhoud voor volwassenen, of ongepaste of bloederige inhoud detecteren

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen afgedrukt in de afbeelding. Zie [Inhoud voor volwassenen, of ongepaste, bloederige inhoud](../../concept-detecting-adult-content.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Kleurenschema van de afbeelding ophalen

Met de volgende code worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's](../../concept-detecting-color-schemes.md) voor meer informatie.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud ophalen

Image analyse kan speciaal model gebruiken voor verdere analyse van installatie kopieën. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md) voor meer informatie. 

Met de volgende code worden gegevens over gedetecteerde beroemdheden in de afbeelding geparseerd.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de afbeelding geparseerd.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldingstype ophalen

Met de volgende code wordt informatie over het type afbeelding afgedrukt&mdash;, of het een illustratie of lijntekening is.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

> [!div class="nextstepaction"]
> [Ik heb een afbeelding geanalyseerd](?success=analyze-image#run-the-application) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=analyze-image)



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

In deze Quick Start hebt u geleerd hoe u de installatie kopie-client bibliotheek kunt installeren en elementaire installatie kopieën kunt aanroepen. Vervolgens leest u meer over de API-functies voor analyse.

> [!div class="nextstepaction"]
>[De analyse-API aanroepen](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Overzicht van image analyse](../../overview-image-analysis.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
