---
title: Resource klasse converteren naar een werkbelasting groep
description: Meer informatie over het maken van een werkbelasting groep die vergelijkbaar is met een resource klasse in een toegewezen SQL-groep.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1207f4856882d8aa0e6d1e41712071536bfecf29
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728553"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Resource klassen omzetten in werkbelasting groepen

Werkbelasting groepen bieden een mechanisme om systeem bronnen te isoleren en te bevatten.  Daarnaast kunt u met werkbelasting groepen uitvoerings regels instellen voor de aanvragen die hierop worden uitgevoerd.  Een regel voor het uitvoeren van een query time-out kan overmatige query's annuleren zonder tussen komst van de gebruiker.  In dit artikel wordt uitgelegd hoe u een bestaande resource klasse maakt en een werkbelasting groep met een vergelijk bare configuratie kunt maken.  Daarnaast wordt een regel voor een optionele time-out voor query's toegevoegd.

> [!NOTE]
> Zie de sectie [toewijzingen van resource klassen combi neren met classificaties](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) in het concept document [classificatie werk belasting](sql-data-warehouse-workload-classification.md) voor richt lijnen voor het gebruik van werkbelasting groepen en resource klassen.

## <a name="understanding-the-existing-resource-class-configuration"></a>Informatie over de configuratie van de bestaande resource klasse

Voor werkbelasting groepen is een para meter met de naam vereist, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` waarmee het percentage van de totale systeem resources die per aanvraag worden toegewezen.  Resource toewijzing wordt uitgevoerd voor [resource klassen](resource-classes-for-workload-management.md#what-are-resource-classes) door gelijktijdigheids sleuven toe te wijzen.  Als u wilt bepalen welke waarde moet worden opgegeven `REQUEST_MIN_RESOURCE_GRANT_PERCENT` , gebruikt u de sys.dm_workload_management_workload_groups_stats <link tbd> dmv.  De onderstaande query query retourneert bijvoorbeeld een waarde die kan worden gebruikt voor de `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para meter om een werkbelasting groep te maken die lijkt op staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Werkbelasting groepen worden uitgevoerd op basis van het percentage van de algemene systeem resources.  

Omdat werkbelasting groepen worden uitgevoerd op basis van het percentage van de algemene systeem bronnen, worden de resources die zijn toegewezen aan statische resource klassen ten opzichte van de algemene systeem resources gewijzigd, wanneer u omhoog of omlaag schaalt.  Staticrc40 op DW1000c wijst bijvoorbeeld 19,2% van de algemene systeem bronnen toe.  Op DW2000c wordt 9,6% toegewezen.  Dit model is vergelijkbaar als u wilt opschalen voor gelijktijdigheid en meer resources per aanvraag wilt toewijzen.

## <a name="create-workload-group"></a>Werkbelasting groep maken

Met de bekende `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kunt u de groep WORKLOAD maken gebruiken <link> om de werkbelasting groep te maken.  U kunt optioneel een waarde opgeven `MIN_PERCENTAGE_RESOURCE` die groter is dan nul om resources voor de werkbelasting groep te isoleren.  U kunt eventueel ook `CAP_PERCENTAGE_RESOURCE` minder dan 100 opgeven om de hoeveelheid resources te beperken die de werkbelasting groep mag verbruiken.  

Met behulp van mediumrc als basis voor een voor beeld wordt de onderstaande code ingesteld `MIN_PERCENTAGE_RESOURCE` op de om 10% van de systeem resources toe te wijzen `wgDataLoads` en garandeert een query alle tijden.  Daarnaast `CAP_PERCENTAGE_RESOURCE` is ingesteld op 40% en wordt deze werkbelasting groep beperkt tot vier gelijktijdige aanvragen.  Door de `QUERY_EXECUTION_TIMEOUT_SEC` para meter in te stellen op 3600, wordt elke query die gedurende meer dan 1 uur wordt uitgevoerd, automatisch geannuleerd.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>De classificatie maken

Voorheen werd de toewijzing van query's aan resource klassen uitgevoerd met [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Als u dezelfde functionaliteit wilt gebruiken en aanvragen wilt toewijzen aan werkbelasting groepen, gebruikt u de [classificatie syntaxis werk belasting maken](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Als u sp_addrolemember gebruikt, hebt u alleen de mogelijkheid om resources toe te wijzen aan een aanvraag op basis van een aanmelding.  Een classificatie biedt extra opties naast aanmelding, zoals:
    - label
    - Sessie
    - tijd in het onderstaande voor beeld worden query's toegewezen uit de `AdfLogin` aanmelding waarvoor ook het [optie label](sql-data-warehouse-develop-label.md)  is ingesteld op `factloads` de eerder gemaakte werkbelasting groep `wgDataLoads` .

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Testen met een voorbeeld query

Hieronder vindt u een voorbeeld query en een DMV-query om te controleren of de werkbelasting groep en de classificatie correct zijn geconfigureerd.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Volgende stappen

- [Isolatie van werk belasting](sql-data-warehouse-workload-isolation.md)
- [Een werkbelasting groep maken](quickstart-configure-workload-isolation-tsql.md)
- [CLASSIFICATIE van werk belasting maken (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql??view=azure-sqldw-latest&preserve-view=true)
- [Werkbelasting groep maken (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest&preserve-view=true)
