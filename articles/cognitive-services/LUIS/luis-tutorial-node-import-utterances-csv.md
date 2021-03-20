---
title: Uitingen importeren met behulp van Node.js-LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een LUIS-app via een programma vanuit bestaande gegevens in CSV-indeling met behulp van de LUIS authoring API.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, devx-track-js
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/05/2019
ms.openlocfilehash: 58eb92f4d0bc3de4671ca2ece14a178a876e4a6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541035"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Een LUIS-app bouwen via een programma met Node.js

LUIS biedt een programmatische API die alles doet wat de [Luis](luis-reference-regions.md) -website doet. Dit kan tijd besparen wanneer u al bestaande gegevens hebt en u kunt sneller een LUIS-app maken dan door hand matig gegevens in te voeren.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Vereisten

* Meld u aan bij de [Luis](luis-reference-regions.md) -website en zoek uw [ontwerp sleutel](luis-how-to-azure-subscription.md#authoring-key) in de account instellingen. U gebruikt deze sleutel om de ontwerp-Api's aan te roepen.
* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.
* Dit artikel begint met een CSV voor een hypothetische logboek bestanden van gebruikers aanvragen van een bedrijf. Down load deze [hier](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Installeer de nieuwste Node.js met NPM. U kunt het [hier](https://nodejs.org/en/download/) downloaden.
* **[Aanbevolen]** Visual Studio [code voor IntelliSense](https://code.visualstudio.com/) en fout opsporing kunt u deze gratis downloaden.

Alle code in dit artikel is beschikbaar in de [github-opslag plaats Azure-samples language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv).

## <a name="map-preexisting-data-to-intents-and-entities"></a>Bestaande gegevens aan intenties en entiteiten toewijzen
Zelfs als u een systeem hebt dat niet met LUIS is gemaakt, kunt u, als het tekstuele gegevens bevat die zijn gekoppeld aan verschillende gebruikers, een toewijzing van de bestaande categorieën gebruikers invoer aan intenties in LUIS. Als u belang rijke woorden of zinsdelen kunt identificeren in wat de gebruikers zei, kunnen deze woorden worden toegewezen aan entiteiten.

Open het [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) bestand. Het bevat een logboek van gebruikers query's naar een hypothetische Home Automation-Service, waaronder de manier waarop ze zijn gecategoriseerd, wat de gebruiker zei en sommige kolommen met nuttige informatie die ze uit hen hebben gehaald.

![CSV-bestand met vooraf bestaande gegevens](./media/luis-tutorial-node-import-utterances-csv/csv.png)

U ziet dat de **RequestType** -kolom mogelijk intenties is en de kolom **aanvraag** bevat een voor beeld van een utterance. De andere velden kunnen entiteiten zijn als deze zich in de utterance voordoen. Omdat er intenties, entiteiten en voor beeld uitingen zijn, hebt u de vereisten voor een eenvoudige, voor beeld-app.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Stappen voor het genereren van een LUIS-app op basis van niet-LUIS gegevens
Een nieuwe LUIS-app genereren vanuit het CSV-bestand:

* De gegevens uit het CSV-bestand parseren:
    * Converteer naar een indeling die u kunt uploaden naar LUIS met behulp van de API voor ontwerpen.
    * Verzamel uit de geparseerde gegevens informatie over intents en entiteiten.
* Maak ontwerp-API-aanroepen naar:
    * Maak de app.
    * Voeg intents en entiteiten toe die zijn verzameld uit de geparseerde gegevens.
    * Nadat u de LUIS-app hebt gemaakt, kunt u het voor beeld uitingen toevoegen uit de geparseerde gegevens.

U kunt deze programma stroom bekijken in het laatste deel van het `index.js` bestand. Kopieer of [down load](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) deze code en sla deze op in `index.js` .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>De CSV parseren

De kolom vermeldingen die de uitingen in het CSV bevatten moeten worden geparseerd naar een JSON-indeling die LUIS kan begrijpen. Deze JSON-indeling moet een `intentName` veld bevatten waarin het doel van de utterance wordt geïdentificeerd. Het moet ook een `entityLabels` veld bevatten. Dit kan leeg zijn als er geen entiteiten in de utterance zijn.

Bijvoorbeeld: de vermelding ' verlichting inschakelen ' aan deze JSON

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

In dit voor beeld `intentName` is het afkomstig van de gebruikers aanvraag onder de kolomkop **aanvraag** in het CSV-bestand, en de `entityName` afkomstig van de andere kolommen met belang rijke informatie. Als er bijvoorbeeld een vermelding is voor de **bewerking** of het **apparaat**, en die teken reeks ook voor komt in de daad werkelijke aanvraag, kan deze worden aangeduid als een entiteit. De volgende code laat zien hoe dit proces wordt geparseerd. U kunt het kopiëren of [downloaden](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) en opslaan in `_parse.js` .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>De LUIS-app maken
Zodra de gegevens zijn geparseerd in JSON, voegt u deze toe aan een LUIS-app. Met de volgende code wordt de LUIS-app gemaakt. Kopieer of [down load](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) deze en sla het op in `_create.js` .

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Intents toevoegen
Zodra u een app hebt, moet u deze aan het doel hebben. Met de volgende code wordt de LUIS-app gemaakt. Kopieer of [down load](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) deze en sla het op in `_intents.js` .

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Entiteiten toevoegen
Met de volgende code worden de entiteiten toegevoegd aan de LUIS-app. Kopieer of [down load](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) deze en sla het op in `_entities.js` .

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]



## <a name="add-utterances"></a>Utterances toevoegen
Zodra de entiteiten en intenties zijn gedefinieerd in de LUIS-app, kunt u de uitingen toevoegen. De volgende code maakt gebruik van de [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) -API, waarmee u maxi maal 100 uitingen per keer kunt toevoegen.  Kopieer of [down load](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) deze en sla het op in `_upload.js` .

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>De code uitvoeren


### <a name="install-nodejs-dependencies"></a>Node.js afhankelijkheden installeren
Installeer de Node.js afhankelijkheden van NPM op de Terminal/opdracht regel.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Configuratie-instellingen wijzigen
Als u deze toepassing wilt gebruiken, moet u de waarden in het index.js-bestand wijzigen in uw eigen eindpunt sleutel en geeft u de naam op die u wilt voor de app. U kunt ook de cultuur van de app instellen of het versie nummer wijzigen.

Open het index.js-bestand en wijzig deze waarden boven aan het bestand.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us";
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Het script uitvoeren
Voer het script uit vanaf een Terminal/opdracht regel met Node.js.

```console
> node index.js
```

of

```console
> npm start
```

### <a name="application-progress"></a>Voortgang van toepassing
Terwijl de toepassing wordt uitgevoerd, wordt de voortgang van de opdracht regel weer gegeven. De uitvoer van de opdracht regel bevat de indeling van de antwoorden van LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>De LUIS-app openen
Zodra het script is voltooid, kunt u zich aanmelden bij [Luis](luis-reference-regions.md) en de Luis-app zien die u hebt gemaakt onder **mijn apps**. U zou de uitingen moeten kunnen zien die u hebt toegevoegd onder de **TurnOn**, **TurnOff** en **geen** intenties.

![TurnOn intentie](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw app testen en trainen op LUIS-website](luis-interactive-test.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Deze voorbeeld toepassing maakt gebruik van de volgende LUIS-Api's:
- [app maken](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [intenties toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [entiteiten toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e)
- [uitingen toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
