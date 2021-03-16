---
title: 'Quickstart: Formulieren labelen, een model trainen en formulieren analyseren met behulp van het voorbeeldhulpprogramma voor labelen, Form Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u Form Recognizer, het voorbeeldhulpprogramma voor labelen om formulierdocumenten handmatig te labelen. Vervolgens traint u een aangepast documentverwerkingsmodel met de gelabelde documenten en gebruikt u het model om sleutel-waardeparen te extraheren.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: documentverwerking
ms.openlocfilehash: 89de0752b3015fb8132bfa50c7dbdce174061bcc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467261"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Een Form Recognizer-model trainen met behulp van het voorbeeldhulpprogramma voor labelen

In deze quickstart gebruikt u de REST API van Form Recognizer met het voorbeeldhulpprogramma voor labelen om een aangepast documentverwerkingsmodel te trainen met handmatig gelabelde gegevens. Zie de sectie [Trainen met labels](../overview.md#train-with-labels) van het overzicht voor meer informatie over leren onder supervisie met Form Recognizer.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Wanneer u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Een Form Recognizer-resource maken"  target="_blank">een Form Recognizer-resource maken </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.
  * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Form Recognizer API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
  * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Een set van minimaal zes formulieren van hetzelfde type. U gebruikt deze gegevens om het model te trainen en een formulier te testen. U kunt een [voorbeeldgegevensverzameling](https://go.microsoft.com/fwlink/?linkid=2090451) gebruiken voor deze quickstart (download en extraheer *sample_data.zip*). Upload de trainingsbestanden naar de hoofdmap van een Blob Storage-container in een Azure Storage-account met een standaardprestatielaag.

## <a name="create-a-form-recognizer-resource"></a>Een Form Recognizer-resource maken

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>Probeer het eens

Als u het Form Recognizer-voorbeeldhulpprogramma voor labelen online wilt uitproberen, gaat u naar de [website van FOTT](https://fott-preview.azurewebsites.net/).

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott.azurewebsites.net/)

---

U hebt een Azure-abonnement ([maak gratis een account](https://azure.microsoft.com/free/cognitive-services)) en een eindpunt en sleutel voor de [Form Recognizer-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) nodig om de Form Recognizer-service te kunnen uitproberen.

## <a name="set-up-the-sample-labeling-tool"></a>Het voorbeeldhulpprogramma voor labelen instellen

U gebruikt de Docker-engine voor het uitvoeren van het voorbeeldhulpprogramma voor labelen. Volg deze stappen om de Docker-container in te stellen. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.

> [!TIP]
> Het OCR-voorbeeldhulpprogramma voor labelen is ook beschikbaar als een opensourceproject op GitHub. Het hulpprogramma is een TypeScript-webtoepassing die is gebouwd met React + Redux. Zie de opslagplaats [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) (OCR-voorbeeldhulpprogramma voor labelen) voor meer informatie of als u een bijdrage wilt leveren. Als u het hulpprogramma online wilt uitproberen, gaat u naar de [FOTT-website](https://fott.azurewebsites.net/).

1. Installeer eerst Docker op een hostcomputer. In deze handleiding wordt uitgelegd hoe u een lokale computer als host kunt gebruiken. Als u een Docker-hostingservice in Azure wilt gebruiken, raadpleegt u de instructiegids [Het voorbeeldhulpprogramma voor labelen implementeren](../deploy-label-tool.md).

   De hostcomputer moet voldoen aan de volgende hardwarevereisten:

    | Container | Minimum | Aanbevolen|
    |:--|:--|:--|
    |Voorbeeldhulpprogramma voor labelen|2 kernen, 4 GB geheugen|4 kernen, 8 GB geheugen|

   Installeer Docker op uw computer door de juiste instructies te volgen voor uw besturingssysteem:

   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [MacOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Haal de container voor het voorbeeldhulpprogramma voor labelen op met de opdracht `docker pull`.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```console
 docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```console
docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
```

---
</br>
  3. U bent nu klaar om de container met `docker run` uit te voeren.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

```console
 docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```console
docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
```

---

   Met deze opdracht wordt het voorbeeldhulpprogramma voor labelen beschikbaar gemaakt via een webbrowser. Ga naar `http://localhost:3000`.

> [!NOTE]
> U kunt ook documenten labelen en modellen trainen met behulp van de REST API van Form Recognizer. Als u wilt trainen en analyseren met de REST API, raadpleegt u [Trainen met labels met behulp van de REST API en Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="set-up-input-data"></a>Invoergegevens instellen

Zorg er eerst voor dat alle trainingsdocumenten dezelfde indeling hebben. Als u formulieren in meerdere indelingen hebt, kunt u deze op basis van hun gemeenschappelijke indeling in submappen ordenen. Wanneer u traint, moet u de API instellen op een submap.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Delen van resources voor meerdere domeinen (CORS) configureren

Schakel CORS in voor uw opslagaccount. Selecteer uw opslag account in de Azure Portal en kies vervolgens het tabblad **CORS** in het linkerdeel venster. Vul in de onderste regel de volgende waarden in. Selecteer bovenaan **Opslaan** .

* Toegestane oorsprongen = *
* Toegestane methoden = \[alles selecteren\]
* Toegestane headers = *
* Zichtbare headers = *
* Maximumleeftijd = 200

> [!div class="mx-imgBorder"]
> ![Instellen van CORS in Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Verbinding maken met het voorbeeldhulpprogramma voor labelen

 Het hulp programma voor het labelen van het voor beeld maakt verbinding met een bron (uw oorspronkelijke geüploade formulieren) en een doel (gemaakte labels en uitvoer gegevens).

Verbindingen kunnen worden projectbreed worden ingesteld en gedeeld. Ze maken gebruik van een uitbreidbaar providermodel, zodat u eenvoudig nieuwe bron-/doelproviders kunt toevoegen.

Als u een nieuwe verbinding wilt maken, selecteert u het pictogram **nieuwe verbindingen** (plug) in de linkernavigatiebalk.

Vul de velden in met de volgende waarden:

* **Weergavenaam**: de weergavenaam van de verbinding.
* **Beschrijving**: de beschrijving van het project.
* **SAS-URL**: de Shared Access Signature-URL (SAS-URL) van de Azure Blob Storage-container. [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS-URL ophalen":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="Verbindingsinstellingen van het voorbeeldhulpprogramma voor labelen.":::

## <a name="create-a-new-project"></a>Een nieuw project maken

In het voorbeeldhulpprogramma voor labelen worden uw configuraties en instellingen opgeslagen in projecten. Maak een nieuw project en vul de velden in met de volgende waarden:

* **Weergavenaam** : de weergavenaam van het project
* **Beveiligingstoken** : sommige projectinstellingen kunnen gevoelige waarden bevatten, zoals API-sleutels of andere gedeelde geheimen. Elk project genereert een beveiligingstoken dat kan worden gebruikt voor het versleutelen/ontsleutelen van gevoelige projectinstellingen. U kunt beveiligings tokens vinden in de toepassings instellingen door onder aan de linkernavigatiebalk het tandwiel pictogram te selecteren.
* **Bronverbinding**: de Azure Blob Storage-verbinding die u in de vorige stap hebt gemaakt en die u voor dit project wilt gebruiken.
* **Mappad**: optioneel: als uw bronformulieren zich in een map in de Blobcontainer bevinden, geeft u de naam van de map hier op
* **URI van de Form Recognizer-service**: de eindpunt-URL van de Form Recognizer.
* **API-sleutel**: de abonnementssleutel van de Form Recognizer.
* **Beschrijving**: optioneel: projectbeschrijving

:::image type="content" source="../media/label-tool/new-project.png" alt-text="Pagina met nieuw project in voorbeeldhulpprogramma voor labelen.":::

## <a name="label-your-forms"></a>Formulieren labelen

Wanneer u een project maakt of opent, wordt hoofdvenster van de tageditor geopend. De tageditor bestaat uit drie delen:

* Een voorbeeldvenster met een verstelbaar formaat dat een te scrollen lijst met formulieren van de bronverbinding bevat.
* Het hoofdvenster van de tageditor waarmee u labels kunt toepassen.
* Het deelvenster van de tageditor waarmee gebruikers labels kunnen wijzigen, vergrendelen, opnieuw ordenen en verwijderen.

### <a name="identify-text-and-tables"></a>Tekst en tabellen identificeren 

Selecteer **OCR uitvoeren op alle bestanden** in het linkerdeel venster om de tekst-en tabel indelings informatie voor elk document op te halen. Er worden begrenzingsvakken rond elk tekstelement getekend.

In het hulp programma labelen worden ook de tabellen weer gegeven die automatisch zijn geëxtraheerd. Selecteer het tabel/raster pictogram aan de linkerkant van het document om de geëxtraheerde tabel weer te geven. Omdat de tabelinhoud automatisch wordt geëxtraheerd, zullen we de tabelinhoud in deze quickstart niet labelen maar zullen we vertrouwen op de geautomatiseerde extractie.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Tabelvisualisatie in voorbeeldhulpprogramma voor labelen.":::

Als in uw trainings document in v 2.1 geen waarde is ingevuld, kunt u een vak tekenen waarin de waarde moet zijn. Gebruik **teken gebied** in de linkerbovenhoek van het venster om de regio taggable te maken.

### <a name="apply-labels-to-text"></a>Labels op tekst toepassen

Vervolgens maakt u tags (labels) en past u deze toe op de tekst elementen die door het model moeten worden geanalyseerd.

### <a name="v20"></a>[v2.0](#tab/v2-1)  

1. Gebruik eerst het deelvenster van de tageditor om de labels te maken die u wilt identificeren.
   1. Selecteer deze optie **+** om een nieuwe tag te maken.
   1. Voer de naam van het label in.
   1. Druk op Enter om het label op te slaan.
1. Selecteer in de hoofd editor woorden uit de gemarkeerde tekst elementen of een regio die u hebt getekend.
1. Selecteer het label dat u wilt Toep assen of druk op de bijbehorende toetsenbord toets. De numerieke toetsen zijn toegewezen als sneltoetsen voor de eerste tien labels. U kunt de volgorde van de labels wijzigen met behulp van de pijlen omhoog en omlaag in het tageditorvenster.
    > [!Tip]
    > Houd rekening met de volgende tips wanneer u een label maakt van uw formulieren:
    >
    > * U kunt slechts één label per geselecteerd tekstelement toepassen.
    > * Elk label kan slechts eenmaal per pagina worden toegepast. Als een waarde meerdere keren op hetzelfde formulier wordt weergegeven, maakt u voor elk exemplaar verschillende labels. Bijvoorbeeld: factuur 1, factuur 2, enzovoort.
    > * Een label kan niet op meerdere pagina's worden toegepast.
    > * De labelwaarden zijn zoals ze op het formulier worden weergegeven. Splits geen waarde in twee delen met twee verschillende labels. Zo moet bijvoorbeeld een adresveld met één label worden gelabeld, ook als het meerdere regels omvat.
    > * Voeg geen sleutels aan de velden met labels toe, alleen de waarden.
    > * Tabelgegevens moeten automatisch worden gedetecteerd en worden beschikbaar in het laatste JSON-uitvoerbestand. Als het model echter niet alle tabelgegevens detecteert, kunt u deze velden ook handmatig labelen. Label elke cel in de tabel met een ander label. Als uw formulieren tabellen met een wisselend aantal rijen bevatten, moet u ervoor zorgen dat u ten minste één formulier met de grootste mogelijke tabel labelt.
    > * Gebruik de knoppen rechts van de **+** om uw labels te zoeken, een nieuwe naam te geven, opnieuw in te delen en te verwijderen.
    > * Als u een toegepast label wilt verwijderen zonder het label zelf te verwijderen, selecteert u de gemarkeerde rechthoek in de documentweergave en drukt u op de verwijdertoets.
    >

### <a name="v20"></a>[v2.0](#tab/v2-0)

1. Gebruik eerst het deelvenster van de tageditor om de labels te maken die u wilt identificeren.
   1. Selecteer deze optie **+** om een nieuwe tag te maken.
   1. Voer de naam van het label in.
   1. Druk op Enter om het label op te slaan.
1. Selecteer in de hoofd editor woorden uit de gemarkeerde tekst elementen.
1. Selecteer het label dat u wilt Toep assen of druk op de bijbehorende toetsenbord toets. De numerieke toetsen zijn toegewezen als sneltoetsen voor de eerste tien labels. U kunt de volgorde van de labels wijzigen met behulp van de pijlen omhoog en omlaag in het tageditorvenster.
    > [!Tip]
    > Houd rekening met de volgende tips wanneer u een label maakt van uw formulieren:
    >
    > * U kunt slechts één label per geselecteerd tekstelement toepassen.
    > * Elk label kan slechts eenmaal per pagina worden toegepast. Als een waarde meerdere keren op hetzelfde formulier wordt weergegeven, maakt u voor elk exemplaar verschillende labels. Bijvoorbeeld: factuur 1, factuur 2, enzovoort.
    > * Een label kan niet op meerdere pagina's worden toegepast.
    > * De labelwaarden zijn zoals ze op het formulier worden weergegeven. Splits geen waarde in twee delen met twee verschillende labels. Zo moet bijvoorbeeld een adresveld met één label worden gelabeld, ook als het meerdere regels omvat.
    > * Voeg geen sleutels aan de velden met labels toe, alleen de waarden.
    > * Tabelgegevens moeten automatisch worden gedetecteerd en worden beschikbaar in het laatste JSON-uitvoerbestand. Als het model echter niet alle tabelgegevens detecteert, kunt u deze velden ook handmatig labelen. Label elke cel in de tabel met een ander label. Als uw formulieren tabellen met een wisselend aantal rijen bevatten, moet u ervoor zorgen dat u ten minste één formulier met de grootste mogelijke tabel labelt.
    > * Gebruik de knoppen rechts van de **+** om uw labels te zoeken, een nieuwe naam te geven, opnieuw in te delen en te verwijderen.
    > * Als u een toegepast label wilt verwijderen zonder het label zelf te verwijderen, selecteert u de gemarkeerde rechthoek in de documentweergave en drukt u op de verwijdertoets.
>

---
---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="Hoofdvenster van de editor van het voorbeeldhulpprogramma voor labelen.":::

Volg de bovenstaande stappen om ten minste vijf formulieren te labelen.

### <a name="specify-tag-value-types"></a>Typen labelwaarden opgeven

U kunt het verwachte gegevens type instellen voor elk label. Open het contextmenu rechts van een label en selecteer een type in het menu. Met deze functie kan de detectie algoritme hypo Thesen worden gemaakt waarmee de nauw keurigheid van de tekst detectie wordt verbeterd. Het zorgt er ook voor dat de gedetecteerde waarden worden geretourneerd in een gestandaardiseerde indeling in de uiteindelijke JSON-uitvoer. Informatie over waardetypen wordt opgeslagen in het bestand **fields.json**, op hetzelfde pad als uw labelbestanden.

> [!div class="mx-imgBorder"]
> ![Selectie van waardetypen met het voorbeeldhulpprogramma voor labelen](../media/whats-new/value-type.png)

De volgende waardetypen en variaties worden momenteel ondersteund:

* `string`
  * standaard, `no-whitespaces`, `alphanumeric`

* `number`
  * standaard, `currency`

* `date`
  * standaard, `dmy`, `mdy`, `ymd`

* `time`
* `integer`
* `selectionMark` – _Nieuw in v2.1-preview.1!_

> [!NOTE]
> Zie de volgende regels voor de datumnotatie:
>
> U moet een indeling (`dmy`, `mdy`, `ymd`) opgeven voor de datumnotatie.
>
> De volgende tekens kunnen worden gebruikt als datumscheidingstekens: `, - / . \`. Witruimte kan niet als scheidingsteken worden gebruikt. Bijvoorbeeld:
>
> * 01.01.2020
> * 01-01-2020
> * 01/01/2020
>
> De dag en maand kunnen worden geschreven met een of twee cijfers; het jaar kan uit twee of vier cijfers bestaan:
>
> * 1-1-2020
> * 1-01-20
>
> Als een datumtekenreeks acht cijfers bevat, is het scheidingsteken optioneel:
>
> * 01012020
> * 01 01 2020
>
> De maand kan ook worden geschreven met de volledige of afgekorte naam. Als de naam wordt gebruikt, zijn de scheidingstekens optioneel. Deze indeling kan echter minder nauwkeurig worden herkend dan andere.
>
> * 01/jan/2020
> * 01jan2020
> * 01 jan 2020

### <a name="label-tables-v21-only"></a>Label tabellen (alleen v 2.1)

Het kan voor komen dat uw gegevens worden aangeduid als een tabel in plaats van sleutel-waardeparen. In dit geval kunt u een tabel code maken door te klikken op een nieuwe tabel label toevoegen, opgeven of de tabel een vast aantal rijen of een variabel aantal rijen bevat, afhankelijk van het document en hoe u het schema definieert.

:::image type="content" source="../media/label-tool/table-tag.png" alt-text="Een tabel code configureren.":::

Wanneer u de tabel code hebt gedefinieerd, labelt u de celwaarden.

:::image type="content" source="../media/table-labeling.png" alt-text="Een tabel labelen.":::

## <a name="train-a-custom-model"></a>Aangepast model trainen

Kies het trein pictogram in het linkerdeel venster om de pagina training te openen. Selecteer vervolgens de **trein** knop om het model te trainen. Zodra het trainingsproces is voltooid, krijgt u de volgende informatie te zien:

* **Model-id**: de id van het model dat is gemaakt en getraind. Elke trainingsaanroep maakt een nieuw model met een eigen id. Kopieer deze tekenreeks naar een veilige locatie. U hebt deze nodig als u voorspellingsaanroepen wilt uitvoeren via de [REST API](./client-library.md?pivots=programming-language-rest-api) of [clientbibliotheek](./client-library.md).
* **Gemiddelde nauwkeurigheid**: de gemiddelde nauwkeurigheid van het model. U kunt de nauw keurigheid van het model verbeteren door extra formulieren te labelen en opnieuw te trainen om een nieuw model te maken. We raden u aan te beginnen met het labelen van vijf formulieren en indien nodig meer formulieren toe te voegen.
* De lijst met labels en de geschatte nauwkeurigheid per label.


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="Trainingsweergave.":::

Nadat de training is voltooid, bekijkt u de waarde **Gemiddelde nauwkeurigheid**. Als deze laag is, moet u meer invoerdocumenten toevoegen en de bovenstaande stappen herhalen. De documenten die u al hebt gelabeld, blijven in de projectindex.

> [!TIP]
> U kunt het trainingsproces ook uitvoeren met een REST API-aanroep. Zie [Trainen met labels met behulp van Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md) voor meer informatie over hoe u dit doet.

## <a name="compose-trained-models"></a>Getrainde modellen samenstellen

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

Met Model samenstellen kunt u maximaal 100 modellen met één model-id samenstellen. Wanneer u analyseren met de opgebouwde aanroept `modelID` , wordt het formulier dat u hebt verzonden eerst geclassificeerd, het beste overeenkomende model gekozen en vervolgens resultaten voor dat model weer gegeven. Deze bewerking is handig wanneer binnenkomende formulieren kunnen deel uitmaken van een van de volgende sjablonen.

Als u modellen wilt opstellen in het hulp programma voor het labelen van het voor beeld, selecteert u het pictogram model opstellen (samen voegen) aan de linkerkant. Selecteer links de modellen die u wilt samenstellen. Modellen met het pijlenpictogram zijn al samengesteld.
Kies de **knop opstellen**. Geef in het pop-upvenster uw nieuwe model naam op en selecteer **opstellen**. Wanneer de bewerking is voltooid, moet uw zojuist opgebouwde model in de lijst worden weer gegeven.

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="UX-weergave van Model samenstellen.":::

### <a name="v20"></a>[v2.0](#tab/v2-0)

Deze functie is momenteel beschikbaar in de preview van v2.1.

---

## <a name="analyze-a-form"></a>Een formulier analyseren

Selecteer het pictogram voor spel (gloei lampje) aan de linkerkant om het model te testen. Upload een formulierdocument dat u niet in het trainingsproces hebt gebruikt. Kies vervolgens de knop voors **pellen** aan de rechter kant om de voor spellingen van sleutel/waarde voor het formulier op te halen. Er worden labels toegepast in begrenzingsvakken en de betrouwbaarheid van elk label wordt gerapporteerd.

> [!TIP]
> U kunt de analyse-API ook met een REST-aanroep uitvoeren. Zie [Trainen met labels met behulp van Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md) voor meer informatie over hoe u dit doet.

## <a name="improve-results"></a>Resultaten verbeteren

Afhankelijk van de gerapporteerde nauwkeurigheid kunt u meer trainingen uitvoeren om het model te verbeteren. Nadat u een voorspelling hebt uitgevoerd, kunt u de betrouwbaarheidswaarden voor elk toegepast label bekijken. Als de gemiddelde nauw keurigheid van de training hoog was, maar de betrouwbaarheids scores laag zijn (of de resultaten onjuist zijn), moet u het Voorspellings bestand toevoegen aan de Trainingsset, het label IT en de training opnieuw uitvoeren.

De gerapporteerde gemiddelde nauw keurigheid, betrouwbaarheids scores en werkelijke nauw keurigheid kunnen inconsistent zijn wanneer de geanalyseerde documenten verschillen van documenten die worden gebruikt in de training. Vergeet niet dat sommige documenten er voor een persoon hetzelfde uit kunnen zien maar voor een AI-model kunnen verschillen. Stel u traint met een formuliertype dat twee variaties kent, terwijl de trainingsset uit 20% van variatie A en 80% van variatie B bestaat. Tijdens de voorspelling zijn de betrouwbaarheidsscores voor documenten van variatie A hoogstwaarschijnlijk lager.

## <a name="save-a-project-and-resume-later"></a>Een project opslaan en later hervatten

Als u het project op een ander tijdstip of in een andere browser wilt hervatten, moet u het beveiligingstoken van het project opslaan en later opnieuw invoeren.

### <a name="get-project-credentials"></a>Projectreferenties ophalen

Ga naar de pagina met projectinstellingen (schuifregelaar) en noteer de naam van het beveiligingstoken. Ga vervolgens naar uw toepassingsinstellingen (tandwielpictogram), waarin alle beveiligingstokens in uw huidige browserexemplaar worden weergegeven. Zoek het beveiligingstoken van uw project en kopieer de naam en sleutelwaarde naar een veilige locatie.

### <a name="restore-project-credentials"></a>Projectreferenties herstellen

Als u uw project wilt hervatten, moet u eerst een verbinding maken met dezelfde Blob Storage-container. Als u dit wilt doen, herhaalt u de bovenstaande stappen. Ga vervolgens naar de pagina met toepassingsinstellingen (tandwielpictogram) en kijk of het beveiligingstoken van uw project aanwezig is. Als dat niet het geval is, voegt u een nieuw beveiligingstoken toe en kopieert u de naam en de sleutel van het token uit de vorige stap. Selecteer **Opslaan** om uw instellingen te behouden.

### <a name="resume-a-project"></a>Een project hervatten

Ga ten slotte naar de hoofd pagina (huis pictogram) en selecteer **Cloud project openen**. Selecteer vervolgens de Blob Storage-verbinding en selecteer het **.fott**-bestand van uw project. Alle projectinstellingen worden geladen omdat het beveiligingstoken aanwezig is.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u het voorbeeldhulpprogramma voor labelen van Form Recognizer met Python gebruikt voor het trainen van een model met handmatig gelabelde gegevens. Als u uw eigen hulpprogramma wilt bouwen voor het labelen van trainingsgegevens, gebruikt u de REST API's die het trainen met gelabelde gegevens verwerken.

> [!div class="nextstepaction"]
> [Trainen met labels met behulp van Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [Wat is Form Recognizer?](../overview.md)
* [Form Recognizer-quickstart](client-library.md)
