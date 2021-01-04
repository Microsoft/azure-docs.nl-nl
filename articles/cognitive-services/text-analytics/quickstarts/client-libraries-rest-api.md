---
title: 'Quickstart: Textmining met behulp van de Text Analytics-clientbibliotheek'
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om sentimentanalyse en meer uit te voeren, met de Text Analytics-API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: aahi
keywords: textmining, sentimentanalyse, tekstanalyse
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: f93b7954861bf3351d2930132e1391e1f21b2a6f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655115"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Quickstart: de clientbibliotheek en REST API van Text Analytics gebruiken

Gebruik dit artikel om aan de slag te gaan met de clientbibliotheek en REST API van Text Analytics. Voer de volgende stappen uit om codevoorbeelden te gebruiken voor tekstanalyse:

* Sentimentanalyse
* Meninganalyse
* Taaldetectie
* Herkenning van entiteiten
* Herkenning van persoonlijke gegevens
* Sleuteltermextractie


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Voor productiescenario's wordt aanbevolen om de batch-asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.
> * Als u Text Analytics wilt gebruiken voor status of asynchrone bewerkingen, bekijkt u de voorbeelden in Github voor [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) of [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Voor productiescenario's wordt aanbevolen om de batch-asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.
Als u Text Analytics wilt gebruiken voor status of asynchrone bewerkingen, bekijkt u de voorbeelden in Github voor [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) of [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Voor productiescenario's wordt aanbevolen om de batch-asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder.
> * U kunt deze versie van de Text Analytics-clientbibliotheek ook uitvoeren [in de browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
>    * Volg alleen de instructies voor de versie die u gebruikt.
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Voor productiescenario's wordt aanbevolen om de batch-asynchrone methoden te gebruiken voor prestaties en schaalbaarheid. Zie de referentiedocumentatie hieronder. Als u Text Analytics wilt gebruiken voor status of asynchrone bewerkingen, bekijkt u de voorbeelden in Github voor [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) of [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * De meest recente stabiele versie van de Text Analytics-API is `3.0`.
>    * Volg alleen de instructies voor de versie die u gebruikt.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een oplossing verkennen](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Overzicht van Text Analytics](../overview.md)
* [Sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Herkenning van entiteiten](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taalherkenning](../how-tos/text-analytics-how-to-language-detection.md)
