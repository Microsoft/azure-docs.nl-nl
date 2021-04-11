---
title: Upgrade Server groep-grootschalige (Citus)-Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u PostgreSQL en Citus kunt bijwerken in Azure Database for PostgreSQL-grootschalige (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 3758e2d458e1a6bd052ac746ac361de033d508e9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023980"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Server groep upgrade grootschalige (Citus)

In deze instructies wordt beschreven hoe u een upgrade uitvoert naar een nieuwe primaire versie van PostgreSQL op alle knoop punten van Server groepen.

## <a name="test-the-upgrade-first"></a>Test eerst de upgrade

Bij het upgraden van PostgreSQL worden er meer wijzigingen aangebracht dan u kunt voordoen, omdat grootschalige (Citus) ook de [database extensies](concepts-hyperscale-extensions.md)bijwerkt, met inbegrip van de Citus-extensie.
We raden u ten zeerste aan om uw toepassing te testen met de nieuwe PostgreSQL-en Citus-versie voordat u uw productie omgeving bijwerkt.

Een handige manier om te testen is door een kopie van uw server groep te maken met behulp van herstel naar een bepaald [tijdstip](concepts-hyperscale-backup.md#point-in-time-restore-pitr). Voer een upgrade uit voor de kopie en test uw toepassing hierop. Als u alles goed hebt gecontroleerd, werkt u de oorspronkelijke server groep bij.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Een server groep in het Azure Portal bijwerken

1. Selecteer de knop **bijwerken** in het gedeelte **overzicht** van een Citus-Server groep (grootschalige).
1. Er wordt een dialoog venster weer gegeven met de huidige versie van PostgreSQL en Citus.
   Kies een nieuwe PostgreSQL-versie in de lijst **upgrade naar** .
1. Controleer de waarde in **Citus-versie nadat de upgrade** is wat u verwacht.
   Deze waarde wordt gewijzigd op basis van de PostgreSQL-versie die u hebt geselecteerd.
1. Selecteer de knop **bijwerken** om door te gaan.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [ondersteunde postgresql-versies](concepts-hyperscale-versions.md).
* Bekijk [welke uitbrei dingen](concepts-hyperscale-extensions.md) zijn verpakt met elke postgresql-versie in een grootschalige-Server groep (Citus).
