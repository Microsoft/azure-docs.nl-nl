---
title: 'Zelfstudie: Geo-replicatie en failover in portal'
description: Geo-replicatie voor een database configureren met de Azure-portal en failover initiëren.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673263"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Zelfstudie: Actieve geo-replicatie en failover configureren in de Azure-portal (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dit artikel leert u hoe u [actieve geo-replicatie voor Azure SQL Database](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) kunt configureren met behulp van de [Azure-portal](https://portal.azure.com) en hoe u een failover initieert.

Raadpleeg [Best practices voor Azure SQL Database](auto-failover-group-overview.md#best-practices-for-sql-database) en [Best practices voor Azure SQL Managed Instance](auto-failover-group-overview.md#best-practices-for-sql-managed-instance) voor best practices voor groepen voor automatische failover. 



## <a name="prerequisites"></a>Vereisten

Om actieve geo-replicatie met de Azure-portal te configureren, hebt u de volgende bron nodig:

* Een database in Azure SQL Database: de primaire database die u wilt repliceren naar een andere geografische regio.

> [!Note]
> Als u Azure Portal gebruikt, kunt u een secundaire database alleen maken in hetzelfde abonnement als de primaire database. Als een secundaire database in een ander abonnement moet worden gemaakt, gebruik dan [Create Database REST API](/rest/api/sql/databases/createorupdate) of [ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Een secundaire database toevoegen

Met de volgende stappen maakt u een nieuwe secundaire database in een geo-replicatie-partnerschap.  

Als u een secundaire database wilt toevoegen, moet u de eigenaar of mede-eigenaar van het abonnement zijn.

De secundaire database heeft dezelfde naam als de primaire database en heeft standaard dezelfde servicelaag en rekenkracht. De secundaire database kan een individuele database of een pooldatabase zijn. Zie [Aankoopmodel op basis van DTU](service-tiers-dtu.md) en [Aankoopmodel op basis van vCore](service-tiers-vcore.md) voor meer informatie.
Nadat de secundaire database is gemaakt en geseed, worden er gegevens gerepliceerd van de primaire database naar de nieuwe secundaire database.

> [!NOTE]
> Als de partnerdatabase al bestaat (bijvoorbeeld als gevolg van het beëindigen van een eerdere geo-replicatie-relatie) mislukt de opdracht.

1. Blader in de [Azure-portal](https://portal.azure.com) naar de database die u wilt instellen voor geo-replicatie.
2. Op de pagina SQL Database selecteert u **Geo-replicatie** en vervolgens selecteert u de regio voor de secundaire database. U kunt een willekeurige regio selecteren (zolang het niet de regio is die de primaire database host) maar we raden de [gekoppelde regio](../../best-practices-availability-paired-regions.md) aan.

    ![Geo-replicatie configureren](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Selecteer of configureer de server en prijscategorie voor de secundaire database.

    ![secundair formulier maken](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Desgewenst kunt u een secundaire database aan een elastische pool toevoegen. Als u de secundaire database in een groep wilt maken, klikt u op **Elastische pool** en selecteert u een pool op de doelserver. Er moet al een pool bestaan op de doelserver. Met deze werkstroom wordt geen pool gemaakt.
5. Klik op **Maken** om de secundaire database toe te voegen.
6. De secundaire database wordt gemaakt en het seedingproces begint.

    ![map met secundaire databases](./media/active-geo-replication-configure-portal/seeding0.png)
7. Wanneer het seedingproces is voltooid, wordt de status van de secundaire database weergegeven.

    ![Seeding voltooid](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Een failover initiëren

U kunt van de secundaire database een primaire database maken.  

1. Blader in de [Azure-portal](https://portal.azure.com) naar de primaire database in het geo-replicatie-partnerschap.
2. Selecteer op de blade SQL Database **Alle instellingen** > **Geo-replicatie**.
3. Selecteer in de lijst **SECUNDAIRE DATABASES** de database waarvan u wilt dat deze de nieuwe primaire database wordt en klik op **Geforceerde failover**.

    ![failover](./media/active-geo-replication-configure-portal/secondaries.png)
4. Klik op **Ja** om de failover te starten.

De opdracht maakt van de secundaire database onmiddellijk de primaire database. Dit proces wordt normaal gesproken binnen 30 seconden afgerond.

Terwijl de rollen worden omgewisseld, is er een korte periode (van 0 tot 25 seconden) waarin beide databases niet beschikbaar zijn. Als de primaire database meerdere secundaire databases heeft, herconfigureert de opdracht automatisch de andere secundaire databases zodat deze verbinding maken met de nieuwe primaire database. Onder normale omstandigheden duurt het minder dan een minuut om deze hele bewerking uit te voeren.

> [!NOTE]
> Deze opdracht is bedoeld voor een snel herstel van de database in het geval van een storing. Er wordt een failover geactiveerd zonder gegevenssynchronisatie (geforceerde failover).  Als de primaire database online is en transacties uitvoert wanneer de opdracht wordt uitgegeven, dan kunnen er enkele gegevens verloren gaan.

## <a name="remove-secondary-database"></a>Secundaire database verwijderen

Met deze bewerking wordt de replicatie naar de secundaire database permanent beëindigd en wordt de rol van de secundaire database gewijzigd in die van een normale lezen-schrijven-database. Als de verbinding met de secundaire database is verbroken, wordt de opdracht uitgevoerd, maar de secundaire database wordt pas een lezen-schrijven-database wanneer de verbinding is hersteld.  

1. Blader in de [Azure-portal](https://portal.azure.com) naar de primaire database in het geo-replicatie-partnerschap.
2. Selecteer op de pagina SQL database **Geo-replicatie**.
3. Selecteer in de lijst **SECUNDAIRE DATABASES** de database die u wilt verwijderen uit het geo-replicatie-partnerschap.
4. Klik op **Replicatie stoppen**.

    ![Secundaire database verwijderen](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Er wordt een bevestigingsvenster geopend. Klik op **Ja** om de database uit het geo-replicatie-partnerschap te verwijderen. (Stel de database in als een lezen-schrijven-database die geen deel uitmaakt van een replicatie.)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Actieve geo-replicatie](active-geo-replication-overview.md) voor meer informatie over actieve geo-replicatie.
* Raadpleeg [Groepen voor automatische failover](auto-failover-group-overview.md) voor meer informatie over groepen voor automatische failover
* Raadpleeg [Overzicht van bedrijfscontinuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) voor een bedrijfscontinuïteitsoverzicht en bedrijfscontinuïteitsscenario's.