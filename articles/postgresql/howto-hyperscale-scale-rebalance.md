---
title: Rebalance Shards-grootschalige (Citus)-Azure Database for PostgreSQL
description: Shards gelijkmatig verdelen over servers voor betere prestaties
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026383"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Shards in grootschalige (Citus)-Server groep opnieuw verdelen

Als u wilt profiteren van nieuwe knoop punten, moet u de gedistribueerde tabel [Shards](concepts-hyperscale-distributed-data.md#shards)opnieuw verdelen. Dit betekent dat u een aantal Shards van bestaande knoop punten naar de nieuwe hebt verplaatst. Controleer eerst of de inrichting van de nieuwe werk rollen is voltooid. Start vervolgens de Shard-herbalancer door verbinding te maken met het knoop punt van de cluster coördinator met psql en wordt uitgevoerd:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Met de functie [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) worden alle tabellen in de groep co- [locatie](concepts-hyperscale-colocation.md) van de tabel met de naam in het argument opnieuw gebalanceerd. U hoeft de functie dus niet aan te roepen voor elke gedistribueerde tabel, maar u kunt deze ook aanroepen in een representatieve tabel vanuit elke groep voor co-locaties.

**Volgende stappen**


- Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
- [Een server groep](howto-hyperscale-scale-grow.md) omhoog of omlaag schalen
- Zie het [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) referentie materiaal
