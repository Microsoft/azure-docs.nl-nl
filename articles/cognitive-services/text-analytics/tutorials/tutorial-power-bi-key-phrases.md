---
title: 'Zelfstudie: Power BI Desktop integreren met de Text Analytics Cognitive service'
titleSuffix: Azure Cognitive Services
description: Leer hoe u de Text Analytics API gebruikt om belangrijke woordgroepen te extraheren uit tekst die in Power BI is opgeslagen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 8444ae08aa2c25c20723b2f8c571422af3b24bc8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736675"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Zelfstudie: Power BI Desktop integreren met de Text Analytics Cognitive service

Microsoft Power BI Desktop is een gratis toepassing waarmee u verbinding kunt maken met uw gegevens en deze kunt transformeren en visualiseren. De Text Analytics-service is onderdeel van Microsoft Azure Cognitive Services, en biedt verwerking van natuurlijke taal. Bij onbewerkte ongestructureerde tekst, kan het de belangrijkste woordgroepen extraheren, sentiment analyseren en belangrijke entiteiten identificeren zoals merken. Samen kunnen deze hulpmiddelen u helpen snel te zien waar uw klanten het over hebben en wat ze ervan vinden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik Power BI Desktop om gegevens te importeren en te transformeren
> * Maak een aangepaste functie Power BI Desktop
> * Power BI Desktop integreren met de sleuteltermextractie-API van Text Analytics
> * De sleuteltermextractie-API van Text Analytics gebruiken om de belangrijkste fragmenten uit feedback van klanten te halen
> * We maken ook een woordwolk van klantenfeedback

## <a name="prerequisites"></a>Vereisten
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop. [Download dit gratis](https://powerbi.microsoft.com/get-started/).
- Een Microsoft Azure-account. [Een gratis account maken](https://azure.microsoft.com/free/cognitive-services/) en [aanmelden](https://portal.azure.com/).
- Een account voor Cognitive Services-API met de Text Analytics-API. Als u geen account hebt kunt zich hier [aanmelden](../../cognitive-services-apis-create-account.md) en gratis 5000 transacties per maand uitvoeren (zie [prijsinformatie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) om deze zelfstudie te voltooien.
- De [Text Analytics-toegangssleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)die voor u is gegenereerd tijden de registratie.
- Opmerkingen van klanten. U kunt [onze voorbeeldgegevens](https://aka.ms/cogsvc/ta) gebruiken of uw eigen gegevens. In deze zelfstudie wordt ervan uitgegaan dat u onze voorbeeldgegevens gebruikt.

## <a name="load-customer-data"></a>Laden klantgegevens
<a name="LoadingData"></a>

Om aan de slag te gaan, opent u Power BI Desktop en laadt u het CSV-bestand (door komma’s gescheiden waarden) `FabrikamComments.csv`dat u hebt gedownload in [Vereisten](#Prerequisites). Dit bestand vertegenwoordigt een hele dag aan hypothetische activiteit op een ondersteuningsforum van een fictief klein bedrijf.

> [!NOTE]
> Power BI kan gegevens gebruiken uit vele verschillende bronnen, zoals Facebook of een SQL-database. Meer informatie in [Facebook-integratie met Power BI](https://powerbi.microsoft.com/integrations/facebook/) en [SQL Server-integratie met Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

Kies in het hoofdvenster van Power BI Desktop het **Start** lint. In de **Externe gegevens** groep van het lint, open de **Gegevens ophalen** vervolgkeuzelijst en selecteer **tekst/SCV**.

![[De knop Gegevens ophalen]](../media/tutorials/power-bi/get-data-button.png)

Het dialoogvenster Openen verschijnt. Ga naar de map waar het `FabrikamComments.csv` bestand hebt gedownload. Klik op `FabrikamComments.csv` en klik vervolgens op de knop **Openen**. Het dialoogvenster CSV-import verschijnt.

![[Het dialoogvenster CSV-import]](../media/tutorials/power-bi/csv-import.png)

In het dialoogvenster CSV-import kunt u verifiëren dat Power BI Desktop de tekenset, het scheidingsteken, de veldnamenrijen en de kolomtypen correct heeft gedetecteerd. Deze gegevens kloppen allemaal, dus klik op **Laden**.

Om de geladen gegevens te zien, klik op de **Gegevensweergave**-knop aan de linkerkant van de Power BI-werkruimte. Er wordt een tabel geopend die de gegevens bevat, net zoals in Microsoft Excel.

![[De eerste weergave van de geïmporteerde gegevens]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>De gegevens voorbereiden
<a name="PreparingData"></a>

U moet uw gegevens in Power BI Desktop mogelijk transformeren voordat ze klaar zijn om te worden verwerkt door de sleuteltermen-API van de Text Analytics-service.

De voorbeeldgegevens bevat een `subject` kolom en een `comment` kolom. Met de functie kolommen samenvoegen in Power BI Desktop kunt u sleuteltermen extraheren van de gegevens in beide kolommen, in plaats van alleen de kolom `comment`.

Kies in Power BI Desktop het **Start** lint. In de groep **Externe gegevens** klikt u op **Query’s bewerken**.

![[De groep Externe gegevens op het Start-lint]](../media/tutorials/power-bi/edit-queries.png)

Selecteer `FabrikamComments` in de lijst **Query’s** aan de linkerkant van het venster als dit nog niet is geselecteerd.

Selecteer nu zowel de kolom `subject` als `comment` in de tabel. U moet mogelijk horizontaal scrollen om deze kolommen te zien. Klik eerst op de kolomkop `subject`, houd vervolgens de Ctrl-toets ingedrukt en klik op de kolomkop `comment`.

![[Samen te voegen velden selecteren]](../media/tutorials/power-bi/select-columns.png)

Selecteer het **Transformeren**-lint. Klik op **Tekstkolommen** -groep in het lint, klik **Tekstkolommen samenvoegen**. Het dialoogvenster Kolommen samenvoegen verschijnt.

![[Velden samenvoegen via het dialoogvenster Kolommen samenvoegen]](../media/tutorials/power-bi/merge-columns.png)

Kies in het dialoogvenster Kolommen samenvoegen, klik op `Tab` als het scheidingsteken en klik vervolgens op **OK.**

U kunt ook overwegen lege berichten te verwijderen via het filter Leeg verwijderen of niet-afdrukbare tekens te verwijderen via de transformatie Opschonen. Als uw gegevens een kolom bevatten zoals de kolom `spamscore` in het voorbeeldbestand, kunt u ‘spam’-opmerkingen overslaan met behulp van een getalfilter.

## <a name="understand-the-api"></a>De API begrijpen
<a name="UnderstandingAPI"></a>

De [Sleuteltermen-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/operations/KeyPhrases) van de Text Analytics-service kan maximaal duizend tekstdocumenten per HTTP-aanvraag verwerken. Power BI verwerkt records echter liever met één tegelijk, dus zullen in deze zelfstudie uw aanvragen aan de API slechts één document bevatten. De Sleuteltermen-API vereist de volgende velden voor elk document dat wordt verwerkt.

| Veld | Beschrijving |
| - | - |
| `id`  | Een unieke ID voor dit document in de aanvraag. Het antwoord bevat dit veld ook. Zo kunt u bij het verwerken van meerdere documenten de geëxtraheerde sleuteltermen gemakkelijk koppelen aan het document waaruit ze kwamen. Omdat u in deze zelfstudie één document per aanvraag verwerkt kunt u de waarde van `id` als harde code invoeren om deze hetzelfde te laten zijn voor elke aanvraag.|
| `text`  | De tekst die moet worden verwerkt. De waarde van dit veld komt uit de kolom `Merged` die u hebt gemaakt in de [vorige sectie](#PreparingData), die de gecombineerde onderwerpregel en opmerkingstekst bevat. De Key Phrases-API vereist dat deze gegevens niet langer dan ongeveer 5.120 tekens zijn.|
| `language` | De code die de natuurlijke taal waarin het document is geschreven. Alle berichten in de voorbeeldgegevens zijn in het Engels, zodat u dit in code kunt zetten met de waarde `en` voor dit veld.|

## <a name="create-a-custom-function"></a>Maak een aangepaste functie
<a name="CreateCustomFunction"></a>

Nu bent u klaar om de aangepaste functie te maken die Power BI en Text Analytics zal integreren. De functie ontvangt de te verwerken tekst als een parameter. Het converteert gegevens naar en vanuit het vereiste JSON-formaat en maakt de HTTP-aanvraag naar de Sleuteltermen-API. Deze functie parseert daarna het antwoord van de API en retourneert een tekenreeks die een door komma’s gescheiden lijst met de geëxtraheerde sleuteltermen bevat.

> [!NOTE]
> Aangepaste Power BI Desktop-functies worden geschreven in de [formuletaal Power Query M](/powerquery-m/power-query-m-reference), of kort gezegd gewoon ‘M’. M is een functionele programmeertaal die op [F#](/dotnet/fsharp/) is gebaseerd. U hoeft echter geen programmeur te zijn om deze studieles te kunnen voltooien; de benodigde code staat hieronder.

Controleer dat u zich nog steeds bevindt in het venster Query-Editor in Power BI Desktop. Indien dit niet het geval is, kies het **Start**-lint en klik in de groep **Externe gegevens** op **Query’s bewerken**.

In het **Start**-lint, in de groep **Nieuwe query**, open de vervolgkeuzelijst **Nieuwe bron** en kies **Blanco query**. 

Er verschijnt een nieuwe query met de initiële naam `Query1` in de lijst met query’s. Dubbelklik op deze query en wijzig de naam in `KeyPhrases`.

In het **Start**-lint, in de groep **Query**, klik op de **Geavanceerde editor** om het venster Geavanceerde editor te openen. Verwijder de code die al in dat venster staat, en plak de volgende code. 

> [!NOTE]
> Vervang het onderstaande voorbeeld-eindpunt (met `<your-custom-subdomain>`) door het eindpunt dat voor uw Text Analytics-resource is gegenereerd. U kunt dit eindpunt vinden door u aan te melden bij de [Azure-portal](https://azure.microsoft.com/features/azure-portal/), uw Text Analytics-abonnement te selecteren en `Quick start` te selecteren.


```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics" & "/v3.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Vervang `YOUR_API_KEY_HERE` door uw geldige Text Analytics-abonnementssleutel. U kunt deze sleutel ook vinden door u aan te melden bij de [Azure Portal](https://azure.microsoft.com/features/azure-portal/), uw Text Analytics-abonnement te selecteren en de overzichtspagina op te roepen. Zorg dat u de aanhalingstekens voor en na de sleutel laat staan. Klik vervolgens op **Gereed**.

## <a name="use-the-custom-function"></a>Aangepaste functie gebruiken
<a name="UseCustomFunction"></a>

We kunnen de aangepaste functie nu gebruiken om de sleuteltermen uit elke klantopmerking op te halen en in een nieuwe kolom in de tabel op te slaan. 

In Power BI Desktop, in het venster Query-Editor gaat u terug naar de `FabrikamComments` query. Selecteer het **Kolom toevoegen**-lint. In de **algemene**-groep, klikt u op **Aangepaste functie aanroepen**.

![[Knop Aangepaste functie aanroepen]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Het dialoogvenster aangepaste functie aanroepen wordt weergegeven. In **Nieuwe kolomnaam**, voert u `keyphrases` in. In **Functie query**, selecteert u de aangepaste functie die u hebt gemaakt, `KeyPhrases`.

Een nieuw veld wordt weergegeven in het dialoogvenster **tekst (optioneel)** . Dit veld vraagt welke kolom we willen gebruiken voor de waarden van de `text` parameter van de Sleuteltermen-API. (Houd er rekening mee dat u al de waarden voor de parameters `language` en `id` hebt vastgelegd in de code) Kies `Merged` (de kolom die we [eerder](#PreparingData) hebben gemaakt door het onderwerp- en berichtveld samen te voegen) uit de vervolgkeuzelijst.

![[Een aangepaste functie aanroepen]](../media/tutorials/power-bi/invoke-custom-function.png)

Klik ten slotte op **OK**.

Als alles gereed is, roept Power BI uw aangepaste functie eenmaal voor elke rij in de tabel. Het verzendt de query's naar Sleuteltermen-API en voegt een nieuwe kolom toe aan de tabel voor het opslaan van de resultaten. Maar voordat dat gebeurt, moet u mogelijk verificatie- en privacyinstellingen specificeren.

## <a name="authentication-and-privacy"></a>Verificatie en privacy
<a name="Authentication"></a>

Nadat u het dialoogvenster Aangepaste functie aanroepen hebt gesloten, verschijnt er mogelijk een banner waarin u wordt gevraagd op te geven hoe er verbinding moet worden gemaakt met de Sleuteltermen-API.

![[referentiebanner]](../media/tutorials/power-bi/credentials-banner.png)

Klik op **Referenties bewerken**, zorg dat `Anonymous` is geselecteerd in het dialoogvenster en klik vervolgens op **Verbinding maken.** 

> [!NOTE]
> U kiest `Anonymous` omdat de Text Analytics-service u verifieert met behulp van de toegangssleutel, zodat Power BI zelf geen referenties voor de HTTP-aanvraag hoeft te bieden.

> [!div class="mx-imgBorder"]
> ![[verificatie instellen op anoniem]](../media/tutorials/power-bi/access-web-content.png)

Als u de banner ‘Referenties bewerken’ ziet nadat u anonieme toegang hebt gekozen, bent u mogelijk vergeten uw Text Analytics-sleutel in de code te plakken in de `KeyPhrases`[Aangepaste functie](#CreateCustomFunction).

Vervolgens verschijnt er mogelijk een banner waarin u wordt gevraagd informatie op te geven over de privacy van uw gegevensbronnen. 

![[privacybanner]](../media/tutorials/power-bi/privacy-banner.png)

Klik op **Doorgaan** en kies `Public` voor elk van de gegevensbronnen in het dialoogvenster. Klik vervolgens op **Opslaan**.

![[privacy van gegevensbronnen instellen]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>Maak een woordwolk
<a name="WordCloud"></a>

Zodra u eventueel verschenen banners hebt afgehandeld, klikt u op **Sluiten en toepassen** op het Start-lint om de Query-editor te sluiten.

Het duurt even voordat Power BI Desktop de benodigde HTTP-aanvragen heeft gemaakt. Voor elke rij in de tabel bevat de nieuwe kolom `keyphrases` de belangrijke woordgroepen die de Key Phrases-API in de tekst heeft gedetecteerd. 

Nu gebruikt u deze kolom gebruiken om een woordwolk te genereren. Om aan de slag te gaan, klikt u in het hoofdvenster van Power BI Desktop op de knop **Rapport**, links van de werkruimte.

> [!NOTE]
> Waarom zou u geëxtraheerde belangrijke woordgroepen gebruiken om een woordwolk te genereren, in plaats van de volledige tekst van elke opmerking? De belangrijke woordgroepen bieden ons de *belangrijke* woorden uit onze klantopmerkingen, niet alleen de *meest voorkomende* woorden. Bovendien is de woordgrootte in de resulterende wolk niet vertekend door het veelvuldig gebruik van een woord in een relatief klein aantal opmerkingen.

Als de aangepaste visual Word Cloud nog niet is geïnstalleerd, installeer deze dan. Klik in het deelvenster Visualisaties rechts van de werkruimte op de drie puntjes ( **...** ) en kies **Importeren vanuit Marketplace**. Zoek vervolgens naar ‘cloud’ en klik op de knop **Toevoegen** naast de Word Cloud-visual. Power BI installeert de Word Cloud-visual en laat u weten dat deze succesvol is geïnstalleerd.

![[een aangepaste visual toevoegen]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Klik eerst op het Word Cloud-pictogram in het deelvenster Visualisaties.

![[Word Cloud-pictogram in deelvenster Visualisaties]](../media/tutorials/power-bi/visualizations-panel.png)

Er verschijnt een nieuw rapport in de werkruimte. Sleep het veld `keyphrases` in het deelvenster Velden naar het veld Categorie in het deelvenster Visualisaties. De woordwolk verschijnt in het rapport.

Ga nu naar de pagina Opmaak van het deelvenster Visualisaties. Schakel **Standaard stopwoorden** in de categorie Stopwoorden in om korte, algemene woorden zoals ’van’ uit de cloud te elimineren. 

![[standaard stopwoorden activeren]](../media/tutorials/power-bi/default-stop-words.png)

Schakel even verderop in dit deelvenster **Tekst draaien** en **Titel** uit.

![[focusmodus activeren]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Klik op het hulpmiddel Focusmodus in het rapport om onze woordwolk beter te bekijken. Het hulpmiddel breidt de woordwolk uit om de hele werkruimte te vullen, zoals hieronder weergegeven.

![[een woordwolk]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Meer Text Analytics-services
<a name="MoreServices"></a>

De Text Analytics-service, een van de Cognitive Services die worden aangeboden door Microsoft Azure, biedt ook sentimentanalyse en taaldetectie. Met name de taaldetectie is handig als uw klantenfeedback niet allemaal in het Engels is.

Deze andere API's lijken allebei heel erg op de Sleuteltermen-API. Dit betekent dat u ze kan integreren met Power BI Desktop met behulp van aangepaste functies die bijna identiek zijn aan de functies die u in deze zelfstudie hebt gemaakt. Maak gewoon een lege query en plak de toepasselijke code hieronder in de Geavanceerde editor, net zoals u eerder hebt gedaan. (Vergeet uw toegangssleutel niet!) Gebruik de functie vervolgens, net zoals eerder, om een nieuwe kolom aan de tabel toe te voegen.

De functie Sentimentanalyse hieronder retourneert een score die aangeeft hoe positief het in de tekst uitgedrukte sentiment is.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[confidenceScores]
in  sentiment
```

Hier zijn twee versies van een taaldetectiefunctie. De eerste retourneert de ISO-taalcode (bijvoorbeeld `en` voor het Engels), terwijl de tweede de ‘vriendelijke’ naam (bijvoorbeeld `English`) retourneert. U merkt wellicht dat alleen de laatste regel van de hoofdtekst verschilt tussen de twee versies.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Tot slot is hier een variant van de Key Phrases-functie die we al hebben laten zien, die de woordgroepen retourneert als een lijstobject in plaats van als één tekenreeks met door komma’s gescheiden woordgroepen. 

> [!NOTE]
> Het retourneren van één tekenreeks vereenvoudigde ons voorbeeld van een woordwolk. Een lijst is echter een flexibelere indeling om te werken met de geretourneerde woordgroepen in Power BI. U kunt lijstobjecten in Power BI Desktop bewerken via de groep Gestructureerde kolom op het lint Transformeren in de Query-editor.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Volgende stappen
<a name="NextSteps"></a>

Leer meer over de Text Analytics-service, de formuletaal Power Query M of Power BI.

> [!div class="nextstepaction"]
> [Naslaginformatie over de Text Analytics-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0)

> [!div class="nextstepaction"]
> [Naslaginformatie over Power Query M](/powerquery-m/power-query-m-reference)

> [!div class="nextstepaction"]
> [Documentatie voor Power BI](https://powerbi.microsoft.com/documentation/powerbi-landing-page/)