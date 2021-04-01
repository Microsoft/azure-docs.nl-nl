---
title: Een upgrade uitvoeren naar Azure Search .NET SDK versie 5
titleSuffix: Azure Cognitive Search
description: Code migreren naar de Azure Search .NET SDK versie 5 van oudere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7f0e994cd41fc55824cf347a51ccfed25589a310
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91539534"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Een upgrade uitvoeren naar Azure Search .NET SDK versie 5

Als u versie 4,0-Preview of ouder van de [.NET SDK](/dotnet/api/overview/azure/search)gebruikt, helpt dit artikel bij het upgraden van uw toepassing tot het gebruik van versie 5.

Zie [Azure Search van een .NET-toepassing gebruiken](search-howto-dotnet-sdk.md)voor een meer algemeen overzicht van de SDK, inclusief voor beelden.

Versie 5 van de Azure Search .NET SDK bevat enkele wijzigingen ten opzichte van eerdere versies. Dit zijn voornamelijk minder jarigen, zodat het wijzigen van uw code slechts een minimale inspanning vereist. Zie de stappen voor het uitvoeren van een [upgrade](#UpgradeSteps) voor instructies over het wijzigen van de code voor het gebruik van de nieuwe SDK-versie.

> [!NOTE]
> Als u versie 2,0-Preview of ouder gebruikt, moet u eerst een upgrade uitvoeren naar versie 3 en vervolgens een upgrade uitvoeren naar versie 5. Zie [upgraden naar de Azure Search .NET SDK versie 3](search-dotnet-sdk-migration.md) voor instructies.
>
> Uw Azure Search service-exemplaar ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Wat is er nieuw in versie 5
Versie 5 van de Azure Search .NET SDK streeft naar de meest recente, algemeen beschik bare versie van de Azure Search REST API, met name 2017-11-11. Dit maakt het mogelijk om nieuwe functies van Azure Search van een .NET-toepassing te gebruiken, met inbegrip van het volgende:

* [Synoniemen](search-synonyms.md).
* U kunt nu programmatisch toegang krijgen tot waarschuwingen in de indexerings geschiedenis (Zie de `Warning` eigenschap van `IndexerExecutionResult` in de [.net-referentie](/dotnet/api/microsoft.azure.search.models.indexerexecutionresult) voor meer informatie).
* Ondersteuning voor .NET Core 2.
* De nieuwe pakket structuur biedt ondersteuning voor het gebruik van alleen de onderdelen van de SDK die u nodig hebt (Zie voor meer informatie [wijzigingen in versie 5 afbreken](#ListOfChanges) ).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade
Werk eerst uw NuGet-verwijzing bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager-console of door met de rechter muisknop te klikken op uw project verwijzingen en vervolgens NuGet-pakketten beheren te selecteren... in Visual Studio.

Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat het geval is, bent u klaar om aan de slag te gaan.

Als uw build mislukt, ziet u een build-fout als de volgende:

```output
The name 'SuggesterSearchMode' does not exist in the current context
```

De volgende stap is het oplossen van deze build-fout. Zie belang rijke [wijzigingen in versie 5](#ListOfChanges) voor meer informatie over de oorzaak van de fout en hoe u deze kunt oplossen.

Als gevolg van wijzigingen in de verpakking van de Azure Search .NET SDK, moet u uw toepassing opnieuw samen stellen om versie 5 te kunnen gebruiken. Deze wijzigingen worden gedetailleerd beschreven in de belang rijke [wijzigingen in versie 5](#ListOfChanges).

Mogelijk worden er aanvullende build-waarschuwingen met betrekking tot verouderde methoden of eigenschappen weer gegeven. De waarschuwingen bevatten instructies over wat u moet gebruiken in plaats van de afgeschafte functie. Als uw toepassing bijvoorbeeld gebruikmaakt van de `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` -methode, ontvangt u een waarschuwing met de tekst ' dit gedrag is nu standaard ingeschakeld, zodat deze methode niet meer nodig is. '

Wanneer u eventuele build-fouten of-waarschuwingen hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Wijzigingen in versie 5 verbreken

### <a name="new-package-structure"></a>Nieuwe pakket structuur

De meest substantiële wijziging in versie 5 is dat de `Microsoft.Azure.Search` Assembly en de inhoud ervan zijn onderverdeeld in vier afzonderlijke assembly's die nu worden gedistribueerd als vier afzonderlijke NuGet-pakketten:

 - `Microsoft.Azure.Search`: Dit is een meta pakket dat alle andere Azure Search pakketten als afhankelijkheden bevat. Als u een upgrade uitvoert van een eerdere versie van de SDK, moet u een upgrade uitvoeren voor het pakket en het opnieuw samen stellen van de nieuwe versie.
 - `Microsoft.Azure.Search.Data`: Gebruik dit pakket als u een .NET-toepassing ontwikkelt met Azure Search en u alleen documenten in uw indexen hoeft op te vragen of bij te werken. Als u ook indexen, synoniemen of andere bronnen op service niveau wilt maken of bijwerken, gebruikt u `Microsoft.Azure.Search` in plaats daarvan het pakket.
 - `Microsoft.Azure.Search.Service`: Gebruik dit pakket als u Automation in .NET ontwikkelt voor het beheren van Azure Search indexen, synoniemen, Indexeer functies, gegevens bronnen of andere bronnen op service niveau. Als u alleen documenten in uw indexen hoeft op te vragen of bij te werken, gebruikt u `Microsoft.Azure.Search.Data` in plaats daarvan het pakket. Als u de functionaliteit van Azure Search nodig hebt, gebruikt u `Microsoft.Azure.Search` in plaats daarvan het pakket.
 - `Microsoft.Azure.Search.Common`: Algemene typen die nodig zijn voor de Azure Search .NET-bibliotheken. U hoeft dit pakket niet rechtstreeks in uw toepassing te gebruiken. Het is alleen bedoeld om te worden gebruikt als afhankelijkheid.
 
Deze wijziging is technisch afgebroken omdat er veel typen tussen assembly's zijn verplaatst. Daarom is het opnieuw samen stellen van uw toepassing nood zakelijk om een upgrade uit te kunnen zetten naar versie 5 van de SDK.

Er is een klein aantal andere belang rijke wijzigingen in versie 5 waarvoor code wijzigingen nodig zijn naast het opnieuw samen stellen van uw toepassing.

### <a name="change-to-suggesters"></a>Wijzigen in suggesties 

De `Suggester` constructor heeft geen `enum` para meter meer voor `SuggesterSearchMode` . Deze enum heeft slechts één waarde en is daarom overbodig. Als er fouten in de build worden weer geven, verwijder dan de verwijzingen naar de `SuggesterSearchMode` para meter.

### <a name="removed-obsolete-members"></a>Verouderde leden zijn verwijderd

Mogelijk worden er Build-fouten met betrekking tot methoden of eigenschappen weer geven die in eerdere versies als verouderd zijn gemarkeerd en vervolgens in versie 5 zijn verwijderd. Als u dergelijke fouten tegen komt, kunt u deze als volgt oplossen:

- Als u de methode gebruikt `IndexingParametersExtensions.IndexStorageMetadataOnly` , gebruikt u `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` in plaats daarvan.
- Als u de methode gebruikt `IndexingParametersExtensions.SkipContent` , gebruikt u `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` in plaats daarvan.

### <a name="removed-preview-features"></a>De preview-functies zijn verwijderd

Als u een upgrade uitvoert van versie 4,0-Preview naar versie 5, moet u er rekening mee houden dat JSON-matrix en de ondersteuning voor het parseren van CSV voor BLOB-Indexeer functies zijn verwijderd, omdat deze onderdelen nog in Preview zijn. Met name de volgende methoden van de `IndexingParametersExtensions` klasse zijn verwijderd:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Als uw toepassing een vaste afhankelijkheid van deze functies heeft, kunt u niet upgraden naar versie 5 van de Azure Search .NET SDK. U kunt versie 4,0-Preview blijven gebruiken. Bedenk echter dat het **gebruik van preview-sdk's in productie toepassingen niet wordt aangeraden**. Preview-functies zijn alleen voor evaluatie en kunnen worden gewijzigd.

## <a name="conclusion"></a>Conclusie
Als u meer informatie wilt over het gebruik van de Azure Search .NET SDK, raadpleegt u de [.net How-to](search-howto-dotnet-sdk.md).

We stellen uw feedback op de SDK. Als u problemen ondervindt, kunt u ons vragen om hulp over [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem voordoet met ' [Azure Search] '.

Hartelijk dank dat u Azure Search hebt gebruikt.