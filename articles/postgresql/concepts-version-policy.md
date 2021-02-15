---
title: Versie beheer beleid-Azure Database for PostgreSQL-één server en flexibele server (preview)
description: Beschrijft het beleid rondom post gres primaire en secundaire versies in Azure Database for PostgreSQL-één server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 62fe1b3391eb4cb2d409a92b936fd3f1ae56d992
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518416"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Azure Database for PostgreSQL-versiebeleid

Op deze pagina wordt het Azure Database for PostgreSQL-versie beleid beschreven en is van toepassing op de implementatie modi Azure Database for PostgreSQL-één server en Azure Database for PostgreSQL-flexibele server (preview).

## <a name="supported--postgresql-versions"></a>Ondersteunde PostgreSQL-versies

Azure Database for PostgreSQL ondersteunt de volgende database versies.

| Versie | Single Server | Flexible Server (preview) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9,6 | X |  |
| *PostgreSQL 9,5 (buiten gebruik gesteld)* | X |  |

## <a name="major-version-support"></a>Ondersteuning voor primaire versie
Elke primaire versie van PostgreSQL wordt ondersteund door Azure Database for PostgreSQL vanaf de datum waarop Azure de versie ondersteunt, totdat de versie wordt ingetrokken door de PostgreSQL-Community, zoals is beschreven in het [postgresql-beleid](https://www.postgresql.org/support/versioning/)voor de Community-versie.

## <a name="minor-version-support"></a>Ondersteuning voor secundaire versie
Azure Database for PostgreSQL voert automatisch secundaire versie-upgrades uit naar de voorkeurs PostgreSQL-versie van Azure als onderdeel van het periodieke onderhoud. 

## <a name="major-version-retirement-policy"></a>Beleid voor primaire versie buiten gebruik stellen
De onderstaande tabel bevat de details van het pensioen voor PostgreSQL primaire versies. De datums volgen het [postgresql-beleid](https://www.postgresql.org/support/versioning/)voor de Community-versie.

| Versie | Nieuwe functies | Start datum voor ondersteuning van Azure | Buitengebruikstellings datum|
| ----- | ----- | ------ | ----- |
| [PostgreSQL 9,5 (buiten gebruik gesteld)](https://www.postgresql.org/about/news/postgresql-132-126-1111-1016-9621-and-9525-released-2165/)| [Functies](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 18 april 2018   | 11 februari 2021
| [PostgreSQL 9,6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Functies](https://wiki.postgresql.org/wiki/NewIn96) | 18 april 2018  | 11 november 2021
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Functies](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 4 juni 2018  | 10 november 2022
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Functies](https://www.postgresql.org/docs/11/release-11.html) | 24 juli 2019  | 9 november 2023
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Functies](https://www.postgresql.org/docs/12/release-12.html) | Sept 22, 2020  | 14 november 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Buiten gebruik gestelde PostgreSQL-Engine versies worden niet ondersteund in Azure Database for PostgreSQL

U kunt door gaan met het uitvoeren van de buiten gebruik gestelde versie in Azure Database for PostgreSQL. Houd echter rekening met de volgende beperkingen na de pensionerings datum voor elke PostgreSQL-database versie:
- Aangezien de community geen verdere oplossingen voor problemen of beveiligingsfixes uitbrengt, zal Azure Database for PostgreSQL de buiten gebruik gestelde data base-engine niet voor eventuele fouten of beveiliging bijwerken of anderszins beveiligings maatregelen treffen met betrekking tot de buiten gebruik gestelde data base-engine. U kunt beveiligings problemen of andere problemen ondervinden als gevolg hiervan. Azure blijft echter periodiek onderhoud en patches uitvoeren voor de host, het besturings systeem, containers en andere service-gerelateerde onderdelen.
- Als er mogelijk ondersteunings problemen optreden met betrekking tot de PostgreSQL-data base, kunnen we u mogelijk geen ondersteuning bieden voor u. In dergelijke gevallen moet u uw data base upgraden om u te helpen bij het verlenen van ondersteuning.
- U kunt geen nieuwe database servers maken voor de buiten gebruik gestelde versie. U kunt echter herstel naar een bepaald tijdstip uitvoeren en voor uw bestaande servers Lees replica's maken...
- Nieuwe service functies die zijn ontwikkeld door Azure Database for PostgreSQL, zijn mogelijk alleen beschikbaar voor ondersteunde database server versies.
- Beschik baarheid van de uptime geldt alleen voor het Azure Database for PostgreSQL van problemen met de service en niet voor uitval tijd die wordt veroorzaakt door fouten met betrekking tot de data base-engine.  
- In het uitzonderlijke geval van een ernstige bedreiging van de service die wordt veroorzaakt door het beveiligings probleem van de PostgreSQL-data base-engine die is geïdentificeerd in de ingecheckte database versie, kan Azure ervoor kiezen uw database server te stoppen om de service te beveiligen. In dat geval wordt u gewaarschuwd om de server te upgraden voordat u de server online brengt.

## <a name="postgresql-version-syntax"></a>Syntaxis van de PostgreSQL-versie
Vóór PostgreSQL versie 10 wordt het [postgresql-versie beleid](https://www.postgresql.org/support/versioning/) beschouwd als een _primaire versie_ -upgrade, zodat het eerste _of_ tweede nummer toeneemt. Bijvoorbeeld: 9,5 tot 9,6 werd beschouwd als een _primaire_ versie-upgrade. Vanaf versie 10 wordt alleen een wijziging in het eerste getal beschouwd als een primaire versie-upgrade. Een voor beeld: 10,0 tot 10,1 is een _kleine_ release-upgrade. Versie 10 tot 11 is een _primaire_ versie-upgrade.

## <a name="next-steps"></a>Volgende stappen
- Zie Azure Database for PostgreSQL- [ondersteunde versies](./concepts-supported-versions.md) van één server
- Zie Azure Database for PostgreSQL-flexibele server (preview) [ondersteunde versies](flexible-server/concepts-supported-versions.md)
- Zie voor meer informatie over het uitvoeren van belang rijke versie-upgrades de documentatie over [belang rijke versie-upgrades](how-to-upgrade-using-dump-and-restore.md) .
- Zie [het document extensies](concepts-extensions.md)voor meer informatie over ondersteunde postgresql-uitbrei dingen.
