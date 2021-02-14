---
title: Azure Database for PostgreSQL Single Server
description: Biedt een overzicht van Azure Database for PostgreSQL Single Server.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 18d944cd2b3bfde1a0ee5fc53e83e4016b11b01f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379213"
---
# <a name="azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL Single Server

[Azure Database for PostgreSQL](./overview.md) op basis van de PostgreSQL Community Edition is beschikbaar in drie implementatiemodi:

- Single Server
- Flexible Server (preview)
- Hyperscale (Citus)

In dit artikel vindt u een overzicht en inleiding tot kernconcepten van het implementatiemodel voor één server. Zie [overzicht van flexibele servers](./flexible-server/overview.md) en Hyperscale (Citus) voor meer informatie over de implementatiemodus voor flexibele servers.

## <a name="overview"></a>Overzicht

Single Server is een volledig beheerde databaseservice met minimale vereisten voor aanpassingen van de database. Het Single Server-platform is ontworpen voor het verwerken van de meeste databasebeheerfuncties, zoals het toepassen van patches, back-ups, hoge beschikbaarheid, beveiliging met minimale gebruikersconfiguratie en beheer. De architectuur is geoptimaliseerd voor een beschikbaarheid van 99,99% in één beschikbaarheidszone. Het ondersteunt de communityversie van PostgreSQL 9.5, 9.6, 10 en 11. De service is momenteel algemeen beschikbaar in allerlei [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/).

Eén server is het meest geschikt voor cloudtoepassingen die zijn ontworpen voor de verwerking van automatische patches, waarbij geen nauwkeurige controle op het patchschema en aangepaste configuratie-instellingen voor PostgreSQL nodig zijn.

## <a name="high-availability"></a>Hoge beschikbaarheid

Het implementatiemodel voor één server is geoptimaliseerd voor ingebouwde hoge beschikbaarheid en flexibiliteit tegen lagere kosten. In de architectuur zijn rekenkracht en opslag van elkaar gescheiden. De database-engine wordt uitgevoerd in een eigen rekencontainer, en gegevensbestanden bevinden zich in Azure Storage. In de opslag worden drie lokaal redundante synchrone kopieën van de databasebestanden onderhouden, zodat duurzaamheid van gegevens wordt gegarandeerd.

Tijdens geplande of niet-geplande gebeurtenissen, als de server uitvalt, behoudt de service hoge beschikbaarheid van de servers met behulp van de volgende geautomatiseerde procedure:

1. Er wordt een nieuwe rekencontainer ingericht.
2. De opslag met gegevensbestanden wordt toegewezen aan de nieuwe container.
3. De PostgreSQL-database-engine wordt online gebracht op de nieuwe rekencontainer.
4. De gateway service zorgt voor een transparante failover waarbij geen wijzigingen aan de kant van de toepassing nodig zijn.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Azure Database for PostgreSQL Single Server":::

Een failover duurt meestal tussen de 60 en 120 seconden. Het systeemeigen cloudontwerp van de cloud van de Single Server-service biedt ondersteuning voor een beschikbaarheid van 99,99%, waardoor de kosten voor de passieve dynamische stand-by worden vermeden.

De toonaangevende serviceovereenkomst (SLA) van Azure met 99,99% beschikbaarheid dankzij een wereldwijd netwerk van door Microsoft beheerde datacenters, zorgt ervoor dat uw toepassingen continu (24 uur per dag, 7 dagen per week) beschikbaar blijven.

## <a name="automated-patching"></a>Automatisch patchen

De service voert automatische patches uit van de onderliggende hardware, het besturingssysteem en de database-engine. De patches omvatten beveiligings- en software-updates. Voor de PostgreSQL-engine worden kleine versie-upgrades automatisch uitgevoerd. Deze zijn onderdeel van de patchcyclus. Gebruikers hoeven zelf geen actie te ondernemen en er zijn geen configuratie-instellingen vereist voor patches. De frequentie waarbij patches worden uitgevoerd, wordt beheerd door de service op basis van de ernst van de nettolading. Over het algemeen volgt de service de maandelijkse releaseplanning, als onderdeel van de continue integratie en releases. Gebruikers kunnen zich abonneren op [meldingen voor gepland onderhoud](). Zij ontvangen dan 72 uur voorafgaand aan het geplande onderhoud een melding.

## <a name="automatic-backups"></a>Automatische back-ups

Met de service voor één server worden automatisch serverback-ups gemaakt en opgeslagen in een door de gebruiker geconfigureerde lokaal redundante of geografisch redundante opslag. Back-ups kunnen worden gebruikt om uw server naar een bepaald tijdstip binnen de retentieperiode van de back-up te herstellen. De standaardretentieperiode voor back-ups is zeven dagen. De retentie kan desgewenst op maximaal 35 dagen worden ingesteld. Alle back-ups worden versleuteld met AES 256-bits versleuteling. Zie [Back-ups](./concepts-backup.md) voor meer informatie.

## <a name="adjust-performance-and-scale-within-seconds"></a>Binnen een paar seconden prestaties en schaal aanpassen

De service voor één server is beschikbaar in drie SKU-lagen: Basic, Algemeen gebruik en Geoptimaliseerd voor geheugen. De Basic-laag is het meest geschikt voor goedkope ontwikkeling en workloads met weinig gelijktijdigheid. Algemeen gebruik en Geoptimaliseerd voor geheugen zijn beter geschikt voor productieworkloads waarvoor hoge gelijktijdigheid, schaalbaarheid en voorspelbare prestaties nodig zijn. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. De opslagschaal is online en ondersteunt automatische groei van opslag. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u gebruikt. Zie [Prijscategorieën]() voor meer details.

## <a name="enterprise-grade-security-compliance-and-governance"></a>Beveiliging, naleving en beheer van zakelijk niveau

De service voor één server maakt gebruik van de door FIPS 140-2 gevalideerde cryptografische module voor opslagversleuteling van niet-actieve gegevens. Gegevens, inclusief back-ups en tijdelijke bestanden die worden gemaakt tijdens het uitvoeren van query's, worden versleuteld. De service maakt gebruikt van de AES 256-bits versleuteling die deel uitmaakt van Azure Storage-versleuteling. De sleutels kunnen door het systeem worden beheerd (standaardinstelling) of door de [klant worden beheerd](). De service versleutelt actieve gegevens met SSL/TLS (Transport Layer Security) dat standaard wordt afgedwongen. De service ondersteunt TLS-versies 1.2, 1.1 en 1.0 en biedt de mogelijkheid om een [minimale TLS-versie]() af te dwingen.

De service staat persoonlijke toegang tot de servers toe met behulp van Private Link en biedt een geavanceerde functie voor beveiliging tegen bedreigingen. Advanced Threat Protection detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of aanvallen op databases.

Naast systeemeigen verificatie ondersteunt de service voor één server Azure Active Directory-verificatie. Azure AD-verificatie is een mechanisme voor het maken van verbinding met de PostgreSQL-servers met behulp van id's die worden gedefinieerd en beheerd in Azure AD. Met Azure AD-verificatie kunt u de gebruikers-id's van databases en andere Azure-services beheren op een centrale locatie, waardoor toegangsbeheer wordt vereenvoudigd en gecentraliseerd.

[Controlelogboekregistratie]() (preview-versie) is beschikbaar om alle activiteiten op databaseniveau bij te houden.

De service met één server voldoet aan alle toonaangevende certificeringen, zoals FedRAMP, HIPAA, PCI DSS. Bezoek het [Vertrouwenscentrum van Azure]() voor informatie over de beveiliging van het Azure-platform.

Zie het [beveiligingsoverzicht]() voor meer informatie over de beveiligingsfuncties van Azure Database for PostgreSQL.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen

De service voor één server is uitgerust met ingebouwde functies voor prestatiebewaking en waarschuwingen. Alle metrische gegevens van Azure hebben een frequentie van één minuut, en elke metriek bevat een geschiedenis van 30 dagen. U kunt waarschuwingen configureren voor de metrische gegevens. De service staat het configureren van logboeken voor langzame query's toe en wordt geleverd met een gedifferentieerde [Query Store](./concepts-query-store.md)-functie. Query Store vereenvoudigt het oplossen van problemen met prestaties, omdat u snel de langste en meest tijdrovende query's kunt vinden. Met deze hulpprogramma's kunt u uw workloads snel optimaliseren en uw server configureren voor de beste prestaties. Zie [Bewaking](./concepts-monitoring.md) voor meer informatie.

## <a name="migration"></a>Migratie

De service voert de communityversie van PostgreSQL uit. Dit biedt volledige toepassingscompatibiliteit en vereist minimale herstructureringskosten voor het migreren van bestaande toepassingen die zijn ontwikkeld op PostgreSQL-engine naar een service met één server. De migratie naar de enkele server kan worden uitgevoerd met behulp van een van de volgende opties:

- **Dumpen en herstellen**: voor offline migraties, waarbij gebruikers zich enige downtime kunnen veroorloven, kunnen dumpen en herstellen met behulp van communityhulpprogramma's, zoals Pg_dump en Pg_restore, de snelste manier zijn voor migratie. Zie [Migreren met behulp van dump en herstel](./howto-migrate-using-dump-and-restore.md) voor meer informatie.
- **Azure Database Migration Service**: voor naadloze en vereenvoudigde migraties naar één server met minimale downtime kan Azure Database Migration Service worden gebruikt. Zie [DMS via de portal](../dms/tutorial-postgresql-azure-postgresql-online-portal.md) en [DMS via CLI](../dms/tutorial-postgresql-azure-postgresql-online.md).

## <a name="contacts"></a>Contactpersonen

Voor eventuele vragen of suggesties over het werken met Azure Database for PostgreSQL stuurt u een e-mailbericht naar het Azure Database for PostgreSQL-team ([@AskAzure DB voor PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Dit e-mailadres is geen alias voor technische ondersteuning.

Overweeg ook de volgende aanspreekpunten, indien van toepassing:

- Als u contact wilt opnemen met Azure-ondersteuning, kunt u een [ticket indienen vanuit Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Volgende stappen

Nu u een inleiding tot de implementatiemodus voor één server van Azure Database for PostgreSQL hebt gelezen, bent u klaar voor het volgende:
- Uw eerste server maken.
