---
title: Problemen met de Azure Cosmos DB aanvraag verhelpen te hoge uitzonde ringen
description: Meer informatie over het vaststellen en oplossen van aanvraag frequentie te grote uitzonde ringen.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c3a692a8df93931bd8fd7982e69fe2f9118444c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94411334"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Problemen vaststellen en oplossen Azure Cosmos DB aanvraag frequentie te grote uitzonde ringen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Een ' aanvraag snelheid te groot '-bericht of fout code 429 geeft aan dat uw aanvragen worden beperkt.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende sectie bevat bekende oorzaken en oplossingen voor te veel aanvragen.

### <a name="check-the-metrics"></a>De metrische gegevens controleren
Controleer [Azure Cosmos DB bewaking](monitor-cosmos-db.md) om het aantal uitzonde ringen van 429 weer te geven.

#### <a name="cause"></a>Oorzaak:
De verbruikte door Voer (aanvraag eenheden per seconde) heeft de [ingerichte door Voer](set-throughput.md)overschreden. De SDK probeert automatisch aanvragen opnieuw uit te voeren op basis van het opgegeven beleid voor opnieuw proberen. Als deze fout vaak optreedt, kunt u overwegen de door Voer van de verzameling te verhogen. Controleer de metrische gegevens van de portal om te zien of er 429 fouten worden weer gegeven. Controleer uw partitie sleutel om ervoor te zorgen dat deze een [gelijkmatige verdeling van de opslag en het aanvraag volume](partitioning-overview.md)levert.

#### <a name="solution"></a>Oplossing:
1. Gebruik de [Portal of de SDK](set-throughput.md) om de ingerichte door voer te verg Roten.
1. Schakel de data base of container over naar [automatisch schalen](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) wanneer u de Azure Cosmos db .NET SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md).
* Problemen [vaststellen en oplossen](troubleshoot-java-sdk-v4-sql.md) wanneer u de Azure Cosmos DB Java v4 SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).