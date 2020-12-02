---
title: 'Quickstart: Clientbibliotheek van Form Recognizer voor .NET'
description: Gebruik de clientbibliotheek van Form Recognizer voor .NET voor het maken van een app voor het verwerken van formulieren waarmee sleutel-/waardeparen en tabelgegevens uit uw aangepaste documenten worden geëxtraheerd.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/06/2020
ms.author: pafarley
ms.openlocfilehash: d7577668d87ecaf2d769136d64990f95fc212fe6
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356525"
---
> [!IMPORTANT]
> De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud.

[Referentiedocumentatie](/dotnet/api/overview/azure/ai.formrecognizer-readme) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) of de huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Een Azure Storage-blob die een set trainingsgegevens bevat. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../../build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze quickstart kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken (downloaden en extraheren van *sample_data.zip*).
* Wanneer u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Een Form Recognizer-resource maken"  target="_blank">een Form Recognizer-resource maken <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Form Recognizer API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `formrecognizer-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *program.cs*. 

```console
dotnet new console -n formrecognizer-quickstart
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

### <a name="install-the-client-library"></a>De clientbibliotheek installeren 

Installeer in de toepassingsmap de Form Recognizer-clientbibliotheek voor .NET met de volgende opdracht:

#### <a name="version-30"></a>[versie 3.0](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

#### <a name="version-31-preview"></a>[versie 3.1 preview](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.1
```
---

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md), waar de codevoorbeelden uit deze quickstart zich bevinden.

Open vanuit de projectmap het bestand *Program.cs* in uw favoriete editor of IDE. Voeg de volgende `using`-instructies toe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Maak in de klasse **Programma** van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Form Recognizer-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

Voeg in de **Hoofd** methode van de toepassing een aanroep toe aan de asynchrone taken die in deze quickstart wordt gebruikt. U gaat ze later implementeren.

#### <a name="version-30"></a>[versie 3.0](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]
#### <a name="version-31-preview"></a>[versie 3.1 preview](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

---


## <a name="object-model"></a>Objectmodel 

Met Form Recognizer kunt u twee verschillende clienttypen maken. De eerste, `FormRecognizerClient`, wordt gebruikt om query's in de service uit te voeren op herkende formuliervelden en -inhoud. De tweede, `FormTrainingClient`, wordt gebruikt voor het maken en beheren van aangepaste modellen die u kunt gebruiken om de herkenning te verbeteren. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` biedt bewerkingen voor:

 - Het herkennen van formuliervelden en -inhoud met aangepaste modellen die zijn getraind om uw aangepaste formulieren te herkennen.  Deze waarden worden geretourneerd in een verzameling `RecognizedForm`-objecten. Zie het voorbeeld [Aangepaste formulieren analyseren](#analyze-forms-with-a-custom-model).
 - Het herkennen van formulierinhoud, met inbegrip van tabellen, regels en woorden, zonder dat u een model hoeft te trainen.  Formulierinhoud wordt geretourneerd in een verzameling `FormPage`-objecten. Zie het voorbeeld [Formulierinhoud herkennen](#recognize-form-content).
 - Het herkennen van algemene velden van Amerikaanse ontvangstbewijzen met behulp van een vooraf getraind ontvangstbewijsmodel in de Form Recognizer-service. Deze velden en metagegevens worden geretourneerd in een verzameling `RecognizedForm`-objecten. Zie het voorbeeld [Ontvangstbewijzen herkennen](#recognize-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` biedt bewerkingen voor:

- Aangepaste modellen trainen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden.  Er wordt een `CustomFormModel` geretourneerd, dat aangeeft welke formuliertypen het model zal herkennen en welke velden het zal extraheren voor elk formuliertype.
- Aangepaste modellen trainen om specifieke velden en waarden te herkennen die u opgeeft door uw aangepaste formulieren te labelen.  Er wordt een `CustomFormModel` geretourneerd, dat aangeeft welke velden het model zal extraheren, evenals de geschatte nauwkeurigheid van elk veld.
- Modellen beheren die zijn gemaakt in uw account.
- Het kopiëren van een aangepast model van de ene Form Recognizer-resource naar de andere.

Zie de voorbeelden voor [Een model trainen](#train-a-custom-model) en [Aangepaste modellen beheren](#manage-custom-models).

> [!NOTE]
> Modellen kunnen ook worden getraind met een grafische gebruikersinterface zoals het [Hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de clientbibliotheek van Form Recognizer voor .NET:

#### <a name="version-30"></a>[versie 3.0](#tab/ga)

* [De client verifiëren](#authenticate-the-client)
* [Formulierinhoud herkennen](#recognize-form-content)
* [Ontvangstbewijzen herkennen](#recognize-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)

#### <a name="version-31-preview"></a>[versie 3.1 preview](#tab/preview)

* [De client verifiëren](#authenticate-the-client)
* [Formulierinhoud herkennen](#recognize-form-content)
* [Ontvangstbewijzen herkennen](#recognize-receipts)
* [Visitekaartjes herkennen](#recognize-business-cards)
* [Facturen herkennen](#recognize-invoices)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>De client verifiëren

Maak onder **Hoofd** een nieuwe methode met de naam `AuthenticateClient`. U zult deze in andere taken gebruiken om uw aanvragen bij de Form Recognizer-service te verifiëren. Deze methode maakt gebruik van een `AzureKeyCredential`-object, zodat u indien nodig de API-sleutel kunt bijwerken zonder nieuwe clientobjecten te maken.

> [!IMPORTANT]
> Haal uw sleutel en eindpunt op vanuit de Azure-portal. Als de Form Recognizer-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Bijvoorbeeld [Azure Key Vault](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Herhaal de bovenstaande stappen voor een nieuwe methode om een trainingsclient te verifiëren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Assets voor testen ophalen 

U moet ook verwijzingen naar de URL's toevoegen voor uw trainings- en testgegevens. Voeg deze toe aan de hoofdmap van uw **Programma**-klasse.

* Als u de SAS-URL voor de trainingsgegevens van uw aangepaste model wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert u **Handtekening voor gedeelde toegang ophalen**. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL**. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.
* Gebruik vervolgens de bovenstaande stappen om de SAS-URL van een afzonderlijk document in Blob Storage op te halen.
* Sla tot slot de URL op van de voorbeeldafbeelding(en) die hieronder zijn opgenomen (ook beschikbaar op [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)). 

#### <a name="version-30"></a>[versie 3.0](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]
#### <a name="version-31-preview"></a>[versie 3.1 preview](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

---


## <a name="recognize-form-content"></a>Formulierinhoud herkennen

U kunt Form Recognizer gebruiken om tabellen, regels en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen. De geretourneerde waarde is een verzameling **FormPage**-objecten: één voor elke pagina in het ingediende document. 

Als u de inhoud van een bestand op een bepaalde URL wilt herkennen, gebruikt u de methode `StartRecognizeContentFromUri`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> U kunt ook inhoud ophalen uit een lokaal bestand. Zie de [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet)-methoden, bijvoorbeeld **StartRecognizeContent**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

Met de rest van deze taak worden de inhoudsgegevens afgedrukt naar de console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

### <a name="output"></a>Uitvoer

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="recognize-receipts"></a>Ontvangstbewijzen herkennen

In deze sectie wordt beschreven hoe u algemene velden in Amerikaanse ontvangstbewijzen kunt herkennen en uitpakken met behulp van een vooraf getraind ontvangstmodel.

Om ontvangstbewijzen te herkennen vanuit een URL, gebruikt u de methode `StartRecognizeReceiptsFromUri`. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> U kunt ook lokale afbeeldingen van ontvangstbewijzen herkennen. Zie de [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet)-methoden, bijvoorbeeld **StartRecognizeReceipts**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling `RecognizedReceipt`-objecten: één voor elke pagina in het ingediende document. Met de volgende code wordt het ontvangstbewijs op de opgegeven URI verwerkt en worden de belangrijkste velden en waarden op de console weergegeven.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

### <a name="output"></a>Uitvoer 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

#### <a name="version-30"></a>[versie 3.0](#tab/ga)

#### <a name="version-31-preview"></a>[versie 3.1 preview](#tab/preview)

## <a name="recognize-business-cards"></a>Visitekaartjes herkennen

In deze sectie wordt beschreven hoe u veelvoorkomende velden in Engelse visitekaartjes kunt herkennen en extraheren, met behulp van een vooraf getraind model.

Gebruik de methode `StartRecognizeBusinessCardsFromUriAsync` om visitekaartjes te herkennen vanuit een URL. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> U kunt ook lokale afbeeldingen van ontvangstbewijzen herkennen. Zie de methoden [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet), zoals **StartRecognizeBusinessCards**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling `RecognizedForm`-objecten: één voor elke kaart in het document. Met de volgende code wordt het visitekaartje op de opgegeven URI verwerkt, en worden de belangrijkste velden en waarden op de console weergegeven.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

## <a name="recognize-invoices"></a>Facturen herkennen

In deze sectie wordt beschreven hoe u veelvoorkomende velden in verkoopfacturen kunt herkennen en extraheren, met behulp van een vooraf getraind model.

Gebruik de methode `StartRecognizeInvoicesFromUriAsync` om facturen te herkennen vanuit een URL. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> U kunt ook lokale afbeeldingen van facturen herkennen. Zie de [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet)-methoden, bijvoorbeeld **StartRecognizeInvoices**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling `RecognizedForm`-objecten: één voor elke factuur in het ingediende document. Met de volgende code wordt de factuur op de opgegeven URI verwerkt en worden de belangrijkste velden en waarden op de console weergegeven.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

---


## <a name="train-a-custom-model"></a>Aangepast model trainen

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd waarin ook de sleutel-waarderelaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

> [!NOTE]
> U kunt ook modellen trainen met een grafische gebruikersinterface, zoals het [voorbeeldhulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden zonder de trainingsdocumenten handmatig te labelen. Met de volgende methode wordt een model voor een bepaalde set documenten getraind en wordt de status van het model in de console weergegeven. 


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]


Het geretourneerde `CustomFormModel`-object bevat informatie over de formuliertypen die het model kan herkennen en de velden die het uit elk formuliertype kan uitpakken. In het volgende codeblok wordt deze informatie op de console weergegeven.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Ten slotte retourneert u de getrainde model-id voor gebruik in latere stappen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Uitvoer

Dit antwoord is ingekort zodat het beter leesbaar is.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt aangepaste modellen ook trainen door de trainingsdocumenten handmatig te labelen. Training met labels leidt in sommige scenario's tot betere prestaties. Als u met labels wilt trainen, moet uw Blob Storage-container naast de trainingsdocumenten ook speciale bestanden met labelinformatie (`\<filename\>.pdf.labels.json`) bevatten. Het [hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md) beschikt over een gebruikersinterface die u kan helpen bij het maken van deze labelbestanden. Zodra u deze hebt, kunt u de methode `StartTrainingAsync` aanroepen met de parameter `uselabels` ingesteld op `true`. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

Het geretourneerde `CustomFormModel` geeft aan welke velden het model kan extraheren, samen met de geschatte nauwkeurigheid van elk veld. In het volgende codeblok wordt deze informatie op de console weergegeven.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]


### <a name="output"></a>Uitvoer

Dit antwoord is ingekort zodat het beter leesbaar is.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

In deze sectie ziet u hoe u belangrijke/waardevolle informatie en andere inhoud uit uw aangepaste formuliertypen kunt ophalen met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de id ervan kunt doorgeven aan onderstaande methode.

U gebruikt de methode `StartRecognizeCustomFormsFromUri`. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> U kunt ook een lokaal bestand analyseren. Zie de [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet)-methoden, bijvoorbeeld **StartRecognizeCustomForms**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling `RecognizedForm`-objecten: één voor elke pagina in het ingediende document. Met de volgende code worden de resultaten van de analyse op de console weergegeven. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheidsscore.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]


### <a name="output"></a>Uitvoer

Dit antwoord is ingekort zodat het beter leesbaar is.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>Aangepaste modellen beheren

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. U voert meerdere bewerkingen uit in de volgende methode:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resourceaccount controleren

In het volgende codeblok wordt het aantal modellen gecontroleerd dat u in uw Form Recognizer-account hebt opgeslagen en wordt dit aantal vergeleken met de limiet voor het account.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Uitvoer 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weergeven die momenteel zijn opgeslagen in het resource-account

In het volgende codeblok worden de huidige modellen in uw account vermeld en worden de details ervan in de console weergegeven.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]


### <a name="output"></a>Uitvoer 

Dit antwoord is ingekort zodat het beter leesbaar is.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Een specifiek model ophalen met de id van het model

Het volgende codeblok traint een nieuw model (net zoals in de sectie [Een model trainen](#train-a-model-without-labels)) en haalt dan met behulp van zijn id een tweede verwijzing op.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Uitvoer 

Dit antwoord is ingekort zodat het beter leesbaar is.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resourceaccount verwijderen

U kunt een model ook uit uw account verwijderen door naar de id te verwijzen. In deze stap wordt ook de methode afgesloten.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]


## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run
```


## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer met behulp van de .NET SDK de Cognitive Services Form Recognizer-clientbibliotheek gebruikt, resulteren fouten geretourneerd door de service in een `RequestFailedException`. Ze bevatten dezelfde HTTP-statuscode die een REST API-aanvraag zou retourneren.

Als u bijvoorbeeld een afbeelding van een ontvangstbewijs verstuurt met een ongeldige URI, dan wordt een `400`-fout geretourneerd met het bericht 'Foute aanvraag'.

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

U ziet dat aanvullende informatie, zoals de clientaanvraag-id van de bewerking, wordt geregistreerd.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de clientbibliotheek van Form Recognizer voor .NET gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens krijgt u tips om een betere set met trainingsgegevens te maken en nauwkeurigere modellen te produceren.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeldcode uit deze gids (en meer) is te vinden op [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).