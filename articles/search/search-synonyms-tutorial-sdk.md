---
title: Synoniemen C#-voor beeld
titleSuffix: Azure Cognitive Search
description: In dit C#-voor beeld leert u hoe u de synoniemen functie kunt toevoegen aan een index in azure Cognitive Search. Een synoniemenkaart is een lijst met equivalente termen. Voor velden met ondersteuning voor synoniemen worden query's uitgebreid met de door de gebruiker opgegeven term en alle gerelateerde synoniemen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704653"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Voor beeld: synoniemen voor Azure-Cognitive Search in C toevoegen #

Met synoniemen breidt u een query uit door termen te gebruiken die semantisch overeenkomen met de ingevoerde term. Mogelijk wilt u bijvoorbeeld dat met 'auto' ook documenten worden geretourneerd met de termen 'voertuig' of 'cabrio'. 

In azure Cognitive Search worden synoniemen gedefinieerd in een *synoniemen toewijzing* via *toewijzings regels* die gelijkwaardige voor waarden koppelen. In dit voor beeld worden de essentiële stappen beschreven voor het toevoegen en gebruiken van synoniemen met een bestaande index.

In dit voor beeld leert u het volgende:

> [!div class="checklist"]
> * Maak een synoniemen toewijzing met behulp van de [SynonymMap-klasse](/dotnet/api/azure.search.documents.indexes.models.synonymmap). 
> * Stel de [eigenschap SynonymMapsName](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) in op velden die query-uitbrei ding moeten ondersteunen via synoniemen.

U kunt een veld met synoniemen uitvoeren zoals u dat normaal zou doen. Er is geen aanvullende query syntaxis vereist voor toegang tot synoniemen.

U kunt meerdere synoniementoewijzingen maken en deze als algemene serviceresource beschikbaar maken voor alle indexen. Daarna kunt u op veldniveau aangeven welke u wilt gebruiken. Op het moment dat een index wordt doorzocht, voert Azure Cognitive Search een zoek opdracht in een synoniemen kaart uit, als er een is opgegeven voor velden die worden gebruikt in de query.

> [!NOTE]
> Synoniemen kunnen worden gemaakt via een programma, maar niet in de portal.

## <a name="prerequisites"></a>Vereisten

Voor de zelfstudie gelden de volgende vereisten:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search-service](search-create-service-portal.md)
* [ Uments-pakketAzure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents/)

Als u niet bekend bent met de .NET-client bibliotheek, raadpleegt u [Azure Cognitive Search gebruiken in .net](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Voorbeeldcode

U vindt de volledige bron code van de voorbeeld toepassing die in dit voor beeld wordt gebruikt op [github](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="overview"></a>Overzicht

Voor-en-after-query's worden gebruikt om de waarde van synoniemen te demonstreren. In dit voor beeld voert een voorbeeld toepassing query's uit en worden resultaten geretourneerd op basis van een voor beeld van een "Hotels"-index die is gevuld met twee documenten. Eerst voert de toepassing Zoek query's uit met termen en zinsdelen die niet worden weer gegeven in de index. Ten tweede schakelt de code de functie synoniemen in, vervolgens worden dezelfde query's opnieuw uitgegeven. deze keer resultaten retour neren op basis van overeenkomsten in de synoniemen kaart. 

In de onderstaande code wordt de algehele stroom gedemonstreerd.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>'Voor'-query's

In `RunQueriesWithNonExistentTermsInIndex` voert u zoekquery's uit met 'vijfsterren', 'internet' en 'voordelig EN hotel'.

Woordgroepen query's, zoals ' vijf sterren ', moeten tussen aanhalings tekens staan en kunnen ook escape tekens hebben, afhankelijk van de client.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

Geen van de twee geïndexeerde documenten bevat de voor waarden, dus de volgende uitvoer wordt opgehaald uit de eerste `RunQueriesWithNonExistentTermsInIndex` :  **er komt geen document overeen**.

## <a name="enable-synonyms"></a>Synoniemen inschakelen

Nadat de query's voor ' before ' zijn uitgevoerd, maakt de voorbeeld code gebruik van synoniemen. U kunt synoniemen op basis van twee stappen inschakelen. Eerst definieert en uploadt u synoniems regels. Ten tweede kunt u velden configureren om ze te gebruiken. Het proces wordt beschreven in `UploadSynonyms` en `EnableSynonymsInHotelsIndex`.

1. Voeg een synoniemtoewijzing toe aan de Search-service. In `UploadSynonyms` worden vier regels gedefinieerd voor de synoniemtoewijzing desc-synonymmap, die vervolgens naar de service worden geüpload.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Configureer doorzoekbare velden voor het gebruik van de synoniemtoewijzing in de indexdefinitie. In `AddSynonymMapsToFields` worden synoniemen ingeschakeld voor twee velden (`category` en `tags`) door de eigenschap `SynonymMapNames` in te stellen op de naam van de zojuist geüploade synoniemtoewijzing.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Wanneer u een synoniemtoewijzing toevoegt, is het niet nodig om indexen opnieuw op te bouwen. U kunt een synoniemtoewijzing toevoegen aan uw service en daarna de bestaande velddefinities aanpassen in een index om de nieuwe synoniemtoewijzing te gebruiken. Het toevoegen van nieuwe kenmerken heeft geen invloed op de beschikbaarheid van indexen. Ditzelfde is van toepassing op het uitschakelen van synoniemen voor een veld. U kunt eenvoudigweg de eigenschap `SynonymMapNames` instellen op een lege lijst.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>'Na'-query's

Wanneer de synoniemtoewijzing is geüpload en de index is bijgewerkt voor gebruik van de synoniemtoewijzing, wordt met de tweede `RunQueriesWithNonExistentTermsInIndex`-aanroep de volgende uitvoer gegenereerd:

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

Met de eerste query wordt het document opgehaald uit de regel `five star=>luxury`. Met de tweede query wordt de zoekopdracht uitgebreid met `internet,wifi` en met de derde met zowel `hotel, motel` als `economy,inexpensive=>budget` bij het zoeken naar de overeenkomende documenten.

Door synoniemen toe te voegen, wordt de zoekervaring volledig veranderd. In dit voor beeld zijn de oorspronkelijke query's niet in staat om zinvolle resultaten te retour neren, zelfs als de documenten in onze index relevant waren. Door synoniemen in te schakelen, breidt u de index zodanig uit dat deze ook termen bevat die regelmatig worden gebruikt, zonder dat daarbij wijzigingen worden aangebracht aan de onderliggende gegevens in de index.

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om na een voor beeld op te schonen is door de resource groep te verwijderen die de Azure Cognitive Search-service bevat. U kunt de resourcegroep nu verwijderen om alles daarin permanent te verwijderen. In de portal bevindt de naam van de resource groep zich op de pagina overzicht van Azure Cognitive Search service.

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld wordt de functie synoniemen in C# code getoond voor het maken en plaatsen van toewijzings regels en roept u vervolgens de synoniemen toewijzing aan in een query. Aanvullende informatie vindt u in de referentiedocumentatie voor de [.NET-SDK](/dotnet/api/overview/azure/search.documents-readme) en [REST-API](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Synoniemen gebruiken in azure Cognitive Search](search-synonyms.md)