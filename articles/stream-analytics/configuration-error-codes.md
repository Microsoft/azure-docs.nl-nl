---
title: Configuratie fout codes-Azure Stream Analytics
description: Problemen met Azure Stream Analytics oplossen met configuratie fout codes.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 6137ceeb0d86b1531adab910175ddbc4722ef858
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019513"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Configuratie fout codes Azure Stream Analytics

U kunt activiteiten logboeken en resource Logboeken gebruiken om fouten in het onverwachte gedrag van uw Azure Stream Analytics-taak op te lossen. In dit artikel vindt u een overzicht van de beschrijving voor elke configuratie fout code. Configuratie fouten zijn gerelateerd aan uw taak configuratie, of in de invoer-en uitvoer configuraties.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **Oorzaak**: Event hub heeft een niet- *geautoriseerde toegangs* fout veroorzaakt.

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Oorzaak**: er is meer dan een event hub-ontvanger met verschillende epoche waarden.
* **Aanbeveling**: Zorg ervoor dat *Service Bus Explorer* of een *EventProcessorHost* -toepassing niet is verbonden terwijl uw stream Analytics-taak wordt uitgevoerd.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **Oorzaak**: stream Analytics kan geen verbinding maken met een partitie omdat het maximum aantal toegestane ontvangers per partitie in een consumenten groep is bereikt.
* **Aanbeveling**: Zorg ervoor dat andere stream Analytics-taken of service bus Explorer niet dezelfde Consumer groep gebruiken.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Oorzaak**: er is een fout opgetreden tijdens het schrijven van gegevens naar de Event hub vanwege beperking.
* **Aanbeveling**: als dit consistent gebeurt, moet u de door Voer bijwerken.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Oorzaak**: de gegeven verbindings configuratie is onjuist.
* **Aanbeveling**: Corrigeer de configuratie en start de taak opnieuw.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **Oorzaak**: de Event hub-host is onbereikbaar.
* **Aanbeveling**: Zorg ervoor dat de opgegeven hostnaam juist is.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **Oorzaak**: de EventHub-afzender heeft een onverwacht aantal EventHub-partities aangetroffen.
* **Aanbeveling**: start de stream Analytics-taak opnieuw als het aantal partities van de EventHub is gewijzigd.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Oorzaak**: stream Analytics de partitie sleutel van een bepaalde Cosmos DB verzameling niet vinden in de data base.
* **Aanbeveling**: Zorg ervoor dat er een geldige partitie sleutel is opgegeven voor de verzameling in Cosmos db.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Oorzaak**: wordt gegenereerd wanneer een partitie sleutel geen blad knooppunt of op het hoogste niveau is.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Oorzaak**: de query-uitvoer mag de kolom \[ -id niet bevatten] als er een andere kolom is geselecteerd als de primaire-sleutel eigenschap.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Oorzaak**: stream Analytics kan geen CosmosDB-data base vinden.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Oorzaak**: stream Analytics kunt een bepaalde Cosmos DB verzameling niet vinden in een Data Base.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Oorzaak**: er is een fout opgetreden tijdens het schrijven van gegevens als gevolg van het beperken van Cosmos db.
* **Aanbeveling**: Voer een upgrade uit van de prestatie tier van de verzameling en stel de prestaties van uw data base af.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **Oorzaak**: de stream Analytics-taak heeft een verificatie fout aangetroffen.
* **Aanbeveling**: Controleer of de SQL database connection string juist is.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **Oorzaak**: de stream Analytics-taak heeft een verificatie fout aangetroffen. 
* **Aanbeveling**: Controleer of de account naam correct is geconfigureerd en of de beheerde identiteit van de taak toegang heeft tot de SQL database.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **Oorzaak**: stream Analytics kunt de schema gegevens voor een bepaalde tabel niet vinden.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Oorzaak**: SQL database wordt niet ondersteund.
* **Aanbeveling**: gebruik exclusieve SQL-groep.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met invoerverbindingen oplossen](stream-analytics-troubleshoot-input.md)
* [Problemen met Azure Stream Analytics uitvoer oplossen](stream-analytics-troubleshoot-output.md)
* [Problemen met Azure Stream Analytics query's oplossen](stream-analytics-troubleshoot-query.md)
* [Problemen met Azure Stream Analytics oplossen met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics gegevens fouten](data-errors.md)
