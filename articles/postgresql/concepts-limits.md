---
title: Limieten-Azure Database for PostgreSQL-één server
description: In dit artikel worden de beperkingen in Azure Database for PostgreSQL-één server beschreven, zoals het aantal opties voor verbinding en opslag engine.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 7cee2be3c1d7a97b87560873d6cef07ec361ee99
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605175"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limieten in Azure Database for PostgreSQL-één server
In de volgende secties worden de capaciteits-en functionele limieten in de database service beschreven. Zie het artikel over de [prijs categorieën](concepts-pricing-tiers.md) als u meer wilt weten over resource-lagen (compute, geheugen, opslag).


## <a name="maximum-connections"></a>Maximum aantal verbindingen
Het maximum aantal verbindingen per prijs categorie en vCores worden hieronder weer gegeven. Het Azure-systeem vereist vijf verbindingen om de Azure Database for PostgreSQL server te bewaken. 

|**Prijscategorie**| **vCore (s)**| **Maximum aantal verbindingen** | **Maximum aantal gebruikers verbindingen** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Algemeen gebruik| 2| 150| 145|
|Algemeen gebruik| 4| 250| 245|
|Algemeen gebruik| 8| 480| 475|
|Algemeen gebruik| 16| 950| 945|
|Algemeen gebruik| 32| 1500| 1495|
|Algemeen gebruik| 64| 1900| 1895|
|Geoptimaliseerd geheugen| 2| 300| 295|
|Geoptimaliseerd geheugen| 4| 500| 495|
|Geoptimaliseerd geheugen| 8| 960| 955|
|Geoptimaliseerd geheugen| 16| 1900| 1895|
|Geoptimaliseerd geheugen| 32| 1987| 1982|

Wanneer verbindingen de limiet overschrijden, wordt mogelijk de volgende fout weer gegeven:
> Onherstelbare fout: er zijn al te veel clients

> [!IMPORTANT]
> Voor de beste ervaring raden we u aan een Pooler voor verbindingen te gebruiken zoals pgBouncer om verbindingen efficiënt te beheren.

Een PostgreSQL-verbinding, zelfs inactief, kan ongeveer 10 MB aan geheugen in beslag nemen. Daarnaast kost het maken van nieuwe verbindingen tijd. De meeste toepassingen aanvragen een groot aantal korte, langdurige verbindingen, waardoor deze situatie wordt beperkt. Het resultaat is minder beschik bare resources voor uw werkelijke workload, waardoor de prestaties afnemen. Een verbindings groep waarmee niet-actieve verbindingen worden verminderd en bestaande verbindingen opnieuw worden gebruikt, kunt u dit voor komen. Ga voor meer informatie naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Functionele beperkingen
### <a name="scale-operations"></a>Schaal bewerkingen
- Dynamisch schalen naar en van de basis prijs categorieën wordt momenteel niet ondersteund.
- Het verminderen van de grootte van de server opslag wordt momenteel niet ondersteund.

### <a name="server-version-upgrades"></a>Server versie-upgrades
- Automatische migratie tussen de primaire data base-engine versies wordt momenteel niet ondersteund. Als u wilt upgraden naar de volgende primaire versie, neemt u een [dump op en herstelt](./howto-migrate-using-dump-and-restore.md) u deze op een server die is gemaakt met de nieuwe engine versie.

> Houd er rekening mee dat het [postgresql-versie beleid](https://www.postgresql.org/support/versioning/) als een _primaire versie_ -upgrade voor postgresql versie 10 als een verhoging van het eerste _of_ tweede nummer moet worden uitgevoerd (bijvoorbeeld 9,5 tot 9,6 werd beschouwd als een _primaire_ versie-upgrade).
> Vanaf versie 10 wordt alleen een wijziging in het eerste nummer beschouwd als een primaire versie-upgrade (bijvoorbeeld 10,0 tot 10,1 is een _secundaire_ versie-upgrade en 10 tot 11 is een _primaire_ versie-upgrade).

### <a name="vnet-service-endpoints"></a>VNeT-service-eindpunten
- Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

### <a name="restoring-a-server"></a>Een server herstellen
- Wanneer u de functie PITR gebruikt, wordt de nieuwe server gemaakt met dezelfde prijs categorie configuraties als de server waarop deze is gebaseerd.
- De nieuwe server die tijdens het herstellen is gemaakt, bevat niet de firewall regels die op de oorspronkelijke server aanwezig waren. Firewall regels moeten afzonderlijk worden ingesteld voor deze nieuwe server.
- Het herstellen van een verwijderde server wordt niet ondersteund.

### <a name="utf-8-characters-on-windows"></a>UTF-8-tekens in Windows
- In sommige scenario's worden UTF-8-tekens niet volledig ondersteund in open-source PostgreSQL in Windows, wat van invloed is op Azure Database for PostgreSQL. Raadpleeg de thread on [Bug #15476 in de postgresql-Archive](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) voor meer informatie.

### <a name="gss-error"></a>GSS-fout
Als er een fout wordt weergegeven die betrekking heeft op **GSS**, gebruikt u waarschijnlijk een nieuwere versie van een client/stuurprogramma die nog niet volledig wordt ondersteund door Azure Postgres Single Server. Deze fout heeft betrekking op [JDBC-stuurprogrammaversies 42.2.15 en 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868).
   - De update wordt naar verwachting eind november voltooid. U kunt in de tussentijd een werkende versie van het stuurprogramma gebruiken.
   - Of overweeg de GSS-aanvraag uit te scha kelen.  Gebruik een verbindingsparameter als `gssEncMode=disable`.

### <a name="storage-size-reduction"></a>Reductie van opslag grootte
De opslag grootte kan niet worden verminderd. U moet een nieuwe server maken met de gewenste opslag grootte, hand matige [dump uitvoeren en](./howto-migrate-using-dump-and-restore.md) uw data base (s) naar de nieuwe server migreren.

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over wat er beschikbaar is in elke prijs categorie](concepts-pricing-tiers.md)
- Meer informatie over [ondersteunde versies van de postgresql-data base](concepts-supported-versions.md)
- Lees [hoe u een back-up maakt en een server herstelt in azure database for PostgreSQL met behulp van de Azure Portal](howto-restore-server-portal.md)
