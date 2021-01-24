---
title: C#-zelfstudie met behulp van AI over Azure-blobs
titleSuffix: Azure Cognitive Search
description: Bekijk een voorbeeld van tekstextractie en natuurlijke taalverwerking over inhoud in blob-opslag met behulp van C# en de .NET SDK van Azure Cognitive Search.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 4bda56f3037469477ddfe059dd20c14cd34586d8
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745714"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Zelfstudie: Door AI gegenereerde doorzoekbare inhoud van Azure-blobs met behulp van de .NET SDK

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kunt u met een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) informatie extraheren en nieuwe inhoud maken voor zoekopdrachten in volledige tekst of kennisanalyse. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een ontwikkelomgeving instellen
> * Definieer een pijp lijn die gebruikmaakt van OCR, taal detectie en entiteits-en sleutel woord herkenning.
> * Voer de pijplijn uit om transformaties en analyses aan te roepen, en om een zoekindex te maken en te laden.
> * Bekijk de resultaten met zoeken in volledige tekst en een uitgebreide query-syntaxis.

Als u geen abonnement op Azure hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="overview"></a>Overzicht

In deze zelf studie wordt C# en de [ **Azure.Search.Documents** -client bibliotheek](/dotnet/api/overview/azure/search.documents-readme) gebruikt voor het maken van een gegevens bron, index, Indexeer functie en vaardig heden.

De Indexeer functie maakt verbinding met een BLOB-container die is opgegeven in het gegevens bron object en stuurt alle geïndexeerde inhoud naar een bestaande zoek index.

De vaardig heden is gekoppeld aan de Indexeer functie. Er worden ingebouwde vaardig heden van micro soft gebruikt om informatie te vinden en uit te pakken. Stappen in de pijplijn omvatten OCR (Optical Character Recognition) voor afbeeldingen, taaldetectie voor tekst, sleuteltermextractie, en entiteitsherkenning (organisatie). Nieuwe gegevens die door de pijp lijn zijn gemaakt, worden opgeslagen in nieuwe velden in een index. Zodra de index is ingevuld, kunt u de velden gebruiken in query's, facetten en filters.

## <a name="prerequisites"></a>Vereisten

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [Azure.Search.Documents 11.x NuGet-pakket](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Azure Storage](https://azure.microsoft.com/services/storage/)
* [Azure Cognitive Search](https://azure.microsoft.com/services/search/)

> [!Note]
> U kunt de gratis zoekservice voor deze zelfstudie gebruiken. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u aan de slag gaat, zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="download-sample-data"></a>Voorbeeldgegevens downloaden

De voorbeeldgegevens bestaan uit 14 bestanden van het gemengde inhoudstype dat u in een latere stap gaat uploaden naar Azure Blob-opslag.

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **Downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechtermuisknop op het zip-bestand en selecteer **Alles extraheren**. Er zijn 14 bestanden van verschillende typen. U gebruikt 7 voor deze oefening.

U kunt ook de broncode voor deze zelfstudie downloaden. De broncode bevindt zich in de map **tutorial-ai-enrichment/v11**, in de opslagplaats [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="1---create-services"></a>1 - Services maken

In deze zelfstudie maakt gebruik van Azure Cognitive Search voor het indexeren en uitvoeren van query's, Cognitive Services op de back-end voor AI-verrijking en Azure Blob-opslag om de gegevens op te geven. Deze zelfstudie bevindt zich onder de gratis toewijzing van 20 transacties per indexeerfunctie per dag op Cognitive Services, dus de enige services die u moet maken, zijn zoeken en opslag.

Maak, indien mogelijk, beide in dezelfde regio en resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kan uw Azure Storage-account zich in een willekeurige regio bevinden.

### <a name="start-with-azure-storage"></a>Aan de slag met Azure Storage

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en klik op **En resourcegroepen maken**.

1. Zoek naar *Opslagaccount* en selecteer de aanbieding van het Microsoft-opslagaccount.

   ![Een opslagaccount maken](media/cognitive-search-tutorial-blob/storage-account.png "Een opslagaccount maken")

1. Op het tabblad Basisinstellingen zijn de volgende items vereist. Accepteer de standaardwaarden voor alle andere.

   * **Resourcegroep**. Selecteer een bestaande naam of maak een nieuwe, maar gebruik dezelfde groep voor alle services, zodat u ze gezamenlijk kunt beheren.

   * **Naam van opslagaccount**. Als u denkt dat u mogelijk meerdere resources van hetzelfde type hebt, gebruikt u de naam om op type en regio te onderscheiden, bijvoorbeeld *blobstoragewestus*. 

   * **Locatie**. Kies indien mogelijk dezelfde locatie die wordt gebruikt voor Azure Cognitive Search en Cognitive Services. Een enkele locatie vermindert bandbreedtekosten aanzienlijk.

   * **Soort account**. Kies de standaardinstelling *StorageV2 (algemeen gebruik v2)* .

1. Klik vervolgens op **Beoordelen en maken** om de service te maken.

1. Zodra de app is gemaakt, klikt u op **Ga naar de resource** om de pagina Overzicht te openen.

1. Klik op **Blobs**-service.

1. Klik op **En container** om een container te maken en geef deze de naam *cog-search-demo*.

1. Selecteer *cog-search-demo* en klik vervolgens op **Uploaden** om de map te openen waar u de downloadbestanden hebt opgeslagen. Selecteer alle veertien bestanden en klik op **OK** om ze te uploaden.

   ![Voorbeeldbestanden uploaden](media/cognitive-search-quickstart-blob/sample-data.png "Voorbeeldbestanden uploaden")

1. Voordat u Azure Storage verlaat, moet u een verbindingsreeks ophalen zodat u een verbinding in Azure Cognitive Search kunt formuleren. 

   1. Ga terug naar de pagina Overzicht van uw opslagaccount (we hebben *blobstoragewestus* als voorbeeld gebruikt). 

   1. Selecteer in het navigatiedeelvenster links de optie **Toegangstoetsen** en kopieer een van de verbindingsreeksen. 

   De verbindingsreeks is een URL die er ongeveer als volgt uitziet:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de verbindingsreeks op in Kladblok. U hebt deze later nodig bij het instellen van de gegevensbronverbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking wordt ondersteund door Cognitive Services, waaronder Text Analytics en Computer Vision voor de verwerking van natuurlijke taal en afbeeldingen. Als u een echt prototype of project wilt voltooien, moet u op dit punt Cognitive Services (in dezelfde regio als Azure Cognitive Search) inrichten, zodat u het kunt koppelen aan indexbewerkingen.

Voor deze oefening kunt u echter het inrichten van resources overslaan omdat Azure Cognitive Search verbinding kan maken met Cognitive Services achter de schermen en u 20 gratis transacties per uitvoering van indexeerfunctie geeft. Omdat in deze zelfstudie veertien transacties worden gebruikt, is de gratis toewijzing voldoende. Voor grotere projecten plant u het inrichten van Cognitive Services op de S0-laag voor betalen per gebruik. Zie voor meer informatie [Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u kunt [maken in de portal](search-create-service-portal.md) of u kunt [een bestaande zoekservice vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw abonnement.

U kunt de gratis laag gebruiken om dit voorbeeld te voltooien. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search kopiëren

Als u wilt communiceren met uw Azure Cognitive Search-service, hebt u de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Kopieer onder **Instellingen** > **Sleutels** een beheersleutel voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de querysleutel op. Het is een aanbevolen procedure voor het uitgeven van queryaanvragen met alleen-lezen-toegang.

   ![De naam van de service en de querysleutels voor beheer ophalen](media/search-get-started-javascript/service-name-and-keys.png)

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-your-environment"></a>2 - De omgeving instellen

Open eerst Visual Studio en maak een nieuw Console App-project dat kan worden uitgevoerd op .NET Core.

### <a name="install-azuresearchdocuments"></a>Azure.Search.Documents installeren

De [.NET SDK van Azure Cognitive Search](/dotnet/api/overview/azure/search) bestaat uit een clientbibliotheek waarmee u indexen, gegevensbronnen, indexeerfuncties en vaardighedensets kunt beheren. U kunt ook documenten uploaden en beheren en query's uitvoeren, zonder dat u zich hoeft bezig te houden met de details van HTTP en JSON. Deze clientbibliotheek wordt gedistribueerd als een NuGet-pakket.

Voor dit project installeert u versie 11 of hoger van het `Azure.Search.Documents` en de meest recente versie van `Microsoft.Extensions.Configuration`.

1. Selecteer in Visual Studio achtereenvolgens **Hulpprogramma's** > **NuGet-pakketbeheer** > **NuGet-pakketten voor Solution...** .

1. Blader naar [Azure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents).

1. Selecteer de nieuwste versie en klik vervolgens op **Installeren**.

1. Herhaal de vorige stappen voor het installeren van [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) en [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json).

### <a name="add-service-connection-information"></a>Verbindingsgegevens van de service toevoegen

1. Klik met de rechtermuisknop in Solution Explorer en selecteer **Toevoegen** > **Nieuw item...** . 

1. Geef het bestand de naam `appsettings.json` en selecteer **Toevoegen**. 

1. Neem dit bestand op in de uitvoermap.
    1. Klik met de rechtermuisknop op `appsettings.json` en selecteer **Eigenschappen**. 
    1. Wijzig de waarde van **Naar uitvoermap kopiëren** in **Kopiëren indien nieuwer**.

1. Kopieer de onderstaande JSON in uw nieuwe JSON-bestand.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Voeg de gegevens van uw zoekservice en blob-opslagaccount toe. U kunt deze informatie ophalen via de stappen voor het inrichten van de service, zoals aangegeven in de vorige sectie.

Voer voor **SearchServiceUri** de volledige URL in.

### <a name="add-namespaces"></a>Naamruimten toevoegen

Voeg in `Program.cs` de volgende naamruimten toe.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Client maken

Maak een exemplaar van een `SearchIndexClient` en een `SearchIndexerClient` onder `Main`.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> De clients maken verbinding met uw zoekservice. Als u wilt voorkomen dat er te veel verbindingen worden geopend, kunt u proberen één exemplaar in uw toepassing te delen, indien mogelijk. De methoden zijn thread-safe om delen in te schakelen.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Functie toevoegen om het programma te sluiten tijdens een fout

Deze zelfstudie is bedoeld om u inzicht te geven in elke stap van de indexeringspijplijn. Als er een kritiek probleem is waarmee wordt voorkomen dat het programma de gegevensbron, vaardighedenset, index of indexeerfunctie kan maken, wordt het foutbericht uitgevoerd en wordt het programma afgesloten, zodat er inzicht in het probleem kan worden verkregen en het kan worden opgelost.

Voeg `ExitProgram` toe aan `Main` voor het afhandelen van scenario's waarvoor het programma moet worden afgesloten.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - De pijplijn maken

In Azure Cognitive Search treedt AI-verwerking op tijdens het indexeren (of gegevensopname). In dit deel van het scenario worden vier objecten gemaakt: gegevensbron, indexdefinitie, vaardighedenset, indexeerfunctie. 

### <a name="step-1-create-a-data-source"></a>Stap 1: Een gegevensbron maken

`SearchIndexerClient` heeft een eigenschap [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename) die u kunt instellen als een object `SearchIndexerDataSourceConnection`. Dit object biedt alle methoden die u nodig hebt om een gegevensbronnen voor Azure Cognitive Search te maken, weer te geven, bij te werken of te verwijderen.

Maak een nieuw `SearchIndexerDataSourceConnection`-exemplaar door `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)` aan te roepen. Met de volgende code wordt een gegevensbron gemaakt van het type `AzureBlob`.

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Als de aanvraag is geslaagd, wordt de gemaakte gegevensbron geretourneerd. Als er een probleem is met de aanvraag, bijvoorbeeld een ongeldige parameter, wordt er een uitzondering gegenereerd.

Voeg nu een regel toe aan `Main` om de `CreateOrUpdateDataSource`-functie aan te roepen die u zojuist hebt toegevoegd.

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Bouw de oplossing en voer deze uit. Aangezien dit uw eerste aanvraag is, controleert u de Azure-portal om te bevestigen dat de gegevensbron in Azure Cognitive Search is gemaakt. Controleer op de overzichtspagina van de zoekservice of de lijst Gegevensbronnen een nieuw item heeft. U moet mogelijk een paar minuten wachten tot de portalpagina is vernieuwd.

  ![De tegel gegevensbronnen in de portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "De tegel gegevensbronnen in de portal")

### <a name="step-2-create-a-skillset"></a>Stap 2: Een set vaardigheden maken

In deze stap definieert u een reeks verrijkingsstappen die u op uw gegevens wilt toepassen. Elke verrijkingsstap wordt een *vaardigheid* genoemd en de reeks verrijkingsstappen heet een *vaardighedenset*. In deze zelfstudie wordt gebruikgemaakt van [ingebouwde cognitieve tekstvaardigheden](cognitive-search-predefined-skills.md) voor de set vaardigheden:

* [Optical Character Recognition](cognitive-search-skill-ocr.md), voor het herkennen van gedrukte en handgeschreven tekst in afbeeldingsbestanden.

* [Tekst samenvoegen](cognitive-search-skill-textmerger.md), om tekst uit een verzameling velden in één veld samen te voegen.

* [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

* [Tekst splitsen](cognitive-search-skill-textsplit.md) om grote inhoud in kleinere stukken op te delen voordat u de vaardigheid voor sleuteltermextractie en die voor entiteitsherkenning aanroept. Sleuteltermextractie en entiteitsherkenning accepteren een invoer van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

* [Herkenning van entiteiten](cognitive-search-skill-entity-recognition.md) voor het extraheren van de namen van organisaties uit inhoud van de blobcontainer.

* [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen.

Tijdens de initiële verwerking opent Azure Cognitive Search elk document om inhoud van verschillende bestandsindelingen uit te pakken. Tekst uit het bronbestand wordt in een gegenereerd veld `content` geplaatst, één voor elk document. Stel de invoer als `"/document/content"` in om deze tekst te gebruiken. Afbeeldingsinhoud is in een gegenereerd veld `normalized_images` geplaatst, opgegeven in een vaardighedenset als `/document/normalized_images/*`.

Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="ocr-skill"></a>Vaardigheid OCR

De vaardigheid **OCR** haalt tekst op uit afbeeldingen. Bij deze vaardigheid wordt ervan uitgegaan dat er een veld normalized_images bestaat. Als u dit veld wilt genereren, stelt u verderop in de zelfstudie de ```"imageAction"```-configuratie in de definitie van de indexeerfunctie in op ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Vaardigheid Samenvoegen

In deze sectie maakt u een vaardigheid **Samenvoegen** waarmee het veld documentinhoud wordt samengevoegd met de tekst die is geproduceerd op de OCR-vaardigheid.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Vaardigheid Taaldetectie

De vaardigheid **Taaldetectie** detecteert in welke taal de ingevoerde tekst is geschreven en rapporteert één taalcode voor elk document dat bij de aanvraag is ingediend. De uitvoer van de vaardigheid **Taaldetectie** wordt gebruikt als onderdeel van de invoer voor de vaardigheid **Tekst splitsen**.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Vaardigheid Tekst splitsen

Met de onderstaande Vaardigheid **Splitsen** wordt de tekst op pagina's gesplitst en wordt de paginalengte beperkt tot 4000 tekens, zoals gemeten door `String.Length`. Het algoritme splitst de tekst in fragmenten die maximaal `maximumPageLength` groot zijn. In dit geval zal het algoritme de zin afbreken op de grens van een zin, zodat de grootte van het fragment iets kleiner dan `maximumPageLength` kan zijn.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Vaardigheid Entiteitsherkenning

Dit `EntityRecognitionSkill`-exemplaar is ingesteld om categorietype `organization` te herkennen. De vaardigheid **Entiteitherkenning** kan ook de categorietypen `person` en `location` herkennen.

U ziet dat het 'contextveld' is ingesteld op ```"/document/pages/*"``` met een asterisk, wat betekent dat de verrijkingsstap wordt aangeroepen voor elke pagina onder ```"/document/pages"```.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Vaardigheid Sleuteltermextractie

Net als voor het `EntityRecognitionSkill`-exemplaar dat zojuist is gemaakt, wordt de vaardigheid **Sleuteltermextractie** voor elke pagina van het document aangeroepen.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>De vaardighedenset compileren en maken

Compileer de `Skillset` met behulp van de vaardigheden die u hebt gemaakt.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Voeg de volgende regels toe aan `Main`.

```csharp
// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();
EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
SplitSkill splitSkill = CreateSplitSkill();
KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

### <a name="step-3-create-an-index"></a>Stap 3: Een index maken

In dit gedeelte kunt u het indexschema definiëren door op te geven welke velden in de doorzoekbare index moeten worden opgenomen en de zoekkenmerken voor elk veld op te geven. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| Veldnamen | Veldtypen |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Klasse DemoIndex maken

De velden voor deze index worden gedefinieerd met behulp van een modelklasse. Elke eigenschap van de modelklasse heeft kenmerken die het zoekgerelateerde gedrag van het bijbehorende indexveld bepalen. 

De modelklasse wordt toegevoegd aan een nieuw C#-bestand. Klik met de rechtermuisknop op het project en selecteer **Toevoegen** > **Nieuw item...** , selecteer Klasse en geef het bestand de naam `DemoIndex.cs`. Selecteer vervolgens **Toevoegen**.

Geef aan dat u typen wilt gebruiken van de naamruimten `Azure.Search.Documents.Indexes` en `System.Text.Json.Serialization`.

Voeg de onderstaande definitie van de modelklasse toe aan `DemoIndex.cs` en neem deze op in dezelfde naamruimte waarin u de index gaat maken.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Nu u een modelklasse hebt gedefinieerd, kunt u in `Program.cs` vrij eenvoudig een indexdefinitie maken. De naam voor deze index wordt `demoindex`. Als er al een index met deze naam bestaat, wordt deze verwijderd.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Tijdens het testen is het mogelijk dat u de index meer dan één keer maakt. Controleer daarom of de index die u gaat maken al bestaat voordat u deze wilt maken.

Voeg de volgende regels toe aan `Main`.

```csharp
// Create the index
Console.WriteLine("Creating the index...");
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Voeg de volgende using-instructie toe om de dubbelzinnige verwijzing te corrigeren.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Zie [Index maken (REST API)](/rest/api/searchservice/create-index) voor meer informatie over indexconcepten.

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: Indexeerfunctie maken en uitvoeren

Tot nu toe hebt u een gegevensbron, een set vaardigheden en een index gemaakt. Deze drie onderdelen gaan deel uitmaken van een [indexeerfunctie](search-indexer-overview.md) die de onderdelen combineert in één meervoudige bewerking. Als u deze in een indexeerfunctie wilt combineren, moet u veldtoewijzingen definiëren.

* De fieldMappings worden vóór de vaardighedenset verwerkt, waarbij bronvelden van de gegevensbron worden toegewezen aan de doelvelden in een index. Als veldnamen en -typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

* De outputFieldMappings worden verwerkt na de vaardighedenset en verwijzen naar sourceFieldNames die niet bestaan totdat het document wordt gekraakt of verrijkt. De targetFieldName is een veld in een index.

Naast het koppelen van invoer naar uitvoer, kunt u ook veldtoewijzingen gebruiken om gegevensstructuren samen te voegen. Zie [Verrijkte velden toewijzen aan een doorzoekbare index](cognitive-search-output-field-mapping.md) voor meer informatie.

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Voeg de volgende regels toe aan `Main`.

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

Het verwerken via de indexeerfunctie kost enige tijd om te voltooien. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. Sommige vaardigheden, zoals afbeeldingsanalyse, zijn langlopend.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven.

### <a name="explore-creating-the-indexer"></a>Indexeerfunctie maken

De code stelt `"maxFailedItems"` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

U ziet ook dat de `"dataToExtract"` is ingesteld op `"contentAndMetadata"`. Deze instructie geeft de indexeerfunctie de opdracht om automatisch de inhoud van verschillende bestandsindelingen te extraheren, evenals metagegevens voor elk bestand.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. Met de configuratie waarbij `"imageAction"` is ingesteld op `"generateNormalizedImages"`, in combinatie met de vaardigheid OCR en de vaardigheid Tekst samenvoegen, krijgt de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4: Het indexeren bewaken

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Als u wilt weten of de indexeerfunctie nog steeds wordt uitgevoerd, gebruikt u de methode `GetStatus`.

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

Met `demoIndexerExecutionInfo` wordt de huidige status en de uitvoeringsgeschiedenis van een indexeerfunctie aangegeven.

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. In deze zelfstudie zijn de waarschuwingen onschadelijk (bijvoorbeeld geen tekstinvoeren van de JPEG-bestanden).

Voeg de volgende regels toe aan `Main`.

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5 - Zoeken

In de console-apps van de Azure Cognitive Search-zelfstudie voegen we meestal een vertraging van 2 seconden toe vóór de uitvoering van query's die resultaten retourneren, maar omdat het enkele minuten duurt voordat de verrijking is voltooid, wordt de console-app gesloten en gebruiken we in plaats hiervan een andere methode.

De meest eenvoudige optie is [Search Explorer](search-explorer.md) in de portal. U kunt eerst een lege query uitvoeren waarmee alle documenten worden geretourneerd, of een gerichte zoekopdracht uitvoeren waarmee nieuwe veldinhoud wordt geretourneerd die is gemaakt via de pijplijn. 

1. Selecteer in de Azure-portal, op de pagina Overzicht, de optie **Indexen**.

1. Zoek **`demoindex`** in de lijst. Het resultaat moet 14 documenten bevatten. Als het aantal documenten nul is, is de Indexeerfunctie nog actief of is de pagina nog niet vernieuwd. 

1. Selecteer **`demoindex`**. Search Explorer is het eerste tabblad.

1. Inhoud is doorzoekbaar zodra het eerste document is geladen. Als u wilt controleren of inhoud bestaat, voert u een niet-opgegeven query uit door te klikken op **Zoeken**. Met deze query worden alle momenteel geïndexeerde documenten geretourneerd, waardoor u een idee krijgt van wat de index bevat.

1. Plak vervolgens de volgende tekenreeks voor meer beheerbare resultaten: `search=*&$select=id, languageCode, organizations`

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeldcode voor deze zelfstudie wordt gecontroleerd op bestaande objecten. Deze worden verwijderd, zodat u de code opnieuw kunt uitvoeren. U kunt de portal ook gebruiken om indexen, indexeerfuncties, gegevensbronnen en vaardighedensets te verwijderen.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een vaardighedenset, een index en een indexeerfunctie.

[Ingebouwde vaardigheden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met de definitie van de vaardighedenset en het mechanisme voor het koppelen van vaardigheden via in- en uitvoer. U hebt ook geleerd dat `outputFieldMappings` vereist is in de definitie van de indexeerfunctie voor de routering van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Cognitive Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. U hebt ook geleerd hoe u de status van de indexeerfunctie kunt controleren en welke objecten u moet verwijderen voordat u een pijplijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkingspijplijn, gaan we kijken naar de definities van vaardighedensets en individuele vaardigheden.

> [!div class="nextstepaction"]
> [Een vaardighedenset maken](cognitive-search-defining-skillset.md)