---
title: 'Quick Start: client bibliotheek voor optische teken herkenning voor Node.js'
description: Aan de slag met de client bibliotheek voor optische teken herkenning voor Node.js met deze Snelstartgids
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: cb4679152740b73d6bb9cf7288fcaa811b6d6141
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073358"
---
<a name="HOLTop"></a>

Gebruik de client bibliotheek voor optische teken herkenning om gedrukte en handgeschreven tekst te lezen met de Lees-API.

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

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak een nieuw bestand, *index.js* en open dit in een tekstbewerkingsprogramma.

### <a name="find-the-subscription-key-and-endpoint"></a>De abonnements sleutel en het eind punt zoeken

Ga naar Azure Portal. Als de Computer Vision-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt de abonnements sleutel en het eind punt op de pagina **sleutel en eind punt** van de resource, onder **resource beheer**. 

Maak variabelen voor de sleutel en het eind punt van uw Computer Vision-abonnement. Plak uw abonnements sleutel en het eind punt in de volgende code, indien aangegeven. Het Computer Vision-eind punt heeft het formulier `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Vergeet niet om de abonnements sleutel uit uw code te verwijderen wanneer u klaar bent en deze nooit openbaar te plaatsen. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Bijvoorbeeld [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Ik heb de client ingesteld](?success=set-up-client#object-model) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de OCR-Node.js-SDK.

|Naam|Beschrijving|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om de meeste afbeeldingsbewerkingen uit te voeren.|

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de OCR-client bibliotheek voor Node.js:

* [De client verifiëren](#authenticate-the-client)
* [Afgedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren


Instantieer een client met uw eindpunt en sleutel. Maak een [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials)-object met uw sleutel en eindpunt en maak hiermee een [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)-object.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Definieer vervolgens een functie `computerVision` en declareer een asynchrone serie met een primaire functie en een callback-functie. U voegt uw quickstartcode toe aan de primaire functie en roept `computerVision`, onder aan het script, aan. De rest van de code in deze quickstart de functie `computerVision` in.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Ik heb de client geverifieerd](?success=authenticate-client#read-printed-and-handwritten-text) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Afgedrukte en handgeschreven tekst lezen

De OCR-service kan de zicht bare tekst in een afbeelding extra heren en converteren naar een teken stroom. In dit voorbeeld worden de leesbewerkingen gebruikt.

### <a name="set-up-test-images"></a>Testafbeeldingen instellen

Sla een verwijzing op van de URL van de afbeeldingen waaruit u tekst wilt extraheren.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> U kunt ook tekst lezen uit een lokale afbeelding. Zie de [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient)-methoden, bijvoorbeeld **readInStream**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="call-the-read-api"></a>De Read-API aanroepen

Definieer de volgende velden in uw functie om de statuswaarden van de read-aanroep aan te geven.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Voeg de onderstaande code toe. Hiermee wordt de `readTextFromURL`-functie aangeroepen voor de opgegeven afbeeldingen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definieer de `readTextFromURL`-functie. Hiermee roept u de **read**-methode aan op het clientobject en wordt een bewerkings-id geretourneerd. Ook wordt een asynchroon proces gestart om de inhoud van de afbeelding te lezen. Vervolgens wordt de bewerkings-id gebruikt om de status van de bewerking te controleren totdat de resultaten worden geretourneerd. Vervolgens worden de geëxtraheerde resultaten geretourneerd.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Definieer nu de hulpfunctie `printRecText`, waarmee de resultaten van de leesbewerking worden afgedrukt via de console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Ik heb tekst gelezen](?success=read-printed-handwritten-text#run-the-application) [Er is een fout opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text)

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

In deze Quick Start hebt u geleerd hoe u de OCR-client bibliotheek installeert en de Lees-API gebruikt. Vervolgens leest u meer over de API-functies voor lezen.

> [!div class="nextstepaction"]
>[De Read-API aanroepen](../../Vision-API-How-to-Topics/call-read-api.md)

* [Overzicht van OCR](../../overview-ocr.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
