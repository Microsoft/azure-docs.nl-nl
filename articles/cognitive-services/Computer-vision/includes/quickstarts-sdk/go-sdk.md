---
title: 'Quickstart: Computer Vision-clientbibliotheek voor Go'
titleSuffix: Azure Cognitive Services
description: Ga met deze quickstart aan de slag met de Computer Vision-clientbibliotheek voor Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 996ffdeb56d41e2c05fd402714876cb16e126021
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96912332"
---
<a name="HOLTop"></a>

Gebruik de Computer Vision-clientbibliotheek voor het volgende:

* Een afbeelding analyseren op tags, tekstbeschrijvingen, gezichten, inhoud voor volwassenen, en meer.
* Afgedrukte en handgeschreven tekst lezen met de Read-API.

[Referentiedocumentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Pakket](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* Nieuwste versie van [Go](https://golang.org/dl/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* [Maak omgevingsvariabelen](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de sleutel- en eindpunt-URL, met respectievelijk de namen `COMPUTER_VISION_SUBSCRIPTION_KEY` en `COMPUTER_VISION_ENDPOINT`.

## <a name="setting-up"></a>Instellen

### <a name="create-a-go-project-directory"></a>Een Go-projectmap maken

Maak in een consolevenster (cmd, PowerShell, Terminal, Bash) een nieuwe werkruimte voor uw Go-project, genaamd `my-app` en navigeer er naartoe.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Uw werkruimte bevat drie mappen:

* **src**: deze map bevat broncode en pakketten. Alle met de opdracht `go get` geïnstalleerde pakketten komen terecht in deze map.
* **pkg**: deze map bevat de gecompileerde Go-pakketobjecten. Deze bestanden hebben allemaal een `.a`-extensie.
* **bin**: deze map bevat de binaire uitvoerbare bestanden die worden gemaakt wanneer u `go install` uitvoert.

> [!TIP]
> Zie de [documentatie over de taal Go](https://golang.org/doc/code.html#Workspaces) voor meer informatie over de structuur van een Go-werkruimte. Deze handleiding bevat informatie om `$GOPATH` en `$GOROOT` in te stellen.

### <a name="install-the-client-library-for-go"></a>De clientbibliotheek installeren voor Go

Installeer vervolgens de clientbibliotheek voor Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Een Go-toepassing maken 

Maak vervolgens een bestand in de map **src** met de naam `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Open `sample-app.go` in uw favoriete IDE of teksteditor. Voeg vervolgens de pakketnaam toe en importeer de volgende bibliotheken:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Declareer ook een context in de hoofdsectie van uw script. U hebt dit object nodig om de meeste functieaanroepen van Computer Vision uit te voeren:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Vervolgens begint u met het toevoegen van code voor het uitvoeren van verschillende Computer Vision-servicebewerkingen.

> [!div class="nextstepaction"]
> [Ik heb de client ingesteld](?success=set-up-client#object-model) [Er is een probleem opgetreden](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Computer Vision Go SDK.

|Naam|Beschrijving|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Deze klasse is nodig voor alle functionaliteit van Computer Vision, zoals het analyseren van afbeeldingen en het lezen van tekst. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om de meeste afbeeldingsbewerkingen uit te voeren.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Dit type bevat de resultaten van een **AnalyzeImage**-functieaanroep. Er zijn soortgelijke typen voor elk van de categorie-specifieke functies.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Dit type bevat de resultaten van een batch-leesbewerking. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Dit type definieert de verschillende soorten afbeeldingsanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set VisualFeatureTypes-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Computer Vision-clientbibliotheek voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Afgedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Bij deze stap wordt ervan uitgegaan dat u [omgevingsvariabelen hebt gemaakt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor uw Computer Vision-sleutel en -eindpunt, met de naam `COMPUTER_VISION_SUBSCRIPTION_KEY` respectievelijk `COMPUTER_VISION_ENDPOINT`.

Maak een `main`-functie en voeg de volgende code toe om een client te instantiëren met uw eindpunt en sleutel.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Ik heb de client geverifieerd](?success=authenticate-client#analyze-an-image) [Er is een probleem opgetreden](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>Een afbeelding analyseren

Met de volgende code wordt het clientobject gebruikt om een externe afbeelding te analyseren en de resultaten weer te geven in de console. U kunt een tekstbeschrijving, categorisatie, een lijst met tags, gedetecteerde objecten, gedetecteerde merken, gedetecteerde gezichten, vlaggen voor inhoud voor volwassenen, hoofdkleuren en afbeeldingstype ophalen.

### <a name="set-up-test-image"></a>Testafbeelding instellen

Sla eerst een verwijzing op naar de URL van de afbeelding die u wilt analyseren. Neem deze op in uw `main`-functie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> U kunt ook een lokale afbeelding analyseren. Zie de [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient)-methoden, zoals **DescribeImageInStream**. Of zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="specify-visual-features"></a>Visuele kenmerken opgeven

Met de volgende functieaanroepen worden diverse visuele kenmerken van de voorbeeldafbeelding geëxtraheerd. U gaat deze functies in de volgende secties definiëren.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

Met de volgende functie wordt de lijst met gegenereerde bijschriften voor de afbeelding opgehaald. Zie [Afbeeldingen beschrijven](../../concept-describing-images.md) voor meer informatie over het beschrijven van afbeeldingen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Categorie van de afbeelding ophalen

Met de volgende functie wordt de gedetecteerde categorie van de afbeelding opgehaald. Zie [Afbeeldingen categoriseren](../../concept-categorizing-images.md) voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Afbeeldingstags ophalen

Met de volgende functie wordt de set met gedetecteerde tags in de afbeelding opgehaald. Zie [Inhoudstags](../../concept-tagging-images.md) voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Objecten detecteren

Met de volgende functie worden algemene objecten in de afbeelding gedetecteerd en worden deze in de console afgedrukt. Raadpleeg [Objectdetectie](../../concept-object-detection.md) voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Merken detecteren

Met de volgende code worden bedrijfsmerken en -logo's in de afbeelding gedetecteerd en worden deze in de console afgedrukt. Raadpleeg [Merkdetectie](../../concept-brand-detection.md) voor meer informatie.

Declareer eerst een verwijzing naar een nieuwe afbeelding in uw `main`-functie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Met de volgende code wordt de merkdetectiefunctie gedefinieerd.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende functie worden de gedetecteerde gezichten in de afbeelding met hun rechthoekcoördinaten als resultaat gegeven en worden bepaalde gezichtskenmerken geselecteerd. Raadpleeg [Gezichtsdetectie](../../concept-detecting-faces.md) voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Inhoud voor volwassenen, of ongepaste of bloederige inhoud detecteren

Met de volgende functie wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen afgedrukt in de afbeelding. Zie [Inhoud voor volwassenen, racistische of ongepaste inhoud](../../concept-detecting-adult-content.md) voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Kleurenschema van de afbeelding ophalen

Met de volgende functie worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's](../../concept-detecting-color-schemes.md) voor meer informatie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud ophalen

Computer Vision kan speciale modellen gebruiken om verdere analyse van afbeeldingen uit te voeren. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md) voor meer informatie. 

Met de volgende code worden gegevens over gedetecteerde beroemdheden in de afbeelding geparseerd.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de afbeelding geparseerd.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldingstype ophalen

Met de volgende functie wordt informatie over het type afbeelding afgedrukt&mdash;, of het nu een illustratie of lijntekening is.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [Ik heb een afbeelding geanalyseerd](?success=analyze-image#read-printed-and-handwritten-text) [Er is een probleem opgetreden](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>Afgedrukte en handgeschreven tekst lezen

Computer Vision kan zichtbare tekst in een afbeelding lezen en deze converteren naar een tekenstroom. De code in deze sectie definieert een functie, `RecognizeTextReadAPIRemoteImage`, die gebruikmaakt van het clientobject om gedrukte of handgeschreven tekst in de afbeelding te detecteren en extraheren.

Voeg de voorbeeldafbeelding en functieaanroep toe aan uw `main`-functie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> U kunt ook tekst extraheren uit een lokale afbeelding. Zie de [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient)-methoden, zoals **BatchReadFileInStream**. Of zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="call-the-read-api"></a>De Read-API aanroepen

Definieer de nieuwe functie voor het lezen van tekst, `RecognizeTextReadAPIRemoteImage`. Voeg de onderstaande code toe, die de methode **BatchReadFile** aanroept voor de gegeven afbeelding. Met deze methode wordt een bewerkings-id geretourneerd en wordt een asynchroon proces gestart om de inhoud van de afbeelding te lezen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Leesresultaten ophalen

Vervolgens haalt u de bewerkings-id op die wordt geretourneerd door de **BatchReadFile**-aanroep en gebruikt u deze met de methode **GetReadOperationResult** om de service te doorzoeken op de resultaten van de bewerking. Met de volgende code wordt de bewerking gecontroleerd met een interval van één seconde totdat de resultaten worden geretourneerd. Vervolgens worden de geëxtraheerde tekstgegevens afgedrukt naar de console.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Leesresultaten weergeven

Voeg de volgende code toe om de opgehaalde tekstgegevens te parseren en weer te geven, en voltooi de functiedefinitie.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Ik heb tekst gelezen](?success=read-printed-handwritten-text#run-the-application) [Er is een fout opgetreden](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text)

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `go run`.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Ik heb de toepassing uitgevoerd](?success=run-the-application#clean-up-resources) [Er is een probleem opgetreden](https://www.research.net/r/7QYZKHL?issue=run-the-application)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ik heb resources opgeschoond](?success=clean-up-resources#next-steps) [Er is een probleem opgetreden](https://www.research.net/r/7QYZKHL?issue=clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Referentie voor de Computer Vision-API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [Wat is Computer Vision?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).