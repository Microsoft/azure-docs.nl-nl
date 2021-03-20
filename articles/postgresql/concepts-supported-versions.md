---
title: Ondersteunde versies-Azure Database for PostgreSQL-één server
description: Beschrijft de ondersteunde post gres primaire en secundaire versies in Azure Database for PostgreSQL-één server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 41662c5e4cc0ed9458f8b1b1279e2753daed789f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518450"
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

## <a name="postgresql-version-95-retired"></a>PostgreSQL-versie 9,5 (buiten gebruik gesteld)
Wanneer post gres wordt uitgelijnd met het [versie beleid](https://www.postgresql.org/support/versioning/)van de community, heeft Azure database for PostgreSQL van 11 februari 2021 post gres versie 9,5 buiten gebruik gesteld. Raadpleeg het [Azure database for PostgreSQL-versie beleid](concepts-version-policy.md) voor meer informatie en beperkingen. Als u deze primaire versie uitvoert, moet u een upgrade uitvoeren naar een hogere versie, bij voor keur PostgreSQL 11 op het eerste gemak.

## <a name="managing-upgrades"></a>Upgrades beheren
Het PostgreSQL-project ondervindt regel matig kleine releases voor het oplossen van gemelde bugs. Op Azure Database for PostgreSQL-servers worden automatisch patches met secundaire releases toegepast tijdens de maandelijkse implementaties van de service. 

Automatische in-place Upgrades voor primaire versies worden niet ondersteund. Als u een upgrade naar een hogere primaire versie wilt uitvoeren, kunt u 
   * Gebruik een van de methoden die worden beschreven in upgrades van de [primaire versie met dump en herstel](./how-to-upgrade-using-dump-and-restore.md).
   * Gebruik [pg_dump en pg_restore](./howto-migrate-using-dump-and-restore.md) om een Data Base te verplaatsen naar een server die is gemaakt met de nieuwe engine versie.
   * Gebruik de [Azure data base Migration service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) voor het uitvoeren van online-upgrades.

### <a name="version-syntax"></a>Versie syntaxis
Vóór PostgreSQL versie 10 wordt het [postgresql-versie beleid](https://www.postgresql.org/support/versioning/) beschouwd als een _primaire versie_ -upgrade, zodat het eerste _of_ tweede nummer toeneemt. Bijvoorbeeld: 9,5 tot 9,6 werd beschouwd als een _primaire_ versie-upgrade. Vanaf versie 10 wordt alleen een wijziging in het eerste getal beschouwd als een primaire versie-upgrade. Een voor beeld: 10,0 tot 10,1 is een _kleine_ release-upgrade. Versie 10 tot 11 is een _primaire_ versie-upgrade.

## <a name="next-steps"></a>Volgende stappen
Zie [het document extensies](concepts-extensions.md)voor meer informatie over ondersteunde postgresql-uitbrei dingen.
