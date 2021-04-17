---
title: Servergroep upgraden - Hyperscale (Citus) - Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u PostgreSQL en Citus kunt upgraden in Azure Database for PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518895"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Een Hyperscale (Citus) servergroep bijwerken

In deze instructies wordt beschreven hoe u op alle knooppunten van de servergroep een upgrade naar een nieuwe belangrijke versie van PostgreSQL kunt uitvoeren.

## <a name="test-the-upgrade-first"></a>De upgrade eerst testen

Het upgraden van PostgreSQL veroorzaakt meer wijzigingen dan Hyperscale (Citus), [](concepts-hyperscale-extensions.md)omdat de database-extensies ook worden geupgraded, inclusief de Citus-extensie.
We raden u ten zeerste aan om uw toepassing te testen met de nieuwe PostgreSQL- en Citus-versie voordat u uw productieomgeving upgradet.

Een handige manier om te testen is om een kopie van uw servergroep te maken met behulp van herstel [naar een bepaald tijdstip.](concepts-hyperscale-backup.md#restore) Werk de kopie bij en test uw toepassing op de kopie. Nadat u hebt gecontroleerd of alles goed werkt, moet u de oorspronkelijke servergroep upgraden.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Een servergroep upgraden in de Azure Portal

1. Selecteer in **de sectie** Overzicht van Hyperscale (Citus) servergroep de **knop Upgraden.**
1. Er wordt een dialoogvenster weergegeven met de huidige versie van PostgreSQL en Citus.
   Kies een nieuwe PostgreSQL-versie in de **lijst Upgraden naar.**
1. Controleer of de waarde in **de Citus-versie na de upgrade** is wat u verwacht.
   Deze waarde wordt gewijzigd op basis van de PostgreSQL-versie die u hebt geselecteerd.
1. Selecteer de **knop Upgraden** om door te gaan.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over ondersteunde PostgreSQL-versies.](concepts-hyperscale-versions.md)
* Zie [welke extensies](concepts-hyperscale-extensions.md) zijn verpakt met elke PostgreSQL-versie in een Hyperscale (Citus) servergroep.
