---
title: Problemen met Azure Cosmos DB verboden uitzonde ringen oplossen
description: Meer informatie over het vaststellen en oplossen van verboden uitzonde ringen.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99971904"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Problemen vaststellen en oplossen Azure Cosmos DB verboden uitzonde ringen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met de HTTP-status code 403 wordt aangegeven dat de aanvraag niet is voltooid.

## <a name="firewall-blocking-requests"></a>Blokkerende firewall aanvragen
In dit scenario worden veelvoorkomende fouten als de onderstaande weer gegeven:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Oplossing
Controleer of de huidige [firewall instellingen](how-to-configure-firewall.md) juist zijn en voeg de IP-adressen of netwerken toe waarmee u verbinding probeert te maken.
Als u deze onlangs hebt bijgewerkt, moet u er rekening mee houden dat de wijzigingen **Maxi maal vijf tien minuten** kunnen worden toegepast.

## <a name="next-steps"></a>Volgende stappen
* [IP-firewall](how-to-configure-firewall.md)configureren.
* Configureer de toegang vanaf [virtuele netwerken](how-to-configure-vnet-service-endpoint.md).
* Configureer de toegang vanaf [privé-eind punten](how-to-configure-private-endpoints.md).
