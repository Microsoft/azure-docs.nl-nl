---
title: Uitvoer van Azure Stream Analytics
description: In dit artikel worden de opties voor gegevens uitvoer beschreven die beschikbaar zijn voor Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: 3ce4f673657561e196520466b569d0cf83d75a8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019343"
---
# <a name="outputs-from-azure-stream-analytics"></a>Uitvoer van Azure Stream Analytics

Een Azure Stream Analytics-taak bestaat uit een invoer, query en een uitvoer. Er zijn verschillende uitvoer typen waarnaar u getransformeerde gegevens kunt verzenden. In dit artikel vindt u een overzicht van de ondersteunde Stream Analytics uitvoer. Wanneer u uw Stream Analytics query ontwerpt, raadpleegt u de naam van de uitvoer met behulp van de [component into](/stream-analytics-query/into-azure-stream-analytics). U kunt één uitvoer per taak of meerdere uitvoer per streaming taak gebruiken (als u deze nodig hebt) door meerdere INTO-componenten aan de query toe te voegen.

Als u Stream Analytics taak uitvoer wilt maken, bewerken en testen, kunt u de [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.net API](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations), [rest API](/rest/api/streamanalytics/)en [Visual Studio](stream-analytics-quick-create-vs.md)gebruiken.

Sommige typen uitvoer ondersteunen [partitionering](#partitioning)en [uitvoer batch formaten](#output-batch-size) zijn afhankelijk van de door voer te optimaliseren. De volgende tabel bevat de functies die worden ondersteund voor elk uitvoer type:

| Uitvoertype | Partitionering | Beveiliging | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Ja|Azure Active Directory-gebruiker </br> , Beheerde identiteit|
|[Azure SQL Database](sql-database-output.md)|Ja, optioneel.|SQL-gebruikers auth, </br> Beheerde identiteit (preview)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Ja|SQL-gebruikers auth, </br> Beheerde identiteit (preview)|
|[Blob-opslag en Azure Data Lake gen 2](blob-storage-azure-data-lake-gen2-output.md)|Ja|Toegangs sleutel, </br> Beheerde identiteit (preview)|
|[Azure Event Hubs](event-hubs-output.md)|Ja, de partitie sleutel kolom moet worden ingesteld in de uitvoer configuratie.|Toegangs sleutel, </br> Beheerde identiteit (preview)|
|[Power BI](power-bi-output.md)|Nee|Azure Active Directory gebruiker, </br> Beheerde identiteit|
|[Azure Table storage](table-storage-output.md)|Ja|Accountsleutel|
|[Azure Service Bus wachtrijen](service-bus-queues-output.md)|Ja|Toegangssleutel|
|[Azure Service Bus onderwerpen](service-bus-topics-output.md)|Ja|Toegangssleutel|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Ja|Toegangssleutel|
|[Azure Functions](azure-functions-output.md)|Ja|Toegangssleutel|

## <a name="partitioning"></a>Partitionering

Stream Analytics ondersteunt partities voor alle uitvoer, met uitzonde ring van Power BI. Voor meer informatie over partitie sleutels en het aantal uitvoer schrijvers raadpleegt u het artikel voor het specifieke uitvoer type waarin u bent geïnteresseerd. Alle uitvoer artikelen zijn gekoppeld in de vorige sectie.  

Voor een geavanceerde afstemming van de partities kan ook het aantal uitvoer schrijvers worden beheerd met behulp van een `INTO <partition count>` ( [](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)Zie) component in uw query. Dit kan handig zijn bij het bereiken van een gewenste taak topologie. Als uw uitvoer adapter niet is gepartitioneerd, veroorzaakt een gebrek aan gegevens in één invoer partitie een vertraging tot de duur van de late aankomst. In dergelijke gevallen wordt de uitvoer samengevoegd met één schrijver, wat kan leiden tot knel punten in de pijp lijn. Zie [Azure stream Analytics overwegingen voor gebeurtenis orders](./stream-analytics-time-handling.md)voor meer informatie over het beleid voor late ontvangst.

## <a name="output-batch-size"></a>Grootte van uitvoer batch

Alle uitvoer bewerkingen ondersteunen batch verwerking, maar slechts een bepaalde ondersteunings Batch grootte expliciet. Azure Stream Analytics gebruikt batches van het formaat variable om gebeurtenissen te verwerken en te schrijven naar uitvoer. Normaal gesp roken schrijft de Stream Analytics engine niet één bericht tegelijk en worden batches gebruikt voor efficiëntie. Wanneer het aantal binnenkomende en uitgaande gebeurtenissen hoog is, gebruikt Stream Analytics grotere batches. Wanneer het uitvoer bedrag laag is, worden kleinere batches gebruikt om latentie laag te blijven.

## <a name="parquet-output-batching-window-properties"></a>Eigenschappen van Parquet-uitvoer Batch venster

Wanneer u Azure Resource Manager-sjabloon implementatie of het REST API gebruikt, zijn de twee eigenschappen van het batch venster:

1. *timeWindow*

   De maximale wacht tijd per batch. De waarde moet een teken reeks van time span zijn. Bijvoorbeeld "00:02:00" voor twee minuten. Na deze periode wordt de batch naar de uitvoer geschreven, zelfs als niet aan de vereiste voor de minimum rijen wordt voldaan. De standaard waarde is 1 minuut en het toegestane maximum is 2 uur. Als uw BLOB-uitvoer een patroon frequentie voor het pad heeft, kan de wacht tijd niet hoger zijn dan het tijds bereik van de partitie.

2. *sizeWindow*

   Het aantal minimum rijen per batch. Voor Parquet maakt elke batch een nieuw bestand. De huidige standaard waarde is 2.000 rijen en het toegestane maximum is 10.000 rijen.

Deze batch venster Eigenschappen worden alleen ondersteund door API versie **2017-04-01-preview**. Hieronder ziet u een voor beeld van de JSON-nettolading voor een REST API aanroep:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>
> [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
