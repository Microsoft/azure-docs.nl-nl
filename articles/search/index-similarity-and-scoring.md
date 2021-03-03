---
title: Gelijkenis en score overzicht
titleSuffix: Azure Cognitive Search
description: Hierin worden de concepten van gelijkenis en waardering beschreven en wat een ontwikkelaar kan doen om het Score resultaat aan te passen.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 72243f896b2cf7dbab61a42514bee634da28d4c6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676327"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Gelijkenis en score in azure Cognitive Search

In dit artikel worden de twee overeenkomst classificatie algoritmen in azure Cognitive Search beschreven. Daarnaast worden er twee verwante functies geïntroduceerd: *Score profielen* (criteria voor het aanpassen van een zoek Score) en de para meter *featuresMode* (Hiermee wordt een zoek Score uitgepakt om meer details weer te geven). 

Een derde semantische, herclassificaties algoritme is momenteel beschikbaar als open bare preview. Voor meer informatie gaat u naar [overzicht van semantisch zoeken](semantic-search-overview.md).

## <a name="similarity-ranking-algorithms"></a>Classificatie algoritmen voor gelijkenis

Azure Cognitive Search ondersteunt twee gelijkenis classificatie algoritmen.

| Algoritme | Score | Beschikbaarheid |
|-----------|-------|--------------|
| ClassicSimilarity | @search.score | Wordt gebruikt door alle zoek services tot en met 15 juli 2020. |
| BM25Similarity | @search.score | Wordt gebruikt door alle zoek services die zijn gemaakt na 15 juli. Oudere services die standaard klassiek gebruiken, kunnen [zich aanmelden bij BM25](index-ranking-similarity.md). |

Zowel de klassieke als de BM25 zijn TF-IDF-achtige ophaal functies die gebruikmaken van de term frequentie (TF) en de inverse document frequentie (IDF) als variabelen voor het berekenen van relevantie scores voor elk document-query paar, dat vervolgens wordt gebruikt voor de classificatie, terwijl het concept lijkt op klassieke, BM25 de hoofdmap in Probabilistic gegevens ophalen om deze te verbeteren. BM25 biedt ook geavanceerde aanpassings opties, zoals het toestaan van de gebruiker om te bepalen hoe de relevantie score wordt geschaald met de term frequentie van overeenkomende voor waarden.

Het volgende video segment is een snelle doorstuur naar een uitleg van de algemeen beschik bare classificatie algoritmen die worden gebruikt in azure Cognitive Search. U kunt de volledige video bekijken voor meer achtergrond.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="relevance-scoring"></a>Relevantiescore

Score verwijst naar de berekening van een zoek score voor elk item dat wordt geretourneerd in Zoek resultaten voor Zoek opdrachten in volledige tekst. De score is een indicatie van de relevantie van een item in de context van de huidige query. Hoe hoger de score, hoe relevanter het item. In Zoek resultaten worden items gerangschikt van hoog naar laag, op basis van de zoek scores die voor elk item worden berekend. De score wordt in het antwoord geretourneerd @search.score op elk document.

Standaard worden de bovenste 50 geretourneerd in het antwoord, maar u kunt de para meter **$Top** gebruiken om een kleiner of groter aantal items te retour neren (maxi maal 1000 in één antwoord) en **$Skip** om de volgende set resultaten op te halen.

De zoek score wordt berekend op basis van de statistische eigenschappen van de gegevens en de query. Met Azure Cognitive Search worden documenten gevonden die overeenkomen met zoek termen (sommige of alle, afhankelijk van [Search mode](/rest/api/searchservice/search-documents#query-parameters)), waarbij wordt voldaan aan documenten die veel exemplaren van de zoek term bevatten. De zoek score gaat zelfs hoger omhoog als de term zelden voor komt in de gegevens index, maar gebruikelijk is binnen het document. De basis voor deze aanpak voor het berekenen van de relevantie is de *IDF TF-of* term frequentie-inverse document frequentie.

Zoek Score waarden kunnen worden herhaald in een resultatenset. Wanneer meerdere treffers dezelfde Zoek score hebben, wordt de volg orde van dezelfde gescoorde items niet gedefinieerd en is deze niet stabiel. Voer de query opnieuw uit en u kunt de positie van items verschuiving zien, met name als u de gratis service of een factureer bare service met meerdere replica's gebruikt. Als er twee items met een identieke Score worden opgegeven, is er geen garantie dat er eerst een wordt weer gegeven.

Als u het koppelen tussen herhaalde scores wilt verstoren, kunt u een **$OrderBy** -component toevoegen aan de eerste order by-Score en vervolgens sorteren op een ander sorteerbaar veld (bijvoorbeeld `$orderby=search.score() desc,Rating desc` ). Zie [$OrderBy](search-query-odata-orderby.md)voor meer informatie.

> [!NOTE]
> Een `@search.score = 1.00` geeft een niet-gescoorde of niet-geclassificeerde resultatenset aan. De score is gelijkmatig verdeeld over alle resultaten. Niet-gescoorde resultaten treden op wanneer het query formulier fuzzy zoeken, joker tekens of regex-query's of een **$filter** expressie is.

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>Score statistieken en plak sessies

Voor schaal baarheid distribueert Azure Cognitive Search elke index horizon taal via een sharding-proces, wat betekent dat [delen van een index fysiek gescheiden zijn](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

De Score van een document wordt standaard berekend op basis van de statistische eigenschappen van de gegevens *in een Shard*. Deze aanpak is over het algemeen geen probleem voor een grote hoeveelheid gegevens verzameling en levert betere prestaties dan het berekenen van de score op basis van informatie over alle Shards. Dit zou ertoe kunnen leiden dat met behulp van deze prestatie optimalisatie twee zeer vergelijk bare documenten (of zelfs identieke documenten) worden opgedeeld als ze in verschillende Shards eindigen op verschillende relevantie scores.

Als u de score wilt berekenen op basis van de statistische eigenschappen van alle Shards, kunt u dit doen door *scoringStatistics = Global* toe te voegen als een [query parameter](/rest/api/searchservice/search-documents) (of door *' scoringStatistics ': ' Global '* toe te voegen als een hoofd parameter van de [query-aanvraag](/rest/api/searchservice/search-documents)).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Als u scoringStatistics gebruikt, zorgt u ervoor dat alle Shards in dezelfde replica dezelfde resultaten hebben. Dat wil zeggen dat verschillende replica's enigszins verschillen van elkaar, aangezien ze altijd worden bijgewerkt met de laatste wijzigingen in uw index. In sommige scenario's wilt u mogelijk dat uw gebruikers tijdens een query sessie meer consistente resultaten krijgen. In dergelijke scenario's kunt u een `sessionId` als onderdeel van uw query's opgeven. De `sessionId` is een unieke teken reeks die u maakt om te verwijzen naar een unieke gebruikers sessie.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Zolang hetzelfde `sessionId` wordt gebruikt, wordt er een poging gedaan om op dezelfde replica te richten, waardoor de consistentie van de resultaten die uw gebruikers worden weer gegeven, wordt verhoogd. 

> [!NOTE]
> Als u dezelfde `sessionId` waarden herhaaldelijk opnieuw gebruikt, kan dit de taak verdeling van de aanvragen tussen replica's beïnvloeden en de prestaties van de zoek service nadelig beïnvloeden. De waarde die wordt gebruikt als sessionId mag niet beginnen met een ' _ '-teken.

## <a name="scoring-profiles"></a>Scoreprofielen

U kunt de manier aanpassen waarop verschillende velden worden gerangschikt door een *Score profiel* te definiëren. Score profielen geven u meer controle over de rang schikking van items in Zoek resultaten. U kunt bijvoorbeeld items verhogen op basis van hun omzet potentieel, nieuwere items promoten of mogelijk objecten verhogen die in de voor Raad te lang zijn. 

Een score profiel maakt deel uit van de definitie van de index, die bestaat uit gewogen velden, functies en para meters. Zie [Score profielen](index-add-scoring-profiles.md)voor meer informatie over het definiëren van een.

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode-para meter (preview-versie)

[Zoek documenten](/rest/api/searchservice/preview-api/search-documents) aanvragen hebben een nieuwe [featuresMode](/rest/api/searchservice/preview-api/search-documents#featuresmode) -para meter die aanvullende details kan geven over relevantie op veld niveau. Terwijl de `@searchScore` wordt berekend voor het document all-up (zoals relevant is dit document in de context van deze query), via featuresMode kunt u informatie ophalen over afzonderlijke velden, zoals wordt weer gegeven in een `@search.features` structuur. De structuur bevat alle velden die in de query worden gebruikt (specifieke velden via **searchFields** in een query of alle velden die worden **doorzocht** in een index). Voor elk veld krijgt u de volgende waarden:

+ Aantal unieke tokens gevonden in het veld
+ Vergelijk bare Score of een meting van de manier waarop de inhoud van het veld relatief is ten opzichte van de query term
+ De term frequentie of het aantal keren dat de query term in het veld is gevonden

Een antwoord dat is opgenomen in de velden Beschrijving en titel, `@search.features` kan er als volgt uitzien:

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

U kunt deze gegevens punten gebruiken in [aangepaste Score oplossingen](https://github.com/Azure-Samples/search-ranking-tutorial) of met de informatie voor fout opsporing in relevantie problemen.

## <a name="see-also"></a>Zie ook

+ [Score profielen](index-add-scoring-profiles.md)
+ [REST API referentie](/rest/api/searchservice/)
+ [Documenten zoeken-API](/rest/api/searchservice/search-documents)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)