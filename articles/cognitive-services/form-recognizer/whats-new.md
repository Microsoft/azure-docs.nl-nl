---
title: Wat is er nieuw in Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Meer informatie over de meest recente wijzigingen in Form Recognizer API.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: e952d481daf53b1806dc3cfbb658c8c0c21f6984
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516294"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-form-recognizer"></a>Wat is er nieuw in Form Recognizer?

De Form Recognizer-service wordt voortdurend bijgewerkt. Gebruik dit artikel om up-to-date te blijven met functieverbeteringen, oplossingen en documentatie-updates.

## <a name="april-2021"></a>April 2021
<!-- markdownlint-disable MD029 -->

### <a name="sdk-updates-api--version-21-preview3"></a>SDK-updates (API-versie 2.1-preview.3)

### <a name="c-version-310-beta4"></a>**C#-versie 3.1.0-beta.4**

* **Nieuwe methoden voor het analyseren van gegevens uit identiteitsdocumenten:**

   **[StartRecognizeIdDocumentsFromUriAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   **[StartRecognizeIdDocumentsAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   Zie Velden die zijn  [geëxtraheerd](concept-identification-cards.md#fields-extracted) in onze Form Recognizer voor een lijst met veldwaarden.

* De set met documenttalen die kan worden opgegeven voor de **[methode StartRecognizeContent](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)** is uitgebreid.

* **Nieuwe eigenschap `Pages`  die wordt ondersteund door de volgende klassen**:

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   Met `Pages` de eigenschap kunt u afzonderlijke of een reeks pagina's selecteren voor PDF- en TIFF-documenten met meerdere pagina's. Voer voor afzonderlijke pagina's het paginanummer in, bijvoorbeeld `3` . Voer voor een bereik van pagina's (zoals pagina 2 en pagina 5-7) de p-leeftijdsnummers en -reeksen in, gescheiden door komma's: `2, 5-7` .    

* **Nieuwe eigenschap `ReadingOrder` die wordt ondersteund voor de volgende klasse**:

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  De eigenschap is een optionele parameter waarmee u kunt opgeven welk leesorderalgoritme, of , moet worden toegepast om de extractie `ReadingOrder` `basic` van `natural` tekstelementen te orden. Als dit niet is opgegeven, is de standaardwaarde `basic` .

#### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

* De client wordt standaard ingesteld op de meest recente ondersteunde serviceversie, momenteel **2.1-preview.3.**

* **[De methode StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** geeft nu een wanneer `RequestFailedException()` een ongeldig bestand wordt doorgegeven.

### <a name="java-version-310-beta3"></a>**Java-versie 3.1.0-beta.3**

* **Nieuwe methoden voor het analyseren van gegevens uit identiteitsdocumenten:**

  **[beginRecognizeIdDocumentsFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocumentsfromurl?view=azure-java-preview&preserve-view=true)**

  **[beginRecognizeIdDocuments](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocuments?view=azure-java-preview&preserve-view=true)**

   Zie Velden die zijn  [geëxtraheerd](concept-identification-cards.md#fields-extracted) in onze documentatie Form Recognizer veldwaarden voor een lijst met veldwaarden.

* **Ondersteuning voor bitmap-afbeeldingsbestand (.bmp) voor aangepaste formulieren en trainingsmethoden in `FormContentType` de enum**:

* `image/bmp`

* **Nieuwe eigenschap `Pages`  die wordt ondersteund door de volgende klassen**:

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  Met de eigenschap kunt u afzonderlijke of een `Pages` reeks pagina's selecteren voor PDF- en TIFF-documenten met meerdere pagina's. Voer voor afzonderlijke pagina's het paginanummer in, bijvoorbeeld `3` . Voer voor een bereik van pagina's (zoals pagina 2 en pagina 5-7) de paginanummers en -reeksen in, gescheiden door komma's: `2, 5-7` .

* **Ondersteuning voor Bitmap Image File (.bmp) voor aangepaste formulieren en trainingsmethoden in de [velden FormContentType:](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields)**

  `image/bmp`

* **Nieuw `ReadingOrder` trefwoordargument dat wordt ondersteund voor de volgende methoden:**

* **[beginRecognizeContent](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?view=azure-java-preview&preserve-view=true)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   Het trefwoordargument is een optionele parameter waarmee u kunt opgeven welk leesalgoritme( of ) moet worden toegepast om de extractie `ReadingOrder` `basic` van `natural` tekstelementen te orden. Als dit niet wordt opgegeven, is de standaardwaarde `basic` .

* De client wordt standaard ingesteld op de meest recente ondersteunde serviceversie, momenteel **2.1-preview.3.**

### <a name="javascript-version-310-beta3"></a>**JavaScript-versie 3.1.0-beta.3**

* **Nieuwe methoden voor het analyseren van gegevens uit identiteitsdocumenten:**

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    Zie Velden die zijn  [geëxtraheerd](concept-identification-cards.md#fields-extracted) in onze Form Recognizer voor een lijst met veldwaarden.

* **Nieuwe veldwaarden toegevoegd aan de FieldValue-interface**:

    `gender`: mogelijke waarden zijn `M` `F` of `X` .</br>
   `country`: mogelijke waarden volgen de drieletterige landcodereeks iso [alpha-3.](https://www.iso.org/obp/ui/#search)

* **Nieuwe optie `pages` die wordt ondersteund door alle methoden voor formulierherkenning (aangepaste formulieren en alle vooraf gebouwde modellen). Met het argument kunt u afzonderlijke of een reeks pagina's selecteren voor PDF- en TIFF-documenten met meerdere pagina's. Voer voor afzonderlijke pagina's het paginanummer in, bijvoorbeeld `3` . Voer voor een bereik van pagina's (zoals pagina 2 en pagina 5-7) de paginanummers en -reeksen in, gescheiden door komma's: `2, 5-7` .

* Ondersteuning voor een **[ReadingOrder-type](/javascript/api/@azure/ai-form-recognizer/readingorder?view=azure-node-preview&preserve-view=true)** toegevoegd aan de methoden voor inhoudsherkenning. Met deze optie kunt u het algoritme bepalen dat de service gebruikt om te bepalen hoe herkende tekstregels moeten worden geordend. U kunt opgeven welk leesorderalgoritme, of , moet worden toegepast om `basic` `natural` de extractie van tekstelementen te orden. Als dit niet is opgegeven, is de standaardwaarde `basic` .

* **[Formulierveldtype splitsen](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** in verschillende interfaces. Dit mag geen API-compatibiliteitsproblemen veroorzaken, behalve in bepaalde edge-gevallen (niet-gedefinieerd valueType).

* Gemigreerd naar **het service-eindpunt 2.1-preview.3** Form Recognizer voor alle REST API aanroepen.

### <a name="python-version--310b4"></a>**Python-versie 3.1.0b4**

* **Nieuwe methoden voor het analyseren van gegevens uit identiteitsdocumenten:**

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  Zie Velden die zijn  [geëxtraheerd](concept-identification-cards.md#fields-extracted) in onze documentatie Form Recognizer veldwaarden voor een lijst met veldwaarden.

* **Nieuwe veldwaarden toegevoegd aan de [enum FieldValueType:](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true)**

   geslacht: mogelijke waarden zijn `M` `F` of `X` .

  country: mogelijke waarden volgen [ISO alpha-3 Country Codes](https://www.iso.org/obp/ui/#search).

* **Ondersteuning voor bitmap-afbeeldingsbestand (.bmp) voor aangepaste formulieren en trainingsmethoden in de enum [FormContentType:](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true)**

    image/bmp

* **Nieuw trefwoordargument `pages` dat wordt ondersteund door de volgende methoden:**

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   Met `pages` het trefwoordargument kunt u afzonderlijke of een reeks pagina's selecteren voor PDF- en TIFF-documenten met meerdere pagina's. Voer voor afzonderlijke pagina's het paginanummer in, bijvoorbeeld `3` . Voer voor een bereik van pagina's (zoals pagina 2 en pagina 5-7) de paginanummers en -reeksen in, gescheiden door komma's: `2, 5-7` .

* **Nieuw trefwoordargument `readingOrder` dat wordt ondersteund voor de volgende methoden:**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   Het trefwoordargument is een optionele parameter waarmee u kunt opgeven welk algoritme voor leesorders, of , moet worden toegepast om de extractie van tekstelementen `readingOrder` `basic` te `natural` orden. Als dit niet is opgegeven, is de standaardwaarde `basic` .

## <a name="march-2021"></a>Maart 2021

**Form Recognizer versie 2.1 openbare preview 3 is nu beschikbaar.** v2.1-preview.3 is uitgebracht, met inbegrip van de volgende functies:

* **Nieuw vooraf gemaakt id-model** Met het nieuwe vooraf gebouwde id-model kunnen klanten id's maken en gestructureerde gegevens retourneren om de verwerking te automatiseren. Het combineert onze krachtige OCR-mogelijkheden (Optical Character Recognition) met modellen voor het begrijpen van id's om belangrijke informatie te extraheren uit paspoorten en Amerikaanse stuurprogrammalicenties, zoals naam, geboortedatum, uitgiftedatum, vervaldatum en meer.

  [Meer informatie over het vooraf gebouwde id-model](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="passport-voorbeeld" lightbox="./media/id-canada-passport-example.png":::

* **Extractie van regelitem voor vooraf gebouwd factuurmodel:** vooraf gebouwd factuurmodel ondersteunt nu extractie van regelitem; Nu worden alle items en de onderdelen ervan geëxtraheert: beschrijving, hoeveelheid, product-id, datum en meer. Met een eenvoudige API/SDK-aanroep kunt u nuttige gegevens extraheren uit uw facturen: tekst, tabel, sleutel-waardeparen en regelitems.

   [Meer informatie over het vooraf gebouwde factuurmodel](concept-invoices.md)

* Labelen en trainen van tabellen onder **supervisie,** labelen met lege waarden: naast de geavanceerde deep [learning-mogelijkheden](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)van Form Recognizer kunnen klanten nu tabellen labelen en trainen. Deze nieuwe release bevat de mogelijkheid om regelitems/-tabellen te labelen en te trainen (dynamisch en vast) en een aangepast model te trainen om sleutel-waardeparen en regelitems te extraheren. Zodra een model is getraind, extraheert het model regelitems als onderdeel van de JSON-uitvoer in de sectie documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Tabellabels" lightbox="./media/table-labeling.png":::

    Naast het labelen van tabellen kunt u nu lege waarden en regio's labelen; Als sommige documenten in uw trainingsset geen waarden voor bepaalde velden hebben, kunt u ze labelen zodat uw model weet dat waarden correct moeten worden geëxtraheerd uit geanalyseerde documenten.

* **Ondersteuning voor 66 nieuwe talen:** Form Recognizer indelings-API en aangepaste modellen bieden nu ondersteuning voor 73 talen.

  [Meer informatie over Form Recognizer taalondersteuning van uw Form Recognizer](language-support.md)

* Natuurlijke **leesorde, handschriftclassificatie** en paginaselectie: met deze update kunt u ervoor kiezen om de uitvoer van de tekstregel op te halen in de natuurlijke leesorde in plaats van de standaardinstelling van links naar rechts en van boven naar beneden. Gebruik de nieuwe queryparameter readingOrder en stel deze in op 'natuurlijke' waarde voor een meer human-friendly reading order-uitvoer. Daarnaast classificeert u voor Latijnse talen Form Recognizer als handgeschreven stijl en geeft u een betrouwbaarheidsscore.

* **Kwaliteitsverbeteringen voor vooraf gebouwde bonmodellen** Deze update bevat veel kwaliteitsverbeteringen voor het vooraf gebouwde ontvangstbewijsmodel, met name wat betreft de extractie van regelitem.

## <a name="november-2020"></a>November 2020

### <a name="new-features"></a>Nieuwe functies

**Form Recognizer versie 2.1 openbare preview 2 is nu beschikbaar.** v2.1-preview.2 is uitgebracht, met inbegrip van de volgende functies:

- **Nieuw vooraf gebouwd factuurmodel:** met het nieuwe vooraf gebouwde factuurmodel kunnen klanten facturen in verschillende indelingen ontvangen en gestructureerde gegevens retourneren om de factuurverwerking te automatiseren. Het combineert onze krachtige OCR-mogelijkheden (Optical Character Recognition) met deep learning-modellen voor factuurbegrip om belangrijke informatie te extraheren uit facturen in het Engels. Er wordt sleuteltekst, tabellen en informatie geëxtraheerd, zoals klant, leverancier, factuur-id, factuurdatum, totaal, verschuldigd bedrag, btw-bedrag, verzenden naar en factureren aan.

  > [Meer informatie over het vooraf gebouwde factuurmodel](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="voorbeeld van factuur" lightbox="./media/invoice-example.jpg":::

- **Verbeterde tabelextractie:** Form Recognizer biedt nu verbeterde tabelextractie, waarin onze krachtige OCR-mogelijkheden (Optical Character Recognition) worden gecombineerd met een deep learning-model voor tabelextractie. Form Recognizer kunt gegevens extraheren uit tabellen, waaronder complexe tabellen met samengevoegde kolommen, rijen, geen randen en meer.

  :::image type="content" source="./media/tables-example.jpg" alt-text="voorbeeld van tabellen" lightbox="./media/tables-example.jpg":::


  > [Meer informatie over indelingsextractie](concept-layout.md)

- **Update van clientbibliotheek:** de nieuwste versies van de [clientbibliotheken](quickstarts/client-library.md) voor .NET, Python, Java en JavaScript ondersteunen de Form Recognizer 2.1 API.
- **Nieuwe ondersteunde taal: Japans** - De volgende nieuwe talen worden nu ondersteund: `AnalyzeLayout` voor en : `AnalyzeCustomForm` Japans ( `ja` ). [Taalondersteuning](language-support.md)
- Stijlindicatie voor tekstregel **(handgeschreven/andere) (alleen** Latijnse talen) - Form Recognizer geeft nu een object weer dat classificeert of elke tekstregel handgeschreven stijl is of niet, samen met een `appearance` betrouwbaarheidsscore. Deze functie wordt alleen ondersteund voor Latijnse talen.
- **Kwaliteitsverbeteringen:** extractieverbeteringen, waaronder extractieverbeteringen met één cijfer.
- **Nieuwe functie** voor uitproberen in het Form Recognizer-hulpprogramma voor voorbeeld en labelen: de mogelijkheid om vooraf ontwikkelde modellen voor facturen, ontvangstbewijzen en visitekaartjes en de indelings-API uit te proberen met behulp van het hulpprogramma Form Recognizer Sample Labeling. Bekijk hoe uw gegevens worden geëxtraheerd zonder code te schrijven.

  > [Het voorbeeldhulpprogramma Form Recognizer uitproberen](https://fott-preview.azurewebsites.net/)

  ![FOTT-voorbeeld](./media/ui-preview.jpg)

- **Feedbacklus:** wanneer u bestanden analyseert via het voorbeeldhulpprogramma voor labelen, kunt u deze nu ook toevoegen aan de trainingsset en de labels zo nodig aanpassen en trainen om het model te verbeteren.
- **Documenten automatisch labelen:** extra documenten worden automatisch gelabeld op basis van eerdere gelabelde documenten in het project.

## <a name="august-2020"></a>Augustus 2020

### <a name="new-features"></a>Nieuwe functies

**Form Recognizer openbare preview v2.1 is nu beschikbaar.** V2.1-preview.1 is uitgebracht, met inbegrip van de volgende functies:


- **REST API-referentie is beschikbaar** - De [naslag voor v2.1-preview.1 weergeven](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
- Nieuwe **ondersteunde** talen Naast Engels [](language-support.md) worden nu de volgende talen ondersteund: voor en `Layout` : Engels ( ), Chinees `Train Custom Model` `en` (vereenvoudigd) ( ), Nederlands ( ), Frans ( ), Duits ( ), Italiaans `zh-Hans` ( ), Portugees ( ) en Spaans ( `nl` `fr` `de` `it` `pt` `es` ).
- **Detectie van selectievakjes/selectiemarkeringen:** Form Recognizer detectie en extractie van selectiemarkeringen, zoals selectievakjes en keuzerondjes. Selectiemarkeringen worden geëxtraheerd in en u kunt nu ook trainen in Trainen met `Layout` `Train Custom Model`  -  _labels om_ sleutel-waardeparen voor selectiemarkeringen te extraheren.
- **Model samenstellen: hiermee** kunnen meerdere modellen worden samengesteld en aangeroepen met één model-id. Wanneer u een document indient dat moet worden geanalyseerd met een samengestelde model-id, wordt eerst een classificatiestap uitgevoerd om het naar het juiste aangepaste model te verzenden. Model opstellen is beschikbaar voor `Train Custom Model`  -  _Trainen met labels_.
- **Modelnaam:** voeg een gebruiksvriendelijke naam toe aan uw aangepaste modellen voor eenvoudiger beheer en tracering.
- **[Nieuw vooraf gebouwd model voor visitekaartjes voor het](concept-business-cards.md)** extraheren van algemene velden in het Engels, taal visitekaartjes.
- **[Nieuwe localen voor vooraf gebouwde bonnen](concept-receipts.md)** naast EN-US. Ondersteuning is nu beschikbaar voor EN-AU, EN-CA, EN-GB, EN-IN
- **Kwaliteitsverbeteringen** `Layout` voor , Trainen zonder `Train Custom Model`  -  _labels_ en _Trainen met labels_.

**v2.0 bevat** de volgende update:

- De [clientbibliotheken](quickstarts/client-library.md) voor NET, Python, Java en JavaScript zijn algemeen beschikbaar.

**Nieuwe voorbeelden** zijn beschikbaar op GitHub.

- Het playbook Knowledge [Extraction Recipes - Forms](https://github.com/microsoft/knowledge-extraction-recipes-forms) verzamelt best practices van echte Form Recognizer klantbetrokkenheid en biedt bruikbaar codevoorbeelden, controlelijsten en voorbeeldpijplijnen die worden gebruikt bij het ontwikkelen van deze projecten.
- Het [voorbeeldhulpprogramma voor labelen](https://github.com/microsoft/OCR-Form-Tools) is bijgewerkt om de nieuwe v2.1-functionaliteit te ondersteunen. Zie deze [quickstart om](quickstarts/label-tool.md) aan de slag te gaan met het hulpprogramma.
- In [het voorbeeld Form Recognizer](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) Intelligent Kiosk ziet u hoe u integreert en `Analyze Receipt` `Train Custom Model`  -  _traint zonder labels._

## <a name="july-2020"></a>Juli 2020

### <a name="new-features"></a>Nieuwe functies
<!-- markdownlint-disable MD004 -->
* **v2.0-referentie** beschikbaar: bekijk de [v2.0 API-verwijzing](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) en de bijgewerkte SDK's voor [.NET,](/dotnet/api/overview/azure/ai.formrecognizer-readme) [Python,](/python/api/overview/azure/) [Java](/java/api/overview/azure/ai-formrecognizer-readme)en [JavaScript.](/javascript/api/overview/azure/)
* **Tabelverbeteringen en** extractieverbeteringen: bevat nauwkeurigheidsverbeteringen en verbeteringen in tabelextracties, met name de mogelijkheid om tabellenheaders en -structuren te leren in aangepaste trainen zonder _labels._

* **Valutaondersteuning:** detectie en extractie van globale valutasymbolen.
* **Azure Gov:** Form Recognizer is nu ook beschikbaar in Azure Gov.
* **Verbeterde beveiligingsfuncties:**
  * **Bring Your Own Key:** Form Recognizer uw gegevens automatisch versleuteld wanneer ze worden opgeslagen in de cloud om deze te beveiligen en om u te helpen te voldoen aan de beveiligings- en nalevingsverplichtingen van uw organisatie. Uw abonnement maakt standaard gebruik van door Microsoft beheerde versleutelingssleutels. U kunt nu ook uw abonnement beheren met uw eigen versleutelingssleutels. Door de klant beheerde sleutels, ook wel [bring your own key (BYOK)](./encrypt-data-at-rest.md)genoemd, bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangsbesturingselementen. U kunt ook de versleutelingssleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.
  * **Privé-eindpunten:** hiermee kunt u in een virtueel netwerk veilig toegang krijgen tot gegevens [via een Private Link.](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>Juni 2020

### <a name="new-features"></a>Nieuwe functies

* **CopyModel-API toegevoegd aan client-SDK's:** u kunt nu de client-SDK's gebruiken om modellen van het ene naar het andere abonnement te kopiëren. Zie [Back-up en herstel van modellen](./disaster-recovery.md) voor algemene informatie over deze functie.
* **Azure Active Directory-integratie:** u kunt nu uw Azure AD-referenties gebruiken om uw clientobjecten Form Recognizer in de SDK's te verifiëren.
* **SDK-specifieke wijzigingen:** deze wijziging omvat zowel kleine functie-toevoegingen als belangrijke wijzigingen. Zie de SDK-changelogs voor meer informatie.
  * [C# SDK Preview 3 changelog](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 changelog](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 changelog](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK Preview 3 changelog](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>April 2020

### <a name="new-features"></a>Nieuwe functies

* **SDK-ondersteuning voor Form Recognizer API v2.0 Public Preview:** deze maand hebben we onze serviceondersteuning uitgebreid met een preview-SDK voor de release van Form Recognizer v2.0 (preview). Gebruik de onderstaande koppelingen om aan de slag te gaan met de taal van uw keuze:
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  De nieuwe SDK ondersteunt alle functies van het v2.0-REST API voor Form Recognizer. U kunt bijvoorbeeld een model trainen met of zonder labels en tekst, sleutel-waardeparen en tabellen uit uw formulieren extraheren, gegevens extraheren uit ontvangstbewijzen met de vooraf gebouwde ontvangstbewijzenservice en tekst en tabellen extraheren met de indelingsservice uit uw documenten. U kunt uw feedback over de SDK's delen via het [SDK-feedbackformulier](https://aka.ms/FR_SDK_v1_feedback).

* **Aangepast model kopiëren** U kunt nu modellen kopiëren tussen regio's en abonnementen met behulp van de nieuwe functie Aangepast model kopiëren. Voordat u de API Aangepast model kopiëren aanroept, moet u eerst autorisatie verkrijgen om naar de doelresource te kopiëren door de bewerking Autorisatie kopiëren aan te roepen voor het doelresource-eindpunt.

  * [Een autorisatie voor kopiëren genereren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [Een aangepast model kopiëren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API

### <a name="security-improvements"></a>Verbeterde beveiliging

* Customer-Managed sleutels zijn nu beschikbaar voor FormRecognizer. Zie Data encryption at rest voor meer [informatie Form Recognizer](./encrypt-data-at-rest.md).
* Beheerde identiteiten gebruiken voor toegang tot Azure-resources met Azure Active Directory. Zie Toegang verlenen tot beheerde identiteiten [voor meer informatie.](../authentication.md#authorize-access-to-managed-identities)

## <a name="march-2020"></a>Maart 2020

### <a name="new-features"></a>Nieuwe functies

* **Waardetypen voor labelen** U kunt nu de typen waarden opgeven die u labelt met het Form Recognizer voorbeeldhulpprogramma voor labelen. De volgende waardetypen en variaties worden momenteel ondersteund:
  * `string`
    * standaard, `no-whitespaces`, `alphanumeric`
  * `number`
    * standaard, `currency`
  * `date`
    * standaard, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Zie de [handleiding Voorbeeldhulpprogramma voor labelen](./quickstarts/label-tool.md#specify-tag-value-types) voor meer informatie over het gebruik van deze functie.


* **Tabelvisualisatie** Het voorbeeldhulpprogramma voor labelen geeft nu tabellen weer die in het document zijn herkend. Met deze functie kunt u de tabellen weergeven die zijn herkend en geëxtraheerd uit het document, voordat u ze labelt en analyseert. Deze functie kan worden in- of uitgeschakeld met behulp van de optie Lagen.

  De volgende afbeelding is een voorbeeld van hoe tabellen worden herkend en geëxtraheerd:

  > [!div class="mx-imgBorder"]
  > ![Tabelvisualisatie met behulp van het voorbeeldhulpprogramma voor labelen](./media/whats-new/table-viz.png)

    De geëxtraheerde tabellen zijn beschikbaar in de JSON-uitvoer onder `"pageResults"` .

  > [!IMPORTANT]
  > Het labelen van tabellen wordt niet ondersteund. Als tabellen niet automatisch worden herkend en extraeerd, kunt u ze alleen labelen als sleutel-waardeparen. Wanneer u tabellen labelt als sleutel-waardeparen, labelt u elke cel als een unieke waarde.

### <a name="extraction-enhancements"></a>Extractieverbeteringen

Deze release bevat extractieverbeteringen en nauwkeurigheidsverbeteringen, met name de mogelijkheid om meerdere sleutel-waardeparen in dezelfde tekstregel te labelen en te extraheren.

### <a name="sample-labeling-tool-is-now-open-source"></a>Voorbeeldhulpprogramma voor labelen is nu opensource

Het Form Recognizer voorbeeldhulpprogramma voor labelen is nu beschikbaar als een opensource-project. U kunt deze integreren in uw oplossingen en klantspecifieke wijzigingen aanbrengen om aan uw behoeften te voldoen.

Lees de documentatie die beschikbaar is op GitHub voor Form Recognizer [voorbeeldhulpprogramma voor labelen.](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)

### <a name="tls-12-enforcement"></a>TLS 1.2 afdwingen

TLS 1.2 wordt nu afgedwongen voor alle HTTP-aanvragen bij deze service. Zie [Beveiliging van Azure Cognitive Services](../cognitive-services-security.md) voor meer informatie.

## <a name="january-2020"></a>Januari 2020

In deze release wordt Form Recognizer versie 2.0 (preview) uitgebracht. In de onderstaande secties vindt u meer informatie over nieuwe functies, verbeteringen en wijzigingen.

### <a name="new-features"></a>Nieuwe functies

* **Aangepast model**
  * **Trainen met labels** U kunt nu een aangepast model trainen met handmatig gelabelde gegevens. Deze methode resulteert in beter presterende modellen en kan modellen produceren die werken met complexe formulieren of formulieren die waarden zonder sleutels bevatten.
  * **Asynchrone API** U kunt asynsync-API-aanroepen gebruiken om grote gegevenssets en bestanden te trainen en te analyseren.
  * **Ondersteuning voor TIFF-bestanden** U kunt nu trainen met en gegevens extraheren uit TIFF-documenten.
  * **Nauwkeurigheidsverbeteringen bij extractie**

* **Vooraf gebouwd ontvangstbewijsmodel**
  * **Fooien** U kunt nu fooien en andere handgeschreven waarden extraheren.
  * **Extractie van regelitem** U kunt regelitemwaarden extraheren uit bonnen.
  * **Betrouwbaarheidswaarden** U kunt de betrouwbaarheid van het model voor elke geëxtraheerde waarde bekijken.
  * **Nauwkeurigheidsverbeteringen bij extractie**

* **Indelingsextractie** U kunt nu de indelings-API gebruiken om tekstgegevens en tabelgegevens uit uw formulieren te extraheren.

### <a name="custom-model-api-changes"></a>Aangepaste model-API-wijzigingen

Alle API's voor het trainen en gebruiken van aangepaste modellen zijn hernoemd en sommige synchrone methoden zijn nu asynchroon. Hier volgen belangrijke wijzigingen:

* Het proces van het trainen van een model is nu asynchroon. U start de training via de **API-aanroep /custom/models.** Deze aanroep retourneert een bewerkings-id, die u kunt doorgeven aan **custom/models/{modelID}** om de trainingsresultaten te retourneren.
* Sleutel-waardeextractie wordt nu geïnitieerd door de API-aanroep **/custom/models/{modelID}/analyze.** Deze aanroep retourneert een bewerkings-id, die u kunt doorgeven aan **custom/models/{modelID}/analyzeResults/{resultID}** om de extractieresultaten te retourneren.
* Bewerkings-ID's voor de Train-bewerking zijn nu te vinden in de **Location-header** van HTTP-antwoorden, niet in de **Operation-Location-header.**

### <a name="receipt-api-changes"></a>Wijzigingen in de ontvangstbewijs-API

De naam van de API's voor het lezen van verkoopbonnen is gewijzigd.

* Extractie van ontvangstgegevens wordt nu gestart door de **API-aanroep /prebuilt/receipt/analyze.** Deze aanroep retourneert een bewerkings-id, die u kunt doorgeven aan **/prebuilt/receipt/analyzeResults/{resultID}** om de extractieresultaten te retourneren.

### <a name="output-format-changes"></a>Wijzigingen in de uitvoerindeling

De JSON-antwoorden voor alle API-aanroepen hebben nieuwe indelingen. Sommige sleutels en waarden zijn toegevoegd, verwijderd of hernoemd. Zie de quickstarts voor voorbeelden van de huidige JSON-indelingen.

## <a name="next-steps"></a>Volgende stappen

Voltooi een [quickstart om](quickstarts/client-library.md) aan de slag te gaan met het schrijven van een app voor Form Recognizer in de ontwikkeltaal van uw keuze.

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](./overview.md)