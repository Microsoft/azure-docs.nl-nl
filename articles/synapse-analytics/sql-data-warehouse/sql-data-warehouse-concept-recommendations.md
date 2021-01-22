---
title: Aanbevelingen voor de toegewezen SQL-groep Azure Advisor
description: Meer informatie over Synapse SQL-aanbevelingen en hoe deze worden gegenereerd
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 6510a7143e78c054130a1f1dddba78342a6785fe
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685273"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Aanbevelingen voor Azure Advisor voor toegewezen SQL-groep in azure Synapse Analytics

In dit artikel worden de aanbevelingen voor de exclusieve SQL-groep beschreven die beschikbaar zijn in Azure Advisor.  

Exclusieve SQL-groep biedt aanbevelingen om ervoor te zorgen dat de werk belasting van uw data warehouse consistent is geoptimaliseerd voor prestaties. Aanbevelingen worden nauw geïntegreerd met [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om u te voorzien van best practices direct binnen de [Azure Portal](https://aka.ms/Azureadvisor). De toegewezen SQL-groep verzamelt telemetrie-en Opper vlakken aanbevelingen voor uw actieve workload op een dagelijks uitgebracht. De ondersteunde aanbevelings scenario's worden hieronder beschreven, samen met het Toep assen van aanbevolen acties.

U kunt [uw aanbevelingen](https://aka.ms/Azureadvisor) vandaag nog controleren. 

## <a name="data-skew"></a>Ongelijkheid in gegevens

Gegevens scheefheid kan leiden tot extra gegevens bewegingen of bron knelpunten wanneer uw werk belasting wordt uitgevoerd. In de volgende documentatie wordt beschreven hoe u het scheef trekken van gegevens identificeert en voor komt dat dit gebeurt door een optimale distributie sleutel te selecteren.

- [Scheefheid identificeren en verwijderen](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Geen of verouderde statistieken

Het uitvoeren van suboptimale statistieken kan een grote invloed hebben op de query prestaties, omdat de SQL-query Optimizer zo kan leiden dat er suboptimale query plannen worden gegenereerd. De volgende documentatie bevat informatie over de aanbevolen procedures voor het maken en bijwerken van statistieken:

- [Tabel statistieken maken en bijwerken](sql-data-warehouse-tables-statistics.md)

Voer het volgende  [T-SQL-script](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)uit om de lijst met betrokken tabellen te bekijken met deze aanbevelingen. Advisor voert continu hetzelfde T-SQL-script uit om deze aanbevelingen te genereren.

## <a name="replicate-tables"></a>Tabellen repliceren

Voor de aanbevelingen van een gerepliceerde tabel, adviseur detecteert de kandidaten van een tabel op basis van de volgende fysieke kenmerken:

- Grootte van gerepliceerde tabel
- Aantal kolommen
- Type tabel distributie
- Aantal partities

Advisor maakt continu gebruik van heuristiek op basis van werk belastingen, zoals de frequentie van tabel toegang, rijen die gemiddeld worden geretourneerd en drempel waarden rond Data Warehouse-grootte en-activiteit, om ervoor te zorgen dat aanbevelingen van hoge kwaliteit worden gegenereerd.

In de volgende sectie vindt u een beschrijving van de heuristiek op basis van werk belastingen die u kunt vinden in de Azure Portal voor elke gerepliceerde tabel aanbeveling:

- Controle Gem: het gemiddelde aantal rijen dat is geretourneerd uit de tabel voor elke toegang tot de tabel gedurende de afgelopen zeven dagen
- Veelvuldig gelezen: geen update: geeft aan dat de tabel in de afgelopen zeven dagen niet is bijgewerkt terwijl de toegangs activiteit wordt weer gegeven
- Verhouding van lezen/bijwerken: de verhouding van hoe vaak de tabel is geopend ten opzichte van wanneer deze in de afgelopen zeven dagen wordt bijgewerkt
- Activiteit: Hiermee wordt het gebruik gemeten op basis van de toegangs activiteit. Met deze activiteit wordt de activiteit voor de tabel toegang vergeleken met de gemiddelde activiteit van de tabel toegang in het data warehouse in de afgelopen zeven dagen.

Momenteel worden in Advisor alleen Maxi maal vier gerepliceerde tabel kandidaten tegelijk met geclusterde column Store-indexen met de prioriteit van de hoogste activiteit weer gegeven.

> [!IMPORTANT]
> De aanbeveling van de gerepliceerde tabel is geen volledige controle en houdt geen rekening met het verplaatsen van gegevens. We werken eraan om dit als een heuristisch toe te voegen, maar ondertussen moet u uw workload altijd valideren nadat u de aanbeveling hebt toegepast. Raadpleeg de volgende [documentatie](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)voor meer informatie over gerepliceerde tabellen.


## <a name="adaptive-gen2-cache-utilization"></a>Adaptief (Gen2) cache gebruik
Wanneer u een grote werkset hebt, kunt u een lage cache treffer percentage en een hoog cache gebruik ervaren. Voor dit scenario moet u omhoog schalen om de cache capaciteit te verhogen en de workload opnieuw uit te voeren. Raadpleeg de volgende [documentatie](./sql-data-warehouse-how-to-monitor-cache.md)voor meer informatie. 

## <a name="tempdb-contention"></a>TempDB-conflicten

De prestaties van query's kunnen afnemen wanneer er hoge TempDB-conflicten zijn.  TempDB-conflicten kunnen optreden via door de gebruiker gedefinieerde tijdelijke tabellen of wanneer er sprake is van een grote hoeveelheid gegevens verplaatsing. Voor dit scenario kunt u schalen voor meer TempDB-toewijzing en [resource klassen en werkbelasting beheer configureren](./sql-data-warehouse-workload-management.md) om meer geheugen te bieden voor uw query's. 

## <a name="data-loading-misconfiguration"></a>Onjuiste configuratie van gegevens laden

U moet altijd gegevens laden van een opslag account in dezelfde regio als uw toegewezen SQL-groep om latentie te minimaliseren. Gebruik de [instructie Copy voor gegevens opname met hoge door Voer](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) en Splits uw gefaseerde bestanden in uw opslag account om de door voer te maximaliseren. Als u de instructie COPY niet kunt gebruiken, gebruikt u de SqlBulkCopy-API of BCP met een hoge Batch grootte voor een betere door voer. Raadpleeg de volgende [documentatie](./guidance-for-loading-data.md)voor meer informatie over het laden van gegevens.