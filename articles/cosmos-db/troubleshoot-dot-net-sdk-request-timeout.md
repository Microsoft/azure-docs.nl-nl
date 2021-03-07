---
title: Problemen met Azure Cosmos DB HTTP 408 of time-outs met de .NET SDK oplossen
description: Meer informatie over het vaststellen en oplossen van time-outuitzonderingen voor .NET SDK-aanvragen.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/05/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: c8d35f7c666562022f503b2777f30f84193d0231
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440000"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Problemen met de time-out van Azure Cosmos DB .NET SDK-aanvragen vaststellen en oplossen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De HTTP 408-fout treedt op als de SDK de aanvraag niet kan volt ooien voordat de time-outlimiet is opgetreden.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>De time-out aanpassen op de Azure Cosmos DB .NET SDK

De SDK heeft twee verschillende alternatieven voor het beheren van time-outs, elk met een ander bereik.

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout` `ConnectionPolicy.RequestTimeout` Met de configuratie van (of voor SDK v2) kunt u een time-out instellen die van invloed is op elke afzonderlijke netwerk aanvraag. Een bewerking die door een gebruiker is gestart, kan meerdere netwerk aanvragen omvatten (er kan bijvoorbeeld een beperking zijn). Deze configuratie is van toepassing op elke netwerk aanvraag bij het opnieuw proberen. Deze time-out is geen end-to-end-time-out voor de bewerkings aanvraag.

### <a name="cancellationtoken"></a>CancellationToken

Alle async-bewerkingen in de SDK hebben een optionele CancellationToken-para meter. Deze [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) -para meter wordt gebruikt tijdens de gehele bewerking, in alle netwerk aanvragen. In tussen netwerk aanvragen kan het annulerings token worden gecontroleerd en een bewerking geannuleerd als het gerelateerde token is verlopen. Het annulerings token moet worden gebruikt voor het definiëren van een geschatte verwachte time-out op het bewerkings bereik.

> [!NOTE]
> De `CancellationToken` para meter is een mechanisme waarbij de bibliotheek de annulering controleert, wanneer deze [geen ongeldige status veroorzaakt](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). De bewerking kan niet exact worden geannuleerd wanneer de tijd die is gedefinieerd in de annulering, actief is. In plaats daarvan wordt het na de tijd geannuleerd wanneer dit veilig is.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor time-outuitzonderingen voor aanvragen.

### <a name="high-cpu-utilization"></a>Hoog CPU-gebruik
Hoog CPU-gebruik is het meest voorkomende geval. Voor een optimale latentie moet het CPU-gebruik ongeveer 40 procent zijn. 10 seconden gebruiken als het interval voor het controleren van het maximum (niet gemiddeld) CPU-gebruik. CPU-pieken komen vaker voor bij query's met kruis partities, waarbij meerdere verbindingen voor één query kunnen worden uitgevoerd.

Als de fout `TransportException` informatie bevat, kan deze ook het `CPU History` volgende bevatten:

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* Als de CPU-metingen meer dan 70% zijn, wordt de time-out waarschijnlijk veroorzaakt door een CPU-uitputting. In dit geval kunt u de bron voor het hoge CPU-gebruik onderzoeken en deze verminderen, of de computer opschalen voor gebruik van grotere resources.
* Als de CPU-metingen niet elke tien seconden plaatsvinden (bijvoorbeeld hiaten of meettijden duiden op langere perioden tussen metingen), is de oorzaak waarschijnlijk threadblokkade. In dit geval kunt u de oorzaak of oorzaken onderzoeken van de threadblokkade (mogelijk vergrendelde threads) of de computers opschalen voor gebruik van een grotere resourcegrootte.

#### <a name="solution"></a>Oplossing:
De client toepassing die gebruikmaakt van de SDK moet omhoog of omlaag worden geschaald.

### <a name="socket-or-port-availability-might-be-low"></a>Beschik baarheid van socket of poort is mogelijk laag
Bij het uitvoeren in azure kunnen clients die gebruikmaken van de .NET SDK, de poort uitputting voor Azure SNAT (PAT) aanraken.

#### <a name="solution-1"></a>Oplossing 1:
Als u werkt met virtuele Azure-machines, volgt u de [hand leiding voor de SNAT-poort uitgeput](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Oplossing 2:
Als u werkt met op Azure App Service, volgt u de [hand leiding voor het oplossen van verbindings fouten](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) en [gebruikt u app service diagnostische gegevens](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Oplossing 3:
Als u werkt met op Azure Functions, controleert u of u de [Azure functions aanbeveling](../azure-functions/manage-connections.md#static-clients) van het onderhouden van Singleton of statische clients voor alle betrokken Services (inclusief Azure Cosmos DB) volgt. Controleer de [service limieten](../azure-functions/functions-scale.md#service-limits) op basis van het type en de grootte van uw functie-app-hosting.

#### <a name="solution-4"></a>Oplossing 4:
Als u een HTTP-proxy gebruikt, moet u ervoor zorgen dat het het aantal verbindingen dat in de SDK is geconfigureerd kan ondersteunen `ConnectionPolicy` . Anders worden er verbindings problemen besproken.

### <a name="create-multiple-client-instances"></a>Meerdere client exemplaren maken
Het maken van meerdere client exemplaren kan leiden tot verbindings conflicten en time-outproblemen.

#### <a name="solution"></a>Oplossing:
Volg de [Tips voor prestaties](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)en gebruik één CosmosClient-exemplaar in een heel proces.

### <a name="hot-partition-key"></a>Hot-partitie sleutel
Azure Cosmos DB distribueert de totale ingerichte door Voer gelijkmatig over fysieke partities. Wanneer er sprake is van een warme partitie, worden de aanvraag eenheden van alle fysieke partities per seconde (RU/s) gebruikt voor een of meer logische partitie sleutels op een fysieke partitie. Op hetzelfde moment worden de RU/s op andere fysieke partities niet meer gebruikt. Als symptoom is het totaal aantal gebruikte RU/s kleiner dan de totale ingerichte RU/s in de data base of container, maar u kunt wel de vertragings factor (429s) op de aanvragen voor de logische partitie sleutel Hot Logical bekijken. Gebruik de [genormaliseerde metrische gegevens van ru-verbruik](monitor-normalized-request-units.md) om te zien of de werk belasting een hete partitie tegen komt. 

#### <a name="solution"></a>Oplossing:
Kies een goede partitie sleutel waarmee het aanvraag volume en de opslag gelijkmatig worden verdeeld. Meer informatie over het [wijzigen van de partitie sleutel](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Hoge mate van gelijktijdigheid
De toepassing heeft een hoog niveau van gelijktijdigheid, wat kan leiden tot conflicten op het kanaal.

#### <a name="solution"></a>Oplossing:
De client toepassing die gebruikmaakt van de SDK moet omhoog of omlaag worden geschaald.

### <a name="large-requests-or-responses"></a>Grote aanvragen of antwoorden
Grote aanvragen of antwoorden kunnen leiden tot hoofd-of-line blok kering van het kanaal en de exacerbate-inhoud, zelfs met een relatief lage mate van gelijktijdigheid.

#### <a name="solution"></a>Oplossing:
De client toepassing die gebruikmaakt van de SDK moet omhoog of omlaag worden geschaald.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>Het aantal fouten ligt binnen de Azure Cosmos DB SLA
De toepassing moet tijdelijke storingen kunnen verwerken en opnieuw proberen wanneer dat nodig is. Er worden geen 408-uitzonde ringen opnieuw uitgevoerd omdat bij het maken van paden niet weet of de service het item heeft gemaakt of niet. Als u hetzelfde item opnieuw verzendt om te maken, treedt er een conflict op. Bedrijfs logica voor gebruikers toepassingen kan aangepaste logica hebben voor het afhandelen van conflicten, wat zou afbreken van de dubbel zinnigheid van een bestaand item versus een conflict met het maken van een nieuwe poging.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Het aantal fouten is in strijd met de Azure Cosmos DB SLA
Neem contact op met de [ondersteuning van Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) wanneer u de Azure Cosmos db .NET SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md).