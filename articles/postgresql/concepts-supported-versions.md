---
title: Ondersteunde versies-Azure Database for PostgreSQL-één server
description: Beschrijft de ondersteunde post gres primaire en secundaire versies in Azure Database for PostgreSQL-één server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 72d774b4ced6471ff7b355b2cb43c3c9127b5975
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658516"
---
# <a name="supported-postgresql-major-versions"></a>Ondersteunde PostgreSQL primaire versies

Raadpleeg het [Azure database for PostgreSQL-versie beleid](concepts-version-policy.md) voor details van het ondersteunings beleid.

Azure Database for PostgreSQL ondersteunt momenteel de volgende primaire versies:

## <a name="postgresql-version-11"></a>PostgreSQL-versie 11
De huidige secundaire versie is 11,6. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/11/static/release-11-6.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-10"></a>PostgreSQL-versie 10
De huidige secundaire versie is 10,11. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/10/static/release-10-11.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-96"></a>PostgreSQL-versie 9,6
De huidige secundaire release is 9.6.16. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-95"></a>PostgreSQL-versie 9,5
De huidige secundaire release is 9.5.20. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

> [!NOTE]
> Bij het uitlijnen met post gres-beleid voor de Community Azure Database for PostgreSQL- [versie](https://www.postgresql.org/support/versioning/)wordt post gres versie 9,5 op 11 februari 2021 buiten gebruik gesteld. Raadpleeg het [Azure database for PostgreSQL-versie beleid](concepts-version-policy.md) voor meer informatie en beperkingen.

## <a name="managing-upgrades"></a>Upgrades beheren
Het PostgreSQL-project ondervindt regel matig kleine releases voor het oplossen van gemelde bugs. Azure Database for PostgreSQL Servert automatisch servers met secundaire releases tijdens de maandelijkse implementaties van de service. 

Automatische in-place Upgrades voor primaire versies worden niet ondersteund. Als u een upgrade wilt uitvoeren naar de volgende primaire versie, kunt u 
   * Zie verschillende methoden voor het uitvoeren [van upgrades van de primaire versie met dump en herstel](./how-to-upgrade-using-dump-and-restore.md)
   * [Pg_dump en pg_restore](./howto-migrate-using-dump-and-restore.md) gebruiken om een Data Base te verplaatsen naar een server die is gemaakt met de nieuwe engine versie
   * U kunt ook een upgrade uitvoeren van PostgreSQL 10 naar 11 met behulp van de [Azure data base Migration service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)

### <a name="version-syntax"></a>Versie syntaxis
Vóór PostgreSQL versie 10 wordt het [postgresql-versie beleid](https://www.postgresql.org/support/versioning/) beschouwd als een _primaire versie_ -upgrade, zodat het eerste _of_ tweede nummer toeneemt. Bijvoorbeeld: 9,5 tot 9,6 werd beschouwd als een _primaire_ versie-upgrade. Vanaf versie 10 wordt alleen een wijziging in het eerste getal beschouwd als een primaire versie-upgrade. Een voor beeld: 10,0 tot 10,1 is een _kleine_ release-upgrade. Versie 10 tot 11 is een _primaire_ versie-upgrade.

## <a name="next-steps"></a>Volgende stappen
Zie [het document extensies](concepts-extensions.md)voor meer informatie over ondersteunde postgresql-uitbrei dingen.
