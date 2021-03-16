---
title: Beleid voor versie ondersteuning-Azure Database for MySQL-één server en flexibele server (preview)
description: Beschrijft het beleid voor de primaire en secundaire versies van MySQL in Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 8ad79f2f27864b4fbc78b7c104828230ff7f93bc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465654"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Ondersteunings beleid voor Azure Database for MySQL-versie

Op deze pagina wordt het Azure Database for MySQL-versie beleid beschreven en is van toepassing op de implementatie modi Azure Database for MySQL-één server en Azure Database for MySQL-flexibele server (preview).

## <a name="supported--mysql-versions"></a>Ondersteunde MySQL-versies

Azure Database for MySQL is ontwikkeld vanuit de [MySQL Community Edition](https://www.mysql.com/products/community/), met behulp van de InnoDB-opslag engine. De service ondersteunt alle huidige primaire versie die wordt ondersteund door de community, namelijk MySQL 5,6, 5,7 en 8,0. MySQL maakt gebruik van het schema X. Y. Z waarbij X de primaire versie is, Y de secundaire versie en Z de release van de fout correctie. Zie de [MySQL-documentatie](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)voor meer informatie over het schema.

> [!NOTE]
> In de implementatie optie voor één server wordt een gateway gebruikt om de verbindingen met Server exemplaren om te leiden. Nadat de verbinding tot stand is gebracht, wordt in de MySQL-client de versie van MySQL weergegeven die in de gateway is ingesteld, niet de feitelijke versie die wordt uitgevoerd op de MySQL-serverinstantie. Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt.

Azure Database for MySQL ondersteunt momenteel de volgende primaire en secundaire versies van MySQL:

| Versie | Enkele server <br/> Huidige secundaire versie |Flexibele server (preview) <br/> Huidige secundaire versie  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL-versie 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(buiten gebruik gesteld) | Niet ondersteund|
|MySQL-versie 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL-versie 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Lees het versie-ondersteunings beleid voor buiten gebruik gestelde versies in de documentatie voor het [versie ondersteunings beleid.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Ondersteuning voor primaire versie
Elke primaire versie van MySQL wordt ondersteund door Azure Database for MySQL vanaf de datum waarop Azure de versie ondersteunt, totdat de versie wordt ingetrokken door de MySQL-Community, zoals is opgenomen in het [versie beleid](https://www.mysql.com/support/eol-notice.html).

## <a name="minor-version-support"></a>Ondersteuning voor secundaire versie
Azure Database for MySQL voert automatisch secundaire versie-upgrades uit naar de voor Keurs-MySQL-versie van Azure als onderdeel van het periodieke onderhoud. 

## <a name="major-version-retirement-policy"></a>Beleid voor primaire versie buiten gebruik stellen
De volgende tabel bevat de details van de buiten gebruiks telling van de primaire MySQL-versie. De datums volgen het [beleid voor MySQL-versie beheer](https://www.mysql.com/support/eol-notice.html).

| Versie | Nieuwe functies | Start datum voor ondersteuning van Azure | Buitengebruikstellings datum|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Functies](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 maart 2018 | Februari 2021
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Functies](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 maart 2018 | Oktober 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Functies](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 december 2019 | April 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Buiten gebruik gestelde MySQL-Engine versies worden niet ondersteund in Azure Database for MySQL

Als u na de datum van uittreding voor elke MySQL-database versie doorgaat, moet u rekening houden met de volgende beperkingen:
- Aangezien de community geen verdere oplossingen voor problemen of beveiligingsfixes uitbrengt, zal Azure Database for MySQL de buiten gebruik gestelde data base-engine niet voor eventuele fouten of beveiliging bijwerken of anderszins beveiligings maatregelen treffen met betrekking tot de buiten gebruik gestelde data base-engine. Azure blijft echter periodiek onderhoud en patches uitvoeren voor de host, het besturings systeem, containers en andere service-gerelateerde onderdelen.
- Als er mogelijk ondersteunings problemen optreden met betrekking tot de MySQL-data base, kunnen we u mogelijk geen ondersteuning bieden voor u. In dergelijke gevallen moet u uw data base upgraden om u te helpen bij het verlenen van ondersteuning.
<!-- - You will not be able to create new database servers for the retired version. However, you will be able to perform point-in-time recoveries and create read replicas for your existing servers. -->
- Nieuwe service functies die zijn ontwikkeld door Azure Database for MySQL, zijn mogelijk alleen beschikbaar voor ondersteunde database server versies.
- Beschik baarheid van de uptime geldt alleen voor het Azure Database for MySQL van problemen met de service en niet voor uitval tijd die wordt veroorzaakt door fouten met betrekking tot de data base-engine.  
- In het uitzonderlijke geval van een ernstige bedreiging van de service die wordt veroorzaakt door het beveiligingslek met betrekking tot de MySQL-data base-engine die is geïdentificeerd in de buiten gebruik gestelde versie van de data base, kan Azure ervoor kiezen het reken knooppunt van uw database server te stoppen om de service eerst te beveiligen. U wordt gevraagd de server bij te werken voordat u de server online brengt. Tijdens het upgrade proces worden uw gegevens altijd beveiligd met automatische back-ups die worden uitgevoerd op de service, die kan worden gebruikt om terug te zetten naar de oudere versie. 



## <a name="next-steps"></a>Volgende stappen
- Zie Azure Database for MySQL- [ondersteunde versies](./concepts-supported-versions.md) van één server
- Zie Azure Database for MySQL-flexibele server (preview) [ondersteunde versies](flexible-server/concepts-supported-versions.md)
- Zie MySQL- [dump en herstellen](./concepts-migrate-dump-restore.md) om upgrades uit te voeren.
