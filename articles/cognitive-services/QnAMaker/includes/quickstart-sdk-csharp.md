---
title: 'Quickstart: QnA Maker-clientbibliotheek voor .NET'
description: In deze quickstart ziet u hoe u aan de slag gaat met de QnA Maker-clientbibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: ad26d02079b09676fc32465b9f56d76aea1a26f7
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256502"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

Gebruik de QnA Maker-clientbibliotheek voor .NET voor het volgende:

 * Een Knowledge Base maken
 * Een Knowledge Base bijwerken
 * Een Knowledge Base publiceren
 * Een eindpuntsleutel voor de voorspellingsruntime ophalen
 * Wachten op een langlopende taak
 * Een Knowledge Base downloaden
 * Antwoord krijgen in een Knowledge Base
 * Een Knowledge Base verwijderen

[Referentiedocumentatie](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1) | [C#-voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Gebruik de QnA Maker-clientbibliotheek voor .NET voor het volgende:

 * Een Knowledge Base maken
 * Een Knowledge Base bijwerken
 * Een Knowledge Base publiceren
 * Wachten op een langlopende taak
 * Een Knowledge Base downloaden
 * Antwoord krijgen in een Knowledge Base
 * Een Knowledge Base verwijderen

[Referentiedocumentatie](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1) | [C#-voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) of de huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Zodra u een Azure-abonnement hebt, maakt u een [QnA Maker-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in Azure Portal om uw auteurssleutel en resourcenaam op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.
    * U hebt de sleutel en de resourcenaam nodig van de resource die u maakt, om de toepassing te verbinden met de QnA Maker-API. Later in de quickstart plakt u uw sleutel en resourcenaam in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) of de huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Zodra u een Azure-abonnement hebt, maakt u een [QnA Maker-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in Azure Portal om uw ontwerpsleutel en eindpunt op te halen.
    * OPMERKING: Vergeet niet het selectievakje **Beheerd** in te schakelen.
    * Nadat uw QnA Maker-resource is geïmplementeerd, selecteert u **Naar de resource gaan**. U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de QnA Maker-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

---

## <a name="setting-up"></a>Instellen

### <a name="visual-studio-ide"></a>Visual Studio IDE

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

Maak met behulp van Visual Studio een .NET Core-toepassing en installeer de clientbibliotheek door met de rechtermuisknop op de oplossing te klikken in **Solution Explorer** en **NuGet-pakketten beheren** te selecteren. Selecteer in Package Manager dat wordt geopend de optie **Bladeren** en zoek naar `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`. Selecteer versie `2.0.1` en vervolgens **Installeren**.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Maak met behulp van Visual Studio een .NET Core-toepassing en installeer de clientbibliotheek door met de rechtermuisknop op de oplossing te klikken in **Solution Explorer** en **NuGet-pakketten beheren** te selecteren. Selecteer in de package manager die wordt geopend de optie **Bladeren**, schakel **Prerelease opnemen** in en zoek naar `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`. Selecteer versie `3.0.0-preview.1` en vervolgens **Installeren**.

---

### <a name="cli"></a>CLI

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `qna-maker-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Installeer in de toepassingsmap de QnA Maker-clientbibliotheek voor .NET met de volgende opdracht:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), waar de codevoorbeelden uit deze quickstart zich bevinden.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs), waar de codevoorbeelden uit deze quickstart zich bevinden.

---

### <a name="using-directives"></a>Instructies gebruiken

Open vanuit de projectmap het bestand *program.cs* en voeg de volgende `using`-instructies toe:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>Abonnementssleutel en resource-eindpunten

Voeg in de methode `Main` van de toepassing variabelen en code toe, zoals wordt weergegeven in de volgende sectie, zodat u de algemene taken in deze quickstart kunt gebruiken.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

> [!IMPORTANT]
> Ga naar Azure Portal en zoek de sleutel en het eindpunt voor de QnA Maker-resource die u bij de vereisten hebt gemaakt. U vindt deze op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**.

- Maak omgevingsvariabelen met de namen QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT, en QNA_MAKER_RUNTIME_ENDPOINT om deze waarden op te slaan.
- De waarde van QNA_MAKER_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- De waarde van QNA_MAKER_RUNTIME_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.azurewebsites.net`.
- Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. [Azure Key Vault](../../../key-vault/general/overview.md) biedt bijvoorbeeld beveiligde sleutelopslag.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

> [!IMPORTANT]
> Ga naar Azure Portal en zoek de sleutel en het eindpunt voor de QnA Maker-resource die u bij de vereisten hebt gemaakt. U vindt deze op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**.

- Maak omgevingsvariabelen met de namen QNA_MAKER_SUBSCRIPTION_KEY en QNA_MAKER_ENDPOINT om deze waarden op te slaan.
- De waarde van QNA_MAKER_ENDPOINT heeft de indeling `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. [Azure Key Vault](../../../key-vault/general/overview.md) biedt bijvoorbeeld beveiligde sleutelopslag.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---

## <a name="object-models"></a>Objectmodellen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

In [QnA Maker](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) worden twee verschillende objectmodellen gebruikt:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object voor het maken, beheren, publiceren en downloaden van de Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is het object waarmee u een query op de Knowledge Base gaat uitvoeren met behulp van de GenerateAnswer-API en nieuwe voorgestelde vragen verzendt met behulp van de Train-API (als onderdeel van [actief leren](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

In [QnA Maker](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) wordt het volgende objectmodel gebruikt:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object waarmee u de Knowledge Base kunt maken, beheren, publiceren, downloaden en er query's op kunt uitvoeren.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-objectmodel

De QnA Maker-ontwerpclient is een [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet)-object dat bij Azure wordt geverifieerd met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Zodra de client is gemaakt, gebruikt u de eigenschap [Knowledge Base](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) om uw Knowledge Base te maken, te beheren en te publiceren.

Beheer uw Knowledge Base door een JSON-object te verzenden. Voor directe bewerkingen wordt via een methode doorgaans een JSON-object geretourneerd waarmee de status wordt aangegeven. Voor langlopende bewerkingen bestaat het antwoord uit de bewerkings-id. Roep de [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet)-methode aan met de bewerkings-id om de [status van de aanvraag](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet) te bepalen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-objectmodel

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

De QnA Maker-voorspellingsclient voor voorspellingen is een [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet)-object dat bij Azure wordt geverifieerd met behulp van Microsoft.Rest.ServiceClientCredentials, die uw voorspellingsruntimesleutel bevat die wordt geretourneerd van de ontwerpclientaanroep, `client.EndpointKeys.GetKeys` nadat de Knowledge Base is gepubliceerd.

Gebruik de methode [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) om een antwoord te krijgen van de runtime van de query.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

Voor een beheerde resource van QnA Maker is geen **QnAMakerRuntimeClient**-object nodig. In plaats daarvan roept u de methode [QnAMakerClient.Knowledge Base](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet-preview).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync?view=azure-dotnet-preview) aan.

---

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de QnA Maker-clientbibliotheek voor .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

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

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

* [De ontwerpclient verifiëren](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisdatabase bijwerken](#update-a-knowledge-base)
* [Een Knowledge Base downloaden](#download-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [De status van een bewerking ophalen](#get-status-of-an-operation)
* [Een antwoord uit de Knowledge Base genereren](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De client voor het ontwerpen van de Knowledge Base verifiëren

Instantieer een clientobject met uw sleutel en gebruik dit samen met uw resource om het eindpunt te maken waarmee u een [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) kunt maken met uw eindpunt en sleutel. Maak een [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet)-object.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en-antwoordparen voor het [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet)-object opgeslagen die afkomstig zijn uit drie bronnen:

* Gebruik voor **redactionele inhoud** het [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet)-object.
    * Als u metagegevens en vervolgprompts wilt gebruiken, kiest u de redactionele context, aangezien deze gegevens op het niveau van een afzonderlijk vraag-en-antwoordpaar wordt toegevoegd.
* Gebruik voor **Bestanden** het [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet)-object. De FileDTO bevat de bestandsnaam en de openbare URL om het bestand te bereiken.
* Gebruik voor **URL's** een lijst met tekenreeksen die openbaar beschikbare URL's vertegenwoordigen.

De maakstap bevat ook eigenschappen voor de Knowledge Base:
* `defaultAnswerUsedForExtraction`: dit wordt geretourneerd als er geen antwoord wordt gevonden
* `enableHierarchicalExtraction`: hiermee worden automatisch promptrelaties tussen geëxtraheerde vraag-en-antwoordparen gemaakt
* `language`: wanneer u de eerste Knowledge Base van een resource maakt, stelt u de taal in die in de Azure Search-index moet worden gebruikt.

Roep de methode [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) aan en geef de geretourneerde bewerkings-id door aan de methode [MonitorOperation](#get-status-of-an-operation) om de status op te vragen.

Met de laatste regel van de volgende code wordt de Knowledge Base-id uit het antwoord van MonitorOperation geretourneerd.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

Vergeet niet de [`MonitorOperation`](#get-status-of-an-operation)-functie op te nemen waarnaar in de bovenstaande code wordt verwezen om een Knowledge Base te maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door de Knowledge Base-id en een [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) met de DTO-objecten [add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) en [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) door te geven aan de [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet)-methode. Gebruik de [MonitorOperation](#get-status-of-an-operation)-methode om te bepalen of de update is geslaagd.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

Vergeet niet de [`MonitorOperation`](#get-status-of-an-operation)-functie op te nemen waarnaar in de bovenstaande code wordt verwezen om een Knowledge Base bij te werken.

## <a name="download-a-knowledge-base"></a>Een Knowledge Base downloaden

Gebruik de [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet)-methode om de database als een lijst van [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet) te downloaden. Dit is _geen_ equivalent voor de exportbewerking vanuit de pagina **Instellingen** van de QnA Maker-portal omdat het resultaat van deze methode geen bestand is.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet)-methode. Hiervoor wordt het momenteel opgeslagen en getrainde model gebruikt waarnaar door de Knowledge Base-id wordt verwezen en wordt dat model naar uw eindpunt gepubliceerd. Dit is een noodzakelijke stap om een query op uw Knowledge Base te kunnen uitvoeren.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

## <a name="get-query-runtime-key"></a>Een queryruntimesleutel ophalen

Zodra een Knowledge Base is gepubliceerd, hebt u de queryruntimesleutel nodig om een query uit te voeren op de runtime. Dit is niet dezelfde sleutel als de sleutel die u hebt gebruikt om het oorspronkelijke clientobject te maken.

Gebruik de [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)-methode om de [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet)-klasse op te halen.

Gebruik een van de sleuteleigenschappen die in het object zijn geretourneerd om een query uit te voeren op de Knowledge Base.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

Er is een runtimesleutel nodig om een query op uw Knowledge Base te kunnen uitvoeren.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime voor het genereren van een antwoord verifiëren

Maak een [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) om een query uit te voeren op de Knowledge Base om een antwoord te genereren of een training uit te voeren via actief leren.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Gebruik QnAMakerRuntimeClient om het volgende te doen:
* een antwoord ophalen uit de Knowledge Base
* nieuwe suggesties voor vragen sturen naar de Knowledge Base voor [actief leren](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren uit de Knowledge Base

Genereer een antwoord uit een gepubliceerde Knowledge Base met behulp van de [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet)-methode. Deze methode accepteert de Knowledge Base-id en de [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Krijg toegang tot aanvullende eigenschappen van de QueryDTO, zoals een [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) en [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) die u in uw chatbot kunt gebruiken.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren uit de Knowledge Base

Genereer een antwoord uit een gepubliceerde Knowledge Base met behulp van de [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet-preview).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync?view=azure-dotnet-preview)-methode. Deze methode accepteert de Knowledge Base-id en de [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet-preview). Krijg toegang tot aanvullende eigenschappen van de QueryDTO, zoals een [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) en [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) die u in uw chatbot kunt gebruiken.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

Dit is een eenvoudig voorbeeld waarbij een query wordt uitgevoerd op de Knowledge Base. Bekijk [Andere queryvoorbeelden](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer) voor meer informatie over geavanceerde queryscenario's.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet)-methode met een parameter van de Knowledge Base-id.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>De status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, duren zo lang dat er niet wordt gewacht tot het proces is beëindigd maar een [bewerking](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) wordt geretourneerd. Gebruik de [bewerkings-id](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) uit de bewerking om een poll uit te voeren (met logica voor opnieuw proberen) om de status van de oorspronkelijke methode te bepalen.

De methoden _loop_ en _Task.Delay_ in het volgende codeblok worden gebruikt om de logica voor nieuwe pogingen te simuleren. Deze moeten worden vervangen door uw eigen logica voor nieuwe pogingen.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/version-1)

De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/version-2)

De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).

---
