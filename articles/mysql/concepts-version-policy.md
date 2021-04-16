---
title: Versieondersteuningsbeleid - Azure Database for MySQL - Enkele server en flexibele server (preview)
description: Beschrijft het beleid voor de hoofd- en secundaire versies van MySQL in Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 49e59c43e9eaedf770b1a8e052dd73aa331d31ce
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389567"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL versieondersteuningsbeleid

Op deze pagina wordt het Azure Database for MySQL versiebeheerbeleid beschreven en is van toepassing op de implementatiemodi Azure Database for MySQL - Single Server en Azure Database for MySQL - Flexible Server (Preview).

## <a name="supported--mysql-versions"></a>Ondersteunde MySQL-versies

Azure Database for MySQL is ontwikkeld op basis van [MySQL Community Edition](https://www.mysql.com/products/community/)met behulp van de InnoDB-opslagen engine. De service ondersteunt alle huidige belangrijke versies die door de community worden ondersteund, namelijk MySQL 5.6, 5.7 en 8.0. MySQL maakt gebruik van het X.Y.Z-naamgevingsschema waarbij X de belangrijkste versie is, Y de secundaire versie en Z de release van de foutfix. Zie de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)voor meer informatie over het schema.

Azure Database for MySQL ondersteunt momenteel de volgende belangrijke en secundaire versies van MySQL:

| Versie | [Single Server](overview.md) <br/> Huidige secundaire versie |[Flexibele server (preview)](/azure/mysql/flexible-server/overview) <br/> Huidige secundaire versie  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL versie 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(gestopt) | Niet ondersteund|
|MySQL versie 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL versie 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> In de implementatieoptie Enkele server wordt een gateway gebruikt om de verbindingen met server-exemplaren om te leiden. Nadat de verbinding tot stand is gebracht, wordt in de MySQL-client de versie van MySQL weergegeven die in de gateway is ingesteld, niet de feitelijke versie die wordt uitgevoerd op de MySQL-serverinstantie. Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt. Als uw toepassing een vereiste heeft om verbinding te maken met een specifieke belangrijke versie, bijvoorbeeld v5.7 of v8.0, kunt u dit doen door de poort in uw server-connection string te wijzigen, zoals wordt uitgelegd in onze documentatie [hier.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> MySQL v5.6 is vanaf de febuary 2021 uit gebruik genomen op één server. Vanaf 1 september 2021 kunt u geen nieuwe v5.6-servers maken op Azure Database for MySQL - Implementatieoptie voor één server. U kunt echter herstel naar een bepaald tijdstip uitvoeren en leesreplica's maken voor uw bestaande servers.

Lees het versieondersteuningsbeleid voor oudere versies in documentatie [over versieondersteuningsbeleid.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Ondersteuning voor de belangrijkste versie
Elke belangrijke versie van MySQL wordt ondersteund door Azure Database for MySQL vanaf de datum waarop Azure begint met het ondersteunen van de versie totdat de versie wordt teruggetrokken door de MySQL-community, zoals is opgegeven in het versiebeleid [.](https://www.mysql.com/support/eol-notice.html)

## <a name="minor-version-support"></a>Ondersteuning voor secundaire versie
Azure Database for MySQL voert automatisch kleine versie-upgrades uit naar de MySQL-versie van De voorkeur van Azure als onderdeel van periodiek onderhoud. 

## <a name="major-version-retirement-policy"></a>Beleid voor het uittreden van hoofdversies
De onderstaande tabel bevat de pensioengegevens voor de belangrijkste mySQL-versies. De datums volgen het [MySQL-versiebeleid](https://www.mysql.com/support/eol-notice.html).

| Versie | Nieuwe functies | ondersteuning voor Azure begindatum | Datum van pensioen|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Functies](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 maart 2018 | Februari 2021
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Functies](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 maart 2018 | Oktober 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Functies](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 december 2019 | April 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Versies van de MySQL-engine die niet worden ondersteund in de Azure Database for MySQL

Als u na de pensioendatum voor elke MySQL-databaseversie doorgaat met het uitvoeren van de versie die niet meer is gebruikt, moet u rekening houden met de volgende beperkingen:
- Omdat de community geen verdere oplossingen voor fouten of beveiligingspatches zal vrijgeven, patcht Azure Database for MySQL de database-engine niet voor eventuele fouten of beveiligingsproblemen en worden er op een andere manier beveiligings maatregelen genomen met betrekking tot de database-engine die niet is teruggetrokken. Azure blijft echter periodiek onderhoud en patches uitvoeren voor de host, het besturingssysteem, containers en andere servicegerelateerde onderdelen.
- Als er een ondersteuningsprobleem is dat betrekking heeft op de MySQL-database, kunnen we u mogelijk geen ondersteuning bieden. In dergelijke gevallen moet u uw database upgraden zodat wij u ondersteuning kunnen bieden.
- U kunt geen nieuwe databaseservers maken voor de versie die niet meer is gebruikt. U kunt echter herstel naar een bepaald tijdstip uitvoeren en leesreplica's maken voor uw bestaande servers.
- Nieuwe servicemogelijkheden die zijn ontwikkeld door Azure Database for MySQL zijn mogelijk alleen beschikbaar voor ondersteunde databaseserverversies.
- Sla's voor uptime zijn alleen van toepassing Azure Database for MySQL problemen met de service en niet op downtime die wordt veroorzaakt door fouten in de database-engine.  
- In het extreme geval van een ernstige bedreiging voor de service die wordt veroorzaakt door het beveiligingsprobleem met de MySQL-database-engine dat is geïdentificeerd in de buiten gebruik stellende databaseversie, kan Azure ervoor kiezen om het reken knooppunt van uw databaseserver te stoppen om de service eerst te beveiligen. U wordt gevraagd de server bij te upgraden voordat u de server online brengt. Tijdens het upgradeproces worden uw gegevens altijd beveiligd met behulp van automatische back-ups die worden uitgevoerd op de service. Deze kunnen worden gebruikt om indien gewenst terug te keren naar de oudere versie. 



## <a name="next-steps"></a>Volgende stappen
- Zie Azure Database for MySQL - Ondersteunde versies van [één server](./concepts-supported-versions.md)
- Zie Azure Database for MySQL - Flexible Server (preview) [ondersteunde versies](flexible-server/concepts-supported-versions.md)
- Zie MySQL dump and restore (MySQL [dumpen en herstellen)](./concepts-migrate-dump-restore.md) om upgrades uit te voeren.
