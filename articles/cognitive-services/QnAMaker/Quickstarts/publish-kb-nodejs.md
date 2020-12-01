---
title: "Snelstartgids: QnA Maker met REST-Api's voor Node.js"
description: In deze Quick start ziet u hoe u aan de slag gaat met de QnA Maker REST Api's voor Node.js. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: 645f9ffb1d907eb576f5414b593ef57679d26daa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351143"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Snelstartgids: QnA Maker REST-Api's voor Node.js

Ga aan de slag met de QnA Maker REST-Api's voor Node.js. Volg deze stappen om de voorbeeld code voor basis taken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.

Gebruik de QnA Maker REST-Api's voor Node.js om:

* Een kennisdatabase maken
* Een kennis database vervangen
* Een kennisdatabase publiceren
* Een knowledge base verwijderen
* Een Knowledge Base downloaden
* De status van een bewerking ophalen

[Referentie documentatie](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api) -voor beelden

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [Node.js](https://nodejs.org).
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u de sleutel en het eind punt (inclusief de resource naam) wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal.

## <a name="setting-up"></a>Instellen

### <a name="create-a-qna-maker-azure-resource"></a>Een QnA Maker Azure-resource maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor QnA Maker met behulp van de [Azure Portal](../../cognitive-services-apis-create-account.md) of [Azure cli](../../cognitive-services-apis-create-account-cli.md) op uw lokale machine.

Nadat u een sleutel van uw resource hebt opgehaald, [maakt u omgevings variabelen](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de resource met de naam `QNAMAKER_RESOURCE_KEY` en `QNAMAKER_AUTHORING_ENDPOINT` . Gebruik de sleutel-en eindpunt waarden die zijn gevonden op de **Quick** start-pagina van de resource in het Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map.

```console
mkdir myapp && cd myapp
```

Voer de `npm init -y` opdracht uit om een knooppunt `package.json` bestand te maken.

```console
npm init -y
```

Voeg de `reqeuestretry` en `request` NPM-pakketten toe:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de QnA Maker REST-Api's voor Node.js:

* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennis database vervangen](#replace-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [Een Knowledge Base downloaden](#download-the-knowledge-base)
* [De status van een bewerking ophalen](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Maak een bestand `rest-apis.js` met de naam en voeg de volgende afhankelijkheden toe.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>Hulp programma functies toevoegen

Voeg de volgende functies van het hulp programma toe.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>Informatie over Azure-resource toevoegen

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

Stel de volgende omgevings waarden in:

* `QNAMAKER_RESOURCE_KEY` -De **sleutel** is een teken reeks van 32 en is beschikbaar in het Azure Portal op de QnA Maker-resource op de pagina **snel starten** . Dit is niet hetzelfde als de Voorspellings eindpunt sleutel.
* `QNAMAKER_AUTHORING_ENDPOINT` -Uw ontwerp-eind punt in de indeling van `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` bevat de naam van uw **resource**. Dit is niet dezelfde URL die wordt gebruikt om een query uit te zoeken op het Voorspellings eindpunt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en antwoord paren opgeslagen, gemaakt op basis van een JSON-object van:

* **Redactionele inhoud**.
* **Bestanden** : lokale bestanden waarvoor geen machtigingen zijn vereist.
* **Url's** -openbaar beschik bare url's.

Gebruik de [rest API om een Knowledge Base te maken](/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>Een kennis database vervangen

Gebruik de [rest API om een Knowledge Base te vervangen](/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base. Dit proces zorgt ervoor dat de Knowledge Base beschikbaar is via een HTTP-query Voorspellings eindpunt.

Gebruik de [rest API om een Knowledge Base te publiceren](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>De Knowledge base downloaden

Gebruik de [rest API om een Knowledge Base te downloaden](/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Wanneer u klaar bent met de Knowledge Base, verwijdert u deze.

Gebruik de [rest API om een Knowledge Base te verwijderen](/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>De status van een bewerking ophalen

Langlopende processen, zoals het aanmaak proces, retourneert een bewerkings-ID, die moet worden gecontroleerd met een afzonderlijke REST API aanroep. Deze functie neemt de hoofd tekst van het antwoord Create. De belang rijke sleutel is de `operationState` , waarmee wordt bepaald of u wilt door gaan met polling.

Gebruik de [rest API om bewerkingen op een Knowledge Base te bewaken](/rest/api/cognitiveservices/qnamaker/operations/getdetails).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>Hoofd methode toevoegen

Voeg de volgende `main` methode toe.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht `node rest-apis.js`.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Zelf studie: een KB maken en beantwoorden](./create-publish-knowledge-base.md)

* [Wat is de QnA Maker-API?](../Overview/overview.md)
* [Een Knowledge Base bewerken](../how-to/edit-knowledge-base.md)
* [Gebruiksanalyse ophalen](../how-to/get-analytics-knowledge-base.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).