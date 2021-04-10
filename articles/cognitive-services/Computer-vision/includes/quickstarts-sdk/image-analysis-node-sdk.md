---
title: 'Quick Start: client bibliotheek voor installatie kopie analyse voor Node.js'
description: Aan de slag met de client bibliotheek voor installatie kopie analyse voor Node.js met deze Snelstartgids
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: d38b1f20547056c30f19dbbc77589643be1663a4
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073619"
---
<a name="HOLTop"></a>

Gebruik de client bibliotheek voor afbeeldings analyse om een afbeelding te analyseren voor Tags, tekst beschrijving, gezichten, inhoud voor volwassenen en meer.

[Referentiedocumentatie](/javascript/api/@azure/cognitiveservices-computervision/) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Pakket (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Voorbeelden](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van [Node.js](https://nodejs.org/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map.

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand.

```console
npm init
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer de NPM-pakketten `ms-rest-azure` en `@azure/cognitiveservices-computervision`:

```console
npm install @azure/cognitiveservices-computervision
```

Installeer ook de async-module:

```console
npm install async
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een nieuw bestand, *index.js* en open dit in een tekstbewerkingsprogramma. Voeg de volgende importeerinstructies toe.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Computer Vision-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

> [!div class="nextstepaction"]
> [Ik heb de client ingesteld](?success=set-up-client#object-model) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de afbeeldings analyse Node.js SDK.

|Naam|Beschrijving|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om de meeste afbeeldingsbewerkingen uit te voeren.|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| Deze opsomming definieert de verschillende typen afbeeldingsanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set **VisualFeatureTypes**-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de client bibliotheek voor installatie kopie analyse voor Node.js:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)

## <a name="authenticate-the-client"></a>De client verifiëren


Instantieer een client met uw eindpunt en sleutel. Maak een [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials)-object met uw sleutel en eindpunt en maak hiermee een [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)-object.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Definieer vervolgens een functie `computerVision` en declareer een asynchrone serie met een primaire functie en een callback-functie. U voegt uw quickstartcode toe aan de primaire functie en roept `computerVision`, onder aan het script, aan. De rest van de code in deze quickstart de functie `computerVision` in.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Ik heb de client geverifieerd](?success=authenticate-client#analyze-an-image) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)

## <a name="analyze-an-image"></a>Een afbeelding analyseren

De code in deze sectie wordt gebruikt om externe afbeeldingen te analyseren om verschillende visuele functies te extraheren. U kunt deze bewerkingen uitvoeren als onderdeel van de **analyzeImage**-methode van het clientobject, of u kunt ze aanroepen met behulp van afzonderlijke methoden. Raadpleeg de [referentiedocumentatie](/javascript/api/@azure/cognitiveservices-computervision/) voor meer informatie.

> [!NOTE]
> U kunt ook een lokale afbeelding analyseren. Zie de [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)-methoden, bijvoorbeeld **analyzeImageInStream**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

Met de volgende code wordt de lijst met gegenereerde bijschriften voor de afbeelding opgehaald. Zie [Afbeeldingen beschrijven](../../concept-describing-images.md) voor meer informatie.

Definieer eerst de URL van de afbeelding die u wilt analyseren:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Voeg daarna de volgende code toe om de afbeeldingsbeschrijving op te halen en deze via de console af te drukken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Categorie van de afbeelding ophalen

Met de volgende code wordt de gedetecteerde categorie van de afbeelding opgehaald. Zie [Afbeeldingen categoriseren](../../concept-categorizing-images.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Definieer de hulpfunctie `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Afbeeldingstags ophalen

Met de volgende code wordt de set met gedetecteerde tags in de afbeelding opgehaald. Zie [Inhoudstags](../../concept-tagging-images.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Definieer de hulpfunctie `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Objecten detecteren

Met de volgende code worden algemene objecten in de afbeelding gedetecteerd en worden deze in de console afgedrukt. Zie [Objectdetectie](../../concept-object-detection.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Definieer de helperfunctie `formatRectObjects` om de boven-, linker-, onder- en rechtercoördinaten op te halen, samen met de breedte en de hoogte.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Merken detecteren

Met de volgende code worden bedrijfsmerken en -logo's in de afbeelding gedetecteerd en worden deze in de console afgedrukt. Zie [Merkdetectie](../../concept-brand-detection.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding met hun rechthoekcoördinaten als resultaat gegeven en worden gezichtskenmerken geselecteerd. Zie [Gezichtsdetectie](../../concept-detecting-faces.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Definieer de hulpfunctie `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Inhoud voor volwassenen, of ongepaste of bloederige inhoud detecteren

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen afgedrukt in de afbeelding. Zie [Inhoud voor volwassenen, of ongepaste, bloederige inhoud](../../concept-detecting-adult-content.md) voor meer informatie.

Definieer de URL van de afbeelding die u wilt gebruiken:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Voeg vervolgens de volgende code toe om inhoud voor volwassenen te detecteren en de resultaten af te drukken via de console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Kleurenschema van de afbeelding ophalen

Met de volgende code worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's](../../concept-detecting-color-schemes.md) voor meer informatie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Definieer de hulpfunctie `printColorScheme` om de details van het kleurenschema via de console af te drukken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud ophalen

Image analyse kan speciaal model gebruiken voor verdere analyse van installatie kopieën. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md) voor meer informatie.

Definieer eerst de URL van de afbeelding die u wilt analyseren:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de afbeelding geparseerd.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Definieer de hulpfunctie `formatRectDomain` om de locatiegegevens over gedetecteerde bezienswaardigheden te parseren.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Het afbeeldingstype ophalen

Met de volgende code wordt informatie over het type afbeelding afgedrukt&mdash;, of het een illustratie of lijntekening is.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Definieer de hulpfunctie `describeType`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [Ik heb een afbeelding geanalyseerd](?success=analyze-image#run-the-application) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image)



## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Ik heb de toepassing uitgevoerd](?success=run-the-application#clean-up-resources) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ik heb resources opgeschoond](?success=clean-up-resources#next-steps) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de installatie kopie-client bibliotheek kunt installeren en elementaire installatie kopieën kunt aanroepen. Vervolgens leest u meer over de API-functies voor analyse.

> [!div class="nextstepaction"]
>[De analyse-API aanroepen](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Overzicht van image analyse](../../overview-image-analysis.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).

