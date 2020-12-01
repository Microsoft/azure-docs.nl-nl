---
title: 'Quickstart: Antwoord uit knowledge base ophalen - REST, Python - QnA Maker'
description: Deze Python REST-quickstart begeleidt u bij het programmatisch ophalen van een antwoord uit een knowledge base.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: d74993d35accea451c5b003ba10df3819d65c43c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345883"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Snelstartgids: Krijg antwoorden op een vraag van een kennis database met python

In deze quickstart wordt beschreven hoe u programmatisch een antwoord uit een gepubliceerde QnA Maker-knowledge base kunt ophalen. De Knowledge Base bevat vragen en antwoorden van [gegevens bronnen](../index.yml) , zoals Veelgestelde vragen. De [vraag](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) wordt verzonden naar de QnA Maker-service. Het [antwoord](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) bevat het meest voorspelde antwoord.

[Referentiedocumentatie](/rest/api/cognitiveservices/qnamaker4.0/Runtime) | [Voorbeeld](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>Vereisten

* [Python 3.6 of hoger](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Selecteer **Sleutels** onder **Resourcebeheer** in het Azure-dashboard voor de QnA Maker-resource om uw sleutel op te halen.
* Pagina Instellingen voor **Publiceren**. Als u nog geen knowledge base hebt gepubliceerd, moet u een lege knowledge base maken, een knowledge base importeren op de pagina **Instellingen** en de knowledge base publiceren. U kunt [deze eenvoudige knowledge base](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) downloaden en gebruiken.

    De pagina Instellingen voor Publiceren bevat de waarde voor POST-route, Host en EndpointKey.

    ![Instellingen voor Publiceren](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Een Python-bestand maken

Open VSCode en maak een nieuw bestand met de naam `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Voeg bovenaan het bestand `get-answer-3x.py` de vereiste afhankelijkheden toe aan het project:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="dependencies":::

<!--TBD - reword this following paragraph -->

De host en route zijn anders dan ze worden weergegeven op de pagina **Publiceren**. Dit komt omdat de Python-bibliotheek geen routering in de host toestaat. De routering die op de pagina **Publiceren** wordt weergegeven als onderdeel van de host, is verplaatst naar de route.

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen

Voeg de vereiste constanten toe voor toegang tot QnA Maker. Deze waarden vindt u op de pagina **Publiceren** nadat u de knowledge base hebt gepubliceerd.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Een POST-aanvraag toevoegen om vragen te verzenden en antwoorden op te halen

Met de volgende code wordt een HTTPS-aanvraag naar de QnA Maker-API verzonden om de vraag naar de knowledge base te verzenden en het antwoord te ontvangen:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="main":::

De waarde van de header van `Authorization` bevat de tekenreeks `EndpointKey`.

## <a name="run-the-program"></a>Het programma uitvoeren

Voer het programma uit vanaf de opdrachtregel. De aanvraag wordt automatisch verzonden naar de QnA Maker-API, waarna het antwoord wordt weergegeven in het consolevenster.

Het bestand uitvoeren:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Meer informatie over de [aanvraag](../how-to/metadata-generateanswer-usage.md#generateanswer-request) en het [antwoord](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)