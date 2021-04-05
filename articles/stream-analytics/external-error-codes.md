---
title: Externe fout codes-Azure Stream Analytics
description: Problemen met Azure Stream Analytics oplossen met externe fout codes.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9f55a715b11b126ea340e665e008d7245e578190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016385"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Externe fout codes Azure Stream Analytics

U kunt activiteiten logboeken en resource Logboeken gebruiken om fouten in het onverwachte gedrag van uw Azure Stream Analytics-taak op te lossen. In dit artikel vindt u een overzicht van de beschrijving van elke externe fout code. Externe fouten zijn algemene fouten die worden gegenereerd door een upstream-of downstream-service die Stream Analytics niet kan worden onderscheiden als een gegevens fout, configuratie fout of externe beschikbaarheids fout.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **Oorzaak**: er is een fout opgetreden tijdens het initialiseren van de adapter.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Oorzaak**: er is een fout opgetreden tijdens het schrijven van gegevens naar een adapter.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Oorzaak**: er is een HTTP-fout geretourneerd door Azure functions.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Oorzaak**: stream Analytics kan geen gebeurtenissen schrijven naar de Azure-functie.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Oorzaak**: er is een omleidings fout bij het uitvoeren van Azure functions.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Oorzaak**: er is een client fout opgetreden tijdens het uitvoeren van Azure functions.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Oorzaak**: er is een server fout opgetreden bij het Azure functions.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Oorzaak**: schrijven naar Azure functions is mislukt omdat de HTTP-aanvraag de time-out heeft overschreden. 
* **Aanbeveling**: controleer uw Azure functions-logboeken op mogelijke vertragingen.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Oorzaak**: invoer offsets zijn ongeldig. Dit kan worden veroorzaakt door een failover.
* **Aanbeveling**: start de stream Analytics-taak opnieuw vanaf de laatste uitvoer tijd.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Oorzaak**: er is een fout opgetreden tijdens het verzenden van gegevens naar Event hub.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Oorzaak**: stream Analytics kan geen verbinding maken met een Cosmos DB account na het maximum aantal nieuwe pogingen.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Oorzaak**: Stream Analytics de Cosmos DB-Data Base en-verzameling niet kunnen opvragen na het maximum aantal nieuwe pogingen.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Oorzaak**: CosmosDB kan geen opgeslagen procedure maken na een aantal nieuwe pogingen.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Oorzaak**: er is een fout geretourneerd door de opgeslagen procedure upsert. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Oorzaak**: Stream Analytics de SQL database uitvoer kan niet worden geïnitialiseerd.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Oorzaak**: stream Analytics kunt geen gebeurtenissen naar de SQL database uitvoer schrijven.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **Oorzaak**: er is een fout opgetreden tijdens het initialiseren van een toegewezen SQL-groeps uitvoer.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Oorzaak**: er is een fout opgetreden bij het schrijven van de uitvoer naar een toegewezen SQL-groep.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met invoerverbindingen oplossen](stream-analytics-troubleshoot-input.md)
* [Problemen met Azure Stream Analytics uitvoer oplossen](stream-analytics-troubleshoot-output.md)
* [Problemen met Azure Stream Analytics query's oplossen](stream-analytics-troubleshoot-query.md)
* [Problemen met Azure Stream Analytics oplossen met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics gegevens fouten](data-errors.md)
