---
title: 'Snelstart: Knowledge base maken - REST, Java - QnA Maker'
description: In deze op Java REST gebaseerde Snelstartgids wordt u begeleid bij het maken van een voor beeld QnA Maker Knowledge Base, die wordt weer gegeven in uw Azure-dash board van uw Cognitive Services API-account.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 50433ee1e5a575ab671d562bfc3fe549b26fe00c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352300"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Snelstart: Een knowledge base maken in QnA Maker met behulp van Java

In deze snelstart wordt beschreven hoe u programmatisch een voorbeeld van een QnA Maker-knowledge base kunt maken. Met QnA Maker worden automatisch vragen en antwoorden opgehaald uit semi-gestructureerde inhoud, zoals veelgestelde vragen, vanuit [gegevensbronnen](../index.yml). Het model voor de knowledge base wordt gedefinieerd in de JSON die in de hoofdtekst van de API-aanvraag wordt verzonden.

In deze snelstart worden QnA Maker-API's aangeroepen:
* [KB maken](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Bewerkingsdetails ophalen](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referentie documentatie](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) -voor beeld

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Go 1.10.1](https://golang.org/dl/)
* U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u de sleutel en het eind punt (inclusief de resource naam) wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal.

De [voorbeeld code](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) is beschikbaar op de GitHub-opslag plaats voor QnA Maker met Java.

## <a name="create-a-knowledge-base-file"></a>Een kennisdatabasebestand maken

Maak een bestand met de naam `CreateKB.java`

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

Voeg bovenaan `CreateKB.java` de volgende regels toe om de nodige afhankelijkheden aan het project toe te voegen:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>De vereiste constanten toevoegen
Voeg na de bovenstaande vereiste afhankelijkheden de vereiste constanten toe aan de klasse `CreateKB` om toegang te krijgen tot QnA Maker.

U moet een [QnA Maker-service](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u de sleutel en de resource naam wilt ophalen, selecteert u **Quick** start in het Azure portal voor uw QnA Maker resource.

Stel de volgende waarden in:

* `<your-qna-maker-subscription-key>` -De **sleutel** is een teken reeks van 32 en is beschikbaar in de Azure Portal, op de QnA Maker-resource, op de pagina Quick Start. Deze sleutel is niet hetzelfde als de Voorspellings eindpunt sleutel.
* `<your-resource-name>` -De **resource naam** wordt gebruikt voor het maken van de URL voor het ontwerpen van eind punten voor het ontwerpen, in de indeling van `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Deze resource naam is niet hetzelfde als die waarmee een query kan worden uitgevoerd op het Voorspellings eindpunt.

U hoeft de laatste accolade niet toe te voegen om de klasse te beëindigen; deze staat in het laatste codefragment aan het einde van deze snelstart.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>De definitieklassen van het KB-model toevoegen
Voeg na de constanten de volgende klassen en functies toe in de klasse `CreateKB` om het modeldefinitieobject in JSON te serialiseren.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>Ondersteunende functies toevoegen

Voeg daarna de volgende ondersteunende functies toe in de klasse `CreateKB`.

1. Voeg de volgende functie toe om JSON in een leesbare indeling weer te geven:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. Voeg de volgende klasse toe om het HTTP-antwoord te beheren:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. Voeg de volgende methode toe om een POST-aanvraag te doen bij de QnA Maker-API's. De `Ocp-Apim-Subscription-Key` is de sleutel van de QnA Maker-service die wordt gebruikt voor verificatie.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. Voeg de volgende methode toe om een GET-aanvraag te doen bij de QnA Maker-API's.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>Een methode toevoegen om de KB te maken
Voeg de volgende methode toe om de KB te maken door de Post-methode aan te roepen.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

Deze API-aanroep retourneert een JSON-antwoord dat de bewerkings-id bevat. Gebruik de bewerkings-id om te bepalen of de KB is gemaakt.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Een methode toevoegen om de status op te halen
Voeg de volgende methode om de maakstatus te controleren.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

Herhaal de aanroep totdat deze lukt of mislukt:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Een hoofdmethode toevoegen
De hoofdmethode maakt de KB en vraagt vervolgens de status na. De bewerkings-ID wordt geretourneerd in de veld **locatie** van de post-antwoord header en vervolgens gebruikt als onderdeel van de route in de GET-aanvraag. De `while` lus wordt opnieuw geprobeerd om de status te herhalen als deze nog niet is voltooid.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>Het programma compileren en uitvoeren

1. Zorg ervoor dat de gson-bibliotheek zich in de directory `./libs` bevindt. Compileer het bestand op de opdracht regel `CreateKB.java` :

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Voer de volgende opdracht in op de opdracht regel om het programma uit te voeren. Hiermee wordt de aanvraag verzonden naar de QnA Maker-API om de KB te maken en vervolgens worden elke 30 seconden de resultaten gecontroleerd. Elk antwoord wordt weergegeven in het consolevenster.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Zodra de knowledge base is gemaakt, kunt u deze weergeven in de QnA Maker-portal op de pagina [Mijn knowledge bases](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)