---
title: Verklarende woordenlijst
titleSuffix: Azure SQL
description: Een verklarende woorden lijst voor het werken met Azure SQL Database, Azure SQL Managed instance en SQL op Azure VM.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/09/2020
ms.openlocfilehash: 8bd1e312463cf89ae30d54bc4a32a497fc7a9a78
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249649"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Woorden lijst met termen Azure SQL Database
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Context|Term|Meer informatie|
|:---|:---|:---|
|Azure-service|Azure SQL Database of SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|Aankoopmodel|Op DTU gebaseerd inkoop model|[Op DTU gebaseerd inkoop model](database/service-tiers-dtu.md)|
||Aankoopmodel op basis van vCore|[Aankoopmodel op basis van vCore](database/service-tiers-vcore.md)|
|Implementatie optie |Individuele database|[Individuele databases](database/single-database-overview.md)|
||Elastische pool|[Elastische pool](database/elastic-pool-overview.md)|
|Servicelaag|Basic, Standard, Premium, Algemeen, grootschalige, Bedrijfskritiek|Zie [SQL database service](database/service-tiers-vcore.md#service-tiers)lagen voor service lagen in het vCore-model. Zie [DTU-model](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)voor service lagen in het DTU-model.|
|Compute-laag|Serverloze compute|[Serverloze compute](database/service-tiers-vcore.md#compute-tiers)
||Ingerichte compute|[Ingerichte compute](database/service-tiers-vcore.md#compute-tiers)
|Compute genereren|GEN5, M-serie, Fsv2-serie, DC-serie|[Hardware gegenereerd](database/service-tiers-vcore.md#hardware-generations)
|Server entiteit| Server |[Logische SQL-servers](database/logical-servers.md)|
|Resourcetype|vCore|Er is een CPU-kern gegeven aan de reken resource voor één data base, elastische pool. |
||Berekenings grootte en opslag hoeveelheid|De berekenings grootte is de maximale hoeveelheid CPU, geheugen en andere niet-opslag gerelateerde resources die beschikbaar zijn voor één data base of elastische pool.  Opslag grootte is de maximale hoeveelheid opslag ruimte die beschikbaar is voor één data base of elastische pool. Zie [vCore single data bases](database/resource-limits-vcore-single-databases.md)en [vCore elastische Pools](database/resource-limits-vcore-elastic-pools.md)voor meer informatie over de grootte van het vCore-model.  (.. /managed-instance/resource-limits.md).  Zie voor de grootte opties in het DTU-model [DTU single data bases](database/resource-limits-dtu-single-databases.md) en [DTU elastische Pools](database/resource-limits-dtu-elastic-pools.md).

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

|Context|Term|Meer informatie|
|:---|:---|:---|
|Azure-service|Azure SQL Managed Instance|[SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md)|
|Aankoopmodel|Aankoopmodel op basis van vCore|[Aankoopmodel op basis van vCore](database/service-tiers-vcore.md)|
|Implementatie optie |Eén exemplaar|[Eén exemplaar](managed-instance/sql-managed-instance-paas-overview.md)|
||Exemplaar groep (preview-versie)|[Exemplaargroepen](managed-instance/instance-pools-overview.md)|
|Servicelaag|Algemeen, Bedrijfskritiek|[Service lagen van SQL Managed instance](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Compute-laag|Ingerichte compute|[Ingerichte compute](database/service-tiers-vcore.md#compute-tiers)|
|Compute genereren|GEN5|[Hardware gegenereerd](database/service-tiers-vcore.md#hardware-generations)
|Server entiteit|Beheerd exemplaar of exemplaar| N.v.t. omdat het SQL Managed instance zich op zichzelf bevindt de server |
|Resourcetype|vCore|Er is een CPU-kern gegeven aan de reken resource voor SQL Managed instance.|
||Berekenings grootte en opslag hoeveelheid|De berekenings grootte is de maximale hoeveelheid CPU, geheugen en andere resources die geen betrekking hebben op opslag voor SQL Managed instance.  Opslag grootte is de maximale hoeveelheid opslag ruimte die beschikbaar is voor een SQL Managed instance.  Voor de grootte opties, [SQL Managed instances](managed-instance/resource-limits.md). |
