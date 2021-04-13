---
title: Rebalance Shards-grootschalige (Citus)-Azure Database for PostgreSQL
description: Shards gelijkmatig verdelen over servers voor betere prestaties
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305698"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Shards in grootschalige (Citus)-Server groep opnieuw verdelen

Als u wilt profiteren van nieuwe knoop punten, moet u de gedistribueerde tabel [Shards](concepts-hyperscale-distributed-data.md#shards)opnieuw verdelen. Dit betekent dat u een aantal Shards van bestaande knoop punten naar de nieuwe hebt verplaatst.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Bepalen of de Server groep moet worden gebalanceerd

Met de Azure Portal kunt u zien of gegevens gelijkmatig worden gedistribueerd tussen werk knooppunten in een server groep. Als u dit wilt zien, gaat u naar de pagina **Shard rebalancer** in het menu **beheer van Server groepen** . Als gegevens tussen werk nemers worden schuingetrokken, **wordt de herverdeling** van het bericht aanbevolen, samen met een lijst met de grootte van elk knoop punt.

Als de gegevens al zijn gebalanceerd, wordt het opnieuw **verdelen van het bericht op dit moment niet aanbevolen**.

## <a name="run-the-shard-rebalancer"></a>De Shard-herbalancer uitvoeren

Als u de Shard-herbalancer wilt starten, moet u verbinding maken met het coördinator knooppunt van de Server groep en de [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL-functie uitvoeren op gedistribueerde tabellen. Met de functie worden alle tabellen in de groep co- [locatie](concepts-hyperscale-colocation.md) van de tabel met de naam in het argument opnieuw gebalanceerd. U hoeft de functie dus niet aan te roepen voor elke gedistribueerde tabel, maar u kunt deze ook aanroepen in een representatieve tabel vanuit elke groep voor co-locaties.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Voortgang van herverdeling bewaken

Als u de herbalancer wilt bekijken nadat u deze hebt gestart, gaat u terug naar de Azure Portal. Open de pagina **herbalancer Shard** in **Server groeps beheer**. Hierin wordt weer gegeven dat de **herverdeling** van berichten wordt uitgevoerd in combi natie met twee tabellen.

In de eerste tabel ziet u het aantal Shards dat wordt verplaatst naar of van een knoop punt, bijvoorbeeld ' 6 van 24 verplaatst in '. In de tweede tabel wordt de voortgang per database tabel weer gegeven: naam, Shard aantal beïnvloed, beïnvloede gegevens grootte en status van herverdeling.

Selecteer de knop **vernieuwen** om de pagina bij te werken. Wanneer de herverdeling is voltooid, wordt dit **op dit moment niet meer aanbevolen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
- [Een server groep](howto-hyperscale-scale-grow.md) omhoog of omlaag schalen
- Zie het [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) referentie materiaal
