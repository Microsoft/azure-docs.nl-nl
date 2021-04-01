---
title: Wat is Azure Database for PostgreSQL?
description: Dit biedt een overzicht van de relationele databaseservice Azure Database for PostgreSQL in de context van flexibele servers.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92491322"
---
# <a name="what-is-azure-database-for-postgresql"></a>Wat is Azure Database for PostgreSQL?

Azure Database for PostgreSQL is een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de [PostgreSQL Community Edition](https://www.postgresql.org/)-database-engine (beschikbaar onder de GPLv2-licentie). Azure Database for PostgreSQL heeft het volgende te bieden:

- Ingebouwde hoge beschikbaarheid.
- Gegevensbescherming met behulp van automatische back-ups en herstel naar een tijdstip tot 35 dagen geleden.
- Automatisch onderhoud voor onderliggende hardware, besturingssysteem en database-engine om de service veilig en up-to-date te houden.
- Voorspelbare prestaties, tegen all-inclusive prijzen op basis van betalen per gebruik.
- Elastisch schalen binnen enkele seconden.
- Beveiliging van ondernemingskwaliteit en toonaangevende compliance voor het beschermen van gevoelige gegevens, zowel in rust als in beweging.
- Bewaking en automatisering om het beheer en de bewaking voor grootschalige implementaties te vereenvoudigen.
- Toonaangevende ondersteuningservaring.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure Database for PostgreSQL":::

Deze mogelijkheden vereisen nauwelijks beheer, en worden alle zonder extra kosten geleverd. Dankzij deze mogelijkheden kunt u zich richten op het sneller ontwikkelen en op de markt brengen van toepassingen, in plaats van kostbare tijd en middelen in te zetten voor het beheer van virtuele machines en infrastructuur. U kunt bovendien de open-source hulpprogramma's en het platform van uw keuze blijven gebruiken om uw toepassing verder te ontwikkelen, en deze te leveren met de snelheid en efficiëntie die uw bedrijf vereist zonder dat u nieuwe vaardigheden hoeft te leren.

## <a name="deployment-models"></a>Implementatiemodellen

Azure Database for PostgreSQL op basis van de PostgreSQL Community Edition is beschikbaar in drie implementatiemodi:

- Single Server
- Flexible Server (preview)
- Hyperscale (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - één server

Azure Database for PostgreSQL Single Server is een volledig beheerde databaseservice met minimale vereisten voor database-aanpassingen. Het Single Server-platform is ontworpen voor het verwerken van de meeste databasebeheerfuncties, zoals het toepassen van patches, back-ups, hoge beschikbaarheid, beveiliging met minimale gebruikersconfiguratie en beheer. De architectuur is geoptimaliseerd voor ingebouwde hoge beschikbaarheid met een beschikbaarheid van 99,99% in één beschikbaarheidszone. Het ondersteunt de communityversie van PostgreSQL 9.5, 9.6, 10 en 11. De service is momenteel algemeen beschikbaar in allerlei [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/).

De implementatieoptie Individuele server biedt de volgende drie prijscategorieën: Basic, Algemeen gebruik en Geoptimaliseerd voor geheugen. Elke categorie biedt verschillende resources ter ondersteuning van de workloads van uw databases. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt op het moment dat u ze nodig hebt. Zie [Prijscategorieën](./concepts-pricing-tiers.md) voor meer details.

Eén server is het meest geschikt voor cloudtoepassingen die zijn ontworpen voor de verwerking van automatische patches, waarbij geen nauwkeurige controle op het patchschema en aangepaste configuratie-instellingen voor PostgreSQL nodig zijn.

Zie [overzicht van één server](./overview-single-server.md)voor een gedetailleerd overzicht van de implementatiemodus met één server.

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL - Flexible server (preview)

Azure Database for PostgreSQL Flexible Server is een volledig beheerde databaseservice die is ontworpen om nauwkeurige controle en flexibiliteit te bieden als het gaat om databasebeheerfuncties en configuratie-instellingen. Over het algemeen biedt de service meer flexibiliteit en aanpassingen op basis van de gebruikersvereisten. Met de flexibele-serverarchitectuur kunnen gebruikers kiezen voor hoge beschikbaarheid binnen één beschikbaarheidszone en tussen meerdere beschikbaarheidszones. Flexibele servers bieden betere besturingselementen voor kostenoptimalisatie, met de mogelijkheid om de server en burstable compute-laag te stoppen of te starten, wat ideaal is voor workloads die niet voortdurend volledige rekencapaciteit nodig hebben. De service ondersteunt momenteel de community-versie van PostgreSQL 11 en 12, met plannen om binnenkort nieuwere versies toe te voegen. De service is momenteel beschikbaar openbare preview, en is beschikbaar in allerlei Azure-regio's.

Flexibele servers zijn het best geschikt voor

- Ontwikkeling van toepassingen die betere controle en aanpassingen vereisen.
- Kostenoptimalisatiebeheer met de mogelijkheid om de server te stoppen/starten.
- Zone-redundante hoge beschikbaarheid
- Beheerde onderhoudsvensters
  
Zie [flexibele-serveroverzicht](./flexible-server/overview.md) voor een gedetailleerd overzicht van de implementatiemodus met flexibele servers.

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – Hyperscale (Citus)

De opties Hyperscale (Citus) schaalt query’s horizontaal over meerdere machines door middel van sharding. De query-engine zorgt voor query-parallellisatie van inkomende SQL-query’s over deze servers voor een snellere respons bij grote gegevenssets. Het is geschikt voor toepassingen die een grotere schaal en betere prestaties vereisen, meestal workloads die de 100 GB benaderen of overschrijden.

De implementatieoptie Hyperscale (Citus) levert het volgende:

- Horizontaal schalen over meerdere machines door middel van sharding
- Query-parallellisatie over deze servers voor een snellere respons bij grote gegevenssets
- Uitstekende ondersteuning voor multi-tenant toepassingen, realtime operationele analyse en transactionele workloads met hoge doorvoer
  
Toepassingen die zijn gebouwd voor PostgreSQL kunnen gedistribueerde query's uitvoeren op Hyperscale (Citus) met standaard [verbindingsbibliotheken](./concepts-connection-libraries.md) en minimale wijzigingen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de drie implementatiemodi voor Azure Database for PostgreSQL en het kiezen van de juiste opties op basis van uw behoeften.

- [Single Server](./overview-single-server.md)
- [Flexible Server](./flexible-server/overview.md)
- Hyperscale (Citus)