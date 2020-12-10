---
title: Meerdere data bases beheren met elastische Pools
description: Meerdere data bases beheren en schalen in Azure SQL Database-honderden en duizenden-gebruik van elastische Pools. Eén prijs voor resources die u kunt distribueren waar nodig.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 12/9/2020
ms.openlocfilehash: d1ba9445441f38c55b40a8f8ca55471ea8b0a06d
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008585"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Meerdere databases in Azure SQL Database beheren en schalen met elastische pools
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database elastische Pools zijn een eenvoudige, rendabele oplossing voor het beheren en schalen van meerdere data bases met verschillende en onvoorspelbare gebruiks vereisten. De data bases in een elastische pool bevinden zich op één server en delen een aantal resources met een ingestelde prijs. Met elastische groepen in Azure SQL Database kunnen SaaS-ontwikkelaars de prijsprestaties voor een groep databases binnen een voorgeschreven budget optimaliseren en flexibele prestaties voor elke database leveren.

## <a name="what-are-sql-elastic-pools"></a>Wat zijn elastische SQL-Pools

SaaS-ontwikkelaars ontwikkelen toepassingen boven op grootschalige gegevenslagen die uit meerdere databases bestaan. Een algemeen patroon is de inrichting van een individuele database voor elke klant. Maar verschillende klanten hebben vaak verschillende en onvoorspelbare gebruiks patronen, en het is moeilijk om de resource vereisten van elke afzonderlijke database gebruiker te voors pellen. Normaal gesp roken hebt u twee opties:

- Resources van meer dan inrichten op basis van piek gebruik en meer dan betalen, of
- Onder-inrichting voor het besparen van kosten, tegen kosten van prestaties en klant tevredenheid tijdens pieken.

Elastische Pools kunnen dit probleem oplossen door ervoor te zorgen dat data bases de benodigde prestatie bronnen krijgen wanneer ze deze nodig hebben. Ze bieden een eenvoudig mechanisme voor het toewijzen van resources binnen een voorspelbaar budget. Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](saas-tenancy-app-design-patterns.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.
>
> [!IMPORTANT]
> Er worden geen kosten per Data Base voor elastische Pools. Er worden kosten in rekening gebracht voor elk uur dat een pool bestaat op het hoogste eDTU-of vCores, ongeacht het gebruik of het feit dat de groep korter dan een uur actief was.

Met elastische Pools kunnen ontwikkel aars bronnen kopen voor een pool die wordt gedeeld door meerdere data bases om te voorzien in onvoorspelbare Peri Oden van gebruik door afzonderlijke data bases. U kunt resources configureren voor de pool op basis van het op [DTU gebaseerde aankoop model](service-tiers-dtu.md) of het [op vCore gebaseerde aankoop model](service-tiers-vcore.md). De resource vereiste voor een groep wordt bepaald door het aggregatie gebruik van de data bases. De hoeveelheid beschik bare bronnen voor de groep wordt bepaald door het budget van de ontwikkelaar. De ontwikkelaar voegt gewoon data bases toe aan de groep, stelt optioneel de minimale en maximale bronnen voor de data bases (mini maal en Maxi maal Dtu's of minimum-of maximum vCores afhankelijk van uw keuze van het model voor opnieuw instellen) in en stelt vervolgens de resources van de groep in op basis van het budget. Met groepen kan een ontwikkelaar services naadloos met een alsmaar groeiende schaal uitbreiden van een kleine startende ondernemer tot een volwassen bedrijf.

Binnen de pool hebben afzonderlijke databases de flexibiliteit om de schaal automatisch aan te passen binnen ingestelde parameters. Onder zware belasting kan een Data Base meer resources gebruiken om aan de vraag te voldoen. Data bases onder lichte belasting worden minder verbruikt en data bases onder geen belasting verbruiken geen resources. De inrichting van resources voor de hele pool in plaats van afzonderlijke databases vereenvoudigt uw beheertaken. Daarnaast hebt u een voorspelbaar budget voor de pool. Extra resources kunnen worden toegevoegd aan een bestaande groep met minimale downtime. En als extra resources niet meer nodig zijn, kunnen ze op elk moment uit een bestaande pool worden verwijderd. En u kunt data bases toevoegen aan of verwijderen uit de groep. Als een database naar verwachting minder resources nodig heeft, kunt u deze verwijderen.

> [!NOTE]
> Wanneer u data bases naar of uit een elastische pool verplaatst, is er geen downtime, met uitzonde ring van een korte periode (in de volg orde van seconden) aan het einde van de bewerking wanneer de database verbindingen worden verwijderd.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Wanneer moet u een SQL Database elastische pool overwegen

Pools zijn geschikt voor een groot aantal databases met specifieke gebruikspatronen. Voor een bepaalde database wordt dit patroon gekenmerkt door een laag gemiddeld gebruik met relatief incidentele gebruikspieken. Daarentegen kunnen meerdere data bases met een permanent gemiddeld hoog gebruik niet in dezelfde elastische pool worden geplaatst.

Hoe meer databases u aan een groep kunt toevoegen, des te groter worden de besparingen. Afhankelijk van het gebruiks patroon van uw toepassing is het mogelijk om besparingen te zien met slechts twee S3-data bases.

In de volgende secties ziet u hoe u kunt bepalen of het voor uw specifieke verzameling databases voordelig is om deel uit te maken van een groep. In de voorbeelden wordt gebruik gemaakt van Standard-groepen, maar dezelfde principes gelden ook voor Basic- en Premium-groepen.

### <a name="assessing-database-utilization-patterns"></a>Databasegebruikspatronen beoordelen

De volgende afbeelding toont een voorbeeld van een database die doorgaans weinig wordt gebruikt, maar bij soms ook extreem actief is. Dit is een gebruikspatroon dat geschikt is voor een groep:

   ![een individuele database die geschikt is voor een groep](./media/elastic-pool-overview/one-database.png)

De grafiek illustreert het DTU-gebruik gedurende een periode van 1 uur van 12:00 tot 1:00 waarbij elk gegevens punt 1 minuut granulatie heeft. Bij 12:10 DB1 pieken tot 90 Dtu's, maar het totale gemiddelde gebruik is minder dan vijf Dtu's. Een S3-reken grootte is vereist voor het uitvoeren van deze werk belasting in één data base, maar hiermee blijven de meeste resources ongebruikt tijdens peri Oden met een lage activiteit.

Met een groep kunnen deze ongebruikte DTU’s worden gedeeld met meerdere databases, wat de benodigde DTU's en de totale kosten vermindert.

Stel dat er in het vorige voorbeeld meer databases zijn die een soortgelijk gebruikspatroon hebben als DB1. In de volgende twee afbeeldingen wordt het gebruik van vier data bases en 20 data bases gelaagd in dezelfde grafiek om de niet-overlappende aard van het gebruik in de loop van de tijd te illustreren met behulp van het op DTU gebaseerde aankoop model:

   ![vier databases met een gebruikspatroon dat geschikt is voor een groep](./media/elastic-pool-overview/four-databases.png)

   ![twintig databases met een gebruikspatroon dat geschikt is voor een groep](./media/elastic-pool-overview/twenty-databases.png)

Het gezamenlijke DTU-gebruik van alle 20 databases wordt aangegeven door de zwarte lijn in voorgaande afbeelding. U ziet dat het totale DTU-gebruik nooit hoger is dan honderd DTU’s en dat de twintig databases gedurende deze periode honderd eDTU's kunnen delen. Dit resulteert in een 20x-reductie in Dtu's en een dertienvoudige-prijs verlaging in vergelijking met het plaatsen van elk van de data bases in S3-reken grootten voor afzonderlijke data bases.

Dit voorbeeld is om de volgende redenen ideaal:

- Er zijn grote verschillen tussen piekgebruik en gemiddeld gebruik per database.
- Het piekgebruik voor elke database vindt plaats op verschillende momenten.
- eDTU‘s worden gedeeld tussen meerdere databases.

In het-aankoop model voor DTU is de prijs van een pool een functie van de groep Edtu's. Hoewel de prijs per eDTU voor een groep 1,5 x groter is dan de prijs per DTU voor een individuele database, **kunnen eDTU's in een groep door veel databases worden gedeeld en zijn er dus minder eDTU's nodig**. Deze verschillen in prijsbepaling en het delen van eDTU's vormen de basis van de mogelijke prijsbesparing die groepen kunnen bieden.

In het vCore-aankoop model is de vCore-eenheids prijs voor elastische Pools hetzelfde als de prijs voor de vCore-eenheid voor afzonderlijke data bases.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hoe kan ik de juiste pool grootte kiezen

De beste grootte voor een pool is afhankelijk van de geaggregeerde resources die nodig zijn voor alle data bases in de groep. Dit omvat het bepalen van het volgende:

- De maximale reken resources die worden gebruikt door alle data bases in de pool.  Reken bronnen worden geïndexeerd door Edtu's of vCores, afhankelijk van uw keuze van een aankoop model.
- De maximum opslag in bytes die door alle databases in de groep wordt gebruikt.

Zie het [op DTU gebaseerde aankoop model](service-tiers-dtu.md) of het [op vCore gebaseerde aankoop model](service-tiers-vcore.md)voor service lagen en resource limieten in elk aankoop model.

Aan de hand van de volgende stappen kunt u schatten of een pool rendabeler is dan de afzonderlijke data bases:

1. U moet als volgt een schatting maken van de Edtu's-of vCores die nodig zijn voor de groep:

Voor op DTU gebaseerd inkoop model:

MAX (<*Totaal aantal db's* x *gemiddeld DTU-gebruik per DB* ->, <*aantal gelijktijdig pieken db's* X *piek-DTU-gebruik per DB*>)

Voor op vCore gebaseerd inkoop model:

MAX (<*totale aantal db's* x- *VCore gebruik per DB* ->, <*aantal gelijktijdig pieken db's* X *piek vCore gebruik per DB*>)

2. Schatting van de totale opslag ruimte die nodig is voor de groep door de benodigde gegevens grootte voor alle data bases in de groep toe te voegen. Bepaal voor het DTU-aankoop model de eDTU-groeps grootte die deze hoeveelheid opslag biedt.
3. Neem voor het op DTU gebaseerde aankoop model meer van de eDTU-schattingen uit stap 1 en stap 2. Neem voor het op vCore gebaseerde aankoop model de vCore-schatting uit stap 1.
4. Bekijk de [pagina met prijzen voor SQL database](https://azure.microsoft.com/pricing/details/sql-database/) en zoek de kleinste groeps grootte die groter is dan de schatting van stap 3.
5. Vergelijk de prijs van de groep uit stap 4 met de prijs voor het gebruik van de juiste reken grootten voor afzonderlijke data bases.

> [!IMPORTANT]
> Als het aantal data bases in een pool het maximum ondersteunt, moet u het [bron beheer overwegen in compacte elastische Pools](elastic-pool-resource-management.md).

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Andere SQL Database-functies gebruiken met elastische Pools

### <a name="elastic-jobs-and-elastic-pools"></a>Elastische taken en elastische Pools

Met een groep worden beheertaken vereenvoudigd door scripts in **[elastische taken](elastic-jobs-overview.md)** uit te voeren. Een elastische taak elimineert de meeste saaie handelingen die komen kijken bij grote aantallen databases.

Zie [Scaling out with Azure SQL Database](elastic-scale-introduction.md) (Uitbreiden met Azure SQL Database) voor meer informatie over andere databasehulpprogramma's voor het werken met meerdere databases.

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opties voor bedrijfs continuïteit voor data bases in een elastische pool

Pooldatabases ondersteunen in het algemeen dezelfde [bedrijfscontinuïteitsfuncties](business-continuity-high-availability-disaster-recover-hadr-overview.md) die beschikbaar zijn voor individuele databases.

- **Terugzetten naar eerder tijdstip**

  Herstel naar een bepaald tijdstip maakt gebruik van automatische database back-ups om een data base in een groep te herstellen naar een specifiek tijdstip. Zie [Herstel naar een bepaald tijdstip](recovery-using-backups.md#point-in-time-restore)

- **Geo-herstel**

  Geo-Restore biedt de standaard herstel optie wanneer een Data Base niet beschikbaar is vanwege een incident in de regio waarin de data base wordt gehost. Zie [een Azure SQL database of failover herstellen naar een secundaire](disaster-recovery-guidance.md)

- **Actieve geo-replicatie**

  Voor toepassingen met meer agressieve herstel vereisten dan geo-Restore kunnen bieden, configureert u [actieve geo-replicatie](active-geo-replication-overview.md) of een [groep met automatische failover](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Een nieuwe SQL Database elastische pool maken met behulp van de Azure Portal

Er zijn twee manieren waarop u een elastische pool kunt maken in de Azure Portal.

1. Ga naar de [Azure Portal](https://portal.azure.com) om een elastische pool te maken. Zoek en selecteer **Azure SQL**.
2. Selecteer **+Toevoegen** om de pagina **SQL-implementatieoptie selecteren** te openen. U kunt aanvullende informatie over elastische Pools weer geven door **Details weer geven** te selecteren op de tegel **data bases** .
3. Selecteer in de tegel **data bases** de optie **elastische groep** in de vervolg keuzelijst **resource type** en selecteer vervolgens **maken**:

   ![Een elastische pool maken](./media/elastic-pool-overview/create-elastic-pool.png)

4. U kunt ook een elastische pool maken door naar een bestaande server te gaan en te klikken op **+ nieuwe groep** om een groep rechtstreeks op die server te maken.

> [!NOTE]
> U kunt meerdere groepen maken op een-server, maar het is niet mogelijk om data bases van verschillende servers toe te voegen aan dezelfde groep.

De servicelaag van de pool bepaalt de functies die beschikbaar zijn voor de elastische elementen in de groep en de maximale hoeveelheid beschik bare bronnen voor elke Data Base. Zie resource limieten voor elastische Pools in het DTU- [model](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)voor meer informatie. Zie voor vCore resource limieten voor elastische Pools op [vCore gebaseerde resource limieten: elastische Pools](resource-limits-vcore-elastic-pools.md).

Klik op **groep configureren** om de resources en prijs van de groep te configureren. Selecteer vervolgens een servicelaag, voeg data bases toe aan de groep en configureer de resource limieten voor de groep en de bijbehorende data bases.

Wanneer u klaar bent met het configureren van de groep, kunt u op Toep assen klikken, de groep een naam krijgen en op OK klikken om de groep te maken.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Een elastische pool en de bijbehorende data bases bewaken

In de Azure Portal kunt u het gebruik van een elastische pool en de data bases in die groep bewaken. U kunt ook een reeks wijzigingen aanbrengen in uw elastische pool en alle wijzigingen tegelijk verzenden. Deze wijzigingen omvatten het toevoegen of verwijderen van data bases, het wijzigen van de instellingen voor de elastische groep of het wijzigen van de data base-instellingen.

U kunt gebruikmaken van de ingebouwde [hulpprogram ma's](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)voor [prestatie bewaking](https://docs.microsoft.com/azure/azure-sql/database/performance-guidance) en waarschuwingen, in combi natie met prestatie classificaties.  Daarnaast kan SQL Database [metrische gegevens en resourcelogboeken verzenden](https://docs.microsoft.com/azure/azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure?tabs=azure-portal) die bewaking eenvoudiger maken.

## <a name="customer-case-studies"></a>Casestudy's van klanten

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart gebruikt elastische Pools met Azure SQL Database om de zakelijke services snel uit te breiden tegen een snelheid van 1.000 nieuwe Azure SQL-data bases per maand.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco maakt gebruik van elastische Pools met Azure SQL Database om services snel in te richten en te schalen voor duizenden tenants in de Cloud.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI maakt gebruik van elastische Pools met Azure SQL Database om de ontwikkelings cyclus te versnellen en de klant Services en-prestaties te verbeteren.

## <a name="next-steps"></a>Volgende stappen

- Zie [prijzen voor elastische Pools](https://azure.microsoft.com/pricing/details/sql-database/elastic)voor prijs informatie.
- Zie [elastische](elastic-pool-scale.md) Pools schalen en [een elastische pool schalen-voorbeeld code](scripts/monitor-and-scale-pool-powershell.md) om elastische Pools te schalen.
- Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](saas-tenancy-app-design-patterns.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.
- Zie [Introduction to the Wingtip SaaS Application](saas-dbpertenant-wingtip-app-overview.md)voor een SaaS-zelf studie over het gebruik van elastische Pools.
- Zie [resource management in dense elastische Pools](elastic-pool-resource-management.md)voor meer informatie over resource beheer in elastische Pools met veel data bases.
