---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/27/2020
ms.openlocfilehash: f050ed5aff63d7e75c45505309b2cf7d18e9ea1a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947508"
---
[Referentiedocumentatie](/javascript/api/@azure/cognitiveservices-personalizer/) |[Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [Node.js](https://nodejs.org) en NPM.
* Zodra u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Een Personalizer-resource maken"  target="_blank">een Personalizer-resource maken <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Personalizer-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map.

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init -y` uit om een bestand `package.json` te maken.

```console
npm init -y
```

Maak een nieuwe Node.js-toepassing in uw voorkeurseditor of IDE met de naam `sample.js` en maak variabelen voor het eindpunt en de abonnementssleutel van uw resource. 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const uuidv1 = require('uuid/v1');
const Personalizer = require('@azure/cognitiveservices-personalizer');
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const readline = require('readline-sync');

// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const serviceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";

// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const baseUri = "https://<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>.cognitiveservices.azure.com";
```

### <a name="install-the-nodejs-library-for-personalizer"></a>De Node.js-bibliotheek installeren voor Personalizer

Installeer de Personalizer-clientbibliotheek voor Node.js met de volgende opdracht:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installeer de resterende NPM-pakketten voor deze quickstart:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objectmodel

De Personalizer-client is een [PersonalizerClient](/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient)-object dat wordt geverifieerd bij Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Als u het beste item van de inhoud wilt hebben, maakt u een [RankRequest](/javascript/api/@azure/cognitiveservices-personalizer/rankrequest) en geeft u dit door aan de methode [client.Rank](/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient#rank-rankrequest--msrest-requestoptionsbase-). Met de methode Rank wordt een RankResponse geretourneerd.

Als u een belonings naar een Personalizer wilt verzenden, maakt u een [RewardRequest](/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest) en geeft u dit door aan de methode [Reward](/javascript/api/@azure/cognitiveservices-personalizer/events#reward-string--rewardrequest--servicecallback-void--) in de Gebeurtenisklasse.

Het bepalen van de belonings in deze quickstart is triviaal. In een productiesysteem kan de bepaling wat van invloed is op de [beloningsscore](../concept-rewards.md) en in welke mate, een complex proces zijn, dat u in de loop van de tijd zou kunnen wijzigen. Dit moet een van de belangrijkste ontwerpbeslissingen in uw Personalizer-architectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt uitvoeren met de Personalizer-clientbibliotheek voor Node.js:

* [Personalizer-client maken](#authenticate-the-client)
* [Positie API](#request-the-best-action)
* [Beloning API](#send-a-reward)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer de `PersonalizerClient` met uw eerder gemaakte `serviceKey` en `baseUri`.

```javascript
const credentials = new CognitiveServicesCredentials(serviceKey);

// Initialize Personalization client.
const personalizerClient = new Personalizer.PersonalizerClient(credentials, baseUri);
```

## <a name="get-content-choices-represented-as-actions"></a>Inhoudskeuzes ophalen die worden weergegeven als acties

Acties vertegenwoordigen de inhoudskeuzes waaruit Personalizer het beste inhoudsitem moet selecteren. Voeg de volgende methoden aan de klasse Programma toe om de reeks acties en hun functies te representeren.

```javascript
function getContextFeaturesList() {
  const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
  const tasteFeatures = ['salty', 'sweet'];

  let answer = readline.question("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n");
  let selection = parseInt(answer);
  const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

  answer = readline.question("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet\n");
  selection = parseInt(answer);
  const taste = selection >= 1 && selection <= 2 ? tasteFeatures[selection - 1] : tasteFeatures[0];

  console.log("Selected features:\n");
  console.log("Time of day: " + timeOfDay + "\n");
  console.log("Taste: " + taste + "\n");

  return [
    {
      "time": timeOfDay
    },
    {
      "taste": taste
    }
  ];
}
```

```javascript
function getActionsList() {
  return [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ];
}
```

## <a name="create-the-learning-loop"></a>De leerlus maken

De Personalizer-leerlus is een cyclus van [Positie](#request-the-best-action)- en [Beloning](#send-a-reward)-aanroepen. In deze quickstart wordt elke Positie-oproep, voor het personaliseren van de inhoud, gevolgd door een Beloning-oproep om Personalizer te laten weten hoe goed de service is uitgevoerd.

Met de volgende code wordt herhaaldelijk vanaf de opdrachtregel de cyclus voor het vragen naar de voorkeuren van de gebruiker doorlopen. Deze gegevens worden naar Personalizer gestuurd om de beste actie te selecteren, waarbij de selectie aan de klant in een lijst wordt gepresenteerd om een keuze te kunnen maken. Vervolgens wordt er een beloning naar Personalizer verzonden waarmee wordt aangegeven hoe goed de service de selectie heeft uitgevoerd.

```javascript
let runLoop = true;

do {

  let rankRequest = {}

  // Generate an ID to associate with the request.
  rankRequest.eventId = uuidv1();

  // Get context information from the user.
  rankRequest.contextFeatures = getContextFeaturesList();

  // Get the actions list to choose from personalization with their features.
  rankRequest.actions = getActionsList();

  // Exclude an action for personalization ranking. This action will be held at its current position.
  rankRequest.excludedActions = getExcludedActionsList();

  rankRequest.deferActivation = false;

  // Rank the actions
  const rankResponse = await personalizerClient.rank(rankRequest);

  console.log("\nPersonalization service thinks you would like to have:\n")
  console.log(rankResponse.rewardActionId);

  // Display top choice to user, user agrees or disagrees with top choice
  const reward = getReward();

  console.log("\nPersonalization service ranked the actions with the probabilities as below:\n");
  for (let i = 0; i < rankResponse.ranking.length; i++) {
    console.log(JSON.stringify(rankResponse.ranking[i]) + "\n");
  }

  // Send the reward for the action based on user response.

  const rewardRequest = {
    value: reward
  }

  await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);

  runLoop = continueLoop();

} while (runLoop);
```

Bekijk de Rank- en Reward-aanroepen in de volgende secties.

Voeg de volgende methoden toe, waarmee [de inhoudskeuzes worden opgehaald](#get-content-choices-represented-as-actions), voordat u het codebestand uitvoert:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>De beste actie aanvragen

Als u de Positie-aanvraag wilt voltooien, wordt naar de voorkeuren van de gebruiker gevraagd om inhoudskeuzes te maken. Het proces kan inhoud maken die van de acties wordt uitgesloten, weergegeven als `excludeActions`. De Positie-aanvraag heeft de [acties](../concepts-features.md#actions-represent-a-list-of-options) en hun kenmerken, currentcontext-kenmerken, excludeActions en een unieke ranggebeurtenis-id nodig om de gerangschikte reactie te kunnen ontvangen.

Deze quickstart bevat eenvoudige contextkenmerken over het tijdstip van de dag en de voedselvoorkeur van de gebruiker. In productiesystemen kan het bepalen en [evalueren](../concept-feature-evaluation.md) van [acties en kenmerken](../concepts-features.md) een ingewikkelde zaak zijn.

```javascript
let rankRequest = {}

// Generate an ID to associate with the request.
rankRequest.eventId = uuidv1();

// Get context information from the user.
rankRequest.contextFeatures = getContextFeaturesList();

// Get the actions list to choose from personalization with their features.
rankRequest.actions = getActionsList();

// Exclude an action for personalization ranking. This action will be held at its current position.
rankRequest.excludedActions = getExcludedActionsList();

rankRequest.deferActivation = false;

// Rank the actions
const rankResponse = await personalizerClient.rank(rankRequest);
```

## <a name="send-a-reward"></a>Een beloning verzenden

Om ervoor te zorgen dat de beloningsscore de Beloning-aanvraag instuurt, haalt het programma de selectie van de gebruiker op vanaf de opdrachtregel, wijst een numerieke waarde toe aan de selectie en verzendt vervolgens de unieke gebeurtenis-id en de beloningsscore als de numerieke waarde naar de Beloning API.

In deze quickstart wordt een eenvoudig getal, een 0 of een 1, aan een beloningsscore toegewezen. In productiesystemen kan het bepalen van wanneer en wat er naar de [Beloning](../concept-rewards.md)-oproep moet worden gezonden een ingewikkelde zaak zijn, afhankelijk van uw specifieke behoeften.

```javascript
const rewardRequest = {
  value: reward
}

await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);
```

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit met de Node.js vanuit uw toepassingsmap.

```console
node sample.js
```

![Het quickstart-programma stelt een aantal vragen om gebruikersvoorkeuren te verzamelen, zogenaamde kenmerken. Vervolgens wordt de beste actie geboden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
