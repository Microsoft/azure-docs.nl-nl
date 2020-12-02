---
title: Resources schalen
description: In dit artikel wordt uitgelegd hoe u uw data base in Azure SQL Database en SQL Managed Instance kunt schalen door toegewezen bronnen toe te voegen of te verwijderen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 453d7e118b946d60eb3d84c6a66abdbea7db2410
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499217"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Database resources dynamisch schalen met minimale downtime
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Met Azure SQL Database en SQL Managed Instance kunt u dynamisch meer resources aan uw data base toevoegen met minimale [downtime](https://azure.microsoft.com/support/legal/sla/sql-database). Er is echter een switch over een periode waarin de verbinding gedurende korte tijd naar de data base verloren gaat, wat kan worden verholpen met de logica voor opnieuw proberen.

## <a name="overview"></a>Overzicht

Wanneer de vraag naar uw app toeneemt van een aantal apparaten en klanten naar miljoenen, wordt de schaal van Azure SQL Database en SQL Managed instance in de vlucht met minimale downtime. Schaal baarheid is een van de belangrijkste kenmerken van platform as a Service (PaaS) waarmee u indien nodig dynamisch meer bronnen aan uw service kunt toevoegen. Met Azure SQL Database kunt u eenvoudig resources (CPU-energie, geheugen, i/o-door Voer en opslag) wijzigen die aan uw data bases zijn toegewezen.

U kunt prestatie problemen verhelpen vanwege een groter gebruik van uw toepassing die niet kan worden hersteld met behulp van methoden voor het opnieuw schrijven van indexeren of query's. Door meer resources toe te voegen, kunt u snel reageren wanneer de data base de huidige limieten voor bronnen heeft bereikt en meer stroom nodig heeft om de binnenkomende werk belasting te verwerken. Met Azure SQL Database kunt u ook de resources omlaag schalen wanneer ze niet nodig zijn om de kosten te verlagen.

U hoeft zich geen zorgen te maken over het aanschaffen van hardware en het wijzigen van de onderliggende infra structuur. Het schalen van een Data Base kan eenvoudig worden uitgevoerd via de Azure Portal met behulp van een schuif regelaar.

![Schaal database prestaties](./media/scale-resources/scale-performance.svg)

Azure SQL Database biedt het [op DTU gebaseerde aankoop model](service-tiers-dtu.md) en het [op vCore gebaseerde aankoop model](service-tiers-vcore.md), terwijl Azure SQL Managed instance alleen het [op vCore gebaseerde aankoop model](service-tiers-vcore.md)biedt. 

- Het [op DTU gebaseerde aankoop model](service-tiers-dtu.md) biedt een combi natie van compute-, geheugen-en I/O-resources in drie service lagen ter ondersteuning van de werk belasting van licht gewicht naar Heavyweight Data Base: Basic, Standard en Premium. Prestatieniveaus binnen elke laag bieden een andere combinatie van deze resources, waaraan u extra opslagbronnen kunt toevoegen.
- [Met het op vCore gebaseerde aankoop model](service-tiers-vcore.md) kunt u het aantal vCores, de hoeveelheid of het geheugen en de hoeveelheid en snelheid van de opslag kiezen. Dit aankoop model biedt drie service lagen: Algemeen, Bedrijfskritiek en grootschalige.

U kunt uw eerste app bouwen op een kleine, afzonderlijke Data Base met een lage prijs per maand in de servicelaag Basic, Standard of Algemeen en vervolgens de servicelaag hand matig of via een programma op elk gewenst moment wijzigen in de service tier Premium of Bedrijfskritiek om te voldoen aan de behoeften van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Dankzij dynamische schaalbaarheid kan uw database op een transparante manier snel reageren op veranderende resourcevereisten en betaalt u alleen voor de resources die u nodig hebt wanneer u ze nodig.

> [!NOTE]
> Dynamische schaalbaarheid is iets anders dan automatisch schalen. Automatisch schalen is wanneer een service wordt geschaald op basis van criteria, terwijl dynamische schaal baarheid hand matig kan worden geschaald met een minimale downtime.

Afzonderlijke data bases in Azure SQL Database ondersteunen hand matige dynamische schaal baarheid, maar niet automatisch schalen. Voor een meer *automatische* ervaring kunt u gebruikmaken van elastische Pools, waarmee data bases bronnen in een pool kunnen delen op basis van de behoeften van individuele data bases.
Er zijn echter scripts waarmee de schaal baarheid voor één data base in Azure SQL Database kan worden geautomatiseerd. Zie [PowerShell gebruiken voor het controleren en schalen van één Azure SQL-database](scripts/monitor-and-scale-database-powershell.md) voor een voorbeeld.

U kunt steeds de [DTU-servicelagen](service-tiers-dtu.md) of [vCore-kenmerken](resource-limits-vcore-single-databases.md) wijzigen met minimale downtime voor de toepassing (meestal minder dan vier seconden). Voor veel bedrijven en apps is het kunnen maken van databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Bij onvoorspelbare gebruikspatronen kan het echter lastig zijn uw kosten en bedrijfsmodel effectief te beheren. Voor dit scenario gebruikt u een elastische pool met een bepaald aantal Edtu's die worden gedeeld door meerdere data bases in de groep.

![Inleiding tot SQL Database: DTU's van individuele database per laag en niveau](./media/scale-resources/single_db_dtus.png)

Azure SQL Database biedt de mogelijkheid om uw data bases dynamisch te schalen:

- Met [één data base](single-database-scale.md)kunt u [DTU](resource-limits-dtu-single-databases.md) -of [vCore](resource-limits-vcore-single-databases.md) -modellen gebruiken om de maximale hoeveelheid resources te definiëren die aan elke Data Base wordt toegewezen.
- Met [elastische Pools](elastic-pool-scale.md) kunt u de maximale resource limiet per groep data bases in de groep definiëren.

Met Azure SQL Managed Instance kunt u ook schalen: 

- [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) maakt gebruik van de modus [vCores](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) en stelt u in staat om Maxi maal CPU-kernen en het maximum aan opslag ruimte te definiëren die aan uw exemplaar zijn toegewezen. Alle data bases in het beheerde exemplaar delen de resources die aan het exemplaar zijn toegewezen.

Het starten van een actie omhoog of omlaag schalen in een van de versies zou het data base-engine proces opnieuw starten en naar een andere virtuele machine verplaatsen, indien nodig. Het proces voor het verplaatsen van de data base-engine naar een nieuwe virtuele machine is **online proces** waar u uw bestaande Azure SQL database-service kunt blijven gebruiken terwijl het proces wordt uitgevoerd. Zodra de engine van de doel database volledig is geïnitialiseerd en gereed is voor het verwerken van de query's, worden de verbindingen [van de bron-naar de doel database-engine overgeschakeld](single-database-scale.md#impact).

> [!NOTE]
> U kunt een korte verbinding verwachten wanneer het omhoog/omlaag schalen proces is voltooid. Als u [pogings logica hebt geïmplementeerd voor standaard tijdelijke fouten](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), zult u de failover niet zien.

## <a name="alternative-scale-methods"></a>Alternatieve schaal methoden

Het schalen van resources is de eenvoudigste en meest efficiënte manier om de prestaties van uw data base te verbeteren zonder de data base of toepassings code te wijzigen. In sommige gevallen is het mogelijk dat zelfs de hoogste service lagen, reken grootten en prestatie optimalisaties uw werk belasting op een succes volle en rendabele manier niet kan verwerken. In dat geval hebt u de volgende extra opties om uw data base te schalen:

- [Uitschalen voor lezen](read-scale-out.md) is een beschik bare functie waarbij u een alleen-lezen replica van uw gegevens krijgt, waar u veeleisende alleen-lezen query's kunt uitvoeren, zoals rapporten. Met een alleen-lezen replica wordt uw alleen-lezen werk belasting afgehandeld zonder het resource gebruik op uw primaire data base te beïnvloeden.
- [Data Base-sharding](elastic-scale-introduction.md) is een reeks technieken waarmee u uw gegevens kunt splitsen in verschillende data bases en deze onafhankelijk kunnen schalen.

## <a name="next-steps"></a>Volgende stappen

- Zie [aanbevelingen voor prestaties zoeken en Toep assen](database-advisor-find-recommendations-portal.md)voor meer informatie over het verbeteren van de database prestaties door het wijzigen van de database code.
- Zie [Automatic tuning](automatic-tuning-overview.md)(Engelstalig) voor meer informatie over het door geven van ingebouwde data bases om uw data base te optimaliseren.
- Zie het [gebruik van alleen-lezen replica's voor het verdelen van alleen-lezen werk belastingen](read-scale-out.md)voor meer informatie over uitschalen in Azure SQL database.
- Zie [uitschalen met Azure SQL database](elastic-scale-introduction.md)voor meer informatie over een Data Base-sharding.
