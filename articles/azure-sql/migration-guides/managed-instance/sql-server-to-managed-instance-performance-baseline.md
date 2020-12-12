---
title: 'SQL Server naar een door SQL beheerd exemplaar: prestatie analyse'
description: Meer informatie over het maken en vergelijken van een basis lijn voor prestaties bij het migreren van uw SQL Server-data bases naar Azure SQL Managed instance.
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: 2b6f9bef445cc07a3aa00377b41010a536855935
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358857"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>Migratie prestaties: SQL Server van de prestatie analyse van SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Maak een basis lijn voor prestaties om de prestaties van uw workload te vergelijken met een SQL-beheerd exemplaar met de oorspronkelijke werk belasting die wordt uitgevoerd op SQL Server. 

## <a name="create-a-baseline"></a>Een basis lijn maken

In het ideale geval is de prestaties gelijk of beter na de migratie. het is dus belang rijk om de basislijn prestatie waarden te meten en op te nemen in de bron en deze vervolgens te vergelijken met de doel omgeving. Een basis lijn voor prestaties is een set para meters die uw gemiddelde werk belasting op uw bron definiëren. 

Selecteer een set query's die belang rijk zijn voor en representatief zijn voor de werk belasting van uw bedrijf. Meet en Documenteer de minimale/gemiddelde/maximale duur en het CPU-gebruik voor deze query's en de prestatie gegevens op de bron server, zoals gemiddeld/Maxi maal CPU-gebruik, gemiddelde/maximale schijf-i/o-latentie, door Voer, IOPS, gemiddelde/maximale pagina levens duur verwachting en gemiddelde maximale grootte van tempdb. 

De volgende bronnen kunnen helpen bij het definiëren van een basis lijn voor prestaties: 

   - [CPU-gebruik bewaken ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Geheugen gebruik](/sql/relational-databases/performance-monitor/monitor-memory-usage)   bewaken en bepalen de hoeveelheid geheugen die wordt gebruikt door verschillende onderdelen, zoals de buffer groep, de plannings cache, de column-Store-groep, de [in-Memory OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage), enzovoort. Bovendien moet u de gemiddelde en piek waarden voor het prestatie meter item pagina-levens verwachting geheugen vinden. 
   - Controleer het gebruik van de schijf-i/o op het bron SQL Server exemplaar met behulp van de [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)   weer gave of [prestatie meter items](/sql/relational-databases/performance-monitor/monitor-disk-usage). 
   - Bewaak de werk belasting en query prestaties door dynamische beheer weergaven (of query Store te controleren als u migreert van SQL Server 2016 en hoger). Bepaal de gemiddelde duur en het CPU-gebruik van de belangrijkste query's in uw werk belasting. 

Prestatie problemen op de bron SQL Server moeten worden verholpen vóór de migratie. Het migreren van bekende problemen naar een nieuw systeem kan leiden tot onverwachte resultaten en een vergelijking van de prestaties ongeldig maken. 


## <a name="compare-performance"></a>Prestaties vergelijken 

Nadat u een basis lijn hebt gedefinieerd, vergelijkt u vergelijk bare werk belasting prestaties op het doel-SQL beheerde exemplaar. Voor nauw keurigheid is het belang rijk dat de SQL Managed instance-omgeving zoveel mogelijk vergelijkbaar is met de SQL Server omgeving. 

Er zijn verschillen in de infra structuur van SQL Managed instance die de prestaties precies onwaarschijnlijk maken. Sommige query's worden mogelijk sneller uitgevoerd dan verwacht, terwijl anderen mogelijk langzamer zijn. Het doel van deze vergelijking is te controleren of de prestaties van de werk belasting in het beheerde exemplaar overeenkomen met de prestaties op SQL Server (gemiddeld) en om essentiële query's te identificeren die niet overeenkomen met de oorspronkelijke prestaties. 

Prestatie vergelijking resulteert waarschijnlijk in de volgende resultaten: 

- De prestaties van de werk belasting op het beheerde exemplaar zijn uitgelijnd of beter dan de prestaties van de werk belasting op uw bron SQL Server. In dit geval hebt u bevestigd dat de migratie is voltooid. 

- Het meren deel van de prestatie parameters en query's in de werk belasting wordt uitgevoerd zoals verwacht, met enkele uitzonde ringen die de prestaties verslechteren. In dit geval kunt u de verschillen en hun belang identificeren. Als er sprake is van een aantal belang rijke query's met verminderde prestaties, onderzoekt u of de onderliggende SQL-abonnementen zijn gewijzigd of dat query's gemeend zijn voor de resource limieten. U kunt dit verhelpen door bepaalde hints toe te passen op essentiële query's (bijvoorbeeld compatibiliteits niveau wijzigen, verouderde kardinaliteit Estimator) hetzij rechtstreeks hetzij met behulp van plan richtlijnen. Zorg ervoor dat de statistieken en indexen in beide omgevingen up-to-date en equivalent zijn. 

- De meeste query's zijn langzamer op een beheerd exemplaar vergeleken met uw bron SQL Server exemplaar. In dit geval kunt u proberen om de hoofd oorzaken van het verschil te identificeren, zoals het [bereiken van een bepaalde resource limiet](../../managed-instance/resource-limits.md#service-tier-characteristics) , zoals i/o-, geheugen-en instantie logboek frequentie limieten. Als er geen resource limieten zijn die het verschil veroorzaken, kunt u proberen om het compatibiliteits niveau van de data base te wijzigen of data base-instellingen te wijzigen, zoals verouderde kardinaliteit en de test opnieuw uit te voeren. Bekijk de aanbevelingen van het beheerde exemplaar of de query Store-weer gaven om de query's met teruggedraaide-prestaties te identificeren. 

SQL Managed instance heeft een ingebouwde functie voor het automatisch corrigeren van plannen die standaard is ingeschakeld. Deze functie zorgt ervoor dat query's die in het verleden goed werken, niet in de toekomst worden afgebroken. Als deze functie niet is ingeschakeld, voert u de workload uit met de oude instellingen zodat SQL Managed instance de prestaties van de basis lijn kan ontdekken. Schakel vervolgens de functie in en voer de workload opnieuw uit met de nieuwe instellingen. 

Breng wijzigingen aan in de para meters van uw test of voer een upgrade uit naar hogere service lagen om de optimale configuratie voor de prestaties van de werk belasting te bereiken die aan uw behoeften voldoet. 

## <a name="monitor-performance"></a>Prestaties bewaken 

SQL Managed instance biedt geavanceerde hulpprogram ma's voor bewaking en probleem oplossing, en u moet ze gebruiken om de prestaties van uw exemplaar te bewaken. Enkele belang rijke metrische gegevens die u kunt bewaken zijn: 

- Het CPU-gebruik van de instantie om te bepalen of het aantal vCores dat u hebt ingericht, het juiste resultaat is voor uw werk belasting. 
- Pagina-Life verwachting op uw beheerde instantie om te bepalen of u [meer geheugen](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)nodig hebt.
-  Statistische gegevens als INSTANCE_LOG_GOVERNOR of PAGEIOLATCH die opslag-i/o-problemen identificeren, met name op de Algemeen-laag, waar u mogelijk bestanden vooraf moet toewijzen om betere IO-prestaties te krijgen. 


## <a name="considerations"></a>Overwegingen  

Houd rekening met het volgende wanneer u de prestaties vergelijkt: 

- De instellingen van de bron en het doel komen overeen. Controleer of de verschillende instanties, data bases en tempdb-instellingen overeenkomen tussen de twee omgevingen. Verschillen in de configuratie, compatibiliteits niveaus, versleutelings instellingen, traceer vlaggen enz., kunnen de prestaties van het hellen afnemen. 

- Opslag is geconfigureerd volgens [Best practices](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525). Voor Algemeen moet u mogelijk de grootte van de bestanden bijvoorbeeld vooraf toewijzen om de prestaties te verbeteren. 

- Er zijn [belang rijke omgevings verschillen](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) die de prestatie verschillen tussen een beheerd exemplaar en SQL Server kunnen veroorzaken. Identificeer de Risico's die relevant zijn voor uw omgeving en die kunnen bijdragen aan een prestatie probleem. 

- Query Store en automatische afstemming moeten worden ingeschakeld op uw door SQL beheerde instantie wanneer u de prestaties van de werk belasting meet en mogelijke prestatie problemen automatisch kunt oplossen. 



## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het optimaliseren van uw nieuwe Azure SQL Managed instance-omgeving: 

- [Hoe kan ik vaststellen waarom de prestaties van de werk belasting van een Azure SQL Managed instance afwijkt van SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [De belangrijkste oorzaken van prestatie verschillen tussen het beheerde exemplaar van SQL en SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Aanbevolen procedures voor opslag prestaties en overwegingen voor Azure SQL Managed instance (Algemeen)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Real-time prestatie bewaking voor Azure SQL Managed instance (dit wordt gearchiveerd, is dit het beoogde doel?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)