---
title: Api's voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens
description: Gebruik deze Api's om programmatisch toegang te krijgen tot Analytics-gegevens in het partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583903"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>Api's voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens

Hieronder vindt u de lijst met Api's voor toegang tot de gegevens van de commerciële Marketplace Analytics en de bijbehorende functies.

- [Pull-Api's voor gegevensset](#dataset-pull-apis)
- [Api's voor query beheer](#query-management-apis)
- [Api's voor rapport beheer](#report-management-apis)
- [Pull-Api's voor rapport uitvoering](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>Pull-Api's voor gegevensset

***Tabel 1: pull-Api's voor gegevensset***

| **API** | **Functionaliteit** |
| --- | --- |
| [Alle gegevens sets ophalen](analytics-api-get-all-datasets.md) | Hiermee worden alle beschik bare gegevens sets opgehaald. Met gegevens sets worden de tabellen, kolommen, metrische gegevens en peri Oden weer gegeven. |
|||

## <a name="query-management-apis"></a>Api's voor query beheer

***Tabel 2: Api's voor query beheer***

| **API** | **Functionaliteit** |
| --- | --- |
| [Rapport query maken](analytics-programmatic-access.md#create-report-query-api) | Hiermee maakt u aangepaste query's waarmee de gegevensset wordt gedefinieerd waaruit de kolommen en metrische gegevens moeten worden geëxporteerd. |
| [Rapport Query's ophalen](analytics-api-get-report-queries.md) | Hiermee worden alle query's opgehaald die beschikbaar zijn voor gebruik in-rapporten. Hiermee worden alle systeem-en door de gebruiker gedefinieerde query's standaard opgehaald. |
| [Rapport Query's verwijderen](analytics-api-delete-report-queries.md) | Hiermee verwijdert u door de gebruiker gedefinieerde query's. |
|||

## <a name="report-management-apis"></a>Api's voor rapport beheer

***Tabel 3: Api's voor rapport beheer***

| **API** | **Functionaliteit** |
| --- | --- |
| [Rapport maken](analytics-programmatic-access.md#create-report-api) | Hiermee wordt een query gepland voor uitvoering met regel matige intervallen. |
| [Query's voor rapporten uitproberen](analytics-api-try-report-queries.md) | Hiermee wordt een rapport query-instructie uitgevoerd. Retourneert slechts tien records die een partner kan gebruiken om te controleren of de gegevens naar verwachting zijn. |
| [Rapport ophalen](analytics-api-get-report.md) | Alle rapporten die zijn gepland ophalen. |
| [Rapport bijwerken](analytics-api-update-report.md) | Een rapport parameter wijzigen. |
| [Rapport verwijderen](analytics-api-delete-report.md) | Hiermee verwijdert u alle rapport-en rapport uitvoerings records. |
| [Rapport uitvoeringen onderbreken](analytics-api-pause-report-executions.md) | Hiermee wordt de geplande uitvoering van rapporten onderbroken. |
| [Rapport uitvoeringen hervatten](analytics-api-resume-report-executions.md) | Hiermee wordt de geplande uitvoering van een onderbroken rapport hervat. |
|||

## <a name="report-execution-pull-apis"></a>Pull-Api's voor rapport uitvoering

***Tabel 4: pull-Api's voor het uitvoeren van rapporten***

| **API** | **Functionaliteit** |
| --- | --- |
| [Rapport uitvoeringen ophalen](analytics-programmatic-access.md#get-report-executions-api) | Alle uitvoeringen ophalen die voor een bepaald rapport hebben plaatsgevonden. |
|||

## <a name="next-steps"></a>Volgende stappen

- U kunt de Api's uitproberen via de [URL van SWAGGER API](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
