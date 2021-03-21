---
title: Logs-grootschalige (Citus)-Azure Database for PostgreSQL
description: Toegang krijgen tot database logboeken voor Azure Database for PostgreSQL-grootschalige (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: ca3cc2873fbc6db72b10c80daecddf1471e30ff4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577069"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Meldt zich aan Azure Database for PostgreSQL-grootschalige (Citus)

PostgreSQL-logboeken zijn beschikbaar op elk knoop punt van een grootschalige (Citus)-Server groep. U kunt Logboeken verzenden naar een opslag server of naar een analyse service. De logboeken kunnen worden gebruikt om configuratie fouten en suboptimale prestaties te identificeren, op te lossen en te herstellen.

## <a name="accessing-logs"></a>Logboeken openen

Om toegang te krijgen tot PostgreSQL-logboeken voor een grootschalige (Citus)-coördinator of worker-knoop punt, opent u het knoop punt in de Azure Portal:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lijst met knoop punten":::

Open voor het geselecteerde knoop punt **Diagnostische instellingen** en klik op **+ Diagnostische instelling toevoegen**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Knop Diagnostische instellingen toevoegen":::

Kies een naam voor de nieuwe diagnostische instellingen en schakel het selectie vakje **PostgreSQLLogs** in.  Kies de doel (en) die de logboeken moeten ontvangen.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="PostgreSQL-logboeken kiezen":::

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met log Analytics-query's](../azure-monitor/logs/log-analytics-tutorial.md)
- Meer informatie over [Azure Event hubs](../event-hubs/event-hubs-about.md)