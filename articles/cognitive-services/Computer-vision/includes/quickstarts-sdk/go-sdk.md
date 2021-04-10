---
title: 'Snelstartgids: client bibliotheek voor optische teken herkenning voor Go'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de client bibliotheek voor optische teken herkenning voor deze Snelstartgids.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 0ae059054c68da662e05342525987f6925184906
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073692"
---
<a name="HOLTop"></a>

Gebruik de OCR-client bibliotheek om gedrukte en handgeschreven tekst uit afbeeldingen te lezen.

[Referentiedocumentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Pakket](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* Nieuwste versie van [Go](https://golang.org/dl/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

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

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go), waar de codevoorbeelden uit deze quickstart zich bevinden.

Open `sample-app.go` in uw favoriete IDE of teksteditor.

Declareer een context in de hoofdmap van uw script. U hebt dit object nodig om de meeste Computer Vision functie aanroepen uit te voeren.

### <a name="find-the-subscription-key-and-endpoint"></a>De abonnements sleutel en het eind punt zoeken

Ga naar Azure Portal. Als de Computer Vision-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt de abonnements sleutel en het eind punt op de pagina **sleutel en eind punt** van de resource, onder **resource beheer**. 

Maak variabelen voor de sleutel en het eind punt van uw Computer Vision-abonnement. Plak uw abonnements sleutel en het eind punt in de volgende code, indien aangegeven. Het Computer Vision-eind punt heeft het formulier `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Vergeet niet om de abonnements sleutel uit uw code te verwijderen wanneer u klaar bent en deze nooit openbaar te plaatsen. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Bijvoorbeeld [Azure Key Vault](../../../../key-vault/general/overview.md).

Vervolgens begint u met het toevoegen van code om verschillende OCR-bewerkingen uit te voeren.

> [!div class="nextstepaction"]
> [Ik heb de client ingesteld](?success=set-up-client#object-model) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de OCR go SDK.

|Naam|Beschrijving|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Deze klasse is nodig voor alle functionaliteit van Computer Vision, zoals het analyseren van afbeeldingen en het lezen van tekst. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om de meeste afbeeldingsbewerkingen uit te voeren.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Dit type bevat de resultaten van een batch-leesbewerking. |

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de OCR-client bibliotheek voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Afgedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> Bij deze stap wordt ervan uitgegaan dat u [omgevingsvariabelen hebt gemaakt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor uw Computer Vision-sleutel en -eindpunt, met de naam `COMPUTER_VISION_SUBSCRIPTION_KEY` respectievelijk `COMPUTER_VISION_ENDPOINT`.

Maak een `main`-functie en voeg de volgende code toe om een client te instantiëren met uw eindpunt en sleutel.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Ik heb de client geverifieerd](?success=authenticate-client#read-printed-and-handwritten-text) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Afgedrukte en handgeschreven tekst lezen

De OCR-service kan zicht bare tekst in een afbeelding lezen en deze converteren naar een teken stroom. De code in deze sectie definieert een functie, `RecognizeTextReadAPIRemoteImage`, die gebruikmaakt van het clientobject om gedrukte of handgeschreven tekst in de afbeelding te detecteren en extraheren.

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
> [Ik heb tekst gelezen](?success=read-printed-handwritten-text#run-the-application) [Er is een fout opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `go run`.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Ik heb de toepassing uitgevoerd](?success=run-the-application#clean-up-resources) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ik heb resources opgeschoond](?success=clean-up-resources#next-steps) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de OCR-client bibliotheek installeert en de Lees-API gebruikt. Vervolgens leest u meer over de API-functies voor lezen.

> [!div class="nextstepaction"]
>[De Read-API aanroepen](../../Vision-API-How-to-Topics/call-read-api.md)

* [Overzicht van OCR](../../overview-ocr.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
