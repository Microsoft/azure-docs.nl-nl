---
title: Hoge Beschik baarheid – grootschalige (Citus)-Azure Database for PostgreSQL
description: Concepten voor hoge Beschik baarheid en herstel na nood gevallen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91314850"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Hoge Beschik baarheid in Azure Database for PostgreSQL – grootschalige (Citus)

Hoge Beschik baarheid (HA) vermijdt downtime van de data base door stand-by replica's van elk knoop punt in een server groep te behouden. Als een knoop punt uitvalt, wordt grootschalige (Citus) de binnenkomende verbindingen van het knoop punt in de stand-bymodus overgeschakeld. Failover vindt binnen enkele minuten plaats en gepromoveerde knoop punten bevatten altijd nieuwe gegevens via synchrone replicatie van PostgreSQL.

Om te profiteren van HA op het coördinator knooppunt, moeten database toepassingen de verbindingen en mislukte trans acties detecteren en opnieuw proberen. De zojuist gepromoveerde coördinator is toegankelijk met dezelfde connection string.

Herstel kan worden onderverdeeld in drie fasen: detectie, failover en volledig herstel.  Grootschalige (Citus) voert periodieke status controles uit op elk knoop punt en na vier mislukte controles wordt vastgesteld dat een knoop punt niet beschikbaar is. Grootschalige (Citus) bevordert vervolgens een stand-by-status voor primaire knoop punt (failover) en richt een nieuwe stand-by-to--uit.
Streaming-replicatie begint, waardoor het nieuwe knoop punt up-to-date is.  Wanneer alle gegevens zijn gerepliceerd, is het knoop punt volledig herstel bereikt.

### <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [inschakelen van hoge Beschik baarheid](howto-hyperscale-high-availability.md) in een grootschalige (Citus)-Server groep.
