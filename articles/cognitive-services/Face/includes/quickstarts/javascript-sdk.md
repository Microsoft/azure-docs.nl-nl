---
title: Quick Start java script-client bibliotheek
description: Gebruik de face-client bibliotheek voor Java script om gezichten te detecteren, vergelijk bare zoeken (gezichts informatie op afbeelding), gezichten identificeren (gezichts herkenning zoeken) en uw gezichts gegevens migreren.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: b4a63f76cbcd9e98295f5edcf7ff2d06979e6556
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244458"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Snelstartgids: Face client-bibliotheek voor Java script

Ga aan de slag met gezichts herkenning met behulp van de face-client bibliotheek voor Java script. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de face-client bibliotheek voor Java script voor het volgende:

* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een groep personen (PersonGroup) maken](#create-a-person-group)
* [Een gezicht identificeren](#identify-a-face)

[Referentiedocumentatie](/javascript/api/@azure/cognitiveservices-face/) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face) | [Pakket (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face) | [Voorbeelden](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De nieuwste versie van [Node.js](https://nodejs.org/en/)
* Wanneer u uw Azure-abonnement hebt, [maakt u een gezichts bron](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) in het Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
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

Installeer als volgt de NPM-pakketten `ms-rest-azure` en `azure-cognitiveservices-face`:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam `index.js` en importeer de volgende bibliotheken:

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](), waar de codevoorbeelden uit deze quickstart zich bevinden.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. 

> [!IMPORTANT]
> Ga naar Azure Portal. Als de bron van het gezicht die u hebt gemaakt in de sectie **vereisten** is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) voor meer informatie.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Face .NET-clientbibliotheek:

|Naam|Beschrijving|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Deze klasse vertegenwoordigt uw autorisatie voor het gebruik van de Face-service. U hebt deze nodig voor alle Face-functies. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken. |
|[Face](/javascript/api/@azure/cognitiveservices-face/face)|Deze klasse verwerkt de basistaken voor detectie en herkenning die u met menselijke gezichten kunt uitvoeren. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd van één gezicht in een afbeelding. U kunt deze gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|Deze klasse beheert de in de cloud opgeslagen **FaceList**-constructies, waarin een geassorteerde set gezichten wordt opgeslagen. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Deze klasse beheert de in de cloud opgeslagen **Person**-constructies, die een set gezichten opslaan die tot één persoon behoren.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| Deze klasse beheert de in de cloud opgeslagen **PersonGroup**-constructies, die een set van verschillende **Person**-objecten opslaan. |

## <a name="code-examples"></a>Codevoorbeelden

De onderstaande codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Face-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een groep personen (PersonGroup) maken](#create-a-person-group)
* [Een gezicht identificeren](#identify-a-face)

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js), waar de codevoorbeelden uit deze quickstart zich bevinden.

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer een client met uw eindpunt en sleutel. Maak een **[ApiKeyCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials)** -object met uw sleutel en gebruik het met uw eind punt om een **[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient)** -object te maken.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Globale waarden declareren en hulp functie

De volgende globale waarden zijn vereist voor verschillende gezichts bewerkingen die u later toevoegt.

De URL verwijst naar een map met voor beelden van installatie kopieën. De UUID fungeert als naam en ID voor de PersonGroup die u gaat maken.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

U gebruikt de volgende functie om te wachten tot de training van de PersonGroup is voltooid.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

### <a name="get-detected-face-objects"></a>Gedetecteerde face-objecten ophalen

Maak een nieuwe methode voor het detecteren van gezichten. Met de `DetectFaceExtract`-methode worden drie van de afbeeldingen bij de opgegeven URL verwerkt en wordt een lijst met **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** -objecten in het programmageheugen gemaakt. In de lijst met **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** -waarden wordt aangegeven welke functies moeten worden geëxtraheerd. 

De `DetectFaceExtract` methode parseert en afdrukt vervolgens de kenmerk gegevens voor elk gedetecteerd gezicht. Elk kenmerk moet afzonderlijk worden opgegeven in de oorspronkelijke gezichtsdetectie-API-aanroep (in de lijst **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** ). De volgende code verwerkt elk kenmerk, maar u zult waarschijnlijk slechts één of enkele gebruiken.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> U kunt ook gezichten detecteren in een lokale afbeelding. Bekijk de [gezichts](/javascript/api/@azure/cognitiveservices-face/face) methoden zoals [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

Met de volgende code wordt op basis van één gedetecteerd gezicht (bron) een reeks andere gezichten (doel) doorzocht om een overeenkomstig gezicht te vinden (gezichten zoeken op afbeelding). Wanneer er een overeenkomst wordt gevonden, wordt de id van het overeenkomende gezicht afgedrukt op de console.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Definieer eerst een tweede gezichtsdetectiemethode. U moet gezichten in afbeeldingen detecteren voordat u ze kunt vergelijken en deze detectiemethode is geoptimaliseerd voor vergelijkingsbewerkingen. Er worden geen gedetailleerde gezichtskenmerken geëxtraheerd, zoals in de bovenstaande sectie, en er wordt een ander herkenningsmodel gebruikt.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Overeenkomsten zoeken

De volgende methode detecteert gezichten in een set doelafbeeldingen en in één bronafbeelding. Vervolgens worden deze vergeleken en worden alle doelafbeeldingen gevonden die vergelijkbaar zijn met de bronafbeelding. Ten slotte worden de gegevens van de overeenkomst naar de console afgedrukt.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Een gezicht identificeren

Bij de bewerking [identificeren](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) wordt een afbeelding van een persoon (of meerdere personen) gebruikt en wordt gezocht naar de identiteit van elk gezicht in de installatie kopie (gezichts herkenning zoeken). Elk gedetecteerd gezicht wordt vergeleken met een [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup), een database van verschillende [Person](/javascript/api/@azure/cognitiveservices-face/person)-objecten waarvan de gezichtskenmerken bekend zijn. Als u de identificerende bewerking wilt uitvoeren, moet u eerst een [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)maken en trainen.

### <a name="add-faces-to-person-group"></a>Gezichten toevoegen aan persoons groep

Maak de volgende functie om gezichten toe te voegen aan de [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-person-group"></a>Wachten op training van persoons groep

Maak de volgende Help-functie om te wachten op de persoons groep om de training te volt ooien.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-person-group"></a>Een groep personen (PersonGroup) maken

De volgende code:
- Hiermee maakt u een [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)
- Voegt gezichten toe aan de persoons groep door aan `AddFacesToPersonGroup` te roepen, die u eerder hebt gedefinieerd.
- Traint de persoons groep.
- Hiermee worden de gezichten in de groep persoon geïdentificeerd.

Deze **Person**-groep en de bijbehorende **Person**-objecten zijn nu klaar om te worden gebruikt in de bewerkingen Verifiëren, Identificeren of Groeperen.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> U kunt ook een **PersonGroup** maken op basis van lokale afbeeldingen. Bekijk de [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) -methoden zoals [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Hoofd

Tot slot maakt `main` u de functie en roept u deze aan.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de face-client bibliotheek voor Java script kunt gebruiken om verlichtings taken uit te voeren. Bestudeer daarna het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
> [Face-API referentie (Java script)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/)

* [Wat is de Face-service?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).
