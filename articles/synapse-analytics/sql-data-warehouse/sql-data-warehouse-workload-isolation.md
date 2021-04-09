---
title: Isolatie van workloads
description: Richt lijnen voor het instellen van workload isolatie met werkbelasting groepen in azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 506aed16f1b8a6c631a759bb1367aef8242859ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734777"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Isolatie van de werkbelasting groep voor Azure Synapse Analytics

In dit artikel wordt uitgelegd hoe werkbelasting groepen kunnen worden gebruikt voor het configureren van de isolatie van werk belastingen, het bevatten van resources en het Toep assen van runtime regels voor het uitvoeren van query's.

## <a name="workload-groups"></a>Werkbelastinggroepen

Werkbelasting groepen zijn containers voor een set aanvragen en vormen de basis voor de manier waarop werkbelasting beheer, waaronder isolatie van werk belasting, is geconfigureerd op een systeem.  Werkbelasting groepen worden gemaakt met de syntaxis [WERKBELASTING groep maken](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Een eenvoudige configuratie van werkbelasting beheer kan het laden van gegevens en gebruikers query's beheren.  Zo definieert een werkbelasting groep met de naam `wgDataLoads` werkbelasting onderdelen voor gegevens die in het systeem worden geladen. Daarnaast definieert een werkbelasting groep `wgUserQueries` met de naam workload-aspecten voor gebruikers die query's uitvoeren om gegevens van het systeem te lezen.

In de volgende secties wordt uitgelegd hoe werkbelasting groepen de mogelijkheid bieden om isolatie, containment en de resource definitie van de aanvraag te definiëren en om uitvoerings regels aan te houden.

## <a name="workload-isolation"></a>Isolatie van workloads

Isolatie van werk belasting betekent dat resources gereserveerd zijn, uitsluitend voor een werkbelasting groep.  De isolatie van de werk belasting wordt bereikt door de para meter MIN_PERCENTAGE_RESOURCE in te stellen op een waarde groter dan nul in de syntaxis van de [groep werk belasting maken](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Voor doorlopende uitvoerings werkbelastingen die moeten voldoen aan de strakke Sla's, wordt door isolatie gegarandeerd dat resources altijd beschikbaar zijn voor de werkbelasting groep.

Door workload-isolatie te configureren, wordt impliciet een gegarandeerd niveau van gelijktijdigheid gedefinieerd. Een werkbelasting groep met een `MIN_PERCENTAGE_RESOURCE` ingesteld op 30% en `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ingesteld op 2% is bijvoorbeeld gegarandeerd 15 gelijktijdigheid.  Het niveau van gelijktijdigheid wordt gegarandeerd omdat 15-2%-sleuven van resources op elk moment worden gereserveerd in de werkbelasting groep (ongeacht hoe dit `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` is geconfigureerd).  Als `REQUEST_MAX_RESOURCE_GRANT_PERCENT` is groter dan `REQUEST_MIN_RESOURCE_GRANT_PERCENT` en `CAP_PERCENTAGE_RESOURCE` groter is dan `MIN_PERCENTAGE_RESOURCE` aanvullende resources, worden per aanvraag toegevoegd.  Als `REQUEST_MAX_RESOURCE_GRANT_PERCENT` en `REQUEST_MIN_RESOURCE_GRANT_PERCENT` gelijk is aan en `CAP_PERCENTAGE_RESOURCE` groter is dan `MIN_PERCENTAGE_RESOURCE` , is aanvullende gelijktijdigheid mogelijk.  Bekijk de onderstaande methode voor het bepalen van gegarandeerde gelijktijdigheid:

[Gegarandeerde gelijktijdigheid] = [ `MIN_PERCENTAGE_RESOURCE` ]/[ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Er zijn specifieke minimale levensvat bare waarden voor het service niveau voor min_percentage_resource.  Zie voor meer informatie [doel treffende waarden](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values) voor meer informatie.

Als er geen isolatie van de werk belasting beschikbaar is, worden de aanvragen uitgevoerd in de [gedeelde groep](#shared-pool-resources) resources.  Toegang tot resources in de gedeelde groep is niet gegarandeerd en wordt op basis van [urgentie](sql-data-warehouse-workload-importance.md) toegewezen.

Het configureren van de isolatie van werk belasting moet voorzichtig zijn wanneer de resources worden toegewezen aan de werkbelasting groep, zelfs als er geen actieve aanvragen in de werkbelasting groep zijn. Over het configureren van isolatie kan leiden tot een afbreuk aan het algehele systeem gebruik.

Gebruikers moeten een beheer oplossing voor werk belastingen voor komen die 100% workload-isolatie configureert: 100% isolatie wordt bereikt wanneer de som van min_percentage_resource dat is geconfigureerd voor alle werkbelasting groepen gelijk is aan 100%.  Dit type configuratie is te beperkend en stijf, waardoor er weinig ruimte is voor resource aanvragen die per ongeluk zijn geclassificeerd. Er is een bepaling om toe te staan dat één aanvraag kan worden uitgevoerd vanuit werkbelasting groepen die niet zijn geconfigureerd voor isolatie. De resources die zijn toegewezen aan deze aanvraag, worden weer gegeven als een nul in de systemen Dmv's en lenen een smallrc niveau van resource toekenning van gereserveerde bronnen van het systeem.

> [!NOTE]
> Als u optimaal gebruik wilt maken van resources, kunt u een oplossing voor workload Management gebruiken die een zekere isolatie benuttt om ervoor te zorgen dat Sla's wordt vervuld en gemengd met gedeelde resources die worden geopend op basis van de urgentie van het [werk belasting](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Containment-werk belasting

Insluiting van de werk belasting verwijst naar het beperken van de hoeveelheid resources die een werkbelasting groep mag verbruiken.  Het opnemen van de werk belasting wordt bereikt door de CAP_PERCENTAGE_RESOURCE-para meter in te stellen op minder dan 100 in de syntaxis voor het maken van een [WERKBELASTING groep](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  .  Denk na over het scenario waarmee gebruikers lees toegang tot het systeem nodig hebben, zodat ze een What-if-analyse kunnen uitvoeren via ad-hoc query's.  Deze typen aanvragen kunnen een negatieve invloed hebben op andere workloads die op het systeem worden uitgevoerd.  Het configureren van de insluiting zorgt ervoor dat de hoeveelheid resources beperkt is.

Het configureren van een workload-containment definieert impliciet een maximum niveau van gelijktijdigheid.  Als een CAP_PERCENTAGE_RESOURCE is ingesteld op 60% en een REQUEST_MIN_RESOURCE_GRANT_PERCENT is ingesteld op 1%, is er Maxi maal 60-gelijktijdigheids niveau toegestaan voor de werkbelasting groep.  Bekijk de onderstaande methode voor het bepalen van de maximale gelijktijdigheid:

[Max. gelijktijdigheids] = [ `CAP_PERCENTAGE_RESOURCE` ]/[ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> De effectief CAP_PERCENTAGE_RESOURCE van een werkbelasting groep is 100% niet bereikt wanneer werkbelasting groepen met MIN_PERCENTAGE_RESOURCE op een hoger niveau dan nul worden gemaakt.  Zie [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor effectief runtime-waarden.

## <a name="resources-per-request-definition"></a>Definitie van resources per aanvraag

Werkbelasting groepen bieden een mechanisme voor het definiëren van de minimum-en maximum hoeveelheid resources die worden toegewezen per aanvraag met de REQUEST_MIN_RESOURCE_GRANT_PERCENT en REQUEST_MAX_RESOURCE_GRANT_PERCENT para meters in de syntaxis van de [WERKBELASTING groep maken](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Resources in dit geval zijn CPU en geheugen.  Als deze waarden worden geconfigureerd, wordt bepaald hoeveel resources en welk niveau van gelijktijdigheid op het systeem kunnen worden behaald.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT is een optionele para meter die wordt ingesteld op de waarde die is opgegeven voor REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Net als bij het kiezen van een resource klasse, stelt REQUEST_MIN_RESOURCE_GRANT_PERCENT de waarde in voor de resources die worden gebruikt door een aanvraag.  De hoeveelheid resources die wordt aangegeven door de set-waarde, wordt gegarandeerd toegewezen aan de aanvraag voordat de uitvoering wordt gestart.  Voor klanten die migreren van resource klassen naar werkbelasting groepen, kunt u het beste de volgende [procedure](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) volgen om een toewijzing van resources-klassen aan werkbelasting groepen uit te zetten als uitgangs punt.

Als u REQUEST_MAX_RESOURCE_GRANT_PERCENT configureert met een waarde die groter is dan REQUEST_MIN_RESOURCE_GRANT_PERCENT, kan het systeem meer resources per aanvraag toewijzen.  Bij het plannen van een aanvraag, bepaalt het systeem de werkelijke toewijzing van resources aan de aanvraag, tussen REQUEST_MIN_RESOURCE_GRANT_PERCENT en REQUEST_MAX_RESOURCE_GRANT_PERCENT, op basis van de beschik baarheid van resources in gedeelde groep en huidige belasting van het systeem.  De resources moeten aanwezig zijn in de [gedeelde groep](#shared-pool-resources) resources wanneer de query is gepland.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT en REQUEST_MAX_RESOURCE_GRANT_PERCENT hebben ingangs waarden die afhankelijk zijn van de juiste MIN_PERCENTAGE_RESOURCE en CAP_PERCENTAGE_RESOURCE waarden.  Zie [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor effectief runtime-waarden.

## <a name="execution-rules"></a>Uitvoerings regels

In AD-hocrapportage kunnen klanten per ongeluk overmatige query's uitvoeren die de productiviteit van anderen aanzienlijk beïnvloeden.  Systeem beheerders worden gedwongen tijd te best Eden aan het doden van overmatige query's om systeem bronnen vrij te maken.  Werkbelasting groepen bieden de mogelijkheid om een time-outregel voor het uitvoeren van query's te configureren om query's te annuleren die de opgegeven waarde hebben overschreden.  De regel wordt geconfigureerd door de `QUERY_EXECUTION_TIMEOUT_SEC` para meter in te stellen in de syntaxis voor het maken van een [werkbelasting groep](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

## <a name="shared-pool-resources"></a>Bronnen van gedeelde groep

Gedeelde pool bronnen zijn de bronnen die niet zijn geconfigureerd voor isolatie.  Werkbelasting groepen waarvoor een MIN_PERCENTAGE_RESOURCE is ingesteld op nul, maken gebruik van resources in de gedeelde groep voor het uitvoeren van aanvragen.  Werkbelasting groepen met een CAP_PERCENTAGE_RESOURCE groter dan MIN_PERCENTAGE_RESOURCE ook gedeelde resources gebruikt.  De hoeveelheid beschik bare resources in de gedeelde groep wordt als volgt berekend.

[Gedeelde groep] = 100-[som van `MIN_PERCENTAGE_RESOURCE` alle werkbelasting groepen]

Toegang tot resources in de gedeelde groep wordt op basis van [urgentie](sql-data-warehouse-workload-importance.md) toegewezen.  Aanvragen met hetzelfde urgentie niveau hebben toegang tot gedeelde pool bronnen op basis van First in/first out.

## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids: isolatie van werk belasting configureren](quickstart-configure-workload-isolation-tsql.md)
- [WERKBELASTING GROEP MAKEN](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Resource klassen omzetten in werkbelasting groepen](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Bewaking van werk belasting Beheerportal](sql-data-warehouse-workload-management-portal-monitor.md).  
