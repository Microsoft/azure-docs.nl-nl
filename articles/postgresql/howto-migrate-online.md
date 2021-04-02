---
title: Migratie met minimale downtime naar Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u een minimale downtime-migratie uitvoert van een PostgreSQL-Data Base naar Azure Database for PostgreSQL-één-server met behulp van de Azure Database Migration Service.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489792"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migratie met minimale downtime naar Azure Database for PostgreSQL-één server
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

U kunt PostgreSQL-migraties uitvoeren om te Azure Database for PostgreSQL met minimale downtime door gebruik te maken van de zojuist geïntroduceerde **voortdurende synchronisatie mogelijkheid** voor de [Azure database Migration service](https://aka.ms/get-dms) (DMS). Deze functionaliteit beperkt de hoeveelheid downtime die door de toepassing wordt gemaakt.

## <a name="overview"></a>Overzicht
Azure DMS voert een initiële belasting van uw on-premises naar Azure Database for PostgreSQL uit en synchroniseert voortdurend nieuwe trans acties naar Azure terwijl de toepassing actief blijft. Nadat de gegevens op de doel-Azure-zijde zijn opgevangen, stopt u de toepassing voor een kort tijdstip (minimale downtime), wacht u tot de laatste batch met gegevens (vanaf het moment dat u de toepassing stopt totdat de toepassing daad werkelijk niet beschikbaar is om een nieuw verkeer te maken) en werkt u vervolgens uw connection string bij om naar Azure te verwijzen. Wanneer u klaar bent, is uw toepassing Live in Azure.

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Doorlopend synchroniseren met de Azure Database Migration Service":::

## <a name="next-steps"></a>Volgende stappen
- Bekijk de modernisering van de video- [apps met Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), die een demonstratie bevat over het migreren van postgresql-apps naar Azure database for PostgreSQL.
- Raadpleeg de zelf studie [migratie postgresql om online te Azure database for PostgreSQL met behulp van DMS](../dms/tutorial-postgresql-azure-postgresql-online.md).