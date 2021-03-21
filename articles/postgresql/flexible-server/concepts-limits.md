---
title: Limieten-Azure Database for PostgreSQL flexibele server
description: In dit artikel worden de beperkingen in Azure Database for PostgreSQL flexibele server beschreven, zoals het aantal opties voor verbinding en opslag engine.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9039bbf006d5e5a677247771346a3a6b43781da2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594934"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Limieten in Azure Database for PostgreSQL flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In de volgende secties worden de capaciteits-en functionele limieten in de database service beschreven. Als u meer wilt weten over resource-lagen (compute, geheugen, opslag), raadpleegt u het artikel [Compute and Storage](concepts-compute-storage.md) .

## <a name="maximum-connections"></a>Maximum aantal verbindingen

Het maximum aantal verbindingen per prijs categorie en vCores worden hieronder weer gegeven. Het Azure-systeem vereist drie verbindingen om de Azure Database for PostgreSQL flexibele server te bewaken.

| SKU-naam             | vCores | Geheugen grootte | Maximum aantal verbindingen | Maximum aantal gebruikers verbindingen |
|----------------------|--------|-------------|-----------------|----------------------|
| **Bebreekbaar**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **Algemeen doel**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **Geoptimaliseerd voor geheugen** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

Wanneer verbindingen de limiet overschrijden, wordt mogelijk de volgende fout weer gegeven:
> Onherstelbare fout: er zijn al te veel clients

> [!IMPORTANT]
> Voor de beste ervaring raden we u aan een Pooler voor verbindingen te gebruiken zoals PgBouncer om verbindingen efficiënt te beheren.

Een PostgreSQL-verbinding, zelfs inactief, kan ongeveer 10 MB aan geheugen in beslag nemen. Daarnaast kost het maken van nieuwe verbindingen tijd. De meeste toepassingen aanvragen een groot aantal korte, langdurige verbindingen, waardoor deze situatie wordt beperkt. Het resultaat is minder beschik bare resources voor uw werkelijke workload, waardoor de prestaties afnemen. Pooling van verbindingen kan worden gebruikt om niet-actieve verbindingen te verlagen en bestaande verbindingen opnieuw te gebruiken. Ga voor meer informatie naar onze [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Functionele beperkingen

### <a name="scale-operations"></a>Schaal bewerkingen

- Voor het schalen van de server opslag moet de server opnieuw worden opgestart.
- Server opslag kan alleen worden geschaald in 2x-stappen, Zie [Compute en Storage](concepts-compute-storage.md) voor meer informatie.
- Het verminderen van de grootte van de server opslag wordt momenteel niet ondersteund.

### <a name="server-version-upgrades"></a>Server versie-upgrades

- Automatische migratie tussen de primaire data base-engine versies wordt momenteel niet ondersteund. Als u wilt upgraden naar de volgende primaire versie, neemt u een [dump op en herstelt](../howto-migrate-using-dump-and-restore.md) u deze op een server die is gemaakt met de nieuwe engine versie.

### <a name="storage"></a>Storage

- Na de configuratie kan de opslag grootte niet worden verminderd. U moet een nieuwe server maken met de gewenste opslag grootte, hand matige [dump uitvoeren en](../howto-migrate-using-dump-and-restore.md) uw data base (s) naar de nieuwe server migreren.
- De functie voor het automatisch uitbreiden van opslag is momenteel niet beschikbaar. Bewaak het gebruik en verg root de opslag naar een hogere grootte. 
- Wanneer het gebruik van de opslag 95% bereikt of als de beschik bare capaciteit minder dan 5 GiB is, wordt de server automatisch overgeschakeld naar de **alleen-lezen modus** om fouten te voor komen die zijn gekoppeld aan schijf-Full-situaties. 
- We raden u aan waarschuwings regels in te stellen voor `storage used` of `storage percent` wanneer ze bepaalde drempel waarden overschrijden zodat u proactief acties kunt ondernemen, zoals het verg Roten van de opslag grootte. U kunt bijvoorbeeld een waarschuwing instellen als het opslag percentage 80% gebruik overschrijdt.
  
### <a name="networking"></a>Netwerken

- Het verplaatsen van en naar het VNET wordt momenteel niet ondersteund.
- Het combi neren van open bare toegang met implementatie binnen een VNET wordt momenteel niet ondersteund.
- Firewall regels worden niet ondersteund op VNET. in plaats daarvan kunnen netwerk beveiligings groepen worden gebruikt.
- Open bare toegang database servers kunnen verbinding maken met openbaar Internet, bijvoorbeeld via `postgres_fdw` , en deze toegang kan niet worden beperkt. Op VNET gebaseerde servers kunnen beperkte uitgaande toegang hebben met behulp van netwerk beveiligings groepen.

### <a name="high-availability-ha"></a>Hoge Beschik baarheid (HA)

- Zone-Redundant HA wordt momenteel niet ondersteund voor breek bare servers.
- Het IP-adres van de database server wordt gewijzigd wanneer een failover van de server naar de stand-by-modus wordt uitgevoerd. Zorg ervoor dat u de DNS-record gebruikt in plaats van het IP-adres van de server.
- Als logische replicatie is geconfigureerd met een met HA geconfigureerde flexibele server, in het geval van een failover naar de stand-by-server, worden de logische replicatie sleuven niet naar de stand-by-server gekopieerd. 
- Raadpleeg de pagina [concepten-ha documentation](concepts-high-availability.md) (Engelstalig) voor meer informatie over zone-redundante ha, met inbegrip van de beperkingen.

### <a name="availability-zones"></a>Beschikbaarheidszones

- Het hand matig verplaatsen van servers naar een andere beschikbaarheids zone wordt momenteel niet ondersteund.
- De beschikbaarheids zone van de HA standby-server kan niet hand matig worden geconfigureerd.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Post gres-engine,-extensies en-PgBouncer

- Post gres 10 en oudere versies worden niet ondersteund. U kunt het beste de optie voor [één server](../overview-single-server.md) gebruiken als u oudere post gres-versies nodig hebt.
- Verlengings ondersteuning is momenteel beperkt tot de post gres- `contrib` extensies.
- De ingebouwde PgBouncer-verbindings groep is momenteel niet beschikbaar voor database servers binnen een VNET, of voor breek bare servers.

### <a name="stopstart-operation"></a>Stoppen/starten-bewerking

- De server kan niet langer dan zeven dagen worden gestopt.

### <a name="scheduled-maintenance"></a>Gepland onderhoud

- Als u het onderhouds venster minder dan vijf dagen vóór een al geplande upgrade wijzigt, heeft dit geen invloed op die upgrade. De wijzigingen worden pas van kracht met het volgende geplande onderhoud.

### <a name="backing-up-a-server"></a>Back-up maken van een server

- Back-ups worden beheerd door het systeem. er is momenteel geen manier om deze back-ups hand matig uit te voeren. We raden u aan `pg_dump` in plaats daarvan te gebruiken.
- Back-ups zijn altijd volledige back-ups op basis van moment opnamen (geen differentiële back-ups), die mogelijk leiden tot een hogere back-upopslag. Houd er rekening mee dat transactie Logboeken (write-Ahead logboeken-WAL) gescheiden zijn van de volledige/differentiële back-ups en voortdurend worden gearchiveerd.

### <a name="restoring-a-server"></a>Een server herstellen

- Wanneer u de functie voor het herstellen van een punt gebruikt, wordt de nieuwe server gemaakt met dezelfde Compute-en opslag configuraties als de server waarop deze is gebaseerd.
- Op VNET gebaseerde database servers worden teruggezet in hetzelfde VNET wanneer u herstelt vanuit een back-up.
- De nieuwe server die tijdens het herstellen is gemaakt, bevat niet de firewall regels die op de oorspronkelijke server aanwezig waren. De firewall regels moeten afzonderlijk worden gemaakt voor de nieuwe server.
- Het herstellen van een verwijderde server wordt niet ondersteund.
- Het terugzetten van meerdere regio's wordt niet ondersteund.

### <a name="other-features"></a>Andere functies

* Azure AD-verificatie wordt nog niet ondersteund. U kunt het beste de optie voor [één server](../overview-single-server.md) gebruiken als u Azure AD-verificatie nodig hebt.
* Het lezen van replica's wordt nog niet ondersteund. U kunt het beste de optie voor [één server](../overview-single-server.md) gebruiken als u lees replica's nodig hebt.
* Het verplaatsen van resources naar een ander abonnement wordt niet ondersteund. 


## <a name="next-steps"></a>Volgende stappen

- Inzicht [in wat er beschikbaar is voor de berekenings-en opslag opties](concepts-compute-storage.md)
- Meer informatie over [ondersteunde versies van de postgresql-data base](concepts-supported-versions.md)
- Lees [hoe u een back-up maakt en een server herstelt in azure database for PostgreSQL met behulp van de Azure Portal](how-to-restore-server-portal.md)
