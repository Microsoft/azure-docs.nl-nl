---
title: Problemen met Azure Cosmos DB HTTP 408 of time-outs met de Java v4 SDK oplossen
description: Meer informatie over het vaststellen en oplossen van time-outuitzonderingen voor het aanvragen van Java SDK met de Java v4-SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a805300ac62d0627c9b06188c9764a6887947afe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411283"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Problemen met de time-out voor de Azure Cosmos DB Java v4 SDK-aanvragen diagnosticeren en oplossen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De HTTP 408-fout treedt op als de SDK de aanvraag niet kan volt ooien voordat de time-outlimiet is opgetreden.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor time-outuitzonderingen voor aanvragen.

### <a name="existing-issues"></a>Bestaande problemen
Als er aanvragen worden weer gegeven die langer duren of een langere tijds duur hebben, moet u een upgrade uitvoeren van de Java v4 SDK naar de nieuwste versie. Opmerking: we raden u ten zeerste aan de versie 4.7.0 en hoger te gebruiken. Uitchecken van de opmerkingen bij de release van de [Java v4 SDK](sql-api-sdk-java-v4.md) voor meer informatie.

### <a name="high-cpu-utilization"></a>Hoog CPU-gebruik
Hoog CPU-gebruik is het meest voorkomende geval. Voor een optimale latentie moet het CPU-gebruik ongeveer 40 procent zijn. 10 seconden gebruiken als het interval voor het controleren van het maximum (niet gemiddeld) CPU-gebruik. CPU-pieken komen vaker voor bij query's met kruis partities, waarbij meerdere verbindingen voor één query kunnen worden uitgevoerd.

#### <a name="solution"></a>Oplossing:
De client toepassing die gebruikmaakt van de SDK moet omhoog of omlaag worden geschaald.

### <a name="connection-throttling"></a>Verbindings beperking
De verbindings beperking kan worden veroorzaakt door een verbindings limiet op een hostcomputer of een overschrijding van een Azure SNAT-poort.

### <a name="connection-limit-on-a-host-machine"></a>Verbindings limiet op een hostcomputer
Sommige Linux-systemen, zoals Red Hat, hebben een bovenlimiet van het totale aantal geopende bestanden. Sockets in Linux worden geïmplementeerd als bestanden, zodat dit aantal ook het totale aantal verbindingen beperkt. Voer de volgende opdracht uit.

```bash
ulimit -a
```

#### <a name="solution"></a>Oplossing:
Het maximum aantal geopende bestanden dat wordt aangeduid als ' geen bestand ' moet ten minste 10.000 of meer zijn. Zie de Azure Cosmos DB Java SDK v4- [prestatie tips](performance-tips-java-sdk-v4-sql.md)voor meer informatie.

### <a name="socket-or-port-availability-might-be-low"></a>Beschik baarheid van socket of poort is mogelijk laag
Bij het uitvoeren in azure kunnen clients die gebruikmaken van de Java-SDK, de poort uitputting voor Azure SNAT (PAT) aanraken.

#### <a name="solution-1"></a>Oplossing 1:
Als u werkt met virtuele Azure-machines, volgt u de [hand leiding voor de SNAT-poort uitgeput](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>Oplossing 2:
Als u werkt met op Azure App Service, volgt u de [hand leiding voor het oplossen van verbindings fouten](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) en [gebruikt u app service diagnostische gegevens](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Oplossing 3:
Als u werkt met op Azure Functions, controleert u of u de [Azure functions aanbeveling](../azure-functions/manage-connections.md#static-clients) van het onderhouden van Singleton of statische clients voor alle betrokken Services (inclusief Azure Cosmos DB) volgt. Controleer de [service limieten](../azure-functions/functions-scale.md#service-limits) op basis van het type en de grootte van uw functie-app-hosting.

#### <a name="solution-4"></a>Oplossing 4:
Als u een HTTP-proxy gebruikt, moet u ervoor zorgen dat het het aantal verbindingen dat in de SDK is geconfigureerd kan ondersteunen `GatewayConnectionConfig` . Anders worden er verbindings problemen besproken.

### <a name="create-multiple-client-instances"></a>Meerdere client exemplaren maken
Het maken van meerdere client exemplaren kan leiden tot verbindings conflicten en time-outproblemen.

#### <a name="solution-1"></a>Oplossing 1:
Volg de [Tips voor prestaties](performance-tips-java-sdk-v4-sql.md#sdk-usage)en gebruik één CosmosClient-exemplaar in een hele toepassing.

#### <a name="solution-2"></a>Oplossing 2:
Als Singleton CosmosClient niet in een toepassing kan worden gebruikt, raden we u aan om verbinding te maken met meerdere Cosmos-clients via deze API `connectionSharingAcrossClientsEnabled(true)` in CosmosClient. Wanneer u meerdere exemplaren van Cosmos-client hebt in dezelfde JVM interactie met meerdere Cosmos-accounts, kan het delen van verbindingen in de directe modus, indien mogelijk, worden toegestaan tussen instanties van Cosmos-client. Houd er rekening mee dat bij het instellen van deze optie de configuratie van de verbinding (bijvoorbeeld time-out voor Socket-configuratie, time-out voor inactiviteit voor niet-actieve configuratie) van de eerste geïnstantieerd client wordt gebruikt voor alle andere client exemplaren.

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
* Problemen [vaststellen en oplossen](troubleshoot-java-sdk-v4-sql.md) wanneer u de Azure Cosmos DB Java v4 SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [Java v4](performance-tips-java-sdk-v4-sql.md).
