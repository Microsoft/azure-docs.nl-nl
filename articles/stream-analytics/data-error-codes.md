---
title: Gegevens fout codes-Azure Stream Analytics
description: Problemen met Azure Stream Analytics oplossen met gegevens fout codes.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016487"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Fout codes voor Azure Stream Analytics gegevens

U kunt activiteiten logboeken en resource Logboeken gebruiken om fouten in het onverwachte gedrag van uw Azure Stream Analytics-taak op te lossen. In dit artikel vindt u een overzicht van de beschrijving voor elke fout code van de gegevens. Gegevens fouten treden op wanneer de gegevens stroom beschadigd is, zoals een onverwacht record schema.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **Oorzaak**: er is een fout opgetreden tijdens het deserialiseren van de invoer gegevens.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **Oorzaak**: stream Analytics kan geen tijds tempel voor de resource ophalen. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **Oorzaak**: de stream Analytics kan de waarde niet ophalen `TIMESTAMP BY OVER COLUMN` .

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **Oorzaak**: een invoer gebeurtenis is later verzonden dan de geconfigureerde tolerantie.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **Oorzaak**: de aankomst tijd van een invoer gebeurtenis is vroeger dan de tijds tempel drempel van de invoer gebeurtenis toepassing.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **Oorzaak**: de bericht uitvoer naar Azure functions overschrijdt de maximale grootte.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **Oorzaak**: een uitvoer record overschrijdt de maximale grootte bij het schrijven naar Event hub.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **Oorzaak**: de waarde of het type van een bepaalde kolom is ongeldig.
* **Aanbeveling**: Geef unieke niet-lege teken reeksen op die niet langer zijn dan 255 tekens.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Oorzaak**: de document-id van de uitvoer record bevat een ongeldig teken.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **Oorzaak**: de uitvoer record bevat niet de kolom \[ -id] die moet worden gebruikt als de primaire-sleutel eigenschap.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Oorzaak**: de uitvoer record bevat niet de eigenschap document-id. 
* **Aanbeveling**: Zorg ervoor dat de query uitvoer de kolom bevat met een unieke niet-lege teken reeks die kleiner is dan 255 tekens.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **Oorzaak**: in het uitvoer record ontbreekt de kolom die moet worden gebruikt als de partitie sleutel eigenschap.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Oorzaak**: het schrijven van een enkele record naar Cosmos DB is te groot.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **Oorzaak**: stream Analytics kunnen gebeurtenis (sen) niet schrijven naar SQL database als gevolg van problemen in de gegevens.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met invoerverbindingen oplossen](stream-analytics-troubleshoot-input.md)
* [Problemen met Azure Stream Analytics uitvoer oplossen](stream-analytics-troubleshoot-output.md)
* [Problemen met Azure Stream Analytics query's oplossen](stream-analytics-troubleshoot-query.md)
* [Problemen met Azure Stream Analytics oplossen met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics gegevens fouten](data-errors.md)
