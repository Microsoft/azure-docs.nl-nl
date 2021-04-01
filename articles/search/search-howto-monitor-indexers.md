---
title: De status en resultaten van de Indexeer functie bewaken
titleSuffix: Azure Cognitive Search
description: De status, voortgang en resultaten van Azure Cognitive Search Indexeer functies in de Azure Portal bewaken met behulp van de REST API of de .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063240"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Azure Cognitive Search Indexeer functie-status en-resultaten controleren

U kunt de verwerking van indexeren in het Azure Portal bewaken, of programmatisch via REST-aanroepen of een Azure-SDK. Naast de status van de Indexeer functie zelf, kunt u de begin-en eind tijden en gedetailleerde fouten en waarschuwingen van een bepaalde uitvoering bekijken.

## <a name="monitor-using-azure-portal"></a>Controleren met behulp van Azure Portal

U kunt de huidige status van al uw Indexeer functies zien op de pagina overzicht van de zoek service. Portal pagina's worden elke paar minuten vernieuwd, zodat u geen bewijs ziet dat een nieuwe Indexeer functie meteen wordt uitgevoerd.

   ![Lijst met Indexeer functies](media/search-monitor-indexers/indexers-list.png "Lijst met Indexeer functies")

| Status | Beschrijving |
|--------|-------------|
| **Wordt uitgevoerd** | Geeft actieve uitvoering aan. De portal rapporteert over gedeeltelijke informatie. Als het indexeren wordt uitgevoerd, kunt u de waarde voor de **geslaagde docs** in antwoord bekijken. Indexeer functies die grote hoeveel heden gegevens verwerken, kunnen veel tijd in beslag nemen. Indexeer functies die miljoenen bron documenten verwerken, kunnen bijvoorbeeld 24 uur worden uitgevoerd en bijna onmiddellijk opnieuw worden opgestart. De status voor Indexeer functies met een hoog volume wordt mogelijk altijd in de portal **uitgevoerd** . Zelfs wanneer een Indexeer functie wordt uitgevoerd, zijn er details beschikbaar over lopende voortgang en eerdere uitvoeringen. |
| **Geslaagd** | Geeft aan dat de uitvoering is geslaagd. Het uitvoeren van een Indexeer functie kan worden geslaagd, zelfs als afzonderlijke documenten fouten bevatten, als het aantal fouten minder is dan de instelling **maximale mislukte items** van de Indexeer functie. |
| **Mislukt** | Het aantal fouten heeft de **maximale hoeveelheid mislukte items** overschreden en de indexering is gestopt. |
| **Opnieuw instellen** | De interne status voor het bijhouden van wijzigingen van de Indexeer functie is opnieuw ingesteld. De Indexeer functie wordt volledig uitgevoerd, alle documenten vernieuwd, en niet alleen die met nieuwe tijds tempels. |

U kunt klikken op een Indexeer functie in de lijst om meer details weer te geven over de huidige en recente uitvoeringen van de Indexeer functie.

   ![Overzicht van Indexeer functie en uitvoerings geschiedenis](media/search-monitor-indexers/indexer-summary.png "Overzicht van Indexeer functie en uitvoerings geschiedenis")

In de samenvattings grafiek van de **Indexeer functie** wordt een grafiek weer gegeven van het aantal documenten dat is verwerkt in de meest recente uitvoeringen.

In de lijst **uitvoerings Details** worden maxi maal 50 van de meest recente uitvoerings resultaten weer gegeven. Klik op een uitvoer resultaat in de lijst om specifieke informatie over die uitvoering weer te geven. Dit geldt ook voor de start-en eind tijden en eventuele fouten en waarschuwingen die zijn opgetreden.

   ![Details van de uitvoering van de Indexeer functie](media/search-monitor-indexers/indexer-execution.png "Details van de uitvoering van de Indexeer functie")

Als er tijdens de uitvoering Documentspecifieke problemen zijn, worden deze weer gegeven in de velden fouten en waarschuwingen.

   ![Details van Indexeer functie met fouten](media/search-monitor-indexers/indexer-execution-error.png "Details van Indexeer functie met fouten")

Waarschuwingen zijn gebruikelijk met bepaalde typen Indexeer functies en geven niet altijd een probleem aan. Bijvoorbeeld Indexeer functies die gebruikmaken van cognitieve Services kunnen waarschuwingen rapporteren wanneer afbeeldings-of PDF-bestanden geen tekst bevatten die moeten worden verwerkt. 

Zie problemen [met algemene Indexeer functies in Azure Cognitive Search oplossen](search-indexer-troubleshooting.md)voor meer informatie over het onderzoeken van fouten en waarschuwingen voor Indexeer functies.

## <a name="monitor-using-get-indexer-status-rest-api"></a>Bewaken met de status van de Indexeer functie ophalen (REST API)

U kunt de status en de uitvoerings geschiedenis van een Indexeer functie ophalen met behulp van de [opdracht Get Indexing-status](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Het antwoord bevat de algemene status van de Indexeer functie, de laatste (of in uitvoering zijnde) indexerings aanroep en de geschiedenis van recente Indexeer functie-aanroepen.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

De uitvoerings geschiedenis bevat tot de 50 meest recente uitvoeringen, die in omgekeerde chronologische volg orde (meest recent eerst) worden gesorteerd.

Houd er rekening mee dat er twee verschillende status waarden zijn. De status op het hoogste niveau is voor de Indexeer functie zelf. Een Indexeer functie-status van **actief** betekent dat de Indexeer functie op de juiste wijze is ingesteld en kan worden uitgevoerd, maar niet dat deze op dat moment wordt uitgevoerd.

Elke uitvoering van de Indexeer functie heeft ook een eigen status die aangeeft of de specifieke uitvoering **actief** is (wordt uitgevoerd) of al is voltooid met de status **geslaagd**, **transientFailure** of **persistentFailure** . 

Wanneer een Indexeer functie opnieuw wordt ingesteld om de status van het bijhouden van wijzigingen te vernieuwen, wordt een afzonderlijke vermelding voor de uitvoerings geschiedenis toegevoegd met de status **opnieuw instellen** .

Zie de status van de [Indexeer functie ophalen](/rest/api/searchservice/get-indexer-status)voor meer informatie over de status codes en de bewakings gegevens van de Indexeer functie.

## <a name="monitor-using-net"></a>Controleren met behulp van .NET

In het volgende C#-voor beeld wordt met behulp van de Azure Cognitive Search .NET SDK informatie over de status van een Indexeer functie en de resultaten van de meest recente (of doorlopende) uitvoering naar de-console geschreven.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

De uitvoer in de-console ziet er ongeveer als volgt uit:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Houd er rekening mee dat er twee verschillende status waarden zijn. De status op het hoogste niveau is de status van de Indexeer functie zelf. Een Indexeer functie-status van **actief** betekent dat de Indexeer functie juist is ingesteld en beschikbaar is voor uitvoering, maar niet dat deze momenteel wordt uitgevoerd.

Elke uitvoering van de Indexeer functie heeft ook een eigen status om te bepalen of die specifieke uitvoering actief is (wordt **uitgevoerd**) of al is voltooid met de status **geslaagd** of **TransientError** . 

Wanneer een Indexeer functie opnieuw wordt ingesteld om de status van het bijhouden van wijzigingen te vernieuwen, wordt een afzonderlijk geschiedenis item toegevoegd met de status **opnieuw instellen** .

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende API-naslag informatie voor meer informatie over de status codes en de bewakings gegevens van de Indexeer functie:

* [GetIndexerStatus (REST API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)