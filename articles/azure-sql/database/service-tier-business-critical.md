---
title: Servicelaag Bedrijfskritiek
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Meer informatie over de Business Critical service-laag voor Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780220"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Bedrijfskritiek tier-Azure SQL Database en Azure SQL Managed instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Bedrijfskritiek laag wordt Premium genoemd in het DTU-aankoop model. Zie [Azure SQL database-inkoop modellen en-resources](purchasing-models.md)voor een vergelijking van het op vCore gebaseerde aankoop model met het DTU-gebaseerde aankoop model.

Azure SQL Database en Azure SQL Managed instance zijn beide gebaseerd op SQL Server data base engine-architectuur die is aangepast voor de cloud omgeving om 99,99% Beschik baarheid te garanderen, zelfs in het geval van infrastructuur fouten. Er zijn drie architectuur modellen die worden gebruikt:
- Algemeen/standaard 
- Bedrijfskritiek/Premium
- Hyperscale

Premium/Bedrijfskritiek service tier model is gebaseerd op een cluster met data base-engine processen. Dit architectuur model is afhankelijk van een feit dat er altijd een quorum van de beschik bare data base-engine knooppunten is en een minimale invloed heeft op de prestaties van uw werk belasting, zelfs tijdens onderhouds activiteiten. De grootschalige is momenteel alleen beschikbaar voor Azure SQL Database (geen SQL Managed instance) en is een zeer schaal bare prestatie-laag voor opslag en berekening die gebruikmaakt van de Azure-architectuur om de opslag-en reken resources voor een Data Base te schalen in Azure SQL Database die de beschik bare limieten voor de Algemeen-en Bedrijfskritiek-service lagen aanzienlijk overschrijden.

Azure upgradet en patches onderliggend besturings systeem, stuur Programma's en SQL Server data base-engine transparant met de minimale verwerkings tijd voor eind gebruikers. 

Premium-Beschik baarheid is ingeschakeld in Premium-en Bedrijfskritiek-service lagen en is ontworpen voor intensieve werk belastingen die geen nadelige invloed kunnen hebben op de prestaties vanwege de voortdurende onderhouds werkzaamheden.

Compute en Storage zijn geïntegreerd op het ene knoop punt in het Premium-model. Hoge Beschik baarheid in dit architectuur model wordt bereikt door de replicatie van Compute (SQL Server data base engine process) en opslag (lokaal gekoppelde SSD) die is geïmplementeerd op een cluster met vier knoop punten en gebruikmaakt van technologie die vergelijkbaar is met SQL Server AlwaysOn- [beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster van data base-engine knooppunten](./media/service-tier-business-critical/business-critical-service-tier.png)

Zowel het proces van de SQL Server data base-engine als de onderliggende. MDF/. ldf-bestanden worden geplaatst op hetzelfde knoop punt met lokaal gekoppelde SSD-opslag met een lage latentie voor uw werk belasting. Hoge Beschik baarheid wordt geïmplementeerd met technologie die vergelijkbaar is met SQL Server AlwaysOn- [beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Elke Data Base is een cluster met database knooppunten met één primaire data base die toegankelijk is voor de werk belasting van de klant en een drie secundaire processen met kopieën van gegevens. Het primaire knoop punt duwt voortdurend wijzigingen in de secundaire knoop punten om ervoor te zorgen dat de gegevens beschikbaar zijn op secundaire replica's als het primaire knoop punt om een of andere reden mislukt. Failover wordt verwerkt door de SQL Server data base-engine – één secundaire replica wordt het primaire knoop punt en er wordt een nieuwe secundaire replica gemaakt om ervoor te zorgen dat er voldoende knoop punten in het cluster zijn. De werk belasting wordt automatisch omgeleid naar het nieuwe primaire knoop punt.

Daarnaast heeft Bedrijfskritiek cluster ingebouwde [Read-out](read-scale-out.md) voor uitgebreide Lees bewerkingen, waarmee een alleen-lezen knoop punt met een vaste hoeveelheid kosten kan worden gebruikt om alleen-lezen query's uit te voeren (bijvoorbeeld rapporten) die de prestaties van uw primaire werk belasting niet mogen beïnvloeden.

## <a name="when-to-choose-this-service-tier"></a>Wanneer u deze servicelaag kiest

Bedrijfskritiek servicelaag is ontworpen voor toepassingen die antwoorden met een lage latentie vereisen van de onderliggende SSD-opslag (gemiddeld 1-2 MS), snel herstel als de onderliggende infra structuur mislukt of rapporten, analyses en alleen-lezen query's moeten worden geladen in de gratis, lees bare secundaire replica van de primaire data base.

De belangrijkste redenen waarom u Bedrijfskritiek servicelaag moet kiezen in plaats van Algemeen laag zijn:
-   **Minimale I/O-latentie vereisten** : werk belastingen waarvoor een snelle reactie nodig is van de opslaglaag (gemiddeld 1-2 milliseconden) moet bedrijfskritiek-laag gebruiken. 
-   **Veelvuldige communicatie tussen toepassing en data base**. Toepassingen die geen gebruik kunnen maken van de caching van de toepassings laag of het [aanvragen van batches](../performance-improve-use-batching.md) en moeten veel SQL-query's verzenden die snel moeten worden verwerkt, zijn goede kandidaten voor de bedrijfskritiek-laag.
-   **Groot aantal updates** : insert-, update-en delete-bewerkingen wijzigen de gegevens pagina's in het geheugen (Dirty pagina) die moeten worden opgeslagen in gegevens bestanden met een `CHECKPOINT` bewerking. Mogelijk is het proces van een data base-engine vastgelopen of kan een failover van de data base met een groot aantal vuile pagina's de herstel tijd in Algemeen laag verg Roten. Gebruik Bedrijfskritiek laag als u een werk belasting hebt die veel in-Memory wijzigingen veroorzaakt. 
-   **Langlopende trans acties waarmee gegevens worden gewijzigd**. Trans acties die gedurende een langere periode worden geopend, voor komen dat logboek bestanden worden afgekapt, waardoor de logboek grootte en het aantal [virtuele logboek bestanden (VLF)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)kunnen worden verg root. Een groot aantal VLFs kan het herstel van de data base na een failover vertragen.
-   **Workload met rapportage-en analyse query's** die kunnen worden omgeleid naar de gratis secundaire alleen-lezen replica.
- **Hogere tolerantie en sneller herstel van fouten**. In het geval van een systeem fout wordt de Data Base op het primaire exemplaar uitgeschakeld en wordt een van de secundaire replica's onmiddellijk een nieuwe lezen-schrijven primaire data base geworden die gereed is voor het verwerken van query's. De data base-engine hoeft geen trans acties uit het logboek bestand te analyseren en opnieuw op te lossen en alle gegevens in de geheugen buffer te laden.
- **Geavanceerde beveiliging tegen beschadiging van gegevens**. Bedrijfskritiek-laag maakt gebruik van database replica's achter de schermen voor bedrijfs continuïteit. de service maakt dus ook gebruik van automatische pagina reparatie. Dit is dezelfde technologie die wordt gebruikt voor SQL Server database [spiegeling en beschikbaarheids groepen](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). In het geval dat een replica een pagina kan lezen vanwege een probleem met de gegevens integriteit, wordt een nieuwe kopie van de pagina opgehaald uit een andere replica, waardoor de onleesbare pagina wordt vervangen zonder gegevens verlies of uitval tijd van de klant. Deze functionaliteit is van toepassing op Algemeen laag als de data base geo-secundaire replica heeft.
- **Hogere Beschik baarheid** -bedrijfskritiek laag in configuratie met meerdere AZ-configuraties garandeert 99,995% Beschik baarheid, vergeleken met 99,99% van de algemeen-laag.
- **Snelle geo-Recovery** -bedrijfskritiek laag die is geconfigureerd met geo-replicatie, heeft een gegarandeerd herstel punt (RPO) van 5 sec en de beoogde herstel tijd (RTO) van 30 sec voor 100% van de geïmplementeerde uren.

## <a name="next-steps"></a>Volgende stappen

- Zoek bron kenmerken (aantal kernen, I/O, geheugen) van Bedrijfskritiek tier in [SQL Managed instance](../managed-instance/resource-limits.md#service-tier-characteristics), Single Data Base [in VCore model](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) of [DTU-model](resource-limits-dtu-single-databases.md#premium-service-tier), of elastische pool in [vCore model](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) en [DTU-model](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits).
- Meer informatie over [Algemeen](service-tier-general-purpose.md) -en [grootschalige](service-tier-hyperscale.md) -lagen.
- Meer informatie over [service Fabric](../../service-fabric/service-fabric-overview.md).
- Zie [bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)voor meer opties voor hoge Beschik baarheid en herstel na nood gevallen.