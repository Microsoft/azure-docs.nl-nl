---
title: 'Quickstart: Clientbibliotheek van Form Recognizer voor Java'
description: Gebruik de clientbibliotheek van Form Recognizer voor Java voor het maken van een app voor het verwerken van formulieren waarmee sleutel-waardeparen en tabelgegevens uit uw aangepaste documenten worden geëxtraheerd.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 48162609c27372937337be87d4b8f78af35a46d5
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95864761"
---
> [!IMPORTANT]
> De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud.

[Referentiedocumentatie](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Pakket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.
* Wanneer u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Een Form Recognizer-resource maken"  target="_blank">een Form Recognizer-resource maken <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Form Recognizer API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Een Azure Storage-blob die een set trainingsgegevens bevat. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../../build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze quickstart kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken (downloaden en extraheren van *sample_data.zip*).


## <a name="setting-up"></a>Instellen

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `gradle init` uit vanuit uw werkmap. Met deze opdracht maakt u essentiële buildbestanden voor Gradle, inclusief *build.gradle.kts*, dat tijdens runtime wordt gebruikt om de toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.


### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Deze quickstart maakt gebruik van de Gradle-afhankelijkheidsmanager. U vindt de clientbibliotheek en informatie voor andere afhankelijkheidsbeheerders in de [Maven Central Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

Zorg ervoor dat u in het bestand *build.gradle.kts* de clientbibliotheek opneemt als een `implementation`-instructie, samen met de vereiste invoegtoepassen en instellingen.

#### <a name="version-30"></a>[versie 3.0](#tab/ga)
```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```
#### <a name="version-31-preview"></a>[versie 3.1 preview](#tab/preview)
```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.1")
}
```
---

### <a name="create-a-java-file"></a>Een Java-bestand maken


Voer de volgende opdracht uit vanuit uw werkmap:

```console
mkdir -p src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *FormRecognizer.java*. Open het bestand in uw voorkeurseditor of IDE en voeg de volgende `import`-instructies toe:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java), waar de codevoorbeelden uit deze quickstart zich bevinden.


Maak in de klasse **FormRecognizer** van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de [productnaam]-resource die u in de sectie **Vereisten** hebt gemaakt, is geïmplementeerd, klikt u op de knop **Naar de resource gaan** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) voor meer informatie.

Voeg in de **hoofdmethode** van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U definieert deze later. U moet ook verwijzingen naar de URL's toevoegen voor uw trainings- en testgegevens.

* Als u de SAS-URL voor de trainingsgegevens van uw aangepaste model wilt ophalen, opent u de Microsoft Azure Storage Explorer, klikt u met de rechtermuisknop op uw container en selecteert u **Handtekening voor gedeelde toegang ophalen**. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL**. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.
* Als u een URL wilt ophalen van een formulier dat u wilt testen, kunt u de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. U kunt ook de URL gebruiken van een document dat zich elders bevindt.
* Gebruik de bovenstaande methode ook om de URL te verkrijgen van een kopie van een ontvangstbewijs.

#### <a name="version-30"></a>[versie 3.0](#tab/ga)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]
#### <a name="version-31-preview"></a>[versie 3.1 preview](#tab/preview)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

---



## <a name="object-model"></a>Objectmodel 

Met Form Recognizer kunt u twee verschillende clienttypen maken. De eerste, `FormRecognizerClient`, wordt gebruikt om query's in de service uit te voeren op herkende formuliervelden en -inhoud. De tweede, `FormTrainingClient`, wordt gebruikt voor het maken en beheren van aangepaste modellen die u kunt gebruiken om de herkenning te verbeteren. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` biedt bewerkingen voor:

- Het herkennen van formuliervelden en -inhoud met aangepaste modellen die zijn getraind om uw aangepaste formulieren te herkennen.  Deze waarden worden geretourneerd in een verzameling `RecognizedForm`-objecten. Zie het voorbeeld [Aangepaste formulieren analyseren](#analyze-forms-with-a-custom-model).
- Het herkennen van formulierinhoud, met inbegrip van tabellen, regels en woorden, zonder dat u een model hoeft te trainen.  Formulierinhoud wordt geretourneerd in een verzameling `FormPage`-objecten. Zie het voorbeeld [Formulierinhoud herkennen](#recognize-form-content).
- Het herkennen van algemene velden van Amerikaanse ontvangstbewijzen met behulp van een vooraf getraind ontvangstbewijsmodel in de Form Recognizer-service.  Deze velden en metagegevens worden geretourneerd in een verzameling `RecognizedForm`-objecten. Zie het voorbeeld [Ontvangstbewijzen herkennen](#recognize-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` biedt bewerkingen voor:

- Aangepaste modellen trainen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden.  Er wordt een `CustomFormModel` geretourneerd, dat aangeeft welke formuliertypen het model zal herkennen en welke velden het zal extraheren voor elk formuliertype.
- Aangepaste modellen trainen om specifieke velden en waarden te herkennen die u opgeeft door uw aangepaste formulieren te labelen.  Er wordt een `CustomFormModel` geretourneerd, dat aangeeft welke velden het model zal extraheren, evenals de geschatte nauwkeurigheid van elk veld.
- Modellen beheren die zijn gemaakt in uw account.
- Het kopiëren van een aangepast model van de ene Form Recognizer-resource naar de andere.

> [!NOTE]
> Modellen kunnen ook worden getraind met een grafische gebruikersinterface zoals het [Hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de clientbibliotheek van Form Recognizer voor Java:

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

Voeg de volgende code toe bovenaan uw **hoofd** methode. Hier gaat u twee clientobjecten verifiëren met behulp van de abonnementsvariabelen die u hierboven hebt gedefinieerd. U gebruikt een **AzureKeyCredential**-object, zodat u indien nodig de API-sleutel kunt bijwerken zonder nieuwe clientobjecten te maken.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="recognize-form-content"></a>Formulierinhoud herkennen

U kunt Form Recognizer gebruiken om tabellen, regels en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen.

Als u de inhoud van een bestand op een bepaalde URL wilt herkennen, gebruikt u de methode **beginRecognizeContentFromUrl**.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> U kunt ook inhoud ophalen uit een lokaal bestand. Zie de [FormRecognizerClient](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable)-methoden, zoals **beginRecognizeContent**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling **FormPage**-objecten: één voor elke pagina in het ingediende document. Met de volgende code worden deze objecten doorlopen en worden de uitgepakte sleutel-/waardeparen en tabelgegevens afgedrukt.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Uitvoer

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="recognize-receipts"></a>Ontvangstbewijzen herkennen

In deze sectie wordt beschreven hoe u algemene velden in Amerikaanse ontvangstbewijzen kunt herkennen en uitpakken met behulp van een vooraf getraind ontvangstmodel.

Om ontvangstbewijzen te herkennen vanuit een URI, gebruikt u de methode **beginRecognizeReceiptsFromUrl**. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> U kunt ook lokale afbeeldingen van ontvangstbewijzen herkennen. Zie de [FormRecognizerClient](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable)-methoden, zoals **beginRecognizeReceipts**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling **RecognizedReceipt**-objecten: één voor elke pagina in het ingediende document. In het volgende codeblok worden de ontvangstbewijzen doorlopen en worden de details ervan naar de console afgedrukt.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

In het volgende codeblok worden de afzonderlijke items die op het ontvangstbewijs zijn gedetecteerd doorlopen en worden de details ervan naar de console afgedrukt.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Uitvoer 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

#### <a name="version-30"></a>[versie 3.0](#tab/ga)

#### <a name="version-31-preview"></a>[versie 3.1 preview](#tab/preview)

## <a name="recognize-business-cards"></a>Visitekaartjes herkennen

In deze sectie wordt beschreven hoe u veelvoorkomende velden in Engelse visitekaartjes kunt herkennen en extraheren, met behulp van een vooraf getraind model.

Gebruik de methode `beginRecognizeBusinessCardsFromUrl` om visitekaartjes te herkennen vanuit een URL. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> U kunt ook lokale afbeeldingen van visitekaartjes herkennen. Zie de methoden [FormRecognizerClient](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable), zoals **beginRecognizeBusinessCards**. Of zie de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling **RecognizedForm**-objecten: één voor elke kaart in het document. Met de volgende code wordt het visitekaartje op de opgegeven URI verwerkt, en worden de belangrijkste velden en waarden op de console weergegeven.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

## <a name="recognize-invoices"></a>Facturen herkennen

In deze sectie wordt beschreven hoe u veelvoorkomende velden in verkoopfacturen kunt herkennen en extraheren, met behulp van een vooraf getraind model.

Gebruik de methode `beginRecognizeInvoicesFromUrl` om visitekaartjes te herkennen vanuit een URL. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> U kunt ook lokale afbeeldingen van facturen herkennen. Zie de methoden [FormRecognizerClient](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable), zoals **beginRecognizeInvoices**. Of zie de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling **RecognizedForm**-objecten: één voor elke factuur in het document. Met de volgende code wordt het visitekaartje op de opgegeven URI verwerkt, en worden de belangrijkste velden en waarden op de console weergegeven.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

---

## <a name="train-a-custom-model"></a>Aangepast model trainen

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd waarin ook de sleutel-waarderelaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

> [!NOTE]
> U kunt ook modellen trainen met een grafische gebruikersinterface, zoals het [voorbeeldhulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden zonder de trainingsdocumenten handmatig te labelen.

Met de volgende methode wordt een model voor een bepaalde set documenten getraind en wordt de status van het model in de console weergegeven. 


[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

Het geretourneerde **CustomFormModel**-object bevat informatie over de formuliertypen die het model kan herkennen en de velden die het uit elk formuliertype kan uitpakken. In het volgende codeblok wordt deze informatie op de console weergegeven.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Ten slotte retourneert deze methode de unieke id van het model.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


### <a name="output"></a>Uitvoer

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt aangepaste modellen ook trainen door de trainingsdocumenten handmatig te labelen. Training met labels leidt in sommige scenario's tot betere prestaties. Als u met labels wilt trainen, moet uw Blob Storage-container naast de trainingsdocumenten ook speciale bestanden met labelinformatie ( *\<filename\>.pdf.labels.json*) bevatten. Het [hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md) beschikt over een gebruikersinterface die u kan helpen bij het maken van deze labelbestanden. Zodra u deze hebt, kunt u de methode **beginTraining** aanroepen met de parameter *useTrainingLabels* ingesteld op `true`.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


Het geretourneerde **CustomFormModel** geeft aan welke velden het model kan extraheren, samen met de geschatte nauwkeurigheid van elk veld. In het volgende codeblok wordt deze informatie op de console weergegeven.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Uitvoer

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

In deze sectie ziet u hoe u belangrijke/waardevolle informatie en andere inhoud uit uw aangepaste formuliertypen kunt ophalen met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de id ervan kunt doorgeven aan onderstaande methode. Zie de sectie [Een model trainen](#train-a-model-without-labels).

U gebruikt de methode **beginRecognizeCustomFormsFromUrl**. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> U kunt ook een lokaal bestand analyseren. Zie de [FormRecognizerClient](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable)-methoden, zoals **beginRecognizeCustomForms**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) voor scenario's met betrekking tot lokale afbeeldingen.

De geretourneerde waarde is een verzameling **RecognizedForm**-objecten: één voor elke pagina in het ingediende document. Met de volgende code worden de resultaten van de analyse naar de console afgedrukt. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheidsscore.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Uitvoer

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>Aangepaste modellen beheren

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. Als voorbeeld worden met de volgende code alle modelbeheertaken in één methode uitgevoerd. Begin met het kopiëren van de onderstaande methodehandtekening:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resourceaccount controleren

In het volgende codeblok wordt het aantal modellen gecontroleerd dat u in uw Form Recognizer-account hebt opgeslagen en wordt dit aantal vergeleken met de limiet voor het account.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Uitvoer 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weergeven die momenteel zijn opgeslagen in het resource-account

In het volgende codeblok worden de huidige modellen in uw account vermeld en worden de details ervan in de console weergegeven.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Uitvoer 

Dit antwoord is ingekort zodat het beter leesbaar is.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resourceaccount verwijderen

U kunt een model ook uit uw account verwijderen door naar de id te verwijzen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]



## <a name="run-the-application"></a>De toepassing uitvoeren

Ga terug naar de hoofdmap van uw project. Bouw vervolgens de app met de volgende opdracht:

```console
gradle build
```

De toepassing uitvoeren met het doel `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

Clients van Form Recognizer veroorzaken `ErrorResponseException`-uitzonderingen. Als u bijvoorbeeld een ongeldige URL voor een bestandsbron opgeeft, wordt er een `ErrorResponseException` gegenereerd met een melding die de oorzaak van de fout aangeeft. In het volgende codefragment wordt de fout mooi verwerkt door de uitzondering vast te leggen en aanvullende informatie over de fout weer te geven.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Logboekregistratie op de client inschakelen

Azure-SDK's voor Java bieden een consistente logboekervaring om u te helpen bij het oplossen van toepassingsfouten en snel vinden van een oplossing. De vastgelegde logboeken bieden een overzicht van een toepassing voordat deze wordt beëindigd om zo de oorzaak van het probleem te vinden. Bekijk de [wiki over logboeken en logboekregistratie](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) voor hulp bij het inschakelen van logboekregistratie.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de clientbibliotheek van Form Recognizer voor Java gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens leert u tips voor het maken van een betere set met trainingsgegevens en het produceren van nauwkeurigere modellen.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeldcode uit deze gids (en meer) is te vinden op [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).