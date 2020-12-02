---
title: 'Quickstart: QnA Maker-clientbibliotheek voor Node.js'
description: In deze quickstart ziet u hoe u aan de slag gaat met de QnA Maker-clientbibliotheek voor Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9eb79223e91d31c76631d17263f6f33632824266
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351002"
---
Gebruik de QnA Maker-clientbibliotheek voor Node.js voor het volgende:

* Een Knowledge Base maken
* Een Knowledge Base bijwerken
* Een Knowledge Base publiceren
* Een eindpuntsleutel voor de voorspellingsruntime ophalen
* Wachten op een langlopende taak
* Een Knowledge Base downloaden
* Antwoord ophalen
* Een Knowledge Base verwijderen

[Referentiedocumentatie](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js-voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [Node.js](https://nodejs.org).
* Zodra u een Azure-abonnement hebt, maakt u een [QnA Maker-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in Azure Portal om uw auteurssleutel en resource op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.
    * U hebt de sleutel en de resourcenaam nodig van de resource die u maakt, om de toepassing te verbinden met de QnA Maker-API. Later in de quickstart plakt u uw sleutel en resourcenaam in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Voer de opdracht `npm init -y` uit om een knooppunttoepassing te maken met een `package.json`-bestand.

```console
npm init -y
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer de volgende NPM-pakketten:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam index.js en importeer de volgende bibliotheken:

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

Maak een variabele voor de Azure-sleutel en resourcenaam van uw resource. Zowel de URL-s voor schrijven en voorspellen gebruiken de resourcenaam als het subdomein.

> [!IMPORTANT]
> Ga naar Azure Portal en zoek de sleutel en het eindpunt voor de QnA Maker-resource die u bij de vereisten hebt gemaakt. U vindt deze op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**.
> U hebt de hele sleutel nodig om uw Knowledge Base te maken. U hebt alleen de resourcenaam van het eindpunt nodig. De indeling is `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. [Azure Key Vault](../../../key-vault/general/overview.md) biedt bijvoorbeeld beveiligde sleutelopslag.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

## <a name="object-models"></a>Objectmodellen

In QnA Maker worden twee verschillende objectmodellen gebruikt:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object voor het maken, beheren, publiceren en downloaden van de Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is het object waarmee u een query op de Knowledge Base gaat uitvoeren met behulp van de GenerateAnswer-API en nieuwe voorgestelde vragen verzendt met behulp van de Train-API (als onderdeel van [actief leren](../concepts/active-learning-suggestions.md)).


### <a name="qnamakerclient-object-model"></a>QnAMakerClient-objectmodel

De QnA Maker-ontwerpclient is een [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest)-object dat bij Azure wordt geverifieerd met behulp van uw referenties, die uw sleutel bevatten.

Zodra de client is gemaakt, gebruikt u de [Knowledge Base](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) om uw Knowledge Base te maken, beheren en publiceren.

Beheer uw Knowledge Base door een JSON-object te verzenden. Voor directe bewerkingen wordt via een methode doorgaans een JSON-object geretourneerd waarmee de status wordt aangegeven. Voor langlopende bewerkingen bestaat het antwoord uit de bewerkings-id. Roep de [client.operations.getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-)-methode aan met de bewerkings-id om de [status van de aanvraag](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest) te bepalen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-objectmodel

De QnA Maker-client is een QnAMakerRuntimeClient-object dat bij Azure wordt geverifieerd met behulp van Microsoft.Rest.ServiceClientCredentials, die uw voorspellingsruntimesleutel bevat die wordt geretourneerd van de ontwerpclientaanroep, [client.EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) nadat de Knowledge Base is gepubliceerd.


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de QnA Maker-clientbibliotheek voor .NET:

* [De ontwerpclient verifiëren](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisdatabase bijwerken](#update-a-knowledge-base)
* [Een Knowledge Base downloaden](#download-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [Een queryruntimesleutel ophalen](#get-query-runtime-key)
* [De status van een bewerking ophalen](#get-status-of-an-operation)
* [De queryruntimeclient verifiëren](#authenticate-the-runtime-for-generating-an-answer)
* [Een antwoord uit de Knowledge Base genereren](#generate-an-answer-from-the-knowledge-base)



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De client voor het ontwerpen van de Knowledge Base verifiëren

Instantieer een client met uw eindpunt en sleutel. Maak een ServiceClientCredentials-object met uw sleutel en gebruik het met uw eindpunt om een [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest)-object te maken.

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en-antwoordparen voor het [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest)-object opgeslagen die afkomstig zijn uit drie bronnen:

* Gebruik voor **redactionele inhoud** het [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest)-object.
    * Als u metagegevens en vervolgprompts wilt gebruiken, kiest u de redactionele context, aangezien deze gegevens op het niveau van een afzonderlijk vraag-en-antwoordpaar wordt toegevoegd.
* Gebruik voor **Bestanden** het [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest)-object. De FileDTO bevat de bestandsnaam en de openbare URL om het bestand te bereiken.
* Gebruik voor **URL's** een lijst met tekenreeksen die openbaar beschikbare URL's vertegenwoordigen.

De maakstap bevat ook eigenschappen voor de Knowledge Base:
* `defaultAnswerUsedForExtraction`: dit wordt geretourneerd als er geen antwoord wordt gevonden
* `enableHierarchicalExtraction`: hiermee worden automatisch promptrelaties tussen geëxtraheerde vraag-en-antwoordparen gemaakt
* `language`: wanneer u de eerste Knowledge Base van een resource maakt, stelt u de taal in die in de Azure Search-index moet worden gebruikt.

Roep de [create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--)-methode aan met de Knowledge Base-informatie. De Knowledge Base-informatie is in feite een JSON-object.

Wanneer een antwoord door de create-methode wordt geretourneerd, geeft u de geretourneerde bewerkings-id door aan de [wait_for_operation](#get-status-of-an-operation)-methode om de status te controleren. De wait_for_operation-methode retourneert een antwoord wanneer de bewerking is voltooid. Parseer de `resourceLocation`-headerwaarde van de geretourneerde bewerking om de nieuwe Knowledge Base-id op te halen.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod&highlight=39,46)]

Vergeet niet de [`wait_for_operation`](#get-status-of-an-operation)-functie op te nemen waarnaar in de bovenstaande code wordt verwezen om een Knowledge Base te maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door de Knowledge Base-id en een [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) met de DTO-objecten [add](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [update](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) en [delete](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) door te geven aan de [update](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-)-methode. De DTO's zijn in feite ook JSON-objecten. Gebruik de [wait_for_operation](#get-status-of-an-operation)-methode om te bepalen of de update is geslaagd.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod&highlight=74,81)]

Vergeet niet de [`wait_for_operation`](#get-status-of-an-operation)-functie op te nemen waarnaar in de bovenstaande code wordt verwezen om een Knowledge Base bij te werken.

## <a name="download-a-knowledge-base"></a>Een Knowledge Base downloaden

Gebruik de [download](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-)-methode om de database als een lijst van [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest) te downloaden. Dit is _geen_ equivalent voor de exportbewerking vanuit de pagina **Instellingen** van de QnA Maker-portal omdat het resultaat van deze methode geen TSV-bestand is.

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB&highlight=2)]


## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de [publish](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-)-methode. Hiervoor wordt het momenteel opgeslagen en getrainde model gebruikt waarnaar door de Knowledge Base-id wordt verwezen, en wordt dat model naar een eindpunt gepubliceerd. Controleer of de HTTP-antwoordcode om te controleren of de publicatie is geslaagd.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB&highlight=3)]


## <a name="get-query-runtime-key"></a>Een queryruntimesleutel ophalen

Zodra een Knowledge Base is gepubliceerd, hebt u de queryruntimesleutel nodig om een query uit te voeren op de runtime. Dit is niet dezelfde sleutel als de sleutel die u hebt gebruikt om het oorspronkelijke clientobject te maken.

Gebruik de [EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest)-methode om de [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest)-klasse op te halen.

Gebruik een van de sleuteleigenschappen die in het object zijn geretourneerd om een query uit te voeren op de Knowledge Base.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey&highlight=4)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime voor het genereren van een antwoord verifiëren

Maak een QnAMakerRuntimeClient om een query uit te voeren op de Knowledge Base om een antwoord te genereren of een training uit te voeren via actief leren.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Gebruik de QnAMakerRuntimeClient om een antwoord uit de Knowledge Base op te halen of om nieuwe vraagvoorstellen naar de Knowledge Base te verzenden voor [actief leren](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Genereer een antwoord uit de Knowledge Base

Genereer een antwoord uit een gepubliceerde Knowledge Base met behulp van de RuntimeClient.runtime.generateAnswer-method. Deze methode accepteert de Knowledge Base-id en de QueryDTO. Krijg toegang tot aanvullende eigenschappen van de QueryDTO, zoals een Top en Context die u in uw chatbot kunt gebruiken.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer&highlight=3)]

Dit is een eenvoudig voorbeeld waarbij een query wordt uitgevoerd op de Knowledge Base. Bekijk [Andere queryvoorbeelden](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer) voor meer informatie over geavanceerde queryscenario's.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de [delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-)-methode met een parameter van de Knowledge Base-id.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>De status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, duren zo lang dat er niet wordt gewacht tot het proces is beëindigd maar een [bewerking](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) wordt geretourneerd. Gebruik de [bewerkings-id](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) uit de bewerking om een poll uit te voeren (met logica voor opnieuw proberen) om de status van de oorspronkelijke methode te bepalen.

De _delayTimer_-aanroep in het volgende codeblok wordt gebruikt om de logica voor opnieuw proberen te simuleren. Vervang deze door uw eigen logica voor opnieuw proberen.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation&highlight=8)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht `node index.js`.

```console
node index.js
```

De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).