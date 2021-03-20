---
title: Migratie met minimale downtime-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u een minimale downtime-migratie van een MySQL-data base naar Azure Database for MySQL uitvoert met behulp van de Azure Database Migration Service.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 75b548b53de4c0f4ea39959c3d70e9ddb361d64c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540328"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimale migratie van downtime naar Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

U kunt MySQL-migraties uitvoeren om te Azure Database for MySQL met minimale downtime door gebruik te maken van de zojuist geïntroduceerde **voortdurende synchronisatie mogelijkheid** voor de [Azure database Migration service](https://aka.ms/get-dms) (DMS). Deze functionaliteit beperkt de hoeveelheid downtime die door de toepassing wordt gemaakt.

U kunt ook de [hand leiding voor database migratie](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) raadplegen voor gedetailleerde informatie en voor beelden over het migreren van data bases naar Azure database for MySQL. Deze hand leiding bevat richt lijnen voor een geslaagde planning en uitvoering van een MySQL-migratie naar Azure.

## <a name="overview"></a>Overzicht
Azure DMS voert een initiële belasting van uw on-premises naar Azure Database for MySQL uit en synchroniseert voortdurend nieuwe trans acties naar Azure terwijl de toepassing actief blijft. Nadat de gegevens op de doel-Azure-zijde zijn opgevangen, stopt u de toepassing voor een kort tijdstip (minimale downtime), wacht u tot de laatste batch met gegevens (vanaf het moment dat u de toepassing stopt totdat de toepassing daad werkelijk niet beschikbaar is om een nieuw verkeer te maken) en werkt u vervolgens uw connection string bij om naar Azure te verwijzen. Wanneer u klaar bent, is uw toepassing Live in Azure.

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Doorlopend synchroniseren met de Azure Database Migration Service":::

## <a name="next-steps"></a>Volgende stappen
- Zie de [hand leiding voor database migratie](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)voor meer informatie over het migreren van data bases naar Azure database for MySQL.
- Bekijk de video [eenvoudig MySQL/PostgreSQL-apps migreren naar een door Azure beheerde service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), die een demonstratie bevat van het migreren van MySQL-apps naar Azure database for MySQL.
- Raadpleeg de zelf studie [MySQL migreren naar Azure database for MySQL online met behulp van DMS](../dms/tutorial-mysql-azure-mysql-online.md).