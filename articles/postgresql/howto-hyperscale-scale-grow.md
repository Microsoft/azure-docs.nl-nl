---
title: Scale-Server groep-grootschalige (Citus)-Azure Database for PostgreSQL
description: De Server groep geheugen, schijf en CPU-bronnen aanpassen om te voorzien in een grotere belasting
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 905224119b9df4e4003b1736443406e9548a49e3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012526"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Een Citus-Server groep (grootschalige) schalen

Azure Database for PostgreSQL-grootschalige (Citus) biedt self-service schaling voor een hogere belasting. Met de Azure Portal kunt u eenvoudig nieuwe werk knooppunten toevoegen en de vCores van bestaande knoop punten verg Roten. Het toevoegen van knoop punten leidt tot uitval tijd en zelfs het verplaatsen van Shards naar de nieuwe knoop punten ( [Shard-herverdeling](howto-hyperscale-scale-rebalance.md)) gebeurt zonder query's te onderbreken.

## <a name="add-worker-nodes"></a>Worker-knoop punten toevoegen

Als u knoop punten wilt toevoegen, gaat u naar het tabblad **Compute + Storage** in de Server groep grootschalige (Citus).  Door de schuif regelaar voor het **aantal worker-knoop punten** te slepen, wijzigt u de waarde.

> [!NOTE]
>
> Een grootschalige-Server groep (Citus) die is gemaakt met de [Basic-laag (preview)](concepts-hyperscale-tiers.md) heeft geen werk rollen. Als u het aantal werk nemers verg root, wordt de Server groep automatisch uitgeschaald naar de laag standaard.
> Na het afronden van een server groep naar de Standard-laag, kunt u deze niet meer downgradeen naar de laag basis.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Resource schuif regelaars":::

Klik op de knop **Opslaan** om de gewijzigde waarde van kracht te laten worden.

> [!NOTE]
> Nadat u het aantal werkknooppunten hebt vergroot en opgeslagen, kan dit niet meer worden verkleind met behulp van de schuifregelaar.

> [!NOTE]
> Als u wilt profiteren van nieuwe knoop punten, moet u de [gedistribueerde tabel Shards opnieuw verdelen](howto-hyperscale-scale-rebalance.md). Dit betekent dat u een aantal [Shards](concepts-hyperscale-distributed-data.md#shards) van bestaande knoop punten naar de nieuwe hebt verplaatst.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Het aantal vCores op knooppunten vergroten of verkleinen

Naast het toevoegen van nieuwe knooppunten kunt u ook de mogelijkheden van bestaande knooppunten uitbreiden. Het aanpassen van de reken capaciteit omhoog en omlaag kan nuttig zijn bij het uitvoeren van prestatie experimenten en op korte of lange termijn wijzigingen in verkeers vereisten.

Als u de vCores voor alle worker-knoop punten wilt wijzigen, past u de schuif regelaar **vCores** aan onder **configuratie (per worker-knoop punt)**. De vCores van het coördinator knooppunt kan onafhankelijk worden aangepast. Pas de schuif regelaar **vCores** aan onder  **configuratie (coördinator knooppunt)**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
- [Herverdeling van gedistribueerde tabel Shards](howto-hyperscale-scale-rebalance.md) zodat alle worker-knoop punten kunnen deel nemen aan parallelle query's
