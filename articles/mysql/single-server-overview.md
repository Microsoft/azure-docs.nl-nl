---
title: Overzicht - Azure Database for MySQL Single Server
description: Meer informatie over de Azure Database for MySQL Single Server, een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: b0f8c5806ba22708db6dc537d391c1f1b1a183ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92543811"
---
# <a name="azure-database-for-mysql-single-server"></a>Azure Database for MySQL Single Server

[Azure Database for MySQL](overview.md) op basis van de MySQL Community Edition, is beschikbaar in twee implementatiemodi:
- Single Server 
- Flexible Server (Preview)

In dit artikel vindt u een overzicht en inleiding tot kernconcepten van het implementatiemodel voor één server. Zie [Overzicht van flexibele servers](flexible-server/index.yml) voor meer informatie over de flexibele server-implementatiemodus. Zie [De juiste optie kiezen voor de MySQL-server in Azure](select-right-deployment-type.md) voor informatie over hoe u kunt bepalen welke implementatieoptie geschikt is voor uw werkbelasting.

## <a name="overview"></a>Overzicht

Single Server is een volledig beheerde databaseservice met minimale vereisten voor aanpassingen van de database. Het Single Server-platform is ontworpen voor het verwerken van de meeste databasebeheerfuncties, zoals het toepassen van patches, back-ups, hoge beschikbaarheid, beveiliging met minimale gebruikersconfiguratie en beheer. De architectuur is geoptimaliseerd voor een beschikbaarheid van 99,99% in één beschikbaarheidszone. Het ondersteunt de Community-versie van MySQL 5.6, 5.7 en 8.0. De service is momenteel algemeen beschikbaar in allerlei [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/). 

Eén server is het meest geschikt voor cloudtoepassingen die zijn ontworpen voor de verwerking van automatische patches, waarbij geen nauwkeurige controle op het patchschema en aangepaste configuratie-instellingen voor MySQL nodig zijn. 

## <a name="high-availability"></a>Hoge beschikbaarheid

Het implementatiemodel voor één server is geoptimaliseerd voor ingebouwde hoge beschikbaarheid en flexibiliteit tegen lagere kosten. In de architectuur zijn rekenkracht en opslag van elkaar gescheiden. De database-engine wordt uitgevoerd in een eigen rekencontainer, terwijl gegevensbestanden zich in Azure Storage bevinden. In de opslag worden drie lokaal redundante synchrone kopieën van de databasebestanden onderhouden, zodat duurzaamheid van gegevens behouden blijft. 

Tijdens geplande of niet-geplande gebeurtenissen, als de server uitvalt, behoudt de service hoge beschikbaarheid van de servers met behulp van de volgende geautomatiseerde procedure:

1. Er wordt een nieuwe rekencontainer ingericht
2. De opslag met gegevensbestanden wordt toegewezen aan de nieuwe container 
3. De MySQL-database-engine wordt online gebracht op de nieuwe rekencontainer
4. De gatewayservice zorgt voor een transparante failover waarbij geen wijzigingen aan de kant van de toepassing nodig zijn. 
  
Een failover duurt meestal tussen de 60 en 120 seconden. Het systeemeigen cloudontwerp van de cloud van de service voor één server biedt ondersteuning voor een beschikbaarheid van 99,99%, waardoor de kosten voor passieve dynamische stand-by worden vermeden.

De toonaangevende serviceovereenkomst (SLA) van Azure met 99,99% beschikbaarheid dankzij een wereldwijd netwerk van door Microsoft beheerde datacenters, zorgt ervoor dat uw toepassingen continu (24 uur per dag, 7 dagen per week) beschikbaar zijn.

:::image type="content" source="media/single-server-overview/1-singleserverarchitecture.png" alt-text="Conceptdiagram van de architectuur van een Azure Database for MySQL Single Server"::: 

## <a name="automated-patching"></a>Automatisch patchen 

De service voert automatische patches uit van de onderliggende hardware, het besturingssysteem en de database-engine. De patches omvatten beveiligings- en software-updates. Voor de MySQL-engine worden kleine versie-upgrades automatisch uitgevoerd. Deze zijn onderdeel van de patchrelease. Gebruikers hoeven zelf geen actie te ondernemen en er zijn geen configuratie-instellingen vereist voor patches. De frequentie waarbij patches worden uitgevoerd, wordt beheerd door de service op basis van de ernst van de nettolading. Over het algemeen volgt de service de maandelijkse releaseplanning, als onderdeel van de continue integratie en releases. Gebruikers kunnen zich abonneren op [meldingen voor gepland onderhoud](concepts-monitoring.md). Zij ontvangen dan 72 uur voorafgaand aan het geplande onderhoud een melding.

## <a name="automatic-backups"></a>Automatische back-ups

Met de service voor één server worden automatisch serverback-ups gemaakt en opgeslagen in een door de gebruiker geconfigureerde lokaal of geografisch redundante opslag. Back-ups kunnen worden gebruikt om uw server naar een bepaald tijdstip binnen de retentieperiode van de back-up te herstellen. De standaardretentieperiode voor back-ups is zeven dagen. De retentie kan desgewenst op maximaal 35 dagen worden ingesteld. Alle back-ups worden versleuteld met AES 256-bits versleuteling. Raadpleeg [Back-ups](concepts-backup.md) voor meer informatie.

## <a name="adjust-performance-and-scale-within-seconds"></a>Binnen een paar seconden prestaties en schaal aanpassen

De service voor één server is beschikbaar in drie SKU-lagen: Basic, Algemeen gebruik en Geoptimaliseerd voor geheugen. De Basis-laag is het meest geschikt voor low-budget ontwikkeling en workloads met weinig gelijktijdigheid. Algemeen gebruik en Geoptimaliseerd voor geheugen zijn beter geschikt voor productieworkloads waarvoor hoge gelijktijdigheid, schaalbaarheid en voorspelbare prestaties nodig zijn. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. De opslagschaal is online en ondersteunt automatische groei van opslag. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u gebruikt. Zie [Prijscategorieën](./concepts-pricing-tiers.md) voor meer details.

## <a name="enterprise-grade-security-compliance-and-governance"></a>Beveiliging, naleving en beheer van bedrijfskwaliteit

De service voor één server maakt gebruik van de door FIPS 140-2 gevalideerde cryptografische module voor opslagversleuteling van niet-actieve gegevens. Gegevens, inclusief back-ups en tijdelijke bestanden die worden gemaakt tijdens het uitvoeren van query's, worden versleuteld. De service maakt gebruikt van de AES 256-bits versleuteling die deel uitmaakt van Azure-opslagversleuteling. De sleutels kunnen door het systeem worden beheerd (standaardinstelling) of door de [klant worden beheerd](concepts-data-encryption-mysql.md). De service versleutelt actieve gegevens met SSL/TLS (Transport Layer Security) dat standaard wordt afgedwongen. De service ondersteunt TLS-versies 1.2, 1.1 en 1.0 en biedt de mogelijkheid om een [minimale TLS-versie](concepts-ssl-connection-security.md) af te dwingen. 

De service staat persoonlijke toegang tot de servers toe met behulp van [persoonlijke koppeling](concepts-data-access-security-private-link.md) en biedt een functie voor [Geavanceerde beveiliging tegen bedreigingen](concepts-data-access-and-security-threat-protection.md). Geavanceerde beveiliging tegen bedreigingen detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of aanvallen op databases.

Naast systeemeigen verificatie ondersteunt de service voor één server [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)-verificatie. Azure AD-verificatie is een mechanisme voor het maken van verbinding met de MySQL-servers met behulp van id's die worden gedefinieerd en beheerd in Azure AD. Met Azure AD-verificatie kunt u de gebruikers-id's van databases en andere Azure-services beheren op een centrale locatie, waardoor toegangsbeheer wordt vereenvoudigd en gecentraliseerd.

[Controlelogboekregistratie](concepts-audit-logs.md) is beschikbaar om alle activiteiten op databaseniveau bij te houden. 

De service voor één server is compatibel met alle toonaangevende certificeringen, zoals FedRAMP, HIPAA en PCI DSS. Bezoek het [Vertrouwenscentrum van Azure](https://www.microsoft.com/trustcenter/security) voor informatie over de beveiliging van het Azure-platform. 

Zie het [beveiligingsoverzicht](concepts-security.md) voor meer informatie over de beveiligingsfuncties van Azure Database for MySQL.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen

De service voor één server is uitgerust met ingebouwde functies voor prestatiebewaking en waarschuwingen. Alle metrische gegevens van Azure hebben een frequentie van één minuut, en elke metriek bevat een geschiedenis van 30 dagen. U kunt waarschuwingen configureren voor de metrische gegevens. De service staat het configureren van logboeken voor langzame query's toe en wordt geleverd met een gedifferentieerde [Query Store](concepts-query-store.md)-functie. Query Store vereenvoudigt het oplossen van problemen met prestaties, omdat u snel de langstlopende en meest tijdrovende query's kunt vinden. Met deze hulpprogramma's kunt u uw workloads snel optimaliseren en uw server configureren voor de beste prestaties. Zie [Bewaking](concepts-monitoring.md) voor meer informatie.

## <a name="migration"></a>Migratie

De service voert de communityversie van MySQL uit. Dit biedt volledige toepassingscompatibiliteit en vereist minimale herstructureringskosten voor het migreren van bestaande toepassingen die zijn ontwikkeld op de MySQL-engine naar een service met één server. De migratie naar de enkele server kan worden uitgevoerd met behulp van een van de volgende opties:

- **Dumpen en herstellen**: voor offline migraties, waarbij gebruikers zich enige downtime kunnen veroorloven, kan dumpen en herstellen met behulp van communityhulpprogramma's, zoals mysqldump/mydumper, de snelste manier zijn voor migratie. Zie [Migreren met behulp van dumpen en herstellen](concepts-migrate-dump-restore.md) voor meer informatie. 
- **Azure Database Migration Service**: voor naadloze en vereenvoudigde migraties naar één server met minimale downtime kan [Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-online.md) worden gebruikt. 
- **Replicatie van binnenkomende gegevens**: voor minimale downtime-migraties kan ook de replicatie van binnenkomende gegevens, die afhankelijk is van replicatie op basis van binlog, worden gebruikt. Replicatie van binnenkomende gegevens heeft de voorkeur voor minimale downtime-migraties van praktijkgerichte experts die meer controle over de migratie willen. Zie [replicatie van binnenkomende gegevens](concepts-data-in-replication.md) voor meer informatie.

## <a name="contacts"></a>Contactpersonen
Voor eventuele vragen of suggesties over het werken met Azure Database for MySQL stuurt u een e-mailbericht naar het Azure Database for MySQL-team ([@AskAzure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Dit e-mailadres is geen alias voor technische ondersteuning.

Overweeg ook de volgende aanspreekpunten, indien van toepassing:

- Als u contact wilt opnemen met Azure-ondersteuning, kunt u een [ticket indienen vanuit Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Volgende stappen
Nu u een inleiding tot de implementatiemodus voor één server van Azure Database for MySQL hebt gelezen, bent u klaar voor het volgende:

- Uw eerste server maken. 
  - [Een Azure-database voor een MySQL-server maken met behulp van Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
  - [Een Azure-database voor een MySQL-server maken met behulp van Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)
  - [Azure CLI-voorbeelden voor Azure Database for MySQL](sample-scripts-azure-cli.md)

- Bouw uw eerste app in de taal van uw voorkeur:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Go](./connect-go.md)
