---
title: Bronnen voor ontwikkel aars-Language Understanding
description: Sdk's, REST Api's, CLI, helpen u bij het ontwikkelen van Language Understanding-apps (LUIS) in uw programmeer taal. Beheer uw Azure-resources en LUIS-voor spellingen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 08a1965aadf8f50625e54d7c5e3b3f6a44c0bbd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98946173"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK-, REST-en CLI-ontwikkelaars bronnen voor Language Understanding (LUIS)

Sdk's, REST Api's, CLI, helpen u bij het ontwikkelen van Language Understanding-apps (LUIS) in uw programmeer taal. Beheer uw Azure-resources en LUIS-voor spellingen.

## <a name="azure-resource-management"></a>Azure-resource beheer

Gebruik de Azure Cognitive Services Management-laag voor het maken, bewerken, weer geven en verwijderen van de Language Understanding of de cognitieve service resource.

Zoek naar de referentie documentatie op basis van het hulp programma:

* [Azure-CLI](/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [PowerShell voor Azure RM](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Aanvragen voor ontwerpen Language Understanding en voor spellingen

De Language Understanding-service wordt geopend vanuit een Azure-resource die u moet maken. Er zijn twee resources:

* Gebruik de **ontwerp** resource voor training voor het maken, bewerken, trainen en publiceren.
* Gebruik de voor **Spelling** voor runtime om de tekst van de gebruiker te verzenden en een voor spelling te ontvangen.

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

Gebruik [Cognitive Services voorbeeld code](https://github.com/Azure-Samples/cognitive-services-quickstart-code) om de meest voorkomende taken te leren en te gebruiken.

### <a name="rest-specifications"></a>REST-specificaties

De [Luis-rest specificaties](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), samen met alle [Azure rest-specificaties](https://github.com/Azure/azure-rest-api-specs), zijn openbaar beschikbaar op github.

### <a name="rest-apis"></a>REST-API’s

De API'S voor ontwerpen en voor spellingen van eind punten zijn beschikbaar via REST-Api's:

|Type|Versie|
|--|--|
|Ontwerpen|[Offload](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Preview v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Voorspelling|[Offload](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-eind punten

LUIS heeft momenteel twee soorten eind punten:

* **ontwerpen** voor het trainings eindpunt
* query **voorspelling** voor het runtime-eind punt.

|Doel|URL|
|--|--|
|V2 ontwerpen voor het trainings eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V3 ontwerpen voor het trainings eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 voor spelling-alle voor spellingen op runtime-eind punt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Voor spelling van v3-versies voor spelling van runtime-eind punt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Voorspellings voorspelling voor voor spellingen voor het runtime-eind punt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

In de volgende tabel worden de para meters beschreven, aangeduid met accolades `{}` in de vorige tabel.

|Parameter|Doel|
|--|--|
|`your-resource-name`|Azure-resource naam|
|`q` of `query`|utterance tekst verzonden vanuit client toepassing zoals chat-bot|
|`version`|versie naam van 10 tekens|
|`slot`| `production` of `staging`|

### <a name="rest-query-string-parameters"></a>Query teken reeks parameters voor REST

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>App-schema

Het [app-schema](app-schema-definition.md) wordt geïmporteerd en geëxporteerd in een- `.json` of- `.lu` indeling.

### <a name="language-based-sdks"></a>Op taal gebaseerde Sdk's

|Taal |Referentiedocumentatie|Pakket|Snelstartgidsen|
|--|--|--|--|
|C#|[Ontwerpen](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Voorspelling](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet ontwerpen](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet-voor spelling](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Ontwerpen](./client-libraries-rest-api.md?pivots=rest-api)<br>[Query voorspelling](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Ontwerpen en voors pellen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Ontwerpen en voors pellen](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven ontwerpen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-voor spelling](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|Javascript|[Ontwerpen](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Voorspelling](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM ontwerpen](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM-voor spelling](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Ontwerpen](./client-libraries-rest-api.md?pivots=rest-api)<br>[Voorspelling](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Ontwerpen en voors pellen](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Ontwerpen](./client-libraries-rest-api.md?pivots=rest-api)<br>[Voorspelling](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Containers

Language Understanding (LUIS) biedt een [container](luis-container-howto.md) om on-premises en opgenomen versies van uw app te bieden.

### <a name="export-and-import-formats"></a>Indelingen exporteren en importeren

Language Understanding biedt de mogelijkheid om uw app en de bijbehorende modellen te beheren in een JSON-indeling, de `.LU` indeling ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) en een gecomprimeerd pakket voor de Language Understanding-container.

Het importeren en exporteren van deze indelingen is beschikbaar via de Api's en vanuit de LUIS-Portal. De portal biedt importeren en exporteren als onderdeel van de lijst met apps en versies.

## <a name="workshops"></a>Constructie

* GitHub: (workshop) [gesprek-AI: NLU met Luis](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Hulpprogramma's voor continue integratie

* GitHub: (preview) [het ontwikkelen van een Luis-app met behulp van DevOps-procedures](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -tools die ondersteuning bieden voor continue integratie en implementatie voor NLU-Services.

## <a name="bot-framework-tools"></a>Bot Framework tools

Het bot-Framework is beschikbaar als [een SDK](https://github.com/Microsoft/botframework) in verschillende talen en als een service met behulp van [Azure bot service](https://dev.botframework.com/).

Bot Framework biedt [verschillende hulpprogram ma's](https://github.com/microsoft/botbuilder-tools) voor het language Understanding, waaronder:
* [Bot Framework-emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) : een bureaublad toepassing die bot-ontwikkel aars in staat stelt om bots te testen en op te sporen met behulp van de bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) : een geïntegreerd hulp programma voor ontwikkel aars en meerdere disciplinaire teams voor het bouwen van bots-en gespreks ervaringen met het micro soft bot Framework
* Voor [beelden van bot-Framework](https://github.com/microsoft/botbuilder-samples) -in #C, java script, type script en python

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de veelvoorkomende [HTTP-fout codes](luis-reference-response-codes.md)
* [Referentie documentatie](../../index.yml) voor alle Api's en sdk's
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) en [Azure bot service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Cognitieve containers](../cognitive-services-container-support.md)
