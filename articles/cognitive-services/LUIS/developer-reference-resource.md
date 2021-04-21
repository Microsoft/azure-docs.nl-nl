---
title: Resources voor ontwikkelaars - Language Understanding
description: SDK's, REST API's, CLI helpen u bij het ontwikkelen Language Understanding apps (LUIS) in uw programmeertaal. Uw Azure-resources en LUIS-voorspellingen beheren.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 04c7d4a7e725d99c7dba94779d365312f8b960af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787103"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK-, REST- en CLI-resources voor ontwikkelaars voor Language Understanding (LUIS)

SDK's, REST API's, CLI helpen u bij het ontwikkelen Language Understanding apps (LUIS) in uw programmeertaal. Uw Azure-resources en LUIS-voorspellingen beheren.

## <a name="azure-resource-management"></a>Azure-resourcebeheer

Gebruik de Azure Cognitive Services Management-laag om de resource van de Language Understanding of Cognitive Service te maken, bewerken, weer te Language Understanding te verwijderen.

Zoek referentiedocumentatie op basis van het hulpprogramma:

* [Azure-CLI](/cli/azure/cognitiveservices#az_cognitiveservices_list)

* [PowerShell voor Azure RM](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding-ontwerp- en voorspellingsaanvragen

De Language Understanding-service is toegankelijk vanuit een Azure-resource die u moet maken. Er zijn twee resources:

* Gebruik de **ontwerpresource** voor training om te maken, bewerken, trainen en publiceren.
* Gebruik de **voorspelling voor** runtime om tekst van de gebruiker te verzenden en een voorspelling te ontvangen.

Meer informatie over het [V3-voorspellings-eindpunt.](luis-migration-api-v3.md)

Gebruik [Cognitive Services voorbeeldcode om](https://github.com/Azure-Samples/cognitive-services-quickstart-code) de meest voorkomende taken te leren en te gebruiken.

### <a name="rest-specifications"></a>REST-specificaties

De [LUIS REST-specificaties](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), samen met alle [Azure REST-specificaties,](https://github.com/Azure/azure-rest-api-specs)zijn openbaar beschikbaar op GitHub.

### <a name="rest-apis"></a>REST-API’s

Api's voor ontwerp- en voorspellings-eindpunten zijn beschikbaar via REST API's:

|Type|Versie|
|--|--|
|Ontwerpen|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[preview-versie V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Voorspelling|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-eindpunten

LUIS heeft momenteel twee typen eindpunten:

* **maken op** het eindpunt van de training
* **queryvoorspelling** op het runtime-eindpunt.

|Doel|URL|
|--|--|
|V2-ontwerp op trainings-eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V3-ontwerp op trainings-eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2-voorspelling - alle voorspellingen over runtime-eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3-voorspelling - versievoorspelling op runtime-eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3-voorspelling - slotvoorspelling op runtime-eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

In de volgende tabel worden de parameters in de vorige tabel uitgelegd, aangeduid met accolades. `{}`

|Parameter|Doel|
|--|--|
|`your-resource-name`|Azure-resourcenaam|
|`q` of `query`|utterancetekst verzonden vanuit clienttoepassing zoals chatbot|
|`version`|Versienaam van 10 tekens|
|`slot`| `production` of `staging`|

### <a name="rest-query-string-parameters"></a>Parameters voor REST-queryreeks

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>App-schema

Het [app-schema](app-schema-definition.md) wordt geïmporteerd en geëxporteerd in de `.json` indeling of `.lu` .

### <a name="language-based-sdks"></a>Op taal gebaseerde SDK's

|Taal |Referentiedocumentatie|Pakket|Snelstartgidsen|
|--|--|--|--|
|C#|[Ontwerpen](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Voorspelling](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet-ontwerp](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet-voorspelling](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Ontwerpen](./client-libraries-rest-api.md?pivots=rest-api)<br>[Queryvoorspelling](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Maken en voorspellen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Sdk](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Maken en voorspellen](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven-ontwerp](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-voorspelling](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|Javascript|[Ontwerpen](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Voorspelling](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM-ontwerp](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM-voorspelling](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Ontwerpen](./client-libraries-rest-api.md?pivots=rest-api)<br>[Voorspelling](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Maken en voorspellen](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Ontwerpen](./client-libraries-rest-api.md?pivots=rest-api)<br>[Voorspelling](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Containers

Language Understanding (LUIS) biedt een [container voor](luis-container-howto.md) on-premises en ingesloten versies van uw app.

### <a name="export-and-import-formats"></a>Indelingen exporteren en importeren

Language Understanding biedt de mogelijkheid om uw app en de modellen ervan te beheren in een JSON-indeling, de `.LU` indeling[(LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)en een gecomprimeerd pakket voor de Language Understanding container.

Het importeren en exporteren van deze indelingen is beschikbaar via de API's en vanuit de LUIS-portal. De portal biedt importeren en exporteren als onderdeel van de lijst Met apps en versies.

## <a name="workshops"></a>Workshops

* GitHub: [Conversational-AI (Workshop) : NLU met LUIS](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Hulpprogramma's voor continue integratie

* GitHub: (preview) [Een LUIS-app ontwikkelen met behulp van DevOps-procedures](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps: hulpprogramma's](https://github.com/microsoft/NLU.DevOps) voor continue integratie en implementatie voor NLU-services.

## <a name="bot-framework-tools"></a>Bot Framework hulpprogramma's

Het bot-framework is beschikbaar [als een SDK](https://github.com/Microsoft/botframework) in verschillende talen en als een service met [behulp van Azure Bot Service](https://dev.botframework.com/).

Bot Framework biedt [verschillende hulpprogramma's](https://github.com/microsoft/botbuilder-tools) voor Language Understanding, waaronder:
* [Bot Framework emulator:](https://github.com/Microsoft/BotFramework-Emulator/releases) een bureaubladtoepassing waarmee botontwikkelaars bots kunnen testen en fouten kunnen opsporen die zijn gebouwd met de Bot Framework SDK
* [Bot Framework Composer:](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) een geïntegreerd ontwikkelhulpprogramma voor ontwikkelaars en teams met meerdere ontwikkelmogelijkheden om bots en gesprekservaringen met de Microsoft Bot Framework
* [Bot Framework voorbeelden](https://github.com/microsoft/botbuilder-samples) : in #C, JavaScript, TypeScript en Python

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de algemene [HTTP-foutcodes](luis-reference-response-codes.md)
* [Referentiedocumentatie](../../index.yml) voor alle API's en SDK's
* [Bot-framework](https://github.com/Microsoft/botbuilder-dotnet) [en -Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Cognitieve containers](../cognitive-services-container-support.md)
