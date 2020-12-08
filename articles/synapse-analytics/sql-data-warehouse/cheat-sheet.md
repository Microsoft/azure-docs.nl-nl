---
title: Cheatsheet voor toegewezen SQL-pool (voorheen SQL DW)
description: Vind koppelingen en best practices om snel uw toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics-te maken.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a236cf99d3131e83619cfab06e8ec028938a87ba
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454610"
---
# <a name="cheat-sheet-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytic"></a>Cheatsheet voor toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics

Dit cheatsheet biedt nuttige tips en best practices voor het maken van uw toegewezen SQL-pooloplossingen (voorheen SQL DW).

De volgende afbeelding geeft het proces weer voor het ontwerpen van een datawarehouse met behulp van een toegewezen SQL-pool (voorheen SQL DW):

![Schema](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Query's en bewerkingen op tabellen

Als u van tevoren de primaire bewerkingen en query's kent die in uw datawarehouse kunnen worden uitgevoerd, kunt u prioriteiten stellen voor deze bewerkingen in uw datawarehouse-architectuur. Deze query's en bewerkingen zijn onder andere:

* Een of meerdere feitentabellen met dimensietabellen samenvoegen, de gecombineerde tabel filteren en vervolgens de resultaten aan een datamart toevoegen.
* Grote of kleine updates in uw feitelijke omzet aanbrengen.
* Alleen gegevens aan uw tabellen toevoegen.

Als u de typen bewerkingen van tevoren kent, kunt u het ontwerp van uw tabellen beter optimaliseren.

## <a name="data-migration"></a>Gegevensmigratie

Laad uw gegevens eerst in [Azure Data Lake Store](../../data-factory/connector-azure-data-lake-store.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of Azure Blob-opslag. Gebruik vervolgens de [instructie COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (preview) om uw gegevens in faseringstabellen te laden. Gebruik de volgende configuratie:

| Ontwerp | Aanbeveling |
|:--- |:--- |
| Distributie | Round robin |
| Indexeren | Heap |
| Partitionering | Geen |
| Resourceklasse | largerc of xlargerc |

Meer informatie over [gegevensmigratie](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), [gegevens laden](design-elt-data-loading.md), en het [ELT-proces (extraheren, laden en transformeren)](design-elt-data-loading.md).

## <a name="distributed-or-replicated-tables"></a>Gedistribueerde of gerepliceerde tabellen

Gebruik de volgende strategieën, afhankelijk van de eigenschappen van de tabel:

| Type | Zeer geschikt voor...| Kijk uit met...|
|:--- |:--- |:--- |
| Gerepliceerd | * Kleine dimensietabellen in een stervormig schema met minder dan 2 GB aan opslagruimte na compressie (ca. 5x compressie) |*    Veel schijftransacties voor de tabel (zoals invoegen, upsert, verwijderen, bijwerken)<br></br>*    Data Warehouse Units (DWU) die u vaak opnieuw inricht<br></br>*    U gebruikt maar twee of drie kolommen terwijl de tabel veel kolommen heeft<br></br>*    U indexeert een gerepliceerde tabel |
| Round robin (standaard) | *    Tijdelijke tabel/faseringstabel<br></br> * Geen duidelijke samenvoegsleutel of goede kandidaatkolom |*    Trage prestaties vanwege een gegevensverplaatsing |
| Hash | * Feitentabellen<br></br>*    Grote dimensietabellen |* De distributiesleutel kan niet worden bijgewerkt |

**Tips:**

* Begin met round robin, maar ga voor een hashdistributiestrategie om te profiteren van een massively parallel-architectuur.
* Zorg ervoor dat algemene hashsleutels dezelfde gegevensindeling hebben.
* Distribueer niet in een varchar-indeling.
* Dimensietabellen met een algemene hashsleutel voor een feitentabel met regelmatige samenvoegbewerkingen kunnen met hash worden verdeeld.
* Gebruik *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* om eventuele asymmetrie in gegevens te analyseren.
* Gebruik *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* om gegevensverplaatsingen achter query's te analyseren, de tijdbroadcast te bewaken en de opnamevolgorde van bewerkingen te wijzigen. Dit is handig om uw distributiestrategie te controleren.

Meer informatie over [gerepliceerde tabellen](design-guidance-for-replicated-tables.md) en [gedistribueerde tabellen](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Uw tabel indexeren

Indexeren is handig voor het snel lezen van tabellen. Er bestaat een unieke reeks technologieën die u op basis van uw behoeften kunt gebruiken:

| Type | Zeer geschikt voor... | Kijk uit met...|
|:--- |:--- |:--- |
| Heap | * Faseringstabel/tijdelijke tabel<br></br>* Kleine tabellen met kleine zoekacties |* Elke zoekopdracht scant de volledige tabel |
| Geclusterde index | * Tabellen met maximaal 100 miljoen rijen<br></br>* Grote tabellen (meer dan 100 miljoen rijen) waarin slechts één of twee kolommen intensief worden gebruikt |*   Gebruikt in een gerepliceerde tabel<br></br>*    U hebt complexe query's met betrekking tot meerdere samenvoeg- en groepeerbewerkingen<br></br>*    U maakt updates op de geïndexeerde kolommen: het neemt geheugen in beslag |
| Geclusterde columnstore-index (CCI) (standaardinstelling) | *    Grote tabellen (meer dan 100 miljoen rijen) | *   Gebruikt in een gerepliceerde tabel<br></br>*    U maakt grootschalige updatebewerkingen voor uw tabel<br></br>*    U maakt te veel partities voor uw tabel: rijgroepen zijn niet verdeeld over verschillende distributieknooppunten en- partities |

**Tips:**

* Mogelijk wilt u boven op een geclusterde index een niet-geclusterde index toevoegen aan een kolom die veel wordt gebruikt voor filteren.
* Wees voorzichtig met hoe u het geheugen van een tabel met CCI beheert. Wanneer u gegevens laadt, wilt u dat de gebruiker (of de query) profiteert van een grote resourceklasse. Voorkom bijsnijden en het maken van veel kleine gecomprimeerde rijgroepen.
* Bij Gen2 worden CCI-tabellen lokaal in de cache op de rekenknooppunten opgeslagen om de prestaties te maximaliseren.
* Bij CCI kunnen trage prestaties optreden vanwege slechte compressie van de rijgroepen. Als dit het geval is, herbouwt u uw CCI of ordent u deze opnieuw. U hebt ten minste 100.000 rijen per gecomprimeerde rijgroepen nodig. Ideaal is 1 miljoen rijen in een rijgroep.
* Afhankelijk van de frequentie en grootte van incrementeel laden, wilt u automatiseren wanneer u uw indexen opnieuw indeelt of herbouwt. Een lenteschoonmaak is altijd handig.
* Ga strategisch te werk bij het inkorten van een rijgroep. Hoe groot zijn de open rijgroepen? Hoeveel gegevens verwacht u dat er in de komende dagen worden geladen?

Meer informatie over [indexen](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Partitionering

U kunt uw tabel partitioneren wanneer het een grote feitentabel is (groter dan 1 miljard rijen). De partitiesleutel moet in 99 procent van de gevallen worden gebaseerd op datum. Zorg ervoor dat geen overpartitionering plaatsvindt, vooral wanneer u een geclusterde columnstore-index hebt.

Met faseringstabellen waarvoor ELT is vereist, kan partitionering voordelen opleveren. Het vergemakkelijkt het beheer van de gegevenslevenscyclus.
Zorg ervoor dat u uw gegevens niet overpartitioneert, vooral bij een geclusterde columnstore-index.

Meer informatie over [partities](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Incrementeel laden

Als u uw gegevens incrementeel laadt, moet u eerst grotere resourceklassen toewijzen voor het laden van uw gegevens.  Dit is vooral belangrijk bij het laden in tabellen met geclusterde columnstore-indexen.  Zie [Resourceklassen](resource-classes-for-workload-management.md) voor meer informatie.  

We raden u aan PolyBase en ADF V2 te gebruiken voor het automatiseren van uw ELT-pijplijnen in uw datawarehouse.

Voor een grote batch met updates in uw historische gegevens kunt u een [CTAS](sql-data-warehouse-develop-ctas.md) gebruiken om de gegevens te schrijven die u in een tabel wilt houden, in plaats van INSERT, UPDATE en DELETE te gebruiken.

## <a name="maintain-statistics"></a>Statistieken bijhouden

Het is belangrijk om uw statistieken bij te werken wanneer er *significante* wijzigingen optreden in uw gegevens. Zie [statistieken bijwerken](sql-data-warehouse-tables-statistics.md#update-statistics) om te bepalen of *aanzienlijke* veranderingen zijn opgetreden. Bijgewerkte statistieken optimaliseren uw queryplannen. Als u vindt dat het onderhouden van al uw statistieken te lang duurt, kunt u selectiever zijn over welke kolommen statistieken bevatten.

U kunt ook de frequentie van de updates definiëren. Zo wilt u datumkolommen, waar nieuwe waarden kunnen zijn toegevoegd, misschien dagelijks bijwerken. U haalt het meeste voordeel uit statistieken bij kolommen die onderdeel uitmaken van samenvoegingen, kolommen met het WHERE-component en kolommen in GROUP BY.

Meer informatie over [statistieken](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Resourceklasse

Resourcegroepen worden gebruikt om geheugen toe te wijzen aan query’s. Als u meer geheugenruimte nodig hebt voor het verbeteren van de query- of laadsnelheid, kunt u hogere resourceklassen toewijzen. Aan de andere kant hebben grotere resourceklassen een impact op de gelijktijdigheid. Denk hier goed over na voordat u alle gebruikers naar een grote resourceklasse verplaatst.

Als u merkt dat query's te lang duren, controleert u of uw gebruikers niet in grote resourceklassen worden uitgevoerd. Grote resourceklassen nemen veel gelijktijdigheidssleuven in beslag. Ze kunnen ervoor zorgen dat andere query's in de wachtrij komen.

Door het gebruik van Gen2 van een [toegewezen SQL-pool (voorheen SQL DW) ](sql-data-warehouse-overview-what-is.md) krijgt iedere resourceklasse uiteindelijk 2,5 keer zoveel geheugen als Gen1.

Meer informatie over het werken met [resourceklassen en gelijktijdigheid](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Uw kosten verlagen

Een belangrijke functie van Azure Synapse is de mogelijkheid om [rekenresources te beheren](sql-data-warehouse-manage-compute-overview.md). U kunt de toegewezen SQL-pool (voorheen SQL DW) onderbreken wanneer u deze niet gebruikt, zodat er geen rekenresources in rekening worden gebracht. U kunt de schaal van resources aanpassen om te voldoen aan uw prestatievereisten. Voor onderbreken gebruikt u [Azure Portal](pause-and-resume-compute-portal.md) of [PowerShell](pause-and-resume-compute-powershell.md). Voor schaal aanpassen gebruikt u [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md) of een [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

U kunt nu op elk moment automatisch de schaal aanpassen met Azure Functions:

[![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

## <a name="optimize-your-architecture-for-performance"></a>De architectuur optimaliseren voor betere prestaties

We raden u aan SQL Database en Azure Analysis Services in een hub-en-spoke-architectuur te gebruiken. Deze oplossing kan workloadisolatie bieden tussen verschillende gebruikersgroepen en tegelijkertijd geavanceerde beveiligingsfuncties van SQL Database en Azure Analysis Services gebruiken. Dit is ook een manier om grenzeloze gelijktijdigheid te bieden aan uw gebruikers.

Meer informatie over [veelvoorkomende architecturen die profiteren van toegewezen SQL-pools (voorheen SQL DW) in Azure Synapse Analytics](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/).

Met één muisklik uw spokes van de toegewezen SQL-pool (voorheen SQL DW) in SQL-databases implementeren:

[![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json)
