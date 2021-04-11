---
title: Ondersteunde versies-Azure Database for MySQL
description: Meer informatie over de versies van de MySQL-server die worden ondersteund in de Azure Database for MySQL-service.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: d84f56e5ae0f3c364a0fd3a08ccb173d7c65a5e2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121758"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Ondersteunde serverversies van Azure Database for MySQL

Azure Database for MySQL is ontwikkeld vanuit de [MySQL Community Edition](https://www.mysql.com/products/community/), met behulp van de InnoDB-opslag engine. De service ondersteunt alle huidige primaire versie die wordt ondersteund door de community, namelijk MySQL 5,6, 5,7 en 8,0. MySQL maakt gebruik van het schema X. Y. Z waarbij X de primaire versie is, Y de secundaire versie en Z de release van de fout correctie. Zie de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)voor meer informatie over het schema.



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Verbinding maken met een gateway knooppunt waarop een specifieke MySQL-versie wordt uitgevoerd

In de implementatie optie voor één server wordt een gateway gebruikt om de verbindingen met Server exemplaren om te leiden. Nadat de verbinding tot stand is gebracht, wordt in de MySQL-client de versie van MySQL weergegeven die in de gateway is ingesteld, niet de feitelijke versie die wordt uitgevoerd op de MySQL-serverinstantie. Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt. Bekijk de [connectiviteits architectuur](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) voor meer informatie over gateways in azure database for MySQL service-architectuur.

Als Azure Database for MySQL ondersteunt primaire versie v 5.6, v 5.7 en v 8.0, de standaard poort 3306 om verbinding te maken met Azure Database for MySQL voert MySQL-client versie 5,6 (kleinste gemeen schappelijke noemer) uit voor de ondersteuning van verbindingen met servers van alle 3 ondersteunde primaire versies. Als uw toepassing echter een vereiste heeft om verbinding te maken met een specifieke primaire versie, bijvoorbeeld: v 5.7 of v 8.0, kunt u dit doen door de poort in uw server connection string te wijzigen.

In Azure Database for MySQL-service luistert gateway knooppunten op poort 3308 voor v 5.7-clients en poort 3309 voor v 8.0-clients. Met andere woorden, als u verbinding wilt maken met de v 5.7-Gateway-client, moet u de volledig gekwalificeerde server naam en poort 3308 gebruiken om verbinding te maken met uw server via client toepassing. En als u verbinding wilt maken met de v 8.0 Gateway-client, kunt u de volledig gekwalificeerde server naam en poort 3309 gebruiken om verbinding te maken met uw server. Raadpleeg het volgende voor beeld voor meer duidelijkheid.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Voor beeld van verbinding maken via verschillende gateway-mysql-versies":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL ondersteunt momenteel de volgende primaire en secundaire versies van MySQL:


| Versie | [Single Server](overview.md) <br/> Huidige secundaire versie |[Flexibele server (preview)](/../flexible-server/overview.md) <br/> Huidige secundaire versie  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL-versie 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (buiten gebruik gesteld) | Niet ondersteund|
|MySQL-versie 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL-versie 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Lees het versie-ondersteunings beleid voor buiten gebruik gestelde versies in de documentatie voor het [versie ondersteunings beleid.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren
De service beheert automatisch patches voor het oplossen van problemen met versie-updates. Bijvoorbeeld 5.7.20 installeren naar 5.7.21.  

De upgrade van de primaire versie wordt momenteel ondersteund door de service voor upgrades van MySQL v 5.6 naar v 5.7. Zie [belang rijke versie-upgrades uitvoeren](how-to-major-version-upgrade.md)voor meer informatie. Als u een upgrade wilt uitvoeren van 5,7 naar 8,0, raden we u aan [dump en herstel](./concepts-migrate-dump-restore.md) uit te voeren op een server die is gemaakt met de nieuwe engine versie.

## <a name="next-steps"></a>Volgende stappen

- Zie [dit document](concepts-version-policy.md)voor meer informatie over Azure database for MySQL-versie beleid.
- Zie [service lagen](./concepts-pricing-tiers.md) voor informatie over specifieke resource quota en beperkingen op basis **van uw servicelaag**.
