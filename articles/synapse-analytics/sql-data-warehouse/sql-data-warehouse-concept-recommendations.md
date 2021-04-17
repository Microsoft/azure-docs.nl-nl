---
title: Aanbevelingen voor toegewezen SQL Azure Advisor pools
description: Meer informatie Synapse SQL aanbevelingen en hoe deze worden gegenereerd
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b418b46199c524ca92d60dece6031073938e159b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568417"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Advisor aanbevelingen voor toegewezen SQL-pool in Azure Synapse Analytics

In dit artikel worden de aanbevelingen voor toegewezen SQL-pool beschreven die beschikbaar zijn in Azure Advisor.  

Toegewezen SQL-pool biedt aanbevelingen om ervoor te zorgen dat de workload van uw datawarehouse consistent is geoptimaliseerd voor prestaties. Aanbevelingen zijn nauw geïntegreerd met Azure Advisor [om](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) u rechtstreeks in de Azure Portal [.](https://aka.ms/Azureadvisor) Een toegewezen SQL-pool verzamelt telemetrie en doet dagelijks aanbevelingen voor uw actieve workload. De ondersteunde aanbevelingsscenario's worden hieronder beschreven, samen met het toepassen van aanbevolen acties.

U kunt [uw aanbevelingen vandaag](https://aka.ms/Azureadvisor) nog controleren. 

## <a name="data-skew"></a>Ongelijkheid in gegevens

Gegevensverschil kan leiden tot extra verplaatsing van gegevens of resourceknelpunten bij het uitvoeren van uw workload. In de volgende documentatie wordt beschreven hoe u gegevensverschil identificeert en voorkomt dat deze optreden door een optimale distributiesleutel te selecteren.

- [Scheefheid identificeren en verwijderen](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Geen of verouderde statistieken

Het gebruik van suboptimale statistieken kan de prestaties van query's sterk beïnvloeden, omdat dit ertoe kan leiden dat de SQL-queryoptimaler suboptimale queryplannen genereert. In de volgende documentatie worden de best practices beschreven voor het maken en bijwerken van statistieken:

- [Tabelstatistieken maken en bijwerken](sql-data-warehouse-tables-statistics.md)

Voer het volgende  [T-SQL-script](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)uit om de lijst met beïnvloede tabellen met deze aanbevelingen weer te geven. Advisor voert continu hetzelfde T-SQL-script uit om deze aanbevelingen te genereren.

## <a name="replicate-tables"></a>Tabellen repliceren

Voor aanbevelingen voor gerepliceerde tabel detecteert Advisor tabelkandidaten op basis van de volgende fysieke kenmerken:

- Grootte van gerepliceerde tabel
- Aantal kolommen
- Tabeldistributietype
- Aantal partities

Advisor maakt voortdurend gebruik van op workloads gebaseerde heuristieken, zoals de toegangsfrequentie voor de tabel, rijen die gemiddeld worden geretourneerd en drempelwaarden voor de grootte en activiteit van datawarehouses om ervoor te zorgen dat aanbevelingen van hoge kwaliteit worden gegenereerd.

In de volgende sectie worden de op workloads gebaseerde heuristiek beschreven die u kunt vinden in de Azure Portal voor elke aanbeveling voor gerepliceerde tabel:

- Gemiddelde scannen: het gemiddelde percentage rijen dat is geretourneerd uit de tabel voor elke tabeltoegang gedurende de afgelopen zeven dagen
- Regelmatig lezen, geen update: geeft aan dat de tabel de afgelopen zeven dagen niet is bijgewerkt terwijl de toegangsactiviteit wordt weergegeven
- Lees-/updateverhouding: de verhouding tussen hoe vaak de tabel is gebruikt ten opzichte van wanneer deze gedurende de afgelopen zeven dagen wordt bijgewerkt
- Activiteit: meet het gebruik op basis van toegangsactiviteit. Met deze activiteit wordt de activiteit voor tabeltoegang vergeleken met de gemiddelde activiteit voor toegang tot de tabel in het datawarehouse gedurende de afgelopen zeven dagen.

Op dit moment toont Advisor slechts vier gerepliceerde tabelkandidaten tegelijk met geclusterde columnstore-indexen die prioriteit geven aan de hoogste activiteit.

> [!IMPORTANT]
> De aanbeveling voor gerepliceerde tabel is niet volledig bewijs en houdt geen rekening met gegevensverkeersbewerkingen. We werken eraan om dit toe te voegen als een heuristiek, maar in de tussentijd moet u uw workload altijd valideren nadat u de aanbeveling hebt toegepast. Ga naar de volgende documentatie voor meer informatie over gerepliceerde [tabellen.](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)


## <a name="adaptive-gen2-cache-utilization"></a>Gebruik van adaptieve cache (Gen2)
Wanneer u een grote werkset hebt, kunt u een laag percentage cache-treffers en een hoog cachegebruik ervaren. Voor dit scenario moet u omhoog schalen om de cachecapaciteit te vergroten en uw workload opnieuw uit te breiden. Ga voor meer informatie naar de volgende [documentatie.](./sql-data-warehouse-how-to-monitor-cache.md) 

## <a name="tempdb-contention"></a>Tempdb-inhoud

De queryprestaties kunnen verslechteren wanneer er sprake is van een hoog tempdb-aantal.  Tempdb-problemen kunnen optreden via door de gebruiker gedefinieerde tijdelijke tabellen of wanneer er een grote hoeveelheid gegevensver movement is. Voor dit scenario kunt u schalen voor meer tempdb-toewijzing en resourceklassen en [workloadbeheer](./sql-data-warehouse-workload-management.md) configureren om uw query's meer geheugen te bieden. 

## <a name="data-loading-misconfiguration"></a>Onjuiste configuratie van gegevens laden

U moet altijd gegevens laden vanuit een opslagaccount in dezelfde regio als uw toegewezen SQL-pool om latentie te minimaliseren. Gebruik de [COPY-instructie voor](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) gegevensinvoer met hoge doorvoer en splits uw gefaseerd bestanden in uw opslagaccount om de doorvoer te maximaliseren. Als u de COPY-instructie niet kunt gebruiken, kunt u de SqlBulkCopy-API of bcp met een grote batchgrootte gebruiken voor een betere doorvoer. Ga naar de volgende documentatie voor aanvullende richtlijnen voor het laden van [gegevens.](./guidance-for-loading-data.md)