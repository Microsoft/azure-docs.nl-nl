---
title: Wat is een enkele data base?
description: Meer informatie over het resource type van één data base in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343316"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Wat is een enkele data base in Azure SQL Database?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Het resource type voor één data base maakt een data base in Azure SQL Database met een eigen set resources en wordt beheerd via een [Server](logical-servers.md). Met één data base is elke Data Base geïsoleerd en draagbaar. Elk heeft een eigen servicelaag in het [op DTU gebaseerde aankoop model](service-tiers-dtu.md) of [op vCore gebaseerde aankoop model](service-tiers-vcore.md) en een gegarandeerde reken grootte.

> [!IMPORTANT]
> Eén data base is een resource type voor Azure SQL Database. De andere is [elastische Pools](elastic-pool-overview.md).

## <a name="dynamic-scalability"></a>Dynamische schaalbaarheid

U kunt uw eerste app bouwen op een kleine, afzonderlijke data base tegen lage kosten in de serverloze Compute-laag of een kleine reken grootte in de ingerichte Compute-laag. U kunt de [Compute-of servicelaag](single-database-scale.md) hand matig of via een programma op elk gewenst moment wijzigen om te voldoen aan de behoeften van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Dankzij dynamische schaalbaarheid kan uw database op een transparante manier snel reageren op veranderende resourcevereisten en betaalt u alleen voor de resources die u nodig hebt wanneer u ze nodig.

## <a name="single-databases-and-elastic-pools"></a>Individuele databases en elastische pools

U kunt één data base verplaatsen naar of van een [elastische pool](elastic-pool-overview.md) voor het delen van resources. Voor veel bedrijven en toepassingen is het kunnen maken van enkele databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Bij onvoorspelbare gebruikspatronen kan het echter lastig zijn uw kosten en bedrijfsmodel effectief te beheren. Elastische pools zijn ontworpen om dit probleem te verhelpen. Het concept is eenvoudig. U wijst prestatie resources toe aan een pool in plaats van een afzonderlijke data base en betaalt voor de collectieve prestaties van de pool in plaats van voor de prestaties van één data base.

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen

U gebruikt de ingebouwde [hulpprogram ma's](alerts-insights-configure-portal.md)voor [prestatie bewaking](performance-guidance.md) en waarschuwingen, gecombineerd met de prestatie classificaties. Met behulp van deze tools kunt u snel beoordelen wat de impact is van het aanpassen van de schaal op basis van uw huidige prestatiebehoeften of de prestatiebehoeften van uw project. Daarnaast kan SQL Database [metrische gegevens en resourcelogboeken verzenden](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) die bewaking eenvoudiger maken.

## <a name="availability-capabilities"></a>Beschikbaarheid

Afzonderlijke data bases en elastische Pools bieden veel beschik baarheids kenmerken. Zie [beschikbaarheids kenmerken](sql-database-paas-overview.md#availability-capabilities)voor meer informatie.

## <a name="transact-sql-differences"></a>Verschillen in Transact-SQL

De meeste Transact-SQL-functies die toepassingen gebruiken, worden volledig ondersteund in zowel Microsoft SQL Server als Azure SQL Database. De belangrijkste SQL-onderdelen, zoals gegevens typen, Opera Tors, teken reeks, reken kundige, logische en cursor functies, werken bijvoorbeeld hetzelfde in SQL Server en SQL Database. Er zijn echter enkele T-SQL-verschillen in DDL-elementen (Data Definition Language) en DML (Data Manipulation Language) die resulteren in T-SQL-instructies en query's die slechts gedeeltelijk worden ondersteund (wat verderop in dit artikel wordt besproken).

Bovendien zijn er enkele functies en syntaxis die niet worden ondersteund omdat Azure SQL Database is ontworpen om functies te isoleren op basis van afhankelijkheden van de hoofd database en het besturings systeem. Daarom zijn de meeste activiteiten op server niveau niet geschikt voor SQL Database. T-SQL-instructies en-opties zijn niet beschikbaar als ze opties op server niveau configureren, onderdelen van het besturings systeem configureren of de bestandssysteem configuratie opgeven. Wanneer dergelijke mogelijkheden vereist zijn, is een geschikt alternatief op een andere manier vaak beschikbaar vanaf SQL Database of vanuit een andere Azure-functie of-service.

Zie voor meer informatie [Transact-SQL-verschillen oplossen tijdens de migratie naar SQL database](transact-sql-tsql-differences-sql-server.md).

## <a name="security"></a>Beveiliging

SQL Database biedt een reeks ingebouwde functies voor [beveiliging en naleving](security-overview.md) om uw toepassing te helpen voldoen aan verschillende vereisten op het gebied van beveiliging en naleving.

> [!IMPORTANT]
> Azure SQL Database is gecertificeerd op basis van een aantal nalevings standaarden. Raadpleeg het [Vertrouwenscentrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) voor meer informatie. Hier vindt u de meest recente lijst met SQL Database-nalevingscertificeringen.

## <a name="next-steps"></a>Volgende stappen

- Als u snel aan de slag wilt gaan met één data base, begint u met de [Snelstartgids voor één data base](quickstart-content-reference-guide.md).
- Zie [migreren naar Azure SQL database](migrate-to-database-from-sql-server.md)voor meer informatie over het migreren van een SQL Server Data Base naar Azure.
- Zie [functies](features-comparison.md)voor meer informatie over ondersteunde functies.
