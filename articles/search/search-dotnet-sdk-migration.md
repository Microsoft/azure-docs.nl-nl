---
title: Een upgrade uitvoeren naar Azure Search .NET SDK versie 3
titleSuffix: Azure Cognitive Search
description: Code migreren naar de Azure Search .NET SDK versie 3 van oudere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e9f4602582a8c24f74d6603b732e7b91d97fa8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002688"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Een upgrade uitvoeren naar Azure Search .NET SDK versie 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Als u versie 2,0-Preview of ouder van de [Azure Search .NET SDK](/dotnet/api/overview/azure/search)gebruikt, helpt dit artikel u bij het upgraden van uw toepassing tot het gebruik van versie 3.

Zie [Azure Search van een .NET-toepassing gebruiken](search-howto-dotnet-sdk.md)voor een meer algemeen overzicht van de SDK, inclusief voor beelden.

Versie 3 van de Azure Search .NET SDK bevat een aantal wijzigingen ten opzichte van eerdere versies. Dit zijn voornamelijk minder jarigen, zodat het wijzigen van uw code slechts een minimale inspanning vereist. Zie de stappen voor het uitvoeren van een [upgrade](#UpgradeSteps) voor instructies over het wijzigen van de code voor het gebruik van de nieuwe SDK-versie.

> [!NOTE]
> Als u versie 1.0.2-Preview of ouder gebruikt, moet u eerst een upgrade uitvoeren naar versie 1,1 en vervolgens een upgrade uitvoeren naar versie 3. Zie [upgraden naar de Azure Search .NET SDK versie 1,1](search-dotnet-sdk-migration-version-1.md) voor instructies.
>
> Uw Azure Search service-exemplaar ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Wat is er nieuw in versie 3
Versie 3 van de Azure Search .NET SDK streeft naar de meest recente, algemeen beschik bare versie van de Azure Search REST API, met name 2016-09-01. Dit maakt het mogelijk om veel nieuwe functies van Azure Search van een .NET-toepassing te gebruiken, met inbegrip van het volgende:

* [Analysevoorzieningen aanpassen](index-add-custom-analyzers.md)
* Ondersteuning voor [azure Blob Storage](search-howto-indexing-azure-blob-storage.md) en [Azure Table Storage](search-howto-indexing-azure-tables.md) Indexer
* Aanpassing van Indexeer functie via [veld Toewijzingen](search-indexer-field-mappings.md)
* ETags-ondersteuning voor het inschakelen van veilig gelijktijdig bijwerken van index definities, Indexeer functies en gegevens bronnen
* Ondersteuning voor het samen stellen van index veld definities declaratief door uw model klasse te verf raaien en de nieuwe klasse te gebruiken `FieldBuilder` .
* Ondersteuning voor .NET core en .NET Portable profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade
Werk eerst uw NuGet-verwijzing bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager-console of door met de rechter muisknop te klikken op uw project verwijzingen en vervolgens NuGet-pakketten beheren te selecteren... in Visual Studio.

Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat het geval is, bent u klaar om aan de slag te gaan.

Als uw build mislukt, ziet u een build-fout als de volgende:

```output
Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)
```

De volgende stap is het oplossen van deze build-fout. Zie belang rijke [wijzigingen in versie 3](#ListOfChanges) voor meer informatie over de oorzaak van de fout en hoe u deze kunt oplossen.

Mogelijk worden er aanvullende build-waarschuwingen met betrekking tot verouderde methoden of eigenschappen weer gegeven. De waarschuwingen bevatten instructies over wat u moet gebruiken in plaats van de afgeschafte functie. Als uw toepassing bijvoorbeeld gebruikmaakt van de `IndexingParameters.Base64EncodeKeys` eigenschap, wordt er een waarschuwing weer gegeven met de tekst `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Wanneer u opgebouwde fouten hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Wijzigingen in versie 3 verbreken
Er is een klein aantal belang rijke wijzigingen in versie 3 waarvoor code wijzigingen nodig zijn naast het opnieuw samen stellen van uw toepassing.

### <a name="indexesgetclient-return-type"></a>Indexes. GetClient retour type
De `Indexes.GetClient` methode heeft een nieuw retour type. Voorheen `SearchIndexClient` werd deze geretourneerd, maar dit is gewijzigd `ISearchIndexClient` in versie 2,0-Preview en deze wijziging gaat naar versie 3. Dit is ter ondersteuning van klanten die de `GetClient` methode voor eenheids testen willen intrekken door een model implementatie van te retour neren `ISearchIndexClient` .

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>Analyzer, data type en andere zijn niet meer impliciet geconverteerd naar teken reeksen
Er zijn veel typen in de Azure Search .NET SDK die zijn afgeleid van `ExtensibleEnum` . Voorheen werden deze typen allemaal impliciet geconverteerd naar type `string` . Er is echter een bug ontdekt in de `Object.Equals` implementatie voor deze klassen en het herstellen van de fout die vereist is om deze impliciete conversie uit te scha kelen. Expliciete omzetting naar `string` is nog steeds toegestaan.

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Verouderde leden zijn verwijderd

Mogelijk worden er compilatie fouten weer gegeven met betrekking tot methoden of eigenschappen die als verouderd zijn gemarkeerd in versie 2,0-Preview en vervolgens worden verwijderd in versie 3. Als u dergelijke fouten tegen komt, kunt u deze als volgt oplossen:

- Als u deze constructor gebruikt: `ScoringParameter(string name, string value)` , gebruikt u deze in plaats daarvan: `ScoringParameter(string name, IEnumerable<string> values)`
- Als u de eigenschap gebruikt `ScoringParameter.Value` , gebruikt u `ScoringParameter.Values` in plaats daarvan de eigenschap of de- `ToString` methode.
- Als u de eigenschap hebt gebruikt `SearchRequestOptions.RequestId` , gebruikt u `ClientRequestId` in plaats daarvan de eigenschap.

### <a name="removed-preview-features"></a>De preview-functies zijn verwijderd

Als u een upgrade uitvoert van versie 2,0-Preview naar versie 3, moet u er rekening mee houden dat JSON-en CSV-parsering ondersteuning voor BLOB-Indexeer functies is verwijderd, omdat deze functies nog in Preview zijn. Met name de volgende methoden van de `IndexingParametersExtensions` klasse zijn verwijderd:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Als uw toepassing een vaste afhankelijkheid van deze functies heeft, kunt u niet upgraden naar versie 3 van de Azure Search .NET SDK. U kunt versie 2,0-Preview blijven gebruiken. Bedenk echter dat het **gebruik van preview-sdk's in productie toepassingen niet wordt aangeraden**. Preview-functies zijn alleen voor evaluatie en kunnen worden gewijzigd.

## <a name="conclusion"></a>Conclusie
Als u meer informatie wilt over het gebruik van de Azure Search .NET SDK, raadpleegt u de [.net How-to](search-howto-dotnet-sdk.md).

We stellen uw feedback op de SDK. Als u problemen ondervindt, kunt u ons vragen om hulp over [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem voordoet met ' [Azure Search] '.

Hartelijk dank dat u Azure Search hebt gebruikt.