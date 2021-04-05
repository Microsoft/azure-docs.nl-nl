---
title: Aankoopmodellen
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Meer informatie over de inkoop modellen die beschikbaar zijn voor Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 8883263d6ddb2fb8ddc809f464288fcd282531bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788822"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>Kiezen tussen de vCore-en DTU-aankoop modellen-Azure SQL Database en SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Met Azure SQL Database en Azure SQL Managed Instance kunt u eenvoudig een volledig beheerd platform as a Service (PaaS)-data base-engine aanschaffen die aan uw prestatie-en kosten behoeften voldoet. Afhankelijk van het implementatie model dat u hebt gekozen voor Azure SQL Database, kunt u het inkoop model selecteren dat voor u geschikt is:

- [Op virtuele kern (vCore) gebaseerd inkoop model](service-tiers-vcore.md) (aanbevolen). Dit aankoop model biedt een keuze tussen een ingerichte Compute-laag en een serverloze Compute-laag. Met de ingerichte Compute-laag kiest u de exacte hoeveelheid reken resources die altijd worden ingericht voor uw werk belasting. Met de compute-laag zonder server geeft u het automatisch schalen van de reken resources over een configureerbaar reken bereik op. Met deze Compute-laag kunt u de data base ook automatisch onderbreken en hervatten op basis van de activiteit van de werk belasting. De vCore eenheids prijs per tijds eenheid is lager in de ingerichte Compute-laag dan in de serverloze Compute-laag.
- [Op DTU (data base Trans Action Unit) gebaseerd aankoop model](service-tiers-dtu.md). Dit aankoop model bevat gebundelde reken-en opslag pakketten die zijn afgebalanceerd voor algemene werk belastingen.

Er zijn twee aankoop modellen:

- [inkoop model op basis van vCore](service-tiers-vcore.md) is beschikbaar voor zowel [Azure SQL database](sql-database-paas-overview.md) als [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md). De [grootschalige](service-tier-hyperscale.md) is beschikbaar voor afzonderlijke data bases die gebruikmaken van het [op vCore gebaseerde aankoop model](service-tiers-vcore.md).
- [Het op DTU gebaseerde aankoop model](service-tiers-dtu.md) is beschikbaar voor [Azure SQL database](single-database-manage.md).

In de volgende tabel en grafiek worden de vCore en de op DTU gebaseerde inkoop modellen vergeleken en tegengesteld:

|**Aankoopmodel**|**Beschrijving**|**Ideaal voor**|
|---|---|---|
|Op basis van DTU|Dit model is gebaseerd op een gebundelde meting van compute-, opslag-en I/O-resources. Rekengrootten worden uitgedrukt in DTU's voor afzonderlijke databases, en in EDTU's (Elastische Data Transaction Unit) voor elastische pools. Zie [Wat zijn DTU's en EDTU's](purchasing-models.md#dtu-based-purchasing-model) voor meer informatie over DTU's en EDTU's.|Klanten die eenvoudige, vooraf geconfigureerde resource opties willen|
|Op vCore gebaseerd|Met dit model kunt u afzonderlijke reken-en opslag Resources kiezen. Met het op vCore gebaseerde aankoopmodel kunt u ook [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) gebruiken voor SQL Server, om kosten te besparen.|Klanten die flexibiliteit, controle en transparantie|
||||  

![Prijs model vergelijking](./media/purchasing-models/pricing-model.png)

Wilt u uw clouduitgaven optimaliseren en geld besparen?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>Reken kosten

### <a name="provisioned-compute-costs"></a>Ingerichte reken kosten

In de ingerichte Compute-laag weerspiegelt de reken kosten de totale reken capaciteit die is ingericht voor de toepassing.

In de servicelaag Bedrijfskritiek worden automatisch ten minste drie replica's toegewezen. Om deze extra toewijzing van reken bronnen weer te geven, is de prijs in het op vCore gebaseerde aankoop model ongeveer 2,7 keer hoger in het Bedrijfskritiek servicetier dan het deel uitmaakt van de Algemeen servicelaag. De hogere opslag prijs per GB in de Bedrijfskritiek servicelaag weerspiegelt ook de hogere i/o-limieten en de lagere latentie van de SSD-opslag.

De kosten voor back-upopslag zijn hetzelfde voor de Bedrijfskritiek servicelaag en de Algemeen servicelaag omdat beide lagen standaard opslag gebruiken voor back-ups.

### <a name="serverless-compute-costs"></a>Reken kosten zonder server

Zie [SQL database serverloze laag](serverless-tier-overview.md)voor een beschrijving van de manier waarop reken capaciteit is gedefinieerd en de kosten worden berekend voor de serverloze Compute-laag.

## <a name="storage-costs"></a>Opslagkosten

Verschillende soorten opslag worden anders gefactureerd. Voor gegevens opslag worden er kosten in rekening gebracht voor de ingerichte opslag op basis van de maximale grootte van de data base of groep die u selecteert. De kosten worden niet gewijzigd tenzij u het maximum vermindert of als u dit verhoogt. Back-upopslag is gekoppeld aan automatische back-ups van uw exemplaar en wordt dynamisch toegewezen. Als u uw back-upperiode verhoogt, wordt de back-upopslag die door uw exemplaar wordt gebruikt, verhoogd.

Standaard worden zeven dagen automatische back-ups van uw data bases gekopieerd naar een standaard Blob Storage-account met geografisch redundante opslag met lees toegang (RA-GRS). Deze opslag wordt gebruikt door wekelijkse volledige back-ups, dagelijkse differentiële back-ups en back-ups van transactie logboeken, die elke vijf minuten worden gekopieerd. De grootte van de transactie Logboeken is afhankelijk van de frequentie waarmee de data base wordt gewijzigd. Er wordt geen extra kosten in rekening gebracht voor een minimale opslag hoeveelheid die gelijk is aan 100 procent van de grootte van de data base. Extra verbruik van back-upopslag wordt in GB per maand in rekening gebracht.

Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/sql-database/single/) voor meer informatie over opslag prijzen.

## <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

Een virtuele kern (vCore) vertegenwoordigt een logische CPU en biedt u de mogelijkheid om te kiezen tussen generaties van hardware en fysieke kenmerken van de hardware (bijvoorbeeld het aantal kernen, het geheugen en de opslag grootte). Het vCore-inkoop model biedt u flexibiliteit, controle, transparantie van het gebruik van afzonderlijke bronnen en een eenvoudige manier om on-premises werkbelasting vereisten te vertalen naar de Cloud. Met dit model kunt u reken-, geheugen-en opslag Resources kiezen op basis van de behoeften van uw werk belasting.

In het op vCore gebaseerde aankoop model kunt u kiezen tussen de [Algemeen](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) -en [bedrijfskritiek](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) -service lagen voor SQL database en SQL Managed instance.  Voor afzonderlijke data bases kunt u ook de [grootschalige-servicelaag](service-tier-hyperscale.md)kiezen.

Met het op vCore gebaseerde aankoop model kunt u afzonderlijke reken-en opslag Resources kiezen, de on-premises prestaties afstemmen en de prijs optimaliseren. In het op vCore gebaseerde aankoop model betaalt u voor:

- Rekenbronnen (de servicelaag, het aantal vCores, de hoeveelheid geheugen en de generatie van de hardware).
- Het type en de hoeveelheid gegevens- en logboekopslag.
- Back-upopslag (RA-GRS).

> [!IMPORTANT]
> Reken resources, I/O en gegevens-en logboek opslag worden in rekening gebracht per data base of elastische pool. Back-upopslag wordt per data base in rekening gebracht. Zie [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md)(Engelstalig) voor meer informatie over de kosten voor SQL Managed instances.
> **Beperkingen van de regio:** Zie voor de huidige lijst met ondersteunde regio's [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Als u een beheerd exemplaar wilt maken in een regio die momenteel niet wordt ondersteund, [verzendt u een ondersteunings aanvraag via de Azure Portal](quota-increase-request.md).

Als uw data base meer dan 300 Dtu's verbruikt, kunt u uw kosten verlagen door te converteren naar het op vCore gebaseerde aankoop model. U kunt converteren met behulp van de API van Choice of met behulp van de Azure Portal, zonder uitval tijd. Conversie is echter niet vereist en wordt niet automatisch uitgevoerd. Als het op DTU gebaseerde aankoop model voldoet aan uw prestatie-en bedrijfs vereisten, moet u het blijven gebruiken.

Als u wilt omzetten van het op DTU gebaseerde aankoop model naar het op vCore gebaseerde aankoop model, raadpleegt u [Migrate from DTU to vCore](migrate-dtu-to-vcore.md).

## <a name="dtu-based-purchasing-model"></a>Op DTU gebaseerd inkoop model

Een Data Base Trans Action Unit (DTU) vertegenwoordigt een overvloei meting van CPU, geheugen, lees bewerkingen en schrijf bewerkingen. Het op DTU gebaseerde aankoop model biedt een aantal vooraf geconfigureerde bundels van reken bronnen en opgenomen opslag om verschillende niveaus van toepassings prestaties te kunnen best Ellen. Als u de voor keur geeft aan de eenvoud van een vooraf geconfigureerde bundel en vaste betalingen per maand, is het op DTU gebaseerde model mogelijk beter geschikt voor uw behoeften.

In het op DTU gebaseerde aankoop model kunt u kiezen tussen de basis-, standaard-en Premium-Service lagen voor Azure SQL Database. Het op DTU gebaseerde aankoop model is niet beschikbaar voor Azure SQL Managed instance.

### <a name="database-transaction-units-dtus"></a>Data base Trans Action units (Dtu's)

Voor één data base met een specifieke reken grootte [binnen een servicelaag](single-database-scale.md)garandeert Azure een bepaald niveau van resources voor die data base (onafhankelijk van andere data bases in de Azure-Cloud). Deze garantie biedt een voorspelbaar prestatie niveau. De hoeveelheid resources die voor een Data Base wordt toegewezen, wordt berekend als een aantal Dtu's en is een gebundelde meting van compute, Storage en I/O-resources.

De verhouding tussen deze resources wordt oorspronkelijk bepaald door een [OLTP-werk belasting (online Trans Action processing)](service-tiers-dtu.md) die is ontworpen om typische fysieke OLTP-workloads te zijn. Wanneer uw werk belasting de hoeveelheid van een van deze resources overschrijdt, wordt uw door Voer beperkt, wat resulteert in tragere prestaties en time-outs.

De resources die door uw werk belasting worden gebruikt, hebben geen invloed op de resources die beschikbaar zijn voor andere data bases in de Azure-Cloud. De resources die worden gebruikt door andere workloads, hebben ook geen invloed op de resources die beschikbaar zijn voor uw data base.

![Begrenzingsvak](./media/purchasing-models/bounding-box.png)

Dtu's zijn het handigst om te weten wat de relatieve resources zijn die zijn toegewezen voor data bases met verschillende reken grootten en service lagen. Bijvoorbeeld:

- Door de Dtu's te verdubbelen door de reken grootte van een Data Base te verg Roten, wordt de set beschik bare bronnen voor die data base verdubbeld.
- Een Premium service tier P11-data base met 1750 Dtu's biedt 350 keer meer DTU-reken kracht dan een eenvoudige data base van de servicelaag met 5 Dtu's.  

Als u meer inzicht wilt krijgen in het gebruik van uw workload door de resource (DTU), gebruikt u [query-prestatie inzichten](query-performance-insight-use.md) om:

- Identificeer de meest voorkomende query's op basis van het aantal CPU/duur/uitvoeringen dat mogelijk kan worden afgestemd op betere prestaties. Een I/O-intensieve query kan bijvoorbeeld profiteren van [optimalisatie technieken in het geheugen](../in-memory-oltp-overview.md) om beter gebruik te maken van het beschik bare geheugen in een bepaalde servicelaag en de berekenings grootte.
- Zoom in op de details van een query om de tekst en de geschiedenis van het resource gebruik weer te geven.
- Aanbevelingen voor het afstemmen van prestaties voor het weer geven van acties die door [SQL database Advisor](database-advisor-implement-performance-recommendations.md)worden uitgevoerd.

### <a name="elastic-database-transaction-units-edtus"></a>Elastic data base Trans Action units (Edtu's)

Voor data bases die altijd beschikbaar zijn in plaats van een speciale set resources (Dtu's) die mogelijk niet altijd nodig zijn, kunt u deze data bases in een [elastische pool](elastic-pool-overview.md)plaatsen. De data bases in een elastische pool bevinden zich op één server en delen een groep resources.

De gedeelde resources in een elastische pool worden gemeten door Elastic data base Trans Action units (Edtu's). Elastische Pools bieden een eenvoudige en kosteneffectieve oplossing voor het beheer van de prestatie doelen voor meerdere data bases met veel verschillende en onvoorspelbare gebruiks patronen. Een elastische groep garandeert dat alle resources niet kunnen worden gebruikt door één data base in de pool, terwijl ervoor wordt gezorgd dat elke data base in de groep altijd een minimale hoeveelheid beschik bare bronnen heeft.

Een pool krijgt een ingesteld aantal Edtu's voor een ingestelde prijs. In de elastische pool kunnen afzonderlijke data bases automatisch worden geschaald binnen de geconfigureerde grenzen. Een Data Base onder een zwaarere belasting neemt meer Edtu's in beslag om aan de vraag te voldoen. Data bases met een lichtere belasting nemen minder Edtu's in beslag. Data bases zonder belasting nemen geen Edtu's in beslag. Omdat resources zijn ingericht voor de hele pool in plaats van per data base, vereenvoudigen elastische Pools uw beheer taken en bieden ze een voorspelbaar budget voor de pool.

U kunt extra Edtu's toevoegen aan een bestaande pool zonder uitval tijd van de data base en zonder dat dit van invloed is op de data bases in de pool. Als u extra Edtu's niet meer nodig hebt, kunt u deze op elk gewenst moment verwijderen uit een bestaande groep. U kunt ook data bases toevoegen aan of verwijderen uit een pool op elk gewenst moment. Als u Edtu's voor andere data bases wilt reserveren, beperkt u het aantal Edtu's dat door een Data Base kan worden gebruikt onder een zware belasting. Als een Data Base consequent bronnen gebruikt, verplaatst u deze uit de pool en configureert u deze als één data base met een voorspel bare hoeveelheid vereiste resources.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Het aantal Dtu's bepalen dat nodig is voor een workload

Als u een bestaande on-premises of SQL Server werk belasting van virtuele machine naar SQL Database wilt migreren, gebruikt u de [DTU-reken machine](https://dtucalculator.azurewebsites.net/) om het aantal benodigde dtu's te benaderen. Gebruik voor een bestaande SQL Database workload [query-prestatie inzichten](query-performance-insight-use.md) om inzicht te krijgen in uw database verbruik (dtu's) en krijg meer inzicht in het optimaliseren van uw werk belasting. Met de [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamische beheer weergave (DMV) kunt u het Resource verbruik voor het afgelopen uur weer geven. In de catalogus weergave van de [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) wordt het Resource verbruik voor de afgelopen 14 dagen weer gegeven, maar een lagere betrouw baarheid van gemiddelden van vijf minuten.

### <a name="determine-dtu-utilization"></a>DTU-gebruik bepalen

Gebruik de volgende formule om het gemiddelde percentage van DTU/eDTU-gebruik te bepalen ten opzichte van de DTU/eDTU-limiet van een Data Base of een elastische pool:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

De invoer waarden voor deze formule kunnen worden verkregen uit [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)en [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) dmv's. Met andere woorden, om het percentage van DTU/eDTU-gebruik te bepalen ten opzichte van de DTU/eDTU-limiet van een Data Base of een elastische pool, kiest u de hoogste percentage waarde uit de volgende opties: `avg_cpu_percent` , `avg_data_io_percent` en `avg_log_write_percent` op een bepaald moment.

> [!NOTE]
> De DTU-limiet van een Data Base wordt bepaald door CPU, lees bewerkingen, schrijf bewerkingen en geheugen dat beschikbaar is voor de data base. Maar omdat de SQL Database-Engine doorgaans al het beschik bare geheugen gebruikt voor de gegevens cache om de prestaties te verbeteren, `avg_memory_usage_percent` wordt de waarde doorgaans bijna 100 procent, ongeacht de huidige laad capaciteit van de data base. Daarom wordt het niet gebruikt in de formule voor het DTU-gebruik, zelfs als het geheugen indirect van invloed is op de DTU-limiet.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Workloads die profiteren van een elastische groep resources

Groepen zijn goed geschikt voor data bases met een gemiddelde en relatief weinig gebruik van pieken voor het resource gebruik. Zie [Wanneer moet u rekening houden met een SQL database elastische pool?](elastic-pool-overview.md)voor meer informatie.

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Hardware gegenereerd in het op DTU gebaseerde aankoop model

In het op DTU gebaseerde aankoop model kunnen klanten de hardware-generatie kiezen die wordt gebruikt voor de data bases. Hoewel een bepaalde data base doorgaans gedurende lange tijd op een specifieke generatie van de hardware blijft, zijn er bepaalde gebeurtenissen die ertoe kunnen leiden dat een Data Base wordt verplaatst naar een andere generatie van hardware.

U kunt bijvoorbeeld een Data Base verplaatsen naar een andere hardware-generatie als deze omhoog of omlaag wordt geschaald naar een andere service doelstelling, of als de huidige infra structuur in een Data Center de capaciteits limieten nadert of als de momenteel gebruikte hardware buiten gebruik wordt gesteld vanwege het einde van de levens duur.

Als een Data Base wordt verplaatst naar andere hardware, kunnen de prestaties van de werk belasting veranderen. Het DTU-model garandeert dat de door Voer en respons tijd van de werk belasting van de [DTU-benchmark](./service-tiers-dtu.md#dtu-benchmark) periode aanzienlijk hetzelfde blijft als de data base wordt verplaatst naar een andere generatie hardware, mits de service doelstelling (het aantal dtu's) hetzelfde blijft.

In het brede spectrum van klant werkbelastingen die worden uitgevoerd in Azure SQL Database, kan de impact van het gebruik van verschillende hardware voor dezelfde service doelstelling echter meer worden uitgesp roken. Verschillende werk belastingen profiteren van verschillende hardwareconfiguraties en functies. Daarom is het mogelijk om voor andere workloads dan de DTU-Bench Mark prestatie verschillen te zien als de data base van de ene hardware-generatie naar de andere wordt verplaatst.

Een toepassing die bijvoorbeeld gevoelig is voor netwerk latentie, kan betere prestaties zien op GEN5-hardware ten opzichte van Gen4 vanwege het gebruik van versneld netwerken in GEN5, maar een toepassing die intensieve Lees-IO gebruikt, kan betere prestaties zien op Gen4-hardware versus GEN5 vanwege een groter geheugen per core-verhouding van Gen4.

Klanten met werk belastingen die gevoelig zijn voor wijzigingen in de hardware of klanten die de keuze van de hardware-generatie voor hun data base willen beheren, kunnen het [vCore](service-tiers-vcore.md) -model gebruiken om hun voorkeurs generatie van hardware te kiezen tijdens het maken en schalen van de data base. In het vCore-model worden resource limieten van elke service doelstelling op elke hardware-generatie gedocumenteerd voor zowel [afzonderlijke data bases](resource-limits-vcore-single-databases.md) als [elastische Pools](resource-limits-vcore-elastic-pools.md). Zie [Hardware-generaties](./service-tiers-vcore.md#hardware-generations)voor meer informatie over hardware-generaties in het vCore-model.

## <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Moet ik mijn toepassing offline zetten om een service tier op basis van DTU te converteren naar een vCore-service tier?

Nee. U hoeft de toepassing niet offline te zetten. De nieuwe service lagen bieden een eenvoudige online conversie methode die vergelijkbaar is met het bestaande proces van het bijwerken van data bases van de standaard naar de Premium-servicelaag en de andere manier. U kunt deze conversie starten met behulp van de Azure Portal, Power shell, de Azure CLI, T-SQL of de REST API. Zie voor meer informatie [Individuele databases beheren](single-database-scale.md) en [Elastische pools beheren](elastic-pool-overview.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kan ik een Data Base van een servicelaag in het op vCore gebaseerde aankoop model converteren naar een servicelaag in het op DTU gebaseerde aankoop model?

Ja, u kunt uw data base eenvoudig converteren naar een ondersteunde prestatie doelstelling door gebruik te maken van de Azure Portal, Power shell, de Azure CLI, T-SQL of de REST API. Zie voor meer informatie [Individuele databases beheren](single-database-scale.md) en [Elastische pools beheren](elastic-pool-overview.md).

## <a name="next-steps"></a>Volgende stappen

- Zie [op vCore gebaseerd inkoop model](service-tiers-vcore.md)voor meer informatie over het op vCore gebaseerde aankoop model.
- Zie het [op DTU gebaseerde aankoop model](service-tiers-dtu.md)voor meer informatie over het op DTU gebaseerde aankoop model.