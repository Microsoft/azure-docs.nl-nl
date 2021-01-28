---
title: 'Quickstart: Clientbibliotheek van Form Recognizer voor JavaScript'
description: Gebruik de clientbibliotheek van Form Recognizer voor JavaScript voor het maken van een app voor het verwerken van formulieren waarmee sleutel-waardeparen en tabelgegevens uit uw aangepaste documenten worden geëxtraheerd.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: a4d29dfb2a57dde2bb21244b2e5335f1a8ea1fcf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947972"
---
> [!IMPORTANT]
> * De code in dit artikel maakt gebruik van synchrone methoden en onbeveiligde referentieopslag voor de eenvoud. Zie de referentiedocumentatie hieronder. 

[Referentiedocumentatie](../../index.yml) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Pakket (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [Node.js](https://nodejs.org/)
* Een Azure Storage-blob die een set trainingsgegevens bevat. Zie [Een set met trainingsgegevens voor een aangepast model bouwen](../../build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze quickstart kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken (downloaden en extraheren van *sample_data.zip*).
* Wanneer u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Een Form Recognizer-resource maken"  target="_blank">een Form Recognizer-resource maken <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Form Recognizer API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand. 

```console
npm init
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer het NPM-pakket `ai-form-recognizer`:

```console
npm install @azure/ai-form-recognizer
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam `index.js` en importeer de volgende bibliotheken:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]


> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Form Recognizer-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

## <a name="object-model"></a>Objectmodel 

Met Form Recognizer kunt u twee verschillende clienttypen maken. De eerste, `FormRecognizerClient`, wordt gebruikt om query's in de service uit te voeren op herkende formuliervelden en -inhoud. De tweede, `FormTrainingClient`, wordt gebruikt voor het maken en beheren van aangepaste modellen die u kunt gebruiken om de herkenning te verbeteren. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` biedt bewerkingen voor:

 * Het herkennen van formuliervelden en -inhoud met aangepaste modellen die zijn getraind om uw aangepaste formulieren te herkennen. Deze waarden worden geretourneerd in een verzameling `RecognizedForm`-objecten.
 * Het herkennen van formulierinhoud, met inbegrip van tabellen, regels en woorden, zonder dat u een model hoeft te trainen. Formulierinhoud wordt geretourneerd in een verzameling `FormPage`-objecten.
 * Het herkennen van algemene velden van ontvangstbewijzen met behulp van een vooraf getraind ontvangstbewijsmodel in de Form Recognizer-service. Deze velden en metagegevens worden geretourneerd in een verzameling `RecognizedReceipt`.

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` biedt bewerkingen voor:

* Aangepaste modellen trainen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden. Er wordt een `CustomFormModel` geretourneerd, dat aangeeft welke formuliertypen het model zal herkennen en welke velden het zal extraheren voor elk formuliertype. Raadpleeg de [servicedocumentatie over het trainen van niet-gelabelde modellen](#train-a-model-without-labels) voor een meer gedetailleerde beschrijving van het maken van een set met trainingsgegevens.
* Aangepaste modellen trainen om specifieke velden en waarden te herkennen die u opgeeft door uw aangepaste formulieren te labelen. Er wordt een `CustomFormModel` geretourneerd, dat aangeeft welke velden het model zal extraheren, evenals de geschatte nauwkeurigheid van elk veld. Raadpleeg de [servicedocumentatie over het trainen van niet-gelabelde modellen](#train-a-model-with-labels) voor een meer gedetailleerde beschrijving van het toepassen van labels op een set met trainingsgegevens.
* Modellen beheren die zijn gemaakt in uw account.
* Het kopiëren van een aangepast model van de ene Form Recognizer-resource naar de andere.

> [!NOTE]
> Modellen kunnen ook worden getraind met een grafische gebruikersinterface zoals het [Hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de clientbibliotheek van Form Recognizer voor JavaScript:

* [De client verifiëren](#authenticate-the-client)
* [Indeling analyseren](#analyze-layout)
* [Ontvangstbewijzen analyseren](#analyze-receipts)
* [Aangepast model trainen](#train-a-custom-model)
* [Formulieren analyseren met een aangepast model](#analyze-forms-with-a-custom-model)
* [Uw aangepaste modellen beheren](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>De client verifiëren



Verifieer een clientobject met de abonnementsvariabelen die u hebt gedefinieerd. U gebruikt een `AzureKeyCredential`-object, zodat u indien nodig de API-sleutel kunt bijwerken zonder nieuwe clientobjecten te maken. U maakt ook een clientobject voor training.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Assets voor testen ophalen

U moet ook verwijzingen naar de URL's toevoegen voor uw trainings- en testgegevens.
* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
   
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS-URL ophalen":::
* Gebruik het voorbeeldformulier en de ontvangstbewijsafbeeldingen die zijn opgenomen in de onderstaande voorbeelden. Ze zijn ook beschikbaar op [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets). U kunt tevens de bovenstaande stappen gebruiken om de SAS-URL van een afzonderlijk document in Blob Storage op te halen. 


## <a name="analyze-layout"></a>Indeling analyseren

U kunt Form Recognizer gebruiken om tabellen, regels en woorden in documenten te herkennen, zonder dat u een model hoeft te trainen. Als u de inhoud van een bestand op een bepaalde URI wilt herkennen, gebruikt u de methode `beginRecognizeContentFromUrl`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]


> [!TIP]
> U kunt ook inhoud ophalen uit een lokaal bestand. Zie de [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient)-methoden, zoals **beginRecognizeContent**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="output"></a>Uitvoer

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>Ontvangstbewijzen analyseren

In deze sectie wordt beschreven hoe u algemene velden in Amerikaanse ontvangstbewijzen kunt herkennen en uitpakken met behulp van een vooraf getraind ontvangstmodel.

Om ontvangstbewijzen te herkennen vanuit een URI, gebruikt u de methode `beginRecognizeReceiptsFromUrl`. Met de volgende code wordt een ontvangstbewijs op de opgegeven URI verwerkt en worden de belangrijkste velden en waarden op de console weergegeven.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> U kunt ook lokale afbeeldingen van ontvangstbewijzen herkennen. Zie de [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient)-methoden, zoals **beginRecognizeReceipts**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="output"></a>Uitvoer

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="train-a-custom-model"></a>Aangepast model trainen

In deze sectie ziet u hoe u een model kunt trainen met uw eigen gegevens. Met een getraind model kunnen gestructureerde gegevens worden uitgevoerd waarin ook de sleutel-waarderelaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

> [!NOTE]
> U kunt ook modellen trainen met een grafische gebruikersinterface, zoals het [voorbeeldhulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Een model trainen zonder labels

Train aangepaste modellen om alle velden en waarden te herkennen die in uw aangepaste formulieren worden gevonden zonder de trainingsdocumenten handmatig te labelen.

Met de volgende functie wordt een model voor een bepaalde set documenten getraind en wordt de status van het model in de console weergegeven. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]


### <a name="output"></a>Uitvoer

Dit is de uitvoer van een model dat is getraind met de trainingsgegevens die beschikbaar zijn via de [JavaScript-SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer). Deze voorbeelduitvoer is ingekort zodat deze beter leesbaar is.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>Een model trainen met labels

U kunt aangepaste modellen ook trainen door de trainingsdocumenten handmatig te labelen. Training met labels leidt in sommige scenario's tot betere prestaties. Als u met labels wilt trainen, moet uw Blob Storage-container naast de trainingsdocumenten ook speciale bestanden met labelinformatie (`\<filename\>.pdf.labels.json`) bevatten. Het [hulpprogramma voor labelen van Form Recognizer](../../quickstarts/label-tool.md) beschikt over een gebruikersinterface die u kan helpen bij het maken van deze labelbestanden. Zodra u deze hebt, kunt u de methode `beginTraining` aanroepen met de parameter `uselabels` ingesteld op `true`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]


### <a name="output"></a>Uitvoer 

Dit is de uitvoer van een model dat is getraind met de trainingsgegevens die beschikbaar zijn via de [JavaScript-SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples). Deze voorbeelduitvoer is ingekort zodat deze beter leesbaar is.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Formulieren analyseren met een aangepast model

In deze sectie ziet u hoe u belangrijke/waardevolle informatie en andere inhoud uit uw aangepaste formuliertypen kunt ophalen met behulp van modellen die u hebt getraind met uw eigen formulieren.

> [!IMPORTANT]
> Als u dit scenario wilt implementeren, moet u al een model hebben getraind, zodat u de id ervan kunt doorgeven aan onderstaande methode. Zie de sectie [Een model trainen](#train-a-model-without-labels).

U gebruikt de methode `beginRecognizeCustomFormsFromUrl`. De geretourneerde waarde is een verzameling `RecognizedForm`-objecten: één voor elke pagina in het ingediende document.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> U kunt ook lokale bestanden analyseren. Zie de [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient)-methoden, zoals **beginRecognizeCustomForms**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) voor scenario's met betrekking tot lokale afbeeldingen.


### <a name="output"></a>Uitvoer

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Uw aangepaste modellen beheren

In deze sectie wordt beschreven hoe u de aangepaste modellen beheert die zijn opgeslagen in uw account. Als voorbeeld worden met de volgende code alle modelbeheertaken in één functie uitgevoerd. 

### <a name="get-number-of-models"></a>Aantal modellen ophalen

In het volgende codeblok wordt het aantal modellen opgehaald dat zich momenteel in uw account bevindt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]


### <a name="get-list-of-models-in-account"></a>Lijst met modellen in account ophalen

Het volgende codeblok biedt een volledige lijst met beschikbare modellen in uw account, waaronder informatie over wanneer het model is gemaakt en de huidige status ervan.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]


### <a name="output"></a>Uitvoer

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Lijst met model-id’s per pagina ophalen

Dit codeblok biedt een gepagineerde lijst met modellen en model-id’s.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]


### <a name="output"></a>Uitvoer

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Model op id ophalen

Met de volgende functie wordt een model-id gebruikt om het overeenkomende modelobject op te halen. Deze functie wordt niet standaard aangeroepen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]


### <a name="delete-a-model-from-the-resource-account"></a>Een model uit het resourceaccount verwijderen

U kunt een model ook uit uw account verwijderen door naar de id te verwijzen. Met deze functie wordt het model met de opgegeven id verwijderd. Deze functie wordt niet standaard aangeroepen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]


### <a name="output"></a>Uitvoer

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="enable-logs"></a>Logboeken inschakelen

U kunt de volgende omgevingsvariabele instellen om logboeken voor foutopsporing weer te geven wanneer u deze bibliotheek gebruikt.

```
export DEBUG=azure*
```

Raadpleeg de [@azure/logger pakket-documenten](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger) voor gedetailleerdere instructies over het inschakelen van logboeken.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de clientbibliotheek van Form Recognizer voor JavaScript gebruikt om modellen te trainen en formulieren op verschillende manieren te analyseren. Vervolgens leert u tips voor het maken van een betere set met trainingsgegevens en het produceren van nauwkeurigere modellen.

> [!div class="nextstepaction"]
> [Een set met trainingsgegevens samenstellen](../../build-training-data-set.md)

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](../../overview.md)
* De voorbeeldcode uit deze gids is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js).
