---
title: Overzicht van Azure Database for PostgreSQL - Hyperscale (Citus)
description: Geeft een overzicht van de implementatieoptie Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 1734128384d63749d3c777cf6315278fced9d140
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025137"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Wat is Azure Database for PostgreSQL - Hyperscale (Citus)?

Azure Database for PostgreSQL is een relationele databaseservice in de Microsoft Cloud die is gebouwd voor ontwikkelaars. Deze is gebaseerd op de communityversie van de opensource-[PostgreSQL](https://www.postgresql.org/)-database-engine.

Hyperscale (Citus) is een implementatieoptie waarmee query’s horizontaal over meerdere machines worden geschaald door middel van sharding. De query-engine zorgt voor query-parallellisatie van inkomende SQL-query’s over deze servers voor een snellere respons bij grote gegevenssets. Het is geschikt voor toepassingen die een grotere schaal en betere prestaties vereisen dan andere implementatieopties: meestal workloads die de 100 GB aan gegevens benaderen of overschrijden.

Hyperscale (Citus) levert:

- Horizontaal schalen over meerdere machines door middel van sharding
- Query-parallellisatie over deze servers voor een snellere respons bij grote gegevenssets
- Uitstekende ondersteuning voor multi-tenant toepassingen, realtime operationele analyse en transactionele workloads met hoge doorvoer

Toepassingen die zijn gebouwd voor PostgreSQL kunnen gedistribueerde query's uitvoeren op Hyperscale (Citus) met standaard [verbindingsbibliotheken](./concepts-connection-libraries.md) en minimale wijzigingen.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van uw eerste](./quickstart-create-hyperscale-portal.md) servergroep met Azure Database for PostgreSQL - Hyperscale (Citus).
- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/) voor kostenvergelijkingen en calculators. Hyperscale (Citus) biedt ook kortingen voor vooraf betaalde gereserveerde instanties. Zie [Hyperscale (Citus) RI-prijzen](concepts-hyperscale-reserved-pricing.md) voor meer informatie.
- Bepaal de beste [begingrootte](howto-hyperscale-scale-initial.md) voor uw servergroep
