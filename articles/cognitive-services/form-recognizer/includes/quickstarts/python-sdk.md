---
title: 'Quickstart: Clientbibliotheek van Form Recognizer voor Python'
description: Gebruik de clientbibliotheek van Form Recognizer voor Python voor het maken van een app voor het verwerken van formulieren waarmee sleutel-/waardeparen en tabelgegevens uit uw aangepaste documenten worden geëxtraheerd.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: e37ff8a003bc10d69fd32794f26acfa8f5326423
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073296"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Zie de referentiedocumentatie hieronder. 

[Referentiedocumentatie](/python/api/azure-ai-formrecognizer) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Package (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * De python-installatie moet [PIP](https://pip.pypa.io/en/stable/)bevatten. U kunt controleren of u PIP hebt geïnstalleerd door uit te voeren `pip --version` op de opdracht regel. Ontvang PIP door de meest recente versie van python te installeren.
* Een Azure Storage-blob die een set trainingsgegevens bevat. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../../build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze quickstart kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken (downloaden en extraheren van *sample_data.zip*).
* Wanneer u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Een Form Recognizer-resource maken"  target="_blank">een Form Recognizer-resource maken </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
  * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Form Recognizer API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
  * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Nadat u Python hebt geïnstalleerd, kunt u de meest recente versie van de Form Recognizer-clientbibliotheek installeren met:

#### <a name="v21-preview"></a>[Preview van v2.1](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> De 3.1.0 SDK van Form Recognizer weerspiegelt _API versie 2,1 Preview. 2_. Gebruik de [**rest API**](../../quickstarts/client-library.md) voor _API versie 2,1 Preview. 3_.

#### <a name="v20"></a>[v2.0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> De 3.0.0 SDK van de formulier herkenning weerspiegelt API v 2.0

---

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuwe Python-toepassing in uw favoriete editor of IDE. Importeer vervolgens de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>Objectmodel

Met Form Recognizer kunt u twee verschillende clienttypen maken. De eerste, `form_recognizer_client` wordt gebruikt om de service te vragen om formulier velden en-inhoud te herkennen. De tweede, `form_training_client` wordt gebruikt voor het maken en beheren van aangepaste modellen die u kunt gebruiken om de herkenning te verbeteren. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` biedt bewerkingen voor:

* Het herkennen van formulier velden en inhoud met aangepaste modellen die zijn getraind om uw aangepaste formulieren te analyseren.
* Het herkennen van formulierinhoud, met inbegrip van tabellen, regels en woorden, zonder dat u een model hoeft te trainen.
* Het herkennen van algemene velden van ontvangstbewijzen met behulp van een vooraf getraind ontvangstbewijsmodel in de Form Recognizer-service.

### <a name="formtrainingclient"></a>FormTrainingClient

`form_training_client` biedt bewerkingen voor:

* Aangepaste modellen trainen om alle velden en waarden te analyseren die in uw aangepaste formulieren worden gevonden. Raadpleeg de [servicedocumentatie over het trainen van niet-gelabelde modellen](#train-a-model-without-labels) voor een meer gedetailleerde beschrijving van het maken van een set met trainingsgegevens.
* Aangepaste modellen trainen om specifieke velden en waarden te analyseren die u opgeeft door uw aangepaste formulieren te labelen. Raadpleeg de [servicedocumentatie over het trainen van niet-gelabelde modellen](#train-a-model-with-labels) voor een meer gedetailleerde beschrijving van het toepassen van labels op een set met trainingsgegevens.
* Modellen beheren die zijn gemaakt in uw account.
* Het kopiëren van een aangepast model van de ene Form Recognizer-resource naar de andere.

> [!NOTE]
> Modellen kunnen ook worden getraind met een grafische gebruikersinterface zoals het [Hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de clientbibliotheek van Form Recognizer voor Python:
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[Preview van v2.1](#tab/preview)

* [De client verifiëren](#authenticate-the-client)
* [Indeling analyseren](#analyze-layout)
* [Ontvangstbewijzen analyseren](#analyze-receipts)
* [Visitekaartjes analyseren](#analyze-business-cards)
* [Facturen analyseren](#analyze-invoices)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)

#### <a name="v20"></a>[v2.0](#tab/ga)

* [De client verifiëren](#authenticate-the-client)
* [Indeling analyseren](#analyze-layout)
* [Ontvangstbewijzen analyseren](#analyze-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>De client verifiëren

Hier gaat u twee clientobjecten verifiëren met behulp van de abonnementsvariabelen die u hierboven hebt gedefinieerd. U gebruikt een **AzureKeyCredential**-object, zodat u indien nodig de API-sleutel kunt bijwerken zonder nieuwe clientobjecten te maken.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Assets voor testen ophalen

U moet verwijzingen naar de URL's toevoegen voor uw trainings- en testgegevens.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL ophalen":::
* Gebruik het voorbeeld formulier en de ontvangstbewijs afbeeldingen die zijn opgenomen in de onderstaande voor beelden (ook beschikbaar op [github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) , of u kunt de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. 

> [!NOTE]
> De codefragmenten in deze gids gebruiken externe formulieren die worden geopend middels URL's. Als u in plaats daarvan lokale formulierdocumenten wilt verwerken, raadpleegt u de gerelateerde methoden in de [referentiedocumentatie](/python/api/azure-ai-formrecognizer) en [voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="analyze-layout"></a>Indeling analyseren

U kunt de formulier herkenner gebruiken voor het analyseren van tabellen, lijnen en woorden in documenten, zonder dat u een model hoeft te trainen. Zie de [conceptuele hand leiding voor lay-out](../../concept-layout.md)voor meer informatie over indelings extractie.

Als u de inhoud van een bestand op een bepaalde URL wilt analyseren, gebruikt u de- `begin_recognize_content_from_url` methode. De geretourneerde waarde is een verzameling `FormPage`-objecten: één voor elke pagina in het ingediende document. Met de volgende code worden deze objecten doorlopen en worden de uitgepakte sleutel-/waardeparen en tabelgegevens afgedrukt.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> U kunt ook inhoud ophalen uit lokale afbeeldingen. Zie de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient)-methoden, bijvoorbeeld `begin_recognize_content`. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="output"></a>Uitvoer

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="analyze-invoices"></a>Facturen analyseren

#### <a name="v21-preview"></a>[Preview van v2.1](#tab/preview)

In deze sectie wordt beschreven hoe u algemene velden van verkoop facturen kunt analyseren en extra heren met behulp van een vooraf getraind model. Zie de [hand leiding voor factuur begrippen](../../concept-invoices.md)voor meer informatie over het analyseren van facturen. Als u facturen van een URL wilt analyseren, gebruikt u de- `begin_recognize_invoices_from_url` methode. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> U kunt ook lokale factuur afbeeldingen analyseren. Zie de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient)-methoden, bijvoorbeeld `begin_recognize_invoices`. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) voor scenario's met betrekking tot lokale afbeeldingen.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Deze functie is niet beschikbaar in de geselecteerde API-versie.

---

## <a name="train-a-custom-model"></a>Aangepast model trainen

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd waarin ook de sleutel-waarderelaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

> [!NOTE]
> U kunt ook modellen trainen met een grafische gebruikersinterface, zoals het [voorbeeldhulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te analyseren die in uw aangepaste formulieren worden gevonden zonder hand matig labels te krijgen voor de trainings documenten.

De volgende code gebruikt de training-client met de `begin_training`-functie om een model op een bepaalde set documenten te trainen. Het geretourneerde `CustomFormModel` object bevat informatie over het formulier. het model kan worden geanalyseerd en de velden die het kan extra heren uit elk formulier type. In het volgende codeblok wordt deze informatie op de console weergegeven.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>Uitvoer

Dit is de uitvoer van een model dat is getraind met de trainingsgegevens die beschikbaar zijn via de [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt aangepaste modellen ook trainen door de trainingsdocumenten handmatig te labelen. Training met labels leidt in sommige scenario's tot betere prestaties. Het geretourneerde `CustomFormModel` geeft aan welke velden het model kan extraheren, samen met de geschatte nauwkeurigheid van elk veld. In het volgende codeblok wordt deze informatie op de console weergegeven.

> [!IMPORTANT]
> Als u met labels wilt trainen, moet uw Blob Storage-container naast de trainingsdocumenten ook speciale bestanden met labelinformatie (`\<filename\>.pdf.labels.json`) bevatten. Het [hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md) beschikt over een gebruikersinterface die u kan helpen bij het maken van deze labelbestanden. Zodra u deze hebt, kunt u de functie `begin_training` aanroepen met de parameter *use_training_labels* ingesteld op `true`.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Uitvoer

Dit is de uitvoer van een model dat is getraind met de trainingsgegevens die beschikbaar zijn via de [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

In deze sectie ziet u hoe u belangrijke/waardevolle informatie en andere inhoud uit uw aangepaste formuliertypen kunt ophalen met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de id ervan kunt doorgeven aan onderstaande methode. Zie de sectie [Een model trainen](#train-a-model-without-labels).

U gebruikt de methode `begin_recognize_custom_forms_from_url`. De geretourneerde waarde is een verzameling `RecognizedForm`-objecten: één voor elke pagina in het ingediende document. Met de volgende code worden de resultaten van de analyse op de console weergegeven. Alle herkende velden en bijbehorende waarden worden afgedrukt, samen met een betrouwbaarheidsscore.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> U kunt ook lokale afbeeldingen analyseren. Zie de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient)-methoden, bijvoorbeeld `begin_recognize_custom_forms`. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) voor scenario's met betrekking tot lokale afbeeldingen.


### <a name="output"></a>Uitvoer

Als u het model uit het vorige voorbeeld gebruikt, wordt de volgende uitvoer gegeven.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="analyze-receipts"></a>Ontvangstbewijzen analyseren

In deze sectie wordt beschreven hoe u algemene velden van Amerikaanse ontvangsten analyseert en extraheert met behulp van een vooraf getraind ontvangst model. Zie de [conceptuele hand leiding voor bevestigingen](../../concept-receipts.md)voor meer informatie over de ontvangst analyse. Als u de ontvangst van een URL wilt analyseren, gebruikt u de- `begin_recognize_receipts_from_url` methode. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> U kunt ook lokale ontvangstbewijs afbeeldingen analyseren. Zie de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient)-methoden, bijvoorbeeld `begin_recognize_receipts`. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="output"></a>Uitvoer

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="analyze-business-cards"></a>Visitekaartjes analyseren

#### <a name="v21-preview"></a>[Preview van v2.1](#tab/preview)

In deze sectie wordt beschreven hoe u algemene velden uit de Engelse visite kaartjes analyseert en extraheert met behulp van een vooraf getraind model. Zie de [conceptuele hand leiding voor visite](../../concept-business-cards.md)kaartjes voor meer informatie over het analyseren van bedrijfs kaarten. Als u visite kaartjes wilt analyseren vanuit een URL, gebruikt u de- `begin_recognize_business_cards_from_url` methode. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> U kunt ook lokale visite kaart afbeeldingen analyseren. Zie de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient)-methoden, bijvoorbeeld `begin_recognize_business_cards`. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) voor scenario's met betrekking tot lokale afbeeldingen.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Deze functie is niet beschikbaar in de geselecteerde API-versie.

---

## <a name="manage-your-custom-models"></a>Uw aangepaste modellen beheren

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Het aantal modellen in het FormRecognizer-resourceaccount controleren

In het volgende codeblok wordt het aantal modellen gecontroleerd dat u in uw Form Recognizer-account hebt opgeslagen en wordt dit aantal vergeleken met de limiet voor het account.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Uitvoer

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>De modellen weergeven die momenteel zijn opgeslagen in het resource-account

In het volgende codeblok worden de huidige modellen in uw account vermeld en worden de details ervan in de console weergegeven. Er wordt ook een verwijzing naar het eerste model opgeslagen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Uitvoer

Dit is een voorbeelduitvoer voor het testaccount.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Een specifiek model ophalen met de id van het model

Het volgende codeblok maakt gebruik van de model-id die is opgeslagen in de vorige sectie en gebruikt deze om details over het model op te halen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Uitvoer

Dit is de voorbeelduitvoer voor het aangepaste model dat in het vorige voorbeeld is gemaakt.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resourceaccount verwijderen

U kunt een model ook uit uw account verwijderen door naar de id te verwijzen. Met deze code wordt het model verwijderd dat in de vorige sectie is gebruikt.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `python` in uw quickstart-bestand.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="general"></a>Algemeen

De Form Recognizer-clientbibliotheek genereert uitzonderingen die zijn gedefinieerd in [Azure Core](https://aka.ms/azsdk-python-azure-core).

### <a name="logging"></a>Logboekregistratie

Deze bibliotheek maakt gebruik van de [standaardbibliotheek voor logboekregistratie](https://docs.python.org/3/library/logging.html) voor logboekregistratie. Basisinformatie over HTTP-sessies (URL's, headers, enzovoort) wordt vastgelegd op INFO-niveau.

Gedetailleerde logboekregistratie op DEBUG-niveau met aanvraag/antwoord-body's en niet-geredigeerde headers, kan worden ingeschakeld op een client met het sleutelwoordargument `logging_enable`:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Op dezelfde manier kan `logging_enable` logboekregistratie voor één bewerking inschakelen, zelfs wanneer dit niet is ingeschakeld voor de client:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de clientbibliotheek van Form Recognizer voor Python gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens leert u tips voor het maken van een betere set met trainingsgegevens en het produceren van nauwkeurigere modellen.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeldcode uit deze gids is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).
