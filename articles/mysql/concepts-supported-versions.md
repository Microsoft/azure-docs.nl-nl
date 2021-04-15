---
title: Ondersteunde versies - Azure Database for MySQL
description: Ontdek welke versies van de MySQL-server worden ondersteund in de Azure Database for MySQL service.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1be15c16a1897797326ea869c34c3590ffb07691
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363866"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Ondersteunde serverversies van Azure Database for MySQL

Azure Database for MySQL is ontwikkeld op basis van [MySQL Community Edition](https://www.mysql.com/products/community/)met behulp van de InnoDB-opslagen engine. De service ondersteunt alle huidige belangrijke versies die door de community worden ondersteund, namelijk MySQL 5.6, 5.7 en 8.0. MySQL maakt gebruik van het X.Y.Z-naamgevingsschema waarbij X de belangrijkste versie is, Y de secundaire versie en Z de release van de foutfix. Zie de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)voor meer informatie over het schema.



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Verbinding maken met een gateway-knooppunt met een specifieke MySQL-versie

In de implementatieoptie Enkele server wordt een gateway gebruikt om de verbindingen met server-exemplaren om te leiden. Nadat de verbinding tot stand is gebracht, wordt in de MySQL-client de versie van MySQL weergegeven die in de gateway is ingesteld, niet de feitelijke versie die wordt uitgevoerd op de MySQL-serverinstantie. Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt. Bekijk [Connectiviteitsarchitectuur](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) voor meer informatie over gateways in Azure Database for MySQL servicearchitectuur.

Omdat Azure Database for MySQL ondersteuning biedt voor de hoofdversie v5.6, v5.7 en v8.0, wordt op de standaardpoort 3306 om verbinding te maken met Azure Database for MySQL MySQL-clientversie 5.6 (minst algemene noemer) uitgevoerd om verbindingen met servers van alle drie de ondersteunde hoofdversies te ondersteunen. Als uw toepassing echter een vereiste heeft om verbinding te maken met specifieke belangrijke versie, bijvoorbeeld v5.7 of v8.0, kunt u dit doen door de poort in uw serverserver te connection string.

In Azure Database for MySQL-service luisteren gatewayknooppunten naar poort 3308 voor v5.7-clients en poort 3309 voor v8.0-clients. Met andere woorden, als u verbinding wilt maken met de v5.7-gatewayclient, moet u uw volledig gekwalificeerde servernaam en poort 3308 gebruiken om verbinding te maken met uw server vanuit de clienttoepassing. En als u verbinding wilt maken met de v8.0-gatewayclient, kunt u de volledig gekwalificeerde servernaam en poort 3309 gebruiken om verbinding te maken met uw server. Controleer het volgende voorbeeld voor meer duidelijkheid.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Voorbeeld van verbinding maken via verschillende mysql-versies van de gateway":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL ondersteunt momenteel de volgende belangrijke en secundaire versies van MySQL:


| Versie | [Single Server](overview.md) <br/> Huidige secundaire versie |[Flexibele server (preview)](/azure/mysql/flexible-server/overview) <br/> Huidige secundaire versie  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL versie 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (gestopt) | Niet ondersteund|
|MySQL versie 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versie 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Lees het versieondersteuningsbeleid voor oudere versies in de documentatie [voor versieondersteuningsbeleid.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren
De service beheert automatisch patching voor versie-updates voor het oplossen van fouten. Bijvoorbeeld 5.7.20 tot 5.7.21.  

Upgrade van de belangrijkste versie wordt momenteel ondersteund door de service voor upgrades van MySQL v5.6 naar v5.7. Raadpleeg belangrijke versie-upgrades uitvoeren [voor meer informatie.](how-to-major-version-upgrade.md) Als u wilt upgraden van 5.7 naar 8.0, raden we u aan [dump](./concepts-migrate-dump-restore.md) en herstel uit te voeren naar een server die is gemaakt met de nieuwe engineversie.

## <a name="next-steps"></a>Volgende stappen

- Zie dit document Azure Database for MySQL informatie over het [versiebeleid.](concepts-version-policy.md)
- Zie Servicelagen voor meer informatie over specifieke resourcequota en beperkingen op basis van [uw servicelaag](./concepts-pricing-tiers.md) 
