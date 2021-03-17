---
title: Wat is de service Azure SQL Database?
description: 'Maak kennis met SQL-Database: technische informatie en mogelijkheden van het RDBMS (Relational Database Management Systeem) van Microsoft in de cloud.'
keywords: inleiding in sql,intro in sql,wat is sql-database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: c6b1129ca4a63e9adc8bb679dce53ff728456f42
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601828"
---
# <a name="what-is-azure-sql-database"></a>Wat is Azure SQL Database?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database is een volledig beheerde PaaS-database-engine (platform as a service), waarmee de meeste databasebeheerfuncties, zoals upgrades, patches, back-ups en controle worden geautomatiseerd. Azure SQL Database wordt altijd uitgevoerd in de meest recente stabiele versie van de SQL Server-database-engine en het besturingssysteem met patches met een beschikbaarheid van 99,99%. Met de PaaS-functionaliteit die is ingebouwd in Azure SQL Database kunt u zich richten op het domeinspecifieke databasebeheer en de optimalisatieactiviteiten die essentieel zijn voor uw bedrijf.

U kunt met Azure SQL Database een maximaal beschikbare gegevensopslaglaag met hoge prestaties maken voor de apps en oplossingen in Azure. SQL Database is de juiste keuze voor verschillende moderne cloudtoepassingen, omdat het u in staat stelt zowel relationele gegevens als [niet-relationele structuren](../multi-model-features.md) te verwerken, zoals grafieken, JSON, ruimtelijk, en XML.

Azure SQL Database is gebaseerd op de meest recente stabiele versie van de [Microsoft SQL Server-databases-engine](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json). U kunt gebruikmaken van geavanceerde functies voor het verwerken van query’s, zoals [in-memory technologieën met hoge prestaties](../in-memory-oltp-overview.md) en [intelligente queryverwerking](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json). De nieuwste functionaliteit van SQL Server wordt zelfs eerst uitgebracht in Azure SQL Database en vervolgens pas in SQL Server zelf. U kunt beschikken over de nieuwste SQL Server-functionaliteit zonder overhead voor het uitvoeren van patches of upgrades en die is getest bij miljoenen databases. 

SQL Database stelt u in staat eenvoudig prestaties te definiëren en te schalen in twee verschillende aankoopmodellen: een [aankoopmodel op basis van vCore](service-tiers-vcore.md) en een [aankoopmodel op basis van DTU](service-tiers-dtu.md). SQL Database is een volledig beheerde service met ingebouwde hoge beschikbaarheid, back-ups en andere veelvoorkomende onderhoudsactiviteiten. Microsoft voert alle patches en updates voor de SQL en besturingssysteemcode uit. U hoeft de onderliggende infrastructuur niet te beheren.

Als u geen ervaring hebt met Azure SQL Database, bekijk dan de video *Overzicht van Azure SQL Database* uit onze diepgaande [Azure SQL-videoserie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

> [!TIP]
> Hoe kunnen we Azure SQL beter maken? [Doe de enquête](https://aka.ms/AzureSQLSurvey).

## <a name="deployment-models"></a>Implementatiemodellen

Azure SQL Database biedt de volgende implementatieopties voor een database:

- [Individuele database](single-database-overview.md) vertegenwoordigt een volledig beheerde, geïsoleerde database. U kunt deze optie gebruiken als u moderne cloudtoepassingen en microservices hebt waarvoor één betrouwbare gegevensbron nodig is. Een individuele database is vergelijkbaar met een [ingesloten database](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json) in de [SQL Server-database-engine](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json).
- [Elastische pool](elastic-pool-overview.md) is een verzameling [individuele databases](single-database-overview.md) met een gedeelde set resources, zoals CPU of geheugen. Individuele databases kunnen naar en uit een elastische pool worden verplaatst.

> [!IMPORTANT]
> Zie [SQL Database-functies](features-comparison.md) voor begrip van de functieverschillen tussen SQL Database en SQL Server, en de verschillen tussen verschillende Azure SQL Database-opties.

SQL Database biedt voorspelbare prestaties met meerdere resourcetypen, servicelagen, en rekenkracht. Het biedt onder andere dynamische schaalbaarheid zonder uitvaltijd, ingebouwde intelligente optimalisatie, wereldwijde schaalbaarheid en beschikbaarheid, en geavanceerde beveiligingsopties. Dankzij deze mogelijkheden kunt u zich richten op het sneller ontwikkelen en op de markt brengen van apps, in plaats van het beheer van virtuele machines en infrastructuur. SQL Database is momenteel beschikbaar in 38 datacentrums wereldwijd. U kunt uw database dus uitvoeren in een datacentrum bij u in de buurt.

## <a name="scalable-performance-and-pools"></a>Schaalbare prestaties en pools

U kunt het aantal toegewezen resources definiëren. 
- Met individuele databases is elke database geïsoleerd van andere databases, en is elke database draagbaar. Elke database heeft een eigen gegarandeerde hoeveelheid reken-, geheugen- en opslagresources. De hoeveelheid resources die aan de database is toegewezen, is toegewezen aan alleen deze database en wordt niet gedeeld met andere databases in Azure. U kunt [individuele databases omhoog of omlaag schalen](single-database-scale.md). De optie voor individuele databases biedt verschillende reken-, geheugen- en opslagresources voor verschillende behoeften. U kunt bijvoorbeeld 1 tot 80 vCores krijgen, of 32 GB tot 4 TB. De [servicelaag Hyperscale](service-tier-hyperscale.md) voor individuele databases stelt u in staat om te schalen tot 100 TB, met mogelijkheden voor snelle back-up en herstel.
- Met elastische pools kunt u resources toewijzen die worden gedeeld door alle databases in de pool. U kunt een nieuwe database maken, of de bestaande individuele databases verplaatsen naar een resourcegroep om het gebruik van resources te maximaliseren en geld te besparen. Deze optie biedt u ook de mogelijkheid om [elastische pools omhoog of omlaag te schalen](elastic-pool-scale.md).

U kunt uw eerste app bouwen in een kleine individuele database, tegen lage maandelijkse kosten, in de servicelaag Algemeen gebruik. U kunt vervolgens de servicelaag op elk gewenst moment handmatig of via een programma wijzigen in de servicelaag Bedrijfskritiek, om te voldoen aan de behoeften van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt, wanneer u ze nodig hebt.

*Dynamische schaalbaarheid* verschilt van *automatisch schalen*. Automatisch schalen vindt plaats wanneer een service automatisch wordt geschaald op basis van criteria, terwijl u met dynamische schaalbaarheid handmatig kunt schalen zonder uitvaltijd. De optie voor individuele databases biedt ondersteuning voor handmatige dynamische schaalbaarheid, maar niet voor automatisch schalen. Voor een meer automatische ervaring zou u elastische pools kunnen gebruiken. Hiermee kunnen databases resources in een pool delen op basis van afzonderlijke databasebehoeften. Een andere optie is het gebruik van scripts die kunnen helpen bij het automatiseren van schaalbaarheid voor een individuele database. Raadpleeg [PowerShell gebruiken voor het bewaken en schalen van een individuele database](scripts/monitor-and-scale-database-powershell.md) voor een voorbeeld.

### <a name="purchasing-models"></a>Aankoopmodellen

SQL Database biedt de volgende aankoopmodellen:
- Met het [aankoopmodel op basis van vCore](service-tiers-vcore.md) kunt u het aantal vCores, de hoeveelheid geheugen, en de hoeveelheid en snelheid van de opslagruimte kiezen. Met het op vCore gebaseerde aankoopmodel kunt u ook [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) gebruiken voor SQL Server, om kosten te besparen. Zie de sectie Veelgestelde vragen later in dit artikel voor meer informatie over Azure Hybrid Benefit.
- Het [aankoopmodel op basis van DTU](service-tiers-dtu.md) biedt een combinatie van reken-, geheugen- en i/o-resources in drie servicelagen, voor lichte tot zware databaseworkloads. De verschillen in rekenkracht binnen elke laag bieden een andere combinatie van deze resources, waaraan u extra opslagresources kunt toevoegen.
- Bij het [serverloze model](serverless-tier-overview.md) wordt rekenkracht automatisch geschaald op basis van de vraag naar workload. Ook worden er kosten in rekening gebracht voor de hoeveelheid rekenkracht die per seconde wordt gebruikt. In de serverloze rekenlaag worden databases ook automatisch gepauzeerd tijdens inactieve perioden, zodat alleen opslag wordt gefactureerd. Wanneer er weer activiteiten plaatsvinden, worden de databases automatisch hervat.

### <a name="service-tiers"></a>Servicelagen

Azure SQL Database biedt drie servicelagen die zijn ontworpen voor verschillende typen toepassingen:
- De servicelaag [Algemeen gebruik/Standard](service-tier-general-purpose.md) is ontworpen voor algemene workloads. Deze laag biedt op het budget gebaseerde uitgebalanceerde reken- en opslagopties.
- De servicelaag [Bedrijfskritiek/Premium](service-tier-business-critical.md) is ontworpen voor OLTP-toepassingen, met een hoge transactiefrequentie en I/O met de laagste latentie. Het biedt de hoogste tolerantie voor fouten bij het gebruik van verschillende geïsoleerde replica's.
- De servicelaag [Hyperscale](service-tier-hyperscale.md) is ontworpen voor een zeer grote OLTP-database, en biedt de mogelijkheid om opslag automatisch te schalen en rekenkracht op vloeiende wijze te schalen.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastische pools voor optimaal resourcegebruik

Voor veel bedrijven en toepassingen is het kunnen maken van enkele databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Onvoorspelbare gebruikspatronen kunnen het lastig maken om uw kosten en bedrijfsmodel effectief te beheren. [Elastische pools](elastic-pool-overview.md) zijn ontworpen om dit probleem te verhelpen. U wijst prestatieresources toe aan een pool, in plaats van aan een afzonderlijke database. U betaalt voor de collectieve prestatieresources van de pool, in plaats van voor de prestaties van een individuele database.

   ![Afbeelding van elastische pools in Basic-, Standard- en Premium-edities](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Met elastische pools hoeft u zich niet bezig te houden met het verhogen en verlagen van de databaseprestaties als de vraag naar resources fluctueert. De pooldatabases maken naar behoefte gebruik van de prestatieresources van de elastische pool. Pooldatabases tellen mee voor het verbruik, tot het maximum voor de pool is bereikt. Zo blijven uw kosten voorspelbaar, ook al is uw gebruik van de individuele database dat niet.

U kunt [databases toevoegen aan of verwijderen uit de pool](elastic-pool-overview.md). Zo kan uw app kan worden opgeschaald van een handjevol databases naar duizenden databases, allemaal binnen het budget dat u zelf bepaalt. U kunt ook de minimale en maximale beschikbare resources die voor databases beschikbaar zijn, in de pool beheren om ervoor te zorgen dat er geen database in de pool is die alle poolresources gebruikt, en dat elke pooldatabase een gegarandeerd minimum aan resources heeft. Zie [Ontwerppatronen voor multitenant SaaS-toepassingen met SQL Database](saas-tenancy-app-design-patterns.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen (Software-as-a-Service) die gebruikmaken van elastische pools.

Scripts kunnen helpen bij het bewaken en schalen van elastische pools. Zie [PowerShell gebruiken om een elastische pool te bewaken en te schalen in Azure SQL Database](scripts/monitor-and-scale-pool-powershell.md) voor een voorbeeld.


### <a name="blend-single-databases-with-pooled-databases"></a>Individuele databases combineren met pooldatabases

U kunt individuele databases combineren met elastische pools, en de servicelagen van individuele databases en elastische pools aanpassen aan de situatie. U kunt ook andere Azure-services combineren en integreren met SQL Database om te voldoen aan de behoeften voor uw unieke app-ontwerp, kosten te besparen en resources efficiënt te beheren. Daarnaast kunt u nieuwe zakelijke verkoopkansen creëren.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Uitgebreide mogelijkheden voor bewaking en waarschuwingen

Azure SQL Database biedt geavanceerde functies voor bewaking en probleemoplossing, waarmee u meer inzicht krijgt in de kenmerken van workloads. Deze functies en hulpprogramma’s omvatten:
 - De ingebouwde bewakingsmogelijkheden die worden geboden in de nieuwste versie van de SQL Server-database-engine. Hiermee kunt u in realtime inzicht krijgen in de prestaties. 
 - Bewakingsmogelijkheden van PaaS geboden in Azure, die u in staat stellen een groot aantal database-exemplaren te bewaken en problemen met deze exemplaren op te lossen.

[Query Store](/sql/relational-databases/performance/best-practice-with-the-query-store), een ingebouwde SQL Server-bewakingsfunctie. Hiermee worden de prestaties van uw query’s in realtime vastgelegd, en kunt u potentiële prestatieproblemen en de grootste resourceverbruikers identificeren. Automatische afstemming en aanbevelingen bieden advies met betrekking tot query’s met verminderde prestaties en ontbrekende of gedupliceerde indexen. Met automatische afstemming in SQL Database kunt u de scripts waarmee de problemen worden opgelost, handmatig toepassen of SQL Database het probleem voor u laten oplossen. In SQL Database kan ook worden getest en gecontroleerd of het probleem voordeel oplevert, en de wijziging worden behouden of teruggedraaid, afhankelijk van de uitkomst. Naast Query Store en mogelijkheden voor automatische afstemming kunt u standaard [DMV’s en XEvent](monitoring-with-dmvs.md) gebruiken om de workloadprestaties te bewaken.

Azure biedt [ingebouwde prestatiebewaking](performance-guidance.md) en hulpprogramma’s voor [waarschuwingen](alerts-insights-configure-portal.md), gecombineerd met prestatiebeoordelingen, die u in staat stellen om de status van duizenden databases te bewaken. Met behulp van deze hulpprogramma’s kunt u snel beoordelen wat de impact is van het aanpassen van de schaal op basis van uw huidige of verwachte prestatiebehoeften. Daarnaast kan SQL Database [metrische gegevens en resourcelogboeken verzenden](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) die bewaking eenvoudiger maken. U kunt SQL Database configureren voor het opslaan van resourcegebruik, werkrollen en sessies, en connectiviteit in een van deze Azure-resources:

- **Azure Storage**: Voor het archiveren van grote hoeveelheden telemetriegegevens voor een lage prijs.
- **Azure Event Hubs**: Voor het integreren van SQL Database-telemetrie in uw eigen bewakingsoplossing of actieve pijplijnen.
- **Azure Monitor-logboeken**: Voor een ingebouwde bewakingsoplossing met functionaliteit voor rapportages, waarschuwingen en risicobeperking.

![Diagram van Azure-bewakingsarchitectuur](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Beschikbaarheid

Met Azure SQL Database kan uw bedrijf blijven werken tijdens onderbrekingen. In een traditionele SQL Server-omgeving hebt u lokaal minstens twee computers ingesteld. Deze computers hebben exacte, synchroon onderhouden, kopieën van de gegevens, als bescherming voor wanneer in één computer of onderdeel een fout optreedt. Deze omgeving biedt een hoge beschikbaarheid, maar biedt geen bescherming tegen een natuurramp waardoor uw datacentrum wordt vernietigd.

Bij herstel na noodgeval wordt ervan uitgegaan dat een catastrofale gebeurtenis lokaal zal plaatsvinden, waarbij ergens verder weg een andere computer of set computers met een kopie van uw gegevens beschikbaar blijft. In SQL Server kunt u AlwaysOn-beschikbaarheidsgroepen gebruiken die worden uitgevoerd in de asynchrone modus, om deze mogelijkheid te krijgen. Gebruikers willen vaak niet wachten op een replicatie die zo ver weg plaatsvindt om een transactie door te voeren, waardoor er mogelijk gegevensverlies optreedt bij het uitvoeren van niet-geplande failovers.

Databases in de servicelagen Premium en Bedrijfskritiek [doen al iets vergelijkbaars](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) met de synchronisatie van een beschikbaarheidsgroep. Databases in lagere servicelagen bieden redundantie via opslag, door een [ander maar soortgelijk mechanisme](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) te gebruiken. Ingebouwde logica helpt te beschermen tegen een fout op één computer. De functie voor geo-replicatie biedt u de mogelijkheid om te beschermen tegen een noodgeval waarbij een hele regio wordt vernietigd.

Azure-beschikbaarheidszones zijn bedoeld als bescherming tegen een storing in één datacentrumgebouw binnen één regio. De functie helpt te beschermen tegen het verlies van stroom of netwerk in een gebouw. In SQL Database plaatst u de verschillende replica’s in verschillende beschikbaarheidszones (verschillende gebouwen, effectief).

In feite zorgt de [SLA](https://azure.microsoft.com/support/legal/sla/) (serviceovereenkomst) van Azure, dankzij een wereldwijd netwerk van door Microsoft beheerde datacentrums, ervoor dat uw app 24/7 beschikbaar blijft. Op het Azure-platform wordt elke database volledig beheerd, en gegevensverlies en een hoge beschikbaarheid van gegevens zijn niet gegarandeerd. Patches, back-ups, replicatie, foutdetectie, onderliggende potentiële hardware-, software- of netwerkfouten, de implementatie van bugfixes, failovers, database-upgrades en andere onderhoudstaken worden in Azure automatisch afgehandeld. Standaard-beschikbaarheid wordt bereikt door de lagen voor berekeningen en opslag te scheiden. Premium-beschikbaarheid wordt bereikt door de reken- en opslaglagen voor prestatiedoeleinden te integreren op één knooppunt, en vervolgens technologie te implementeren die vergelijkbaar is met AlwaysOn-beschikbaarheidsgroepen. Zie [Beschikbaarheid van SQL Database](high-availability-sla.md) voor een volledige beschrijving van de mogelijkheden voor hoge beschikbaarheid van Azure SQL Database. 

Daarnaast biedt SQL Database ingebouwde functies voor [bedrijfscontinuïteit en wereldwijde schaalbaarheid](business-continuity-high-availability-disaster-recover-hadr-overview.md). Deze omvatten:

- [Automatische back-ups](automated-backups-overview.md):

  In SQL Database worden automatisch volledige back-ups, differentiële back-ups, en back-ups van transactielogboeken gemaakt voor databases, zodat u kunt herstellen naar elk tijdstip. Voor afzonderlijke databases en pooldatabases kunt u SQL Database configureren om volledige databaseback-ups op te slaan in Azure Storage, voor back-ups met een langetermijnbewaarperiode. Voor beheerde exemplaren kunt u ook back-ups met het kenmerk Alleen-lezen uitvoeren voor back-ups met een langetermijnbewaarperiode.

- [Herstel naar een bepaald tijdstip](recovery-using-backups.md):

  Alle SQL Database-implementatieopties bieden ondersteuning voor herstel naar elk gewenst tijdstip, binnen de automatische bewaarperiode voor back-ups voor een willekeurige database.
- [Actieve geo-replicatie](active-geo-replication-overview.md):

  Met de individuele database en pooldatabases kunt u maximaal vier leesbare secundaire databases configureren in hetzelfde Azure-datacentrum of in wereldwijd gedistribueerde datacentrums. Als u bijvoorbeeld een SaaS-toepassing hebt met een catalogusdatabase met een groot volume aan gelijktijdige alleen-lezentransacties, kunt u actieve geo-replicatie gebruiken om het lezen wereldwijd te schalen. Dit verwijdert knelpunten die vanwege leesworkloads ontstaan op de primaire database. Voor beheerde exemplaren gebruikt u groepen voor automatische failover.
- [Groepen voor automatische failover](auto-failover-group-overview.md):

  Alle SQL Database-implementatieopties bieden u de mogelijkheid om failovergroepen te gebruiken voor hoge beschikbaarheid en taakverdeling op globale schaal. Dit omvat transparante geo-replicatie en failover van grote sets databases, elastische pools, en beheerde exemplaren. Failovergroepen maken het mogelijk om globaal gedistribueerde SaaS-toepassingen te maken, met minimale beheer-overhead. Dit laat alle complexe indeling voor bewaking, routering en failover over aan de SQL Database.
- [Zone-redundante databases](high-availability-sla.md):

  Met SQL Database kunt u Premium-databases of Bedrijfskritieke-databases of elastische pools inrichten in meerdere beschikbaarheidszones. Omdat deze databases en elastische pools meerdere redundante replica's hebben om een hoge beschikbaarheid te garanderen, biedt het plaatsen van deze replica's in meerdere beschikbaarheidszones betere weerbaarheid. Dit omvat de mogelijkheid om zonder verlies van gegevens automatisch te herstellen bij schalingsfouten in het datacentrum.

## <a name="built-in-intelligence"></a>Ingebouwde intelligentie

Met SQL Database beschikt u over ingebouwde intelligentie waarmee u de kosten voor het uitvoeren en beheren van databases drastisch verlaagt, en de prestaties en beveiliging van uw toepassing optimaliseert. SQL Database verwerkt 24 uur per dag miljoenen workloads van klanten, en verzamelt en verwerkt daarbij enorme hoeveelheden telemetriegegevens, waarbij de privacy van klanten volledig wordt gerespecteerd. Er worden verschillende algoritmen gebruikt om de telemetriegegevens continu te evalueren zodat de service zich kan aanpassen aan uw toepassing.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisch bewaking en afstemming van prestaties

SQL Database biedt gedetailleerde informatie over de query’s die u wilt bewaken. SQL Database leert uw databasepatronen zodat u uw databaseschema kunt aanpassen aan de workload. SQL Database geeft [aanbevelingen voor het afstemmen van de prestaties](database-advisor-implement-performance-recommendations.md), waarmee u de aanbevolen acties kunt weergeven en uitvoeren.

Maar doorlopende databasebewaking is een moeilijke, tijdrovende taak, zeker wanneer u te maken hebt met veel databases. [Intelligent Insights](intelligent-insights-overview.md) voert deze taak voor u uit door automatisch de prestaties van SQL Database op schaal te controleren. U wordt geïnformeerd over problemen met prestatievermindering. De hoofdoorzaak van elk probleem wordt vastgesteld, en u ontvangt, waar mogelijk, aanbevelingen over prestatieverbeteringen.

Het is vrijwel onmogelijk om het beheer van enorme hoeveelheden databases op een efficiënte manier uit te voeren, zelfs met de hulpprogramma’s en rapporten van SQL Database en Azure. In plaats van het handmatig bewaken en afstemmen van uw database, kunt u overwegen sommige van deze taken over te laten aan SQL Database, met behulp van [automatische afstemming](automatic-tuning-overview.md). Aanbevolen acties worden automatisch in SQL Database uitgevoerd, getest en geverifieerd om te garanderen dat de prestaties optimaal blijven. Op deze manier past SQL Database zich automatisch op een gecontroleerde en veilige manier aan uw workloads aan. Automatische afstemming betekent dat de prestaties van uw database zorgvuldig worden bewaakt en vergeleken voor en na elke afstemactie. Als de prestaties niet zijn verbeterd, wordt de betreffende actie teruggedraaid.

Veel van onze partners die [multitenant SaaS-apps](saas-tenancy-app-design-patterns.md) uitvoeren in SQL Database, vertrouwen al op deze automatische afstemming, zodat de prestaties van hun toepassingen altijd stabiel en voorspelbaar zijn. Dankzij deze functie wordt de kans op prestatieproblemen (op welk moment dan ook) aanzienlijk kleiner. En omdat een deel van hun klanten ook werkt met SQL Server, maken ze gebruik van dezelfde indexeringsaanbevelingen van SQL Database om hun SQL Server-klanten te helpen.

Twee automatisch afstemmingsmethoden zijn [beschikbaar in SQL Database](automatic-tuning-overview.md):

- **Automatisch indexbeheer**: hiermee worden indexen geïdentificeerd die moeten worden toegevoegd aan of verwijderd uit uw database.
- **Automatische correctie van plannen**: Identificeert problematische abonnementen en lost problemen met de prestaties van het SQL-plan op.

### <a name="adaptive-query-processing"></a>Verwerking van adaptieve query’s

U kunt ook [verwerking van adaptieve query’s](/sql/relational-databases/performance/intelligent-query-processing) gebruiken, waaronder interleave-uitvoering van tabelfuncties met meerdere instructies, feedback over geheugentoekenning in batchmodus, en adaptieve samenvoegingen in batchmodus. Elk van deze verwerkingsfuncties voor adaptieve query’s werkt met vergelijkbare technieken voor ‘leren en aanpassen’, voor het oplossen van prestatieproblemen die het gevolg zijn van hardnekkige problemen met queryoptimalisatie.

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

SQL Database biedt een reeks [ingebouwde functies voor beveiliging en naleving](../../active-directory/identity-protection/concept-identity-protection-security-overview.md) zodat uw toepassing voldoet aan diverse vereisten op het gebied van beveiliging en de naleving van voorschriften.

> [!IMPORTANT]
> Microsoft heeft Azure SQL Database gecertificeerd (alle implementatieopties) voor een aantal nalevingsstandaarden. Raadpleeg het [Vertrouwenscentrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) voor meer informatie. Hier vindt u de meest recente lijst met SQL Database-nalevingscertificeringen.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Azure Defender for SQL is een geïntegreerd pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Het bevat functionaliteit voor het beheren van beveiligingsproblemen in uw database, en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging van de database. Het is bovendien een centraal punt voor het inschakelen en beheren van deze mogelijkheden.

- [Beoordeling van beveiligingsproblemen](sql-vulnerability-assessment.md):

  Met deze service kunt u potentiële beveiligingsproblemen van de database detecteren, bijhouden en herstellen. Deze service biedt u inzicht in de status van de beveiliging en bruikbare stappen om beveiligingsproblemen op te lossen en de beveiliging van uw database te verbeteren.
- [Detectie van bedreigingen](threat-detection-configure.md):

  Met deze functie worden vreemde activiteiten gedetecteerd die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of aanvallen uit te voeren op uw database. Hiermee wordt uw database continu gecontroleerd op verdachte activiteiten en wordt u onmiddellijk gewaarschuwd bij mogelijke beveiligingsproblemen, SQL-injectieaanvallen en afwijkende databasetoegangspatronen. Meldingen over bedreigingsdetectie bevatten gedetailleerde informatie over verdachte activiteiten en aanbevelingen voor het onderzoeken en tegenhouden ervan.

### <a name="auditing-for-compliance-and-security"></a>Controles voor naleving en beveiliging

Met [Controles](../../azure-sql/database/auditing-overview.md) worden databasegebeurtenissen bijgehouden en naar een auditlogboek in uw Azure Storage-account geschreven. Controle kan u helpen om wet- en regelgeving na te leven, activiteiten in de database te begrijpen en inzicht te krijgen in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.

### <a name="data-encryption"></a>Gegevensversleuteling

SQL Database helpt u om uw gegevens te beveiligen met versleuteling. Voor gegevens onderweg wordt [Transport Layer Security](https://support.microsoft.com/kb/3135244) gebruikt. Voor data-at-rest wordt [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) gebruikt. Voor gebruikte gegevens wordt [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) gebruikt.

### <a name="data-discovery-and-classification"></a>Gegevensdetectie en -classificatie

[Gegevensdetectie en -classificatie](data-discovery-and-classification-overview.md) biedt mogelijkheden die zijn ingebouwd in Azure SQL Database, voor het detecteren, classificeren, labelen en beveiligen van de gevoelige gegevens in uw databases. Het biedt inzicht in de classificatiestatus van gegevens in uw database, en het houdt de toegang tot gevoelige gegevens bij, binnen en buiten de database.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Dankzij [Azure Active Directory-integratie](authentication-aad-overview.md) kunt u in SQL Database de identiteit van databasegebruikers en andere Microsoft-services centraal beheren. Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [meervoudige verificatie](authentication-mfa-ssms-overview.md) voor betere beveiliging van gegevens en toepassingen, en ondersteunt ook een proces voor eenmalige aanmelding.

## <a name="easy-to-use-tools"></a>Gebruiksvriendelijke hulpprogramma’s

SQL Database maakt het bouwen en onderhouden van toepassingen makkelijker en productiever. Met SQL Database kunt u zich concentreren op dat waar u het beste in bent: fantastische apps bouwen. In SQL Database kunt u beheren en ontwikkelen door de hulpprogramma’s en vaardigheden te gebruiken die u al hebt.

|Hulpprogramma|Beschrijving|
|:---|:---|
|[Azure Portal](https://portal.azure.com/)|Een webtoepassing voor het beheren van alle Azure-services.|
|[Azure Data Studio](/sql/azure-data-studio/)|Een databasehulpprogramma voor meerdere platforms dat wordt uitgevoerd in Windows, macOS en Linux.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|Een gratis te downloaden clienttoepassing voor het beheren van elke SQL-infrastructuur, van SQL Server tot SQL Database.|
|[SQL Server Data Tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)|Een gratis te downloaden clienttoepassing voor het ontwikkelen van relationele SQL Server-databases, databases in Azure SQL Database, Integration Services-pakketten, Analysis Services-gegevensmodellen en Reporting Services-rapporten.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Een gratis, downloadbare, opensource-code-editor voor Windows, macOS en Linux. Het biedt ondersteuning voor extensies, waaronder de [mssql-extensie](https://aka.ms/mssql-marketplace) voor het uitvoeren van query's in Microsoft SQL Server, Azure SQL Database en Azure Synapse Analytics.|

SQL Database biedt ondersteuning voor het maken van toepassingen met Python, Java, Node.js, PHP, Ruby en .NET, in macOS, Linux en Windows. SQL Database ondersteunt dezelfde [verbindingsbibliotheken](connect-query-content-reference-guide.md#libraries) als SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Veelgestelde vragen over SQL Database

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan ik bepalen wanneer downtime van patches plaatsvindt?

Nee. De impact van patches is over het algemeen niet merkbaar als u [pogingslogica implementeert](develop-overview.md#resiliency) in uw app. Raadpleeg [Planning voor Azure-onderhoudsgebeurtenissen in Azure SQL Database](planned-maintenance.md) voor meer informatie.



## <a name="engage-with-the-sql-server-engineering-team"></a>Contact met het technische team van SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Voor vragen over databasebeheer.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Voor ontwikkelingsvragen.
- [Microsoft Q&A-vragenpagina](/answers/topics/azure-synapse-analytics.html): Voor technische vragen.
- [Feedback](https://aka.ms/sqlfeedback): Voor het rapporteren van bugs en aanvragen van functies.
- [Reddit](https://www.reddit.com/r/SQLServer/): Voor discussies over SQL Server.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [prijzenpagina](https://azure.microsoft.com/pricing/details/sql-database/) voor kostenvergelijkingen en rekenmachines met betrekking tot individuele databases een elastische pools.
- Raadpleeg deze quickstarts om aan de slag te gaan:

  - [Een database maken in Azure Portal](single-database-create-quickstart.md)  
  - [Een database maken met de Azure CLI](az-cli-script-samples-content-guide.md)
  - [Een database maken met PowerShell](powershell-script-content-guide.md)

- Zie de volgende artikelen voor een reeks Azure CLI- en PowerShell-voorbeelden:
  - [Azure CLI-voorbeelden voor SQL Database](az-cli-script-samples-content-guide.md)
  - [Azure PowerShell-voorbeelden voor SQL Database](powershell-script-content-guide.md)

- Raadpleeg [Azure Roadmap voor SQL Database](https://azure.microsoft.com/roadmap/?category=databases) voor informatie over nieuwe aangekondigde mogelijkheden.
- Zie de [Azure SQL Database-blog](https://azure.microsoft.com/blog/topics/database) waar leden van het SQL Server-productteam schrijven over nieuws en functies van SQL Database.