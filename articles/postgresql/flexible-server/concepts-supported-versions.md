---
title: Ondersteunde versies-Azure Database for PostgreSQL-flexibele server
description: Hierin worden de ondersteunde primaire en secundaire versies van PostgreSQL in Azure Database for PostgreSQL-flexibele server beschreven.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5fda7ebb5a72dd9bbfab0ba72511540cf141563f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608847"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Ondersteunde PostgreSQL primaire versies in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL-flexibele server ondersteunt momenteel de volgende primaire versies:

## <a name="postgresql-version-12"></a>PostgreSQL-versie 12

De huidige secundaire versie is 12,5. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/12/static/release-12-4.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-11"></a>PostgreSQL-versie 11

De huidige secundaire versie is 11,10. Raadpleeg de [postgresql-documentatie](https://www.postgresql.org/docs/11/static/release-11-9.html) voor meer informatie over verbeteringen en oplossingen in deze kleine release.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL-versie 10 en ouder

PostgreSQL versie 10 en ouder voor Azure Database for PostgreSQL-flexibele server worden niet ondersteund. Gebruik de implementatie optie voor [één server](../concepts-supported-versions.md) als u oudere versies nodig hebt.

## <a name="managing-upgrades"></a>Upgrades beheren

Het PostgreSQL-project ondervindt regel matig kleine releases voor het oplossen van gemelde bugs. Op Azure Database for PostgreSQL-servers worden automatisch patches met secundaire releases toegepast tijdens de maandelijkse implementaties van de service.

Automation voor de upgrade van de primaire versie wordt nog niet ondersteund. Er is bijvoorbeeld momenteel geen automatische upgrade van PostgreSQL 11 naar PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->