---
title: 'Quickstart: QnA Maker-clientbibliotheek voor Python'
description: In deze quickstart ziet u hoe u aan de slag gaat met de QnA Maker-clientbibliotheek voor Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 39eeb00ba8b59219054c7743f19f6f378d941d5d
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104499"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

Gebruik de QnA Maker-clientbibliotheek voor Python voor het volgende:

* Een Knowledge Base maken
* Een Knowledge Base bijwerken
* Een Knowledge Base publiceren
* Een eindpuntsleutel voor de voorspellingsruntime ophalen
* Wachten op een langlopende taak
* Een Knowledge Base downloaden
* Antwoord krijgen in een Knowledge Base
* Een Knowledge Base verwijderen

[Referentiedocumentatie](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/0.2.0/) | [Python-voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Gebruik de QnA Maker-clientbibliotheek voor Python voor het volgende:

* Een Knowledge Base maken
* Een Knowledge Base bijwerken
* Een Knowledge Base publiceren
* Wachten op een langlopende taak
* Een Knowledge Base downloaden
* Antwoord krijgen in een Knowledge Base
* Een Knowledge Base verwijderen

[Referentiedocumentatie](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python-voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Zodra u een Azure-abonnement hebt, maakt u een [QnA Maker-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in Azure Portal om uw ontwerpsleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de QnA Maker-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Zodra u een Azure-abonnement hebt, maakt u een [QnA Maker-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in Azure Portal om uw ontwerpsleutel en eindpunt op te halen.
    * OPMERKING: Vergeet niet het selectievakje **Beheerd** in te schakelen.
    * Nadat uw QnA Maker-resource is geïmplementeerd, selecteert u **Naar de resource gaan**. U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de QnA Maker-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

---

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

Na de installatie van Python kunt u de clientbibliotheek installeren met:

```console
pip install azure-cognitiveservices-knowledge-qnamaker==0.2.0
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Na de installatie van Python kunt u de clientbibliotheek installeren met:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

---

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuw Python-bestand met de naam `quickstart-file.py` en importeer de volgende bibliotheken.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

---

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

> [!IMPORTANT]
> Ga naar Azure Portal en zoek de sleutel en het eindpunt voor de QnA Maker-resource die u bij de vereisten hebt gemaakt. U vindt deze op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**.

- De waarde van QNA_MAKER_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Ga naar de QnA Maker-resource in de Azure Portal en klik op **sleutels en eind punt** om de ontwerp-en QnA Maker-eind punt te zoeken.

 ![QnA Maker-eind punt voor ontwerpen](../media/keys-endpoint.png)
 
- De waarde van QNA_MAKER_RUNTIME_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.azurewebsites.net`.
   
- Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. [Azure Key Vault](../../../key-vault/general/overview.md) biedt bijvoorbeeld beveiligde sleutelopslag.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

> [!IMPORTANT]
> Ga naar Azure Portal en zoek de sleutel en het eindpunt voor de QnA Maker-resource die u bij de vereisten hebt gemaakt. U vindt deze op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**.

- De waarde van QNA_MAKER_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Ga naar de QnA Maker-resource in de Azure Portal en klik op **sleutels en eind punt** om de ontwerp-en QnA Maker-eind punt te zoeken.

 ![QnA Maker-eind punt voor ontwerpen](../media/keys-endpoint.png)
 
- Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. [Azure Key Vault](../../../key-vault/general/overview.md) biedt bijvoorbeeld beveiligde sleutelopslag.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

---

## <a name="object-models"></a>Objectmodellen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

In [QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) worden twee verschillende objectmodellen gebruikt:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object voor het maken, beheren, publiceren en downloaden van de Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is het object waarmee u een query op de Knowledge Base gaat uitvoeren met behulp van de GenerateAnswer-API en nieuwe voorgestelde vragen verzendt met behulp van de Train-API (als onderdeel van [actief leren](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

In [QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) wordt het volgende objectmodel gebruikt:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object waarmee u de Knowledge Base kunt maken, beheren, publiceren, downloaden en er query's op kunt uitvoeren.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-objectmodel

De QnA Maker-ontwerpclient is een [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient)-object dat bij Azure wordt geverifieerd met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Zodra de client is gemaakt, gebruikt u de eigenschap [Knowledge Base](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations) om uw Knowledge Base te maken, te beheren en te publiceren.

Beheer uw Knowledge Base door een JSON-object te verzenden. Voor directe bewerkingen wordt via een methode doorgaans een JSON-object geretourneerd waarmee de status wordt aangegeven. Voor langlopende bewerkingen bestaat het antwoord uit de bewerkings-id. Roep de methode [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#get-details-kb-id--custom-headers-none--raw-false----operation-config-) aan met de bewerkings-id om de [status van de aanvraag](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype) te bepalen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-objectmodel

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

De QnA Maker-voorspellingsclient is een [QnAMakerRuntimeClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpoint_keys_operations.endpointkeysoperations#get-keys-custom-headers-none--raw-false----operation-config-)-object dat bij Azure wordt geverifieerd met behulp van Microsoft.Rest.ServiceClientCredentials, die uw voorspellingsruntimesleutel bevat die wordt geretourneerd van de ontwerpclientaanroep, `QnAMakerRuntimeClient`, nadat de Knowledge Base is gepubliceerd.

Gebruik de methode `generate_answer` om een antwoord te krijgen van de runtime van de query.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Voor een beheerde resource van QnA Maker is geen QnAMakerRuntimeClient-object nodig. In plaats daarvan roept u [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) rechtstreeks aan op het [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient)-object.

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De client voor het ontwerpen van de Knowledge Base verifiëren

Instantieer een client met uw eindpunt en sleutel. Maak een CognitiveServicesCredentials-object met uw sleutel en gebruik het met uw eindpunt om een [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient)-object te maken.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

Gebruik het clientobject om een [knowledge base operations](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations)-object op te halen.

In een Knowledge Base worden vraag-en-antwoordparen voor het [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto)-object opgeslagen die afkomstig zijn uit drie bronnen:

* Gebruik voor **redactionele inhoud** het [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto)-object.
    * Als u metagegevens en vervolgprompts wilt gebruiken, kiest u de redactionele context, aangezien deze gegevens op het niveau van een afzonderlijk vraag-en-antwoordpaar wordt toegevoegd.
* Gebruik voor **Bestanden** het [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto)-object. De FileDTO bevat de bestandsnaam en de openbare URL om het bestand te bereiken.
* Gebruik voor **URL's** een lijst met tekenreeksen die openbaar beschikbare URL's vertegenwoordigen.

Roep de methode [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) aan en geef de geretourneerde bewerkings-id door aan de methode [Operations.getDetails](#get-status-of-an-operation) om de status op te vragen.

Met de laatste regel van de volgende code wordt de Knowledge Base-id uit het antwoord van MonitorOperation geretourneerd.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

---

Vergeet niet de [`_monitor_operation`](#get-status-of-an-operation)-functie op te nemen waarnaar in de bovenstaande code wordt verwezen om een Knowledge Base te maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door de Knowledge Base-id en een [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) met de DTO-objecten [add](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate) en [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) door te geven aan de [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations)-methode. Gebruik de methode [Operation.getDetail](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

---

Vergeet niet de [`_monitor_operation`](#get-status-of-an-operation)-functie op te nemen waarnaar in de bovenstaande code wordt verwezen om een Knowledge Base bij te werken.

## <a name="download-a-knowledge-base"></a>Een Knowledge Base downloaden

Gebruik de [download](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations)-methode om de database als een lijst van [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto) te downloaden. Dit is _geen_ equivalent voor de exportbewerking vanuit de pagina **Instellingen** van de QnA Maker-portal omdat het resultaat van deze methode geen TSV-bestand is.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de [publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#publish-kb-id--custom-headers-none--raw-false----operation-config-)-methode. Hiervoor wordt het momenteel opgeslagen en getrainde model gebruikt waarnaar door de Knowledge Base-id wordt verwezen, en wordt dat model naar een eindpunt gepubliceerd.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Een query uitvoeren op een Knowledge Base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Een queryruntimesleutel ophalen

Zodra een Knowledge Base is gepubliceerd, hebt u de queryruntimesleutel nodig om een query uit te voeren op de runtime. Dit is niet dezelfde sleutel als de sleutel die u hebt gebruikt om het oorspronkelijke clientobject te maken.

Gebruik de methode [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations#get-keys-custom-headers-none--raw-false----operation-config-) om de [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto)-klasse op te halen.

Gebruik een van de sleuteleigenschappen die in het object zijn geretourneerd om een query uit te voeren op de Knowledge Base.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime voor het genereren van een antwoord verifiëren

Maak een [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient) om een query uit te voeren op de Knowledge Base om een antwoord te genereren of een training uit te voeren via actief leren.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Gebruik de QnAMakerRuntimeClient om een antwoord uit de Knowledge Base op te halen of om nieuwe vraagvoorstellen naar de Knowledge Base te verzenden voor [actief leren](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Genereer een antwoord uit de Knowledge Base

Genereer een antwoord uit een gepubliceerde Knowledge Base met behulp van de methode QnAMakerRuntimeClient.runtime.generateAnswer. Deze methode accepteert de Knowledge Base-id en de [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Krijg toegang tot aanvullende eigenschappen van de QueryDTO, zoals een Top en Context die u in uw chatbot kunt gebruiken.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren uit de Knowledge Base

Genereer een antwoord uit een gepubliceerde Knowledge Base met behulp van de methode [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-). Deze methode accepteert de Knowledge Base-id en de [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Krijg toegang tot aanvullende eigenschappen van de QueryDTO, zoals een Top en Context die u in uw chatbot kunt gebruiken.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

---

Dit is een eenvoudig voorbeeld waarin een query wordt uitgevoerd op de Knowledge Base. Bekijk [Andere queryvoorbeelden](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer) voor meer informatie over geavanceerde queryscenario's.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#delete-kb-id--custom-headers-none--raw-false----operation-config-)-methode met een parameter van de Knowledge Base-id.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>De status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, duren zo lang dat er niet wordt gewacht tot het proces is beëindigd maar een [bewerking](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)) wordt geretourneerd. Gebruik de bewerkings-id uit de bewerking om een poll uit te voeren (met logica voor opnieuw proberen) om de status van de oorspronkelijke methode te bepalen.

De _setTimeout_-aanroep in het volgende codeblok wordt gebruikt om asynchrone code te simuleren. Vervang deze door logica voor opnieuw proberen.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

---

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de python-opdracht in uw quickstart-bestand.

```console
python quickstart-file.py
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py).

---
