---
title: Een upgrade uitvoeren naar Azure Search .NET SDK versie 9
titleSuffix: Azure Cognitive Search
description: Code migreren naar de Azure Search .NET SDK versie 9 van oudere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 76cb2f94c949217c36fdccb8c64a53555bab227c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606331"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Een upgrade uitvoeren naar Azure Search .NET SDK versie 9

Als u versie 7,0-Preview of ouder van de [Azure Search .NET SDK](/dotnet/api/overview/azure/search)gebruikt, helpt dit artikel u bij het upgraden van uw toepassing tot het gebruik van versie 9.

> [!NOTE]
> Als u versie 8,0-Preview wilt gebruiken om functies te evalueren die nog niet algemeen beschikbaar zijn, kunt u ook de instructies in dit artikel volgen om een upgrade uit te voeren naar 8,0-Preview van eerdere versies.

Zie [Azure Search van een .NET-toepassing gebruiken](search-howto-dotnet-sdk.md)voor een meer algemeen overzicht van de SDK, inclusief voor beelden.

Versie 9 van de Azure Search .NET SDK bevat veel wijzigingen ten opzichte van eerdere versies. Sommige hiervan zijn belang rijke wijzigingen, maar er mogen alleen relatief kleine wijzigingen in uw code nodig zijn. Zie de stappen voor het uitvoeren van een [upgrade](#UpgradeSteps) voor instructies over het wijzigen van de code voor het gebruik van de nieuwe SDK-versie.

> [!NOTE]
> Als u versie 4,0-Preview of ouder gebruikt, moet u eerst een upgrade uitvoeren naar versie 5 en vervolgens een upgrade uitvoeren naar versie 9. Zie [upgraden naar de Azure Search .NET SDK versie 5](search-dotnet-sdk-migration-version-5.md) voor instructies.
>
> Uw Azure Search service-exemplaar ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Wat is er nieuw in versie 9
Versie 9 van de Azure Search .NET SDK 2019-05-06-versie van Azure Search REST API met de volgende functies:

* [AI-verrijking](cognitive-search-concept-intro.md) is de mogelijkheid om tekst te extra heren uit afbeeldingen, blobs en andere ongestructureerde gegevens bronnen: de inhoud verrijken zodat deze meer doorzoekbaar is in een Azure search index.
* Dankzij ondersteuning voor [complexe typen](search-howto-complex-data-types.md) kunt u nagenoeg elke geneste JSON-structuur in een Azure search index model leren.
* [Automatisch aanvullen](search-add-autocomplete-suggestions.md) biedt een alternatief voor de API Voorst **Ellen** voor het implementeren van zoek gedrag op type. Met automatisch aanvullen wordt het woord of de woord groep voltooid die een gebruiker momenteel typt.
* Met de [JsonLines-parserings modus](search-howto-index-json-blobs.md), onderdeel van Azure Blob-indexering, wordt één Zoek document per JSON-entiteit gemaakt dat wordt gescheiden door een nieuwe regel.

### <a name="new-preview-features-in-version-80-preview"></a>Nieuwe preview-functies in versie 8,0-Preview
Versie 8,0-Preview van de Azure Search .NET SDK doelen API-versie 2017-11-11-preview. Deze versie bevat alle dezelfde functies van versie 9, plus:

* Door de [klant beheerde versleutelings sleutels](search-security-manage-encryption-keys.md) voor versleuteling aan de service zijde is een nieuwe preview-functie. Naast de ingebouwde versleutelings-op-rest die door micro soft wordt beheerd, kunt u een extra laag versleuteling Toep assen waarbij u de enige eigenaar van de sleutels bent.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade
Werk eerst uw NuGet-verwijzing bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager-console of door met de rechter muisknop te klikken op uw project verwijzingen en vervolgens NuGet-pakketten beheren te selecteren... in Visual Studio.

Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat het geval is, bent u klaar om aan de slag te gaan.

Als uw build is mislukt, moet u elke compilatie fout herstellen. Zie belang rijke [wijzigingen in versie 9](#ListOfChanges) voor meer informatie over het oplossen van elke mogelijke build-fout.

Mogelijk worden er aanvullende build-waarschuwingen met betrekking tot verouderde methoden of eigenschappen weer gegeven. De waarschuwingen bevatten instructies over wat u moet gebruiken in plaats van de afgeschafte functie. Als uw toepassing bijvoorbeeld gebruikmaakt van de `DataSourceType.DocumentDb` eigenschap, wordt een waarschuwing weer gegeven met de tekst ' dit lid is afgeschaft. Gebruik in plaats daarvan CosmosDb.

Wanneer u eventuele build-fouten of-waarschuwingen hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Wijzigingen in versie 9 verbreken

Er zijn verschillende belang rijke wijzigingen in versie 9 waarvoor code wijzigingen nodig zijn naast het opnieuw samen stellen van uw toepassing.

> [!NOTE]
> De onderstaande lijst met wijzigingen is niet limitatief. Sommige wijzigingen leiden waarschijnlijk niet tot fouten in de build, maar worden technisch afgebroken omdat ze binaire compatibiliteit met assembly's die afhankelijk zijn van eerdere versies van de Azure Search .NET SDK-assembly's, afgebroken. Dergelijke wijzigingen worden hieronder niet weer gegeven. Bouw uw toepassing opnieuw op wanneer u een upgrade uitvoert naar versie 9 om problemen met binaire compatibiliteit te voor komen.

### <a name="immutable-properties"></a>Onveranderbare eigenschappen

De open bare eigenschappen van verschillende model klassen zijn nu onveranderbaar. Als u aangepaste instanties van deze klassen wilt maken om te testen, kunt u de nieuwe geparametriseerde constructors gebruiken:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Wijzigingen in veld

De `Field` klasse is nu gewijzigd, zodat deze ook complexe velden kan vertegenwoordigen.

De volgende `bool` eigenschappen zijn nu null-baar:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Dit komt doordat deze eigenschappen nu `null` in het geval van complexe velden moeten zijn. Als u code hebt die deze eigenschappen leest, moet deze worden voor bereid om te worden verwerkt `null` . Houd er rekening mee dat alle andere eigenschappen van al `Field` dan niet zijn toegestaan, en dat sommige daarvan ook `null` in het geval van complexe velden worden weer geven, met name het volgende:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

De constructor zonder para meters van `Field` is gemaakt `internal` . Vanaf nu `Field` moet elk een expliciete naam en gegevens type zijn op het moment van de bouw.

### <a name="simplified-batch-and-results-types"></a>Vereenvoudigde batch-en resultaat typen

In versie 7,0-Preview en eerder zijn de verschillende klassen die groepen documenten inkapseld ingedeeld in parallelle klassen hiërarchieën:

  -  `DocumentSearchResult` en `DocumentSearchResult<T>` overgenomen van `DocumentSearchResultBase`
  -  `DocumentSuggestResult` en `DocumentSuggestResult<T>` overgenomen van `DocumentSuggestResultBase`
  -  `IndexAction` en `IndexAction<T>` overgenomen van `IndexActionBase`
  -  `IndexBatch` en `IndexBatch<T>` overgenomen van `IndexBatchBase`
  -  `SearchResult` en `SearchResult<T>` overgenomen van `SearchResultBase`
  -  `SuggestResult` en `SuggestResult<T>` overgenomen van `SuggestResultBase`

De afgeleide typen zonder een generieke type parameter zijn bedoeld om te worden gebruikt in ' dynamisch getypte ' scenario's en aangenomen gebruik van het `Document` type.

Vanaf versie 8,0-Preview zijn de basis klassen en niet-algemene afgeleide klassen allemaal verwijderd. Voor scenario's met dynamisch typen kunt u `IndexBatch<Document>` ,,, enzovoort, gebruiken `DocumentSearchResult<Document>` .
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum verwijderd

De `ExtensibleEnum` basis klasse is verwijderd. Alle klassen die hiervan zijn afgeleid, zijn nu structs, zoals `AnalyzerName` , `DataType` , en `DataSourceType` bijvoorbeeld. De `Create` methoden zijn ook verwijderd. U kunt alleen aanroepen naar verwijderen `Create` omdat deze typen impliciet zijn geconverteerd vanuit teken reeksen. Als dat resulteert in Compileer fouten, kunt u de conversie operator expliciet aanroepen via Casting naar dubbel zinnigheid types. U kunt bijvoorbeeld code als volgt wijzigen:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

in deze:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Eigenschappen die optionele waarden van deze typen behouden, worden nu expliciet getypt als null-waarde, zodat ze optioneel blijven zijn.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults en HitHighlights zijn verwijderd

De `FacetResults` `HitHighlights` klassen en zijn verwijderd. Facet resultaten worden nu getypt als `IDictionary<string, IList<FacetResult>>` en worden gemarkeerd als `IDictionary<string, IList<string>>` . Een snelle manier om Build-fouten op te lossen die door deze wijziging worden geïntroduceerd, is door aliassen toe te voegen aan `using` het begin van elk bestand dat gebruikmaakt van het verwijderde type. Bijvoorbeeld:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Wijzigen in SynonymMap 

De `SynonymMap` constructor heeft geen `enum` para meter meer voor `SynonymMapFormat` . Deze enum heeft slechts één waarde en is daarom overbodig. Als er fouten in de build worden weer geven, verwijder dan de verwijzingen naar de `SynonymMapFormat` para meter.

### <a name="miscellaneous-model-class-changes"></a>Wijzigingen in diverse model klassen

De `AutocompleteMode` eigenschap van `AutocompleteParameters` is niet meer Null-waarden. Als u code hebt waarmee deze eigenschap wordt toegewezen `null` , kunt u deze eenvoudigweg verwijderen en wordt de eigenschap automatisch geïnitialiseerd op de standaard waarde.

De volg orde van de para meters voor de `IndexAction` constructor is gewijzigd, nu deze constructor automatisch wordt gegenereerd. In plaats van de constructor te gebruiken, raden we u aan de fabrieks methoden,, enzovoort, te gebruiken `IndexAction.Upload` `IndexAction.Merge` .

### <a name="removed-preview-features"></a>De preview-functies zijn verwijderd

Als u een upgrade uitvoert van versie 8,0-Preview naar versie 9, moet u er rekening mee houden dat versleuteling met door de klant beheerde sleutels is verwijderd omdat deze functie nog in preview is. Met name de `EncryptionKey` Eigenschappen van `Index` en `SynonymMap` zijn verwijderd.

Als uw toepassing een vaste afhankelijkheid heeft van deze functie, kunt u geen upgrade uitvoeren naar versie 9 van de Azure Search .NET SDK. U kunt versie 8,0-Preview blijven gebruiken. Bedenk echter dat het **gebruik van preview-sdk's in productie toepassingen niet wordt aangeraden**. Preview-functies zijn alleen voor evaluatie en kunnen worden gewijzigd.

> [!NOTE]
> Als u versleutelde indexen of synoniemen hebt gemaakt met versie 8,0-Preview van de SDK, kunt u deze nog steeds gebruiken en de definities wijzigen met versie 9 van de SDK zonder dat dit van invloed is op de versleutelings status. In versie 9 van de SDK wordt de `encryptionKey` eigenschap niet naar de rest API verzonden en als gevolg hiervan wordt de versleutelings status van de resource niet door de rest API gewijzigd. 

### <a name="behavioral-change-in-data-retrieval"></a>Gedrags wijziging in het ophalen van gegevens

Als u gebruikmaakt van de "dynamisch getypeerde" `Search` , `Suggest` of `Get` api's die exemplaren van het type retour neren `Document` , moet u er rekening mee houden dat er nu lege JSON-matrices worden gedeserialiseerd in `object[]` plaats van `string[]` .

## <a name="conclusion"></a>Conclusie
Als u meer informatie wilt over het gebruik van de Azure Search .NET SDK, raadpleegt u de [.net How-to](search-howto-dotnet-sdk.md).

We stellen uw feedback op de SDK. Als u problemen ondervindt, kunt u ons vragen om hulp over [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem voordoet met ' [Azure Search] '.

Hartelijk dank dat u Azure Search hebt gebruikt.