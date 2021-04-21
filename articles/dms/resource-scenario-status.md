---
title: Status van het databasemigratiescenario
titleSuffix: Azure Database Migration Service
description: Meer informatie over de status van de migratiescenario's die worden ondersteund door Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: f06c1a055b2f08259bf5318afe3c969d763bbbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818616"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status van migratiescenario's die worden ondersteund door Azure Database Migration Service

Azure Database Migration Service is ontworpen ter ondersteuning van verschillende migratiescenario's (bron-/doelparen) voor zowel offlinemigraties (een keer) als onlinemigraties (continue synchronisatie). De scenariodekking van Azure Database Migration Service wordt in de toekomst uitgebreid. Er worden regelmatig nieuwe scenario's toegevoegd. In dit artikel worden migratiescenario's beschreven die momenteel worden ondersteund door Azure Database Migration Service en de status (privépreview, openbare preview of algemene beschikbaarheid) voor elk scenario.

## <a name="offline-versus-online-migrations"></a>Offline versus onlinemigraties

Met Azure Database Migration Service kunt u een offline- of onlinemigratie doen. Bij *offlinemigraties* begint de downtime van de toepassing op het moment dat de migratie wordt gestart. Gebruik een *onlinemigratie* om downtime te beperken tot de tijd die nodig is om over te zetten naar de nieuwe omgeving wanneer de migratie is voltooid. Het is raadzaam om een offlinemigratie te testen om te bepalen of de downtime acceptabel is; Zo niet, dan kunt u een onlinemigratie maken.

## <a name="migration-scenario-status"></a>Status van migratiescenario

De status van migratiescenario's die worden ondersteund door Azure Database Migration Service varieert met de tijd. Over het algemeen worden scenario's eerst uitgebracht in **de private preview.** Na de privépreview wordt de status van het scenario gewijzigd in **openbare preview.** Azure Database Migration Service kunnen migratiescenario's in openbare preview rechtstreeks vanuit de gebruikersinterface uitproberen. Er is geen aanmelding vereist.  Migratiescenario's in openbare preview zijn echter mogelijk niet in alle regio's beschikbaar en kunnen vóór de definitieve release aanvullende wijzigingen ondergaan. Na de openbare preview wordt de status van het scenario gewijzigd in **algemene beschikbaarheid.** Algemene beschikbaarheid (GA) is de definitieve releasestatus en de functionaliteit is volledig en toegankelijk voor alle gebruikers.

## <a name="migration-scenario-support"></a>Ondersteuning voor migratiescenario's

De volgende tabellen laten zien welke migratiescenario's worden ondersteund bij het gebruik van Azure Database Migration Service.

> [!NOTE]
> Als een scenario dat hieronder wordt vermeld, niet wordt weergegeven in de gebruikersinterface, neemt u contact op met de alias [Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) vragen voor meer informatie.

> [!IMPORTANT]
> Als u alle scenario's wilt bekijken die momenteel worden Azure Database Migration Service in Private Preview, gaat u naar de [DMS Preview-site.](https://aka.ms/dms-preview)

### <a name="offline-one-time-migration-support"></a>Ondersteuning voor offlinemigratie (een keer)

In de volgende tabel ziet Azure Database Migration Service ondersteuning voor offlinemigraties.

| Doel  | Bron | Ondersteuning | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL Database** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | RDS SQL | X |  |
|   | Oracle | X |   |
| **Azure SQL-VM** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Algemene beschikbaarheid |
| **Azure DB voor MySQL** | MySQL | ✔ |   |
|   | RDS MySQL | X |   |
| **Azure DB for PostgreSQL - Enkele server** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB for PostgreSQL - Hyperscale (Citus)** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>Ondersteuning voor onlinemigratie (continue synchronisatie)

In de volgende tabel ziet Azure Database Migration Service ondersteuning voor onlinemigraties.

| Doel  | Bron | Ondersteuning | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL Database** | SQL Server | X |  |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL-VM** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Algemene beschikbaarheid |
| **Azure DB voor MySQL** | MySQL | ✔ | Algemene beschikbaarheid |
|   | RDS MySQL | ✔ | Algemene beschikbaarheid |
| **Azure DB for PostgreSQL - Enkele server** | PostgreSQL | ✔ | Algemene beschikbaarheid |
|   | Azure DB for PostgreSQL - Enkele server | ✔ | Algemene beschikbaarheid |
|   | RDS PostgreSQL | ✔ | Algemene beschikbaarheid |
|   | Oracle | ✔ | Openbare preview (wordt afgeschaft na 1 mei 2021) |
| **Azure DB for PostgreSQL : Hyperscale (Citus)** | PostgreSQL | ✔ | Algemene beschikbaarheid |
|   | RDS PostgreSQL | ✔ | Algemene beschikbaarheid |

> [!IMPORTANT]
> 'Oracle to Azure Database for PostgreSQL'-migratiescenario (momenteel in preview) is niet meer beschikbaar na 1 mei 2021. We blijven ondersteuning bieden via alternatieve hulpprogramma's (zoals Ora2pg) en bieden de beste migratie-ervaring voor Migraties van Oracle naar PostgreSQL. Zie Oracle to [Azure Database for PostgreSQL migration guide (Oracle to Azure Database for PostgreSQL migratiehandleiding) voor de best practices voor migratie.](https://aka.ms/OracletoPGguide)


## <a name="next-steps"></a>Volgende stappen

Zie het artikel Wat [is](dms-overview.md)Azure Database Migration Service voor een overzicht van de Azure Database Migration Service.
