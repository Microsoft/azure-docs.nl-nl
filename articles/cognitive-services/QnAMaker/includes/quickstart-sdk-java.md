---
title: 'Quickstart: QnA Maker-clientbibliotheek voor Java'
description: In deze quickstart ziet u hoe u aan de slag gaat met de QnA Maker-clientbibliotheek voor Java.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 6460c8c6d9ffb868d7ac6640c19608f2c10e4d4b
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105849"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

Gebruik de QnA Maker-clientbibliotheek voor Java voor het volgende:

* Een Knowledge Base maken
* Een Knowledge Base bijwerken
* Een Knowledge Base publiceren
* Een eindpuntsleutel voor de voorspellingsruntime ophalen
* Wachten op een langlopende taak
* Een Knowledge Base downloaden
* Antwoord krijgen in een Knowledge Base
* Een Knowledge Base verwijderen

[Broncode van de bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [Pakket](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Gebruik de QnA Maker-clientbibliotheek voor Java voor het volgende:

* Een Knowledge Base maken
* Een Knowledge Base bijwerken
* Een Knowledge Base publiceren
* Wachten op een langlopende taak
* Een Knowledge Base downloaden
* Antwoord krijgen in een Knowledge Base
* Een Knowledge Base verwijderen

[Broncode van de bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [Pakket](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.2) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/preview-sdk/quickstart.java)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Zodra u een Azure-abonnement hebt, maakt u een [QnA Maker-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in Azure Portal om uw ontwerpsleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de QnA Maker-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Zodra u een Azure-abonnement hebt, maakt u een [QnA Maker-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in Azure Portal om uw ontwerpsleutel en eindpunt op te halen.
    * OPMERKING: Vergeet niet het selectievakje **Beheerd** in te schakelen.
    * Nadat uw QnA Maker-resource is geïmplementeerd, selecteert u **Naar de resource gaan**. U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de QnA Maker-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

---

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-libraries"></a>De clientbibliotheken installeren

Na het installeren van Java kunt u de clientbibliotheken installeren met behulp van [Maven](https://maven.apache.org/) uit de [MVN-opslagplaats](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker).

### <a name="create-a-new-java-application"></a>Een nieuwe Java-toepassing maken

Maak een nieuw bestand met de naam `quickstart.java` en importeer de volgende bibliotheken.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=dependencies)]

---

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

> [!IMPORTANT]
> Ga naar Azure Portal en zoek de sleutel en het eindpunt voor de QnA Maker-resource die u bij de vereisten hebt gemaakt. U vindt deze op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**.

- De waarde van QNA_MAKER_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Ga naar de QnA Maker-resource in de Azure Portal en klik op **sleutels en eind punt** om de ontwerp-en QnA Maker-eind punt te zoeken.

 ![QnA Maker-eind punt voor ontwerpen](../media/keys-endpoint.png)
 
- De waarde van QNA_MAKER_RUNTIME_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.azurewebsites.net`.
   
- Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. [Azure Key Vault](../../../key-vault/general/overview.md) biedt bijvoorbeeld beveiligde sleutelopslag.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

> [!IMPORTANT]
> Ga naar Azure Portal en zoek de sleutel en het eindpunt voor de QnA Maker-resource die u bij de vereisten hebt gemaakt. U vindt deze op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**.

- De waarde van QNA_MAKER_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.  Ga naar de QnA Maker-resource in de Azure Portal en klik op **sleutels en eind punt** om de ontwerp-en QnA Maker-eind punt te zoeken.

 ![QnA Maker-eind punt voor ontwerpen](../media/keys-endpoint.png)
 
- Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. [Azure Key Vault](../../../key-vault/general/overview.md) biedt bijvoorbeeld beveiligde sleutelopslag.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=resourceKeys)]

---

## <a name="object-models"></a>Objectmodellen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

In QnA Maker worden twee verschillende objectmodellen gebruikt:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object voor het maken, beheren, publiceren en downloaden van de Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is het object waarmee u een query op de Knowledge Base gaat uitvoeren met behulp van de GenerateAnswer-API en nieuwe voorgestelde vragen verzendt met behulp van de Train-API (als onderdeel van [actief leren](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

In QnA Maker wordt het volgende objectmodel gebruikt:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object waarmee u de Knowledge Base kunt maken, beheren, publiceren, downloaden en er query's op kunt uitvoeren.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-objectmodel

De QnA Maker-creatieclient is een [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java)-object dat bij Azure wordt geverifieerd met behulp van MsRest::ServiceClientCredentials, waarin uw sleutel zich bevindt.

Zodra de client is gemaakt, gebruikt u de methoden van de eigenschap [Knowledgebases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) van de client om uw Knowledge Base te maken, te beheren en te publiceren.

Voor directe bewerkingen wordt via een methode doorgaans het resultaat geretourneerd, als dat er is. Voor langlopende bewerkingen bestaat het antwoord uit een [Operation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java)-object. Roep de methode [getDetails](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) aan met de waarde `operation.operationId` om de [status van de aanvraag](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java) te bepalen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-objectmodel

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

De runtime-client van QnA Maker is een [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java)-object.

Nadat u uw Knowledge Base hebt gepubliceerd met behulp van de creatieclient, gebruikt u de methode [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) van de runtime-client om een antwoord te krijgen van de Knowledge Base.

U maakt een runtime-client door [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) aan te roepen en een runtime-eindpuntsleutel door te geven. U haalt de runtime-eindpuntsleutel op door gebruik te maken van de creatieclient om [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) aan te roepen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Voor een beheerde resource van QnA Maker is geen QnAMakerRuntimeClient-object nodig. In plaats daarvan roept u [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) rechtstreeks aan op het [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java)-object.

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De client voor het ontwerpen van de Knowledge Base verifiëren

Instantieer een client met uw creatie-eindpunt en abonnementssleutel.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=authenticate)]

---

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en-antwoordparen voor het [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java)-object opgeslagen die afkomstig zijn uit drie bronnen:

* Gebruik voor **redactionele inhoud** het [QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java)-object.
    * Als u metagegevens en vervolgprompts wilt gebruiken, kiest u de redactionele context, aangezien deze gegevens op het niveau van een afzonderlijk vraag-en-antwoordpaar wordt toegevoegd.
* Gebruik voor **Bestanden** het [FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java)-object. De FileDTO bevat de bestandsnaam en de openbare URL om het bestand te bereiken.
* Gebruik voor **URL's** een lijst met tekenreeksen die openbaar beschikbare URL's vertegenwoordigen.

Roep de methode [create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173) aan en geef de eigenschap `operationId` van de geretourneerde bewerking door aan de methode [getDetails](#get-status-of-an-operation) om de status op te vragen.

Met de laatste regel van de volgende code wordt de Knowledge Base-id geretourneerd.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=createKb)]

---

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door [update](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) aan te roepen en de Knowledge Base-id en een [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java)-object door te geven. Dat object kan het volgende bevatten:
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [update](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

Geef de eigenschap `operationId` van de geretourneerde bewerking door aan de methode [getDetails](#get-status-of-an-operation) om de status op te vragen.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=updateKb)]

---

## <a name="download-a-knowledge-base"></a>Een Knowledge Base downloaden

Gebruik de [download](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196)-methode om de database als een lijst van [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java) te downloaden. Dit is _geen_ equivalent voor de exportbewerking vanuit de pagina **Instellingen** van de QnA Maker-portal omdat het resultaat van deze methode geen TSV-bestand is.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=downloadKb)]

---

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de [publish](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196)-methode. Hiervoor wordt het momenteel opgeslagen en getrainde model gebruikt waarnaar door de Knowledge Base-id wordt verwezen, en wordt dat model naar een eindpunt gepubliceerd.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=publishKb)]

---

## <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren uit de Knowledge Base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

Zodra een Knowledge Base is gepubliceerd, hebt u de runtime-eindpuntsleutel nodig om een query uit te voeren op de Knowledge Base. Dit is niet dezelfde abonnementssleutel als waarmee de creatieclient is gemaakt.

Gebruik de methode [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) om een [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java)-object op te halen.

Maak een runtime-client door [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) aan te roepen en een runtime-eindpuntsleutel van het EndpointKeysDTO-object door te geven.

Genereer een antwoord uit een gepubliceerde Knowledge Base met behulp van de methode [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36). Deze methode accepteert de Knowledge Base-id en het object [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java).

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Genereer een antwoord uit een gepubliceerde Knowledge Base met behulp van de methode [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308). Deze methode accepteert de Knowledge Base-id en het object [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java).

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=queryKb)]

---

Dit is een eenvoudig voorbeeld van het uitvoeren van een query op een Knowledge Base. Bekijk [Andere queryvoorbeelden](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer) voor meer informatie over geavanceerde queryscenario's.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de [delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81)-methode met een parameter van de Knowledge Base-id.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=deleteKb)]

---

## <a name="get-status-of-an-operation"></a>De status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, duren zo lang dat er niet wordt gewacht tot het proces is beëindigd maar een [bewerking](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) wordt geretourneerd. Gebruik de bewerkings-id uit de bewerking om een poll uit te voeren (met logica voor opnieuw proberen) om de status van de oorspronkelijke methode te bepalen.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=waitForOperation)]

---

## <a name="run-the-application"></a>De toepassing uitvoeren

Hier volgt de belangrijkste methode voor de toepassing.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=main)]

---

Voer de toepassing als volgt uit. Hierbij wordt ervan uitgegaan dat de naam van de klasse `Quickstart` is en dat uw afhankelijkheden zich in een submap met de naam `lib` onder de huidige map bevinden.

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/preview-sdk/quickstart.java).

---
