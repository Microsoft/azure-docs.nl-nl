---
title: 'Quickstart: Antwoord uit knowledge base ophalen - REST, C# - QnA Maker'
description: Deze C# REST-quickstart begeleidt u bij het programmatisch ophalen van een antwoord uit een knowledge base.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: 44ef7fd8d08584acdb5155c102d6b6b4b0675d88
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023698"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Snelstartgids: antwoorden op een vraag van een Knowledge Base verkrijgen met C #

In deze quickstart wordt beschreven hoe u programmatisch een antwoord uit een gepubliceerde QnA Maker-knowledge base kunt ophalen. De Knowledge Base bevat vragen en antwoorden van [gegevens bronnen](../Concepts/knowledge-base.md) , zoals Veelgestelde vragen. De [vraag](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) wordt verzonden naar de QnA Maker-service. Het [antwoord](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) bevat het meest voorspelde antwoord.

[Referentiedocumentatie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/Runtime) | [Voorbeeld](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Vereisten

* De meest recente [**Visual Studio Community edition**](https://www.visualstudio.com/downloads/).
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Selecteer **Sleutels** onder **Resourcebeheer** in het Azure-dashboard voor de QnA Maker-resource om uw sleutel op te halen.
* Pagina Instellingen voor **Publiceren**. Als u nog geen knowledge base hebt gepubliceerd, moet u een lege knowledge base maken, een knowledge base importeren op de pagina **Instellingen** en de knowledge base publiceren. U kunt [deze eenvoudige knowledge base](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) downloaden en gebruiken.

    De pagina Instellingen voor Publiceren bevat de waarde voor POST-route, Host en EndpointKey.

    ![Instellingen voor Publiceren](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Een project met knowledge base maken

1. Open Visual Studio 2019 Community Edition.
1. Maak een nieuw console-app (.NET core)-project en geef het project de naam QnaMakerQuickstart. Accepteer de standaardwaarden voor de overige instellingen.

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Boven in het bestand Program.cs vervangt u de enige using-instructie door de volgende regels. U voegt op deze manier de benodigde afhankelijkheden toe aan het project:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen

Boven aan de klasse `Program` in de `Main` voegt u de vereiste constanten toe voor toegang tot QnA Maker. Deze waarden vindt u op de pagina **Publiceren** nadat u de knowledge base hebt gepubliceerd.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Een POST-aanvraag toevoegen om vragen te verzenden en antwoorden op te halen

Met de volgende code wordt een HTTPS-aanvraag naar de QnA Maker-API verzonden om de vraag naar de knowledge base te verzenden en het antwoord te ontvangen:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/query-kb.cs" id="post":::

De waarde van de header van `Authorization` bevat de tekenreeks `EndpointKey`.

Meer informatie over de [aanvraag](../how-to/metadata-generateanswer-usage.md#generateanswer-request) en het [antwoord](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Het programma bouwen en uitvoeren

Bouw het programma en voer het uit vanuit Visual Studio. De aanvraag wordt automatisch verzonden naar de QnA Maker-API, waarna het antwoord wordt weergegeven in het consolevenster.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
