---
title: 'SQL Server naar Azure SQL Managed Instance: Prestatiebasislijn'
description: Leer hoe u een basislijn voor prestaties maakt en vergelijkt wanneer u uw SQL Server databases naar Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: a47684bf29f1f34b8c9c59c04b7d33d234505cc2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389703"
---
# <a name="migration-performance-sql-server-to--azure-sql-managed-instance-performance-baseline"></a>Migratieprestaties: SQL Server basislijn Azure SQL Managed Instance prestaties
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Maak een basislijn voor prestaties om de prestaties van uw workload op een SQL Managed Instance te vergelijken met de oorspronkelijke workload die wordt uitgevoerd op SQL Server. 

## <a name="create-a-baseline"></a>Een basislijn maken

In het ideale ideale moment zijn de prestaties na de migratie vergelijkbaar of beter. Het is dus belangrijk om de prestatiewaarden van de basislijn op de bron te meten en vast te stellen en deze vervolgens te vergelijken met de doelomgeving. Een prestatiebasislijn is een set parameters die uw gemiddelde workload voor uw bron definiëren. 

Selecteer een reeks query's die belangrijk zijn voor en die representatief zijn voor uw zakelijke workload. Meet en documenteert de minimale/gemiddelde/maximale duur en het CPU-gebruik voor deze query's, evenals metrische prestatiegegevens op de bronserver, zoals gemiddelde/maximale CPU-gebruik, gemiddelde/maximale schijf-I/O-latentie, doorvoer, IOPS, gemiddelde/maximale levensduur van pagina's en gemiddelde maximale grootte van tempdb. 

De volgende resources kunnen helpen bij het definiëren van een basislijn voor prestaties: 

   - [CPU-gebruik bewaken ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Geheugengebruik bewaken](/sql/relational-databases/performance-monitor/monitor-memory-usage)   en bepalen de hoeveelheid geheugen die wordt gebruikt door verschillende onderdelen, zoals buffergroep, plancache, kolomopslaggroep, [In-Memory OLTP,](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)enzovoort. Daarnaast moet u de gemiddelde en piekwaarden van het prestatiemetermeter voor de levensverwachting van pagina's vinden. 
   - Controleer het I/O-gebruik van de SQL Server bronbron met behulp [van](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   sys.dm_io_virtual_file_stats-weergave of [prestatiemeters](/sql/relational-databases/performance-monitor/monitor-disk-usage). 
   - Controleer de workload- en queryprestaties door dynamische beheerweergaven te bekijken (of Query Store als u migreert van SQL Server 2016 en hoger). Identificeer de gemiddelde duur en het CPU-gebruik van de belangrijkste query's in uw workload. 

Prestatieproblemen op de SQL Server moeten vóór de migratie worden opgelost. Het migreren van bekende problemen naar een nieuw systeem kan onverwachte resultaten veroorzaken en een prestatievergelijking ongeldig maken. 


## <a name="compare-performance"></a>Prestaties vergelijken 

Nadat u een basislijn hebt gedefinieerd, vergelijkt u vergelijkbare workloadprestaties op het doelniveau SQL Managed Instance. Voor nauwkeurigheid is het belangrijk dat de SQL Managed Instance omgeving zo veel mogelijk vergelijkbaar is met SQL Server omgeving. 

Er zijn SQL Managed Instance infrastructuurverschillen die overeenkomende prestaties precies onwaarschijnlijk maken. Sommige query's kunnen sneller worden uitgevoerd dan verwacht, terwijl andere mogelijk langzamer zijn. Het doel van deze vergelijking is om te controleren of de prestaties van de workload in het beheerde exemplaar overeenkomen met de prestaties op SQL Server (gemiddeld) en om kritieke query's te identificeren met prestaties die niet overeenkomen met uw oorspronkelijke prestaties. 

Prestatievergelijking resulteert waarschijnlijk in de volgende resultaten: 

- De prestaties van de workload op het beheerde exemplaar zijn uitgelijnd of beter dan de prestaties van de werkbelasting op uw SQL Server. In dit geval hebt u bevestigd dat de migratie is geslaagd. 

- De meeste prestatieparameters en query's in de workload werken zoals verwacht, met enkele uitzonderingen die leiden tot slechtere prestaties. In dit geval moet u de verschillen en hun belang identificeren. Als er enkele belangrijke query's met slechtere prestaties zijn, moet u onderzoeken of de onderliggende SQL-plannen zijn gewijzigd of dat query's resourcelimieten overschrijden. U kunt dit verhelpen door een aantal hints toe te passen op kritieke query's (bijvoorbeeld wijzigingscompatibiliteitsniveau, verouderde kardinaliteitsschaker) rechtstreeks of met behulp van planhandleidingen. Zorg ervoor dat statistieken en indexen in beide omgevingen up-to-date en gelijkwaardig zijn. 

- De meeste query's zijn langzamer op een beheerd exemplaar in vergelijking met uw bron-SQL Server exemplaar. Probeer in dit geval de hoofdoorzaken van het verschil te identificeren, zoals het bereiken van een [resourcelimiet,](../../managed-instance/resource-limits.md#service-tier-characteristics) zoals IO, geheugen of logboekfrequentielimieten voor exemplaren. Als er geen resourcelimieten zijn die het verschil veroorzaken, kunt u het compatibiliteitsniveau van de database wijzigen of database-instellingen wijzigen, zoals schatting van verouderde kardinaliteit, en de test opnieuw uitvoeren. Bekijk de aanbevelingen van het beheerde exemplaar of de Query Store-weergaven om de query's met teruggeslagen prestaties te identificeren. 

SQL Managed Instance heeft een ingebouwde functie voor automatische correctie van plannen die standaard is ingeschakeld. Deze functie zorgt ervoor dat query's die in het verleden goed hebben gewerkt, in de toekomst niet verslechteren. Als deze functie niet is ingeschakeld, moet u de workload uitvoeren met de oude instellingen, zodat SQL Managed Instance de prestatiebasislijn kunt leren. Schakel vervolgens de functie in en voer de workload opnieuw uit met de nieuwe instellingen. 

Wijzigingen aanbrengen in de parameters van uw test of upgraden naar hogere servicelagen om de optimale configuratie te bereiken voor de prestaties van de werkbelasting die bij uw behoeften passen. 

## <a name="monitor-performance"></a>Prestaties bewaken 

SQL Managed Instance biedt geavanceerde hulpprogramma's voor bewaking en probleemoplossing, en u moet deze gebruiken om de prestaties van uw exemplaar te bewaken. Enkele van de belangrijkste metrische gegevens die moeten worden bewaakt, zijn: 

- CPU-gebruik op het exemplaar om te bepalen of het aantal vCores dat u hebt ingericht, de juiste overeenkomst is voor uw workload. 
- De levensverwachting van pagina's op uw beheerde exemplaar om te bepalen of u extra [geheugen nodig hebt.](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)
-  Statistieken zoals INSTANCE_LOG_GOVERNOR of PAGEIOLATCH die problemen met opslag-I/O identificeren, met name op de Algemeen-laag, waar u mogelijk vooraf bestanden moet toewijzen om betere I/O-prestaties te krijgen. 


## <a name="considerations"></a>Overwegingen  

Houd bij het vergelijken van de prestaties rekening met het volgende: 

- Instellingen komen overeen tussen bron en doel. Controleer of verschillende exemplaar-, database- en tempdb-instellingen gelijkwaardig zijn tussen de twee omgevingen. Verschillen in configuratie, compatibiliteitsniveaus, versleutelingsinstellingen, traceringsvlaggen, enzovoort, kunnen allemaal scheeftrekken. 

- Opslag wordt geconfigureerd volgens [best practices.](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525) Voor een Algemeen moet u bijvoorbeeld vooraf de grootte van de bestanden toewijzen om de prestaties te verbeteren. 

- Er zijn [belangrijke omgevingsverschillen](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) die de prestatieverschillen tussen een beheerd exemplaar en een SQL Server. Identificeer risico's die relevant zijn voor uw omgeving die kunnen bijdragen aan een prestatieprobleem. 

- Query Store en automatisch afstemmen moeten zijn ingeschakeld op uw SQL Managed Instance omdat deze u helpen de prestaties van de werkbelasting te meten en potentiële prestatieproblemen automatisch te verhelpen. 



## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over het optimaliseren Azure SQL Managed Instance nieuwe omgeving: 

- [Hoe kan ik bepalen waarom de prestaties van workloads op Azure SQL Managed Instance anders zijn dan SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Belangrijke oorzaken van prestatieverschillen tussen SQL Managed Instance en SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Best practices en overwegingen voor opslagprestaties voor Azure SQL Managed Instance (Algemeen)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Realtime prestatiebewaking voor Azure SQL Managed Instance (dit is gearchiveerd, is dit het beoogde doel?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)