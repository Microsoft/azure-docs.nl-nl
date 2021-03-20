---
title: Ondersteunde versies-Azure Database for MySQL flexibele server
description: Meer informatie over de versies van de MySQL-server die worden ondersteund op de Azure Database for MySQL flexibele server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7ad6a576262b8e722b16c81af544a9370c2b49b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93242259"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Ondersteunde versies voor Azure Database for MySQL-flexibele server


> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.


Azure Database for MySQL flexibele server wordt aangedreven door de [MySQL Community Edition](https://www.mysql.com/products/community/), met behulp van de InnoDB-engine.

MySQL maakt gebruik van het schema X. Y. Z. X is de primaire versie, Y de secundaire versie en Z de release van de fout correctie. Zie de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)voor meer informatie over het schema.

> [!NOTE]
> Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt.

Azure Database for MySQL ondersteunt momenteel de volgende versies:

## <a name="mysql-version-57"></a>MySQL-versie 5,7

Release van de fout oplossing: 5.7.29

De service voert automatische patches uit van de onderliggende hardware, het besturingssysteem en de database-engine. De patches omvatten beveiligings- en software-updates. Voor de MySQL-engine maken kleine versie-upgrades ook deel uit van de geplande onderhoudsrelease. Gebruikers kunnen het schema voor het toepassen van patches configureren voor beheer door het systeem of zelf hun eigen aangepaste schema definiëren. Tijdens het onderhoudsschema wordt de patch toegepast en moet de server mogelijk opnieuw worden opgestart als onderdeel van het patchproces om de update te voltooien. Met een aangepast schema kunnen gebruikers hun patchcyclus voorspelbaar maken en een onderhoudsvenster kiezen met minimale gevolgen voor het bedrijf. Over het algemeen volgt de service de maandelijkse releaseplanning, als onderdeel van de continue integratie en releases.

Raadpleeg de opmerkingen bij de [versie](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) van MySQL voor meer informatie over verbeteringen en oplossingen in deze versie.

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren
De service beheert automatisch patches voor het oplossen van problemen met versie-updates. Bijvoorbeeld 5.7.29 naar 5.7.30.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Een PHP-app bouwen in Windows met MySQL](../../app-service/tutorial-php-mysql-app.md)<br/>
>[PHP-app bouwen op Linux met MySQL](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[Op Java gebaseerde lente-app bouwen met MySQL](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>