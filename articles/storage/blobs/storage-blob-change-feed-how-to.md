---
title: Wijzigings feed verwerken in Azure Blob Storage | Microsoft Docs
description: Meer informatie over het verwerken van Logboeken voor wijzigings invoer in een .NET-client toepassing
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e89fdfba852fc056cf48efd1b92daabb272cf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89568248"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Wijzigings feed in Azure Blob Storage verwerken

Change feed biedt transactie logboeken van alle wijzigingen die plaatsvinden in de blobs en de BLOB-meta gegevens in uw opslag account. In dit artikel leest u hoe u wijzigingen in feeds kunt lezen met behulp van de processor bibliotheek voor het wijzigen van de blob.

Zie [Change feed in Azure Blob Storage](storage-blob-change-feed.md)voor meer informatie over de wijzigings feed.

## <a name="get-the-blob-change-feed-processor-library"></a>De processor bibliotheek voor het wijzigen van de BLOB-feed ophalen

1. Open een opdracht venster (bijvoorbeeld: Windows Power shell).
2. Installeer in de projectmap het [ **Azure. storage. blobs. Changefeed** NuGet-pakket](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/).

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>Records lezen

> [!NOTE]
> De wijzigings feed is een onveranderlijke en alleen-lezen entiteit in uw opslag account. Een wille keurig aantal toepassingen kan de wijzigings feed gelijktijdig en onafhankelijk van hun eigen gemak lezen en verwerken. Records worden niet uit de wijzigings feed verwijderd wanneer deze door een toepassing worden gelezen. De lees-of iteratie status van elke verbruikte lezer is onafhankelijk en wordt alleen door uw toepassing onderhouden.

In dit voor beeld worden alle records in de wijzigings feed door lopen, worden deze toegevoegd aan een lijst en wordt die lijst vervolgens geretourneerd naar de aanroeper.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

In dit voor beeld wordt een aantal waarden uit elke record in de lijst naar de console afgedrukt. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Het lezen van records vanaf een opgeslagen positie hervatten

U kunt ervoor kiezen om uw Lees positie op te slaan in de wijzigings feed en vervolgens de gegevens in een later tijdstip opnieuw te doorzoeken. U kunt de Lees positie opslaan door de cursor voor de wijzigings invoer op te halen. De cursor is een **teken reeks** en uw toepassing kan deze teken reeks op een wille keurige manier opslaan die zinvol is voor het ontwerp van uw toepassing (bijvoorbeeld: naar een bestand of data base).

In dit voor beeld worden alle records in de wijzigings feed door lopen, worden deze toegevoegd aan een lijst en wordt de cursor opgeslagen. De lijst en de cursor worden geretourneerd naar de aanroeper. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Stroom verwerking van records

U kunt ervoor kiezen om wijzigingen in de feed te verwerken wanneer ze worden toegewezen aan de wijzigings feed. Zie [specificaties](storage-blob-change-feed.md#specifications). De wijzigings gebeurtenissen worden gemiddeld met een periode van 60 seconden gepubliceerd in de wijzigings feed. U wordt aangeraden te controleren of er nieuwe wijzigingen zijn met deze periode wanneer u uw polling-interval opgeeft.

In dit voor beeld wordt periodiek gecontroleerd op wijzigingen.  Als er wijzigings records bestaan, worden deze door de code verwerkt en wordt de cursor voor wijzigings invoer opgeslagen. Op die manier als het proces wordt gestopt en vervolgens weer wordt gestart, kan de toepassing de cursor gebruiken om de verwerking van records te hervatten waar deze voor het laatst was uitgeschakeld. In dit voor beeld wordt de cursor opgeslagen in een configuratie bestand van een lokale toepassing, maar de toepassing kan deze opslaan in een wille keurige vorm die het handigst is voor uw scenario. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Records in een tijds bereik lezen

U kunt records lezen die binnen een bepaald tijds bereik vallen. In dit voor beeld worden alle records in de wijzigings feed door lopen van 3:00 uur op 2 2020 en 2:00 uur op 7 2020 augustus, worden deze toegevoegd aan een lijst en wordt die lijst vervolgens geretourneerd naar de aanroeper.

### <a name="selecting-segments-for-a-time-range"></a>Segmenten selecteren voor een tijds bereik

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

De start tijd die u opgeeft, wordt naar beneden afgerond op het dichtstbijzijnde uur en de eind tijd wordt naar boven afgerond op het dichtstbijzijnde uur. Het is mogelijk dat gebruikers gebeurtenissen kunnen zien die zijn opgetreden vóór de begin tijd en na de eind tijd. Het is ook mogelijk dat sommige gebeurtenissen die plaatsvinden tussen de begin-en eind tijd niet worden weer gegeven. Dat komt omdat gebeurtenissen mogelijk worden vastgelegd in het uur vóór de begin tijd of tijdens het uur na de eind tijd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over feeds voor wijzigings Logboeken. Zie [Change feed in Azure Blob Storage](storage-blob-change-feed.md)
