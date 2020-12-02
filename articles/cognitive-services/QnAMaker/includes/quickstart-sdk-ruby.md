---
title: 'Quickstart: QnA Maker-clientbibliotheek voor Ruby'
description: In deze quickstart ziet u hoe u aan de slag gaat met de QnA Maker-clientbibliotheek voor Ruby.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: edb881b10db1d617e1b1f6b1167c014d6d51020e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351003"
---
Gebruik de QnA Maker-clientbibliotheek voor Ruby voor het volgende:

* Een Knowledge Base maken
* Een Knowledge Base bijwerken
* Een Knowledge Base publiceren
* Een eindpuntsleutel voor de voorspellingsruntime ophalen
* Wachten op een langlopende taak
* Een Knowledge Base downloaden
* Antwoord ophalen
* Een Knowledge Base verwijderen

[Broncode van de bibliotheek (creatie)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated) | [Broncode van bibliotheek (runtime)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated) | [Pakket (creatie)](https://rubygems.org/gems/azure_cognitiveservices_qnamaker) | [Pakket (runtime)](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime) | [Ruby-samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Ruby 2.x](https://www.ruby-lang.org/)
* Zodra u een Azure-abonnement hebt, maakt u een [QnA Maker-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in Azure Portal om uw ontwerpsleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de QnA Maker-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-libraries"></a>De clientbibliotheken installeren

Na de installatie van Ruby kunt u de clientbibliotheken installeren met:

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>Een nieuwe Ruby-toepassing maken

Maak een nieuw bestand met de naam `quickstart.rb` en importeer de volgende bibliotheken.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

> [!IMPORTANT]
> Ga naar Azure Portal en zoek de sleutel en het eindpunt voor de QnA Maker-resource die u bij de vereisten hebt gemaakt. U vindt deze op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**.
> U hebt de hele sleutel nodig om uw Knowledge Base te maken. U hebt alleen de resourcenaam van het eindpunt nodig. De indeling is `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. [Azure Key Vault](../../../key-vault/general/overview.md) biedt bijvoorbeeld beveiligde sleutelopslag.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>Objectmodellen

In QnA Maker worden twee verschillende objectmodellen gebruikt:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object voor het maken, beheren, publiceren en downloaden van de Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is het object waarmee u een query op de Knowledge Base gaat uitvoeren met behulp van de GenerateAnswer-API en nieuwe voorgestelde vragen verzendt met behulp van de Train-API (als onderdeel van [actief leren](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-objectmodel

De QnA Maker-ontwerpclient is een [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb)-object dat bij Azure wordt geverifieerd met behulp van MsRest::ServiceClientCredentials, waarin uw sleutel zich bevindt.

Zodra de client is gemaakt, gebruikt u de methoden van de eigenschap [knowledgebase](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb) van de client om uw Knowledge Base te maken, te beheren en te publiceren.

Voor directe bewerkingen wordt via een methode doorgaans het resultaat geretourneerd, als dat er is. Voor langlopende bewerkingen bestaat het antwoord uit een [operation](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb)-object. Roep de methode [operations.get_details](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33) aan met de waarde `operation.operation_id` om de [status van de aanvraag](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb) te bepalen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-objectmodel

De runtimeclient van QnA Maker is een [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb)-object.

Nadat u uw Knowledge Base hebt gepubliceerd met behulp van de creatieclient, gebruikt u de methode [runtime.generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) van de runtimeclient om een antwoord te krijgen van de Knowledge Base.

Wanneer u `generate_answer` aanroept, geeft u een hash door voor de optionele parameter `custom_headers`. Deze hash moet een sleutel `Authorization` en een waarde `EndpointKey YOUR_ENDPOINT_KEY` bevatten. Gebruik voor de waarde van YOUR_ENDPOINT_KEY de ontwerpclient om [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) aan te roepen.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De client voor het ontwerpen van de Knowledge Base verifiëren

Instantieer een client met uw creatie-eindpunt en abonnementssleutel.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en-antwoordparen voor het [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb)-object opgeslagen die afkomstig zijn uit drie bronnen:

* Gebruik voor **redactionele inhoud** het [QnADTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb)-object.
    * Als u metagegevens en vervolgprompts wilt gebruiken, kiest u de redactionele context, aangezien deze gegevens op het niveau van een afzonderlijk vraag-en-antwoordpaar wordt toegevoegd.
* Gebruik voor **Bestanden** het [FileDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb)-object. De FileDTO bevat de bestandsnaam en de openbare URL om het bestand te bereiken.
* Gebruik voor **URL's** een lijst met tekenreeksen die openbaar beschikbare URL's vertegenwoordigen.

Roep de methode [create](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554) aan en geef de eigenschap `operation_id` van de geretourneerde bewerking door aan de methode [operations.get_details](#get-status-of-an-operation) om de status op te vragen.

Met de laatste regel van de volgende code wordt de Knowledge Base-id geretourneerd.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door [knowledgebase.update](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455) aan te roepen en de Knowledge Base-id en een [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb)-object door de geven. Dat object kan het volgende bevatten:
- [add](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [update](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [verwijderen](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

Roep de methode `operation_id` aan en geef de geretourneerde bewerking door aan de methode [operations.get_details](#get-status-of-an-operation) om de status op te vragen.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>Een Knowledge Base downloaden

Gebruik de methode [knowledgebase.download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651) om de database als een lijst van [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb) te downloaden. Dit is _geen_ equivalent voor de exportbewerking vanuit de pagina **Instellingen** van de QnA Maker-portal omdat het resultaat van deze methode geen TSV-bestand is.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de methode [knowledgebase.publish](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283). Hiervoor wordt het momenteel opgeslagen en getrainde model gebruikt waarnaar door de Knowledge Base-id wordt verwezen, en wordt dat model naar een eindpunt gepubliceerd.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>Een queryruntimesleutel ophalen

Zodra een Knowledge Base is gepubliceerd, hebt u de runtime-eindpuntsleutel nodig om een query uit te voeren op de Knowledge Base. Dit is niet dezelfde abonnementssleutel als waarmee de ontwerpclient is gemaakt.

Gebruik de methode [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) om een [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb)-object op te halen.

Gebruik een van de sleuteleigenschappen die in het object zijn geretourneerd om een query uit te voeren op de Knowledge Base.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime voor het genereren van een antwoord verifiëren

Maak een [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) om een query uit te voeren op de Knowledge Base om een antwoord te genereren of een training uit te voeren via actief leren.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

Gebruik de QnAMakerRuntimeClient om een antwoord uit de Knowledge Base op te halen of om nieuwe vraagvoorstellen naar de Knowledge Base te verzenden voor [actief leren](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Genereer een antwoord uit de Knowledge Base

Genereer een antwoord uit een gepubliceerde Knowledge Base met behulp van de RuntimeClient.runtime.generateAnswer-method. Deze methode accepteert de Knowledge Base-id en de QueryDTO. Krijg toegang tot aanvullende eigenschappen van de QueryDTO, zoals een Top en Context die u in uw chatbot kunt gebruiken.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

Dit is een eenvoudig voorbeeld van het uitvoeren van een query op een Knowledge Base. Bekijk [Andere queryvoorbeelden](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer) voor meer informatie over geavanceerde queryscenario's.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de methode [knowledgebase.delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205) met een parameter van de Knowledge Base-id.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>De status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, duren zo lang dat er niet wordt gewacht tot het proces is beëindigd maar een [bewerking](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) wordt geretourneerd. Gebruik de bewerkings-id uit de bewerking om een poll uit te voeren (met logica voor opnieuw proberen) om de status van de oorspronkelijke methode te bepalen.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>De toepassing uitvoeren

Hier volgt de belangrijkste methode voor de toepassing.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

Voer de toepassing uit met de opdracht ruby in uw quickstart-bestand.

```console
ruby quickstart.rb
```

De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb).