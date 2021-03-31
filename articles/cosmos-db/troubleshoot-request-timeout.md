---
title: Time-outuitzonderingen voor Azure Cosmos DB service aanvragen oplossen
description: Meer informatie over het vaststellen en oplossen van time-outuitzonderingen voor Azure Cosmos DB service aanvragen.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5b188021de30561222f098e2b5782bada25d7ce0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411249"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Uitzonde ringen voor de time-out van Azure Cosmos DB aanvragen vaststellen en oplossen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB heeft een time-out voor een HTTP 408-aanvraag geretourneerd.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor time-outuitzonderingen voor aanvragen.

### <a name="check-the-sla"></a>Controleer de SLA
Controleer [Azure Cosmos DB bewaking](monitor-cosmos-db.md) om te zien of het aantal 408 uitzonde ringen in strijd is met de Azure Cosmos DB sla.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Oplossing 1: de Azure Cosmos DB SLA is niet geschonden
De toepassing moet dit scenario afhandelen en opnieuw proberen met deze tijdelijke fouten.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Oplossing 2: de Azure Cosmos DB SLA is geschonden
Neem contact op met de [ondersteuning van Azure](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Hot-partitie sleutel
Azure Cosmos DB distribueert de totale ingerichte door Voer gelijkmatig over fysieke partities. Wanneer er sprake is van een warme partitie, worden de aanvraag eenheden van alle fysieke partities per seconde (RU/s) gebruikt voor een of meer logische partitie sleutels op een fysieke partitie. Op hetzelfde moment worden de RU/s op andere fysieke partities niet meer gebruikt. Als symptoom is het totale aantal gebruikte RU/s kleiner dan de totale ingerichte RU/s op de data base of container. U ziet nog steeds gashendel (429s) op de aanvragen voor de logische partitie sleutel. Gebruik de [genormaliseerde metrische gegevens van ru-verbruik](monitor-normalized-request-units.md) om te zien of de werk belasting een hete partitie tegen komt. 

#### <a name="solution"></a>Oplossing:
Kies een goede partitie sleutel waarmee het aanvraag volume en de opslag gelijkmatig worden verdeeld. Meer informatie over het [wijzigen van de partitie sleutel](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) wanneer u de Azure Cosmos db .NET SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md).
* Problemen [vaststellen en oplossen](troubleshoot-java-sdk-v4-sql.md) wanneer u de Azure Cosmos DB Java v4 SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).