---
title: Azure Functions trigger voor Cosmos DB geavanceerde configuratie
description: Meer informatie over het configureren van logboek registratie en verbindings beleid dat wordt gebruikt door Azure Functions trigger voor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 30328db465e0d9bf8c1ce67d92e48c688c51e043
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574613"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Logboek registratie en connectiviteit configureren met de Azure Functions trigger voor Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel worden geavanceerde configuratie opties beschreven die u kunt instellen wanneer u de Azure Functions trigger voor Cosmos DB gebruikt.

## <a name="enabling-trigger-specific-logs"></a>Trigger-specifieke logboeken inschakelen

De Azure Functions trigger voor Cosmos DB gebruikt de [bibliotheek voor de wijzigings doorvoer processor](./change-feed-processor.md) intern en de bibliotheek genereert een set status logboeken die kunnen worden gebruikt voor het bewaken van interne bewerkingen voor het [oplossen van problemen](./troubleshoot-changefeed-functions.md).

De status logboeken beschrijven hoe de Azure Functions trigger voor Cosmos DB zich gedraagt bij het uitvoeren van bewerkingen tijdens taak verdeling of-initialisatie.

### <a name="enabling-logging"></a>Logboek registratie inschakelen

Als u logboek registratie wilt inschakelen wanneer u Azure Functions trigger voor Cosmos DB gebruikt, zoekt u het `host.json` bestand in uw Azure functions project of Azure functions app en [configureert u het niveau van de vereiste logboek registratie](../azure-functions/functions-monitoring.md#log-levels-and-categories). Schakel de traceringen  `Host.Triggers.CosmosDB` in, zoals wordt weer gegeven in het volgende voor beeld:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Nadat de Azure function is geïmplementeerd met de bijgewerkte configuratie, ziet u de Azure Functions trigger voor Cosmos DB-Logboeken als onderdeel van uw traceringen. U kunt de logboeken weer geven in uw geconfigureerde logboek registratie provider onder de *categorie* `Host.Triggers.CosmosDB` .

### <a name="query-the-logs"></a>Query's uitvoeren op de logboeken

Voer de volgende query uit om een query uit te voeren op de logboeken die zijn gegenereerd door de Azure Functions trigger voor Cosmos DB in [Azure-toepassing Insights-analyse](../azure-monitor/logs/log-query-overview.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Het verbindings beleid configureren

Er zijn twee verbindings modi: directe modus en gateway modus. Zie het artikel [verbindings modi](sql-sdk-connection-modes.md) voor meer informatie over deze verbindings modi. Standaard wordt **Gateway** gebruikt om alle verbindingen te maken op de Azure functions trigger voor Cosmos db. Het is echter mogelijk niet de beste optie voor prestatie gerichte scenario's.

### <a name="changing-the-connection-mode-and-protocol"></a>De verbindings modus en het protocol wijzigen

Er zijn twee belang rijke configuratie-instellingen beschikbaar voor het configureren van het beleid voor client verbindingen: de **verbindings modus** en het **verbindings protocol**. U kunt de standaard verbindings modus en het protocol dat wordt gebruikt door de Azure Functions trigger voor Cosmos DB en alle [Azure Cosmos DB bindingen](../azure-functions/functions-bindings-cosmosdb-v2-output.md)) wijzigen. Als u de standaard instellingen wilt wijzigen, moet u het `host.json` bestand in uw Azure functions project of Azure functions app zoeken en de volgende [extra instelling](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)toevoegen:

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Waar `connectionMode` moet de gewenste verbindings modus (direct of gateway) en `protocol` het gewenste verbindings Protocol (TCP of https) hebben. 

Als uw Azure Functions project werkt met Azure Functions v1-runtime, heeft de configuratie een geringe naam verschil. u moet `documentDB` in plaats van het `cosmosDB` volgende gebruiken:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Wanneer u uw functie-app in een verbruiks abonnement host, heeft elk exemplaar een limiet voor de hoeveelheid socket verbindingen die het kan onderhouden. Wanneer u werkt met de modus direct/TCP, worden er meer verbindingen gemaakt en kunnen er de [limieten voor verbruiks abonnementen](../azure-functions/manage-connections.md#connection-limit)worden bereikt. in dat geval kunt u de gateway modus of in plaats daarvan hosten voor uw functie-app in een [Premium-abonnement](../azure-functions/functions-premium-plan.md) of een [speciaal (app service) abonnement](../azure-functions/dedicated-plan.md).

## <a name="next-steps"></a>Volgende stappen

* [Verbindings limieten in Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Schakel bewaking](../azure-functions/functions-monitoring.md) in uw Azure functions-toepassingen in.
* Meer informatie over het [vaststellen en oplossen van veelvoorkomende problemen](./troubleshoot-changefeed-functions.md) bij het gebruik van de Azure functions trigger voor Cosmos db.