---
title: 'Back-ups maken en herstellen: moment opnamen, geografisch redundant'
description: Meer informatie over het werken met back-ups en herstellen in azure Synapse Analytics exclusieve SQL-groep. Gebruik back-ups om uw data warehouse te herstellen naar een herstel punt in de primaire regio. Gebruik geo-redundante back-ups om te herstellen naar een andere geografische regio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 8fd64023b9c07e8dd426b2b51916db4515a5405a
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451503"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Back-ups maken en herstellen in een toegewezen SQL-groep in azure Synapse

Meer informatie over het gebruik van back-up en herstel in azure Synapse exclusieve SQL-pool. Gebruik speciale herstel punten voor een SQL-groep om uw data warehouse te herstellen of te kopiëren naar een eerdere status in de primaire regio. Gebruik geo-redundante back-ups van data warehouse om te herstellen naar een andere geografische regio.

## <a name="what-is-a-data-warehouse-snapshot"></a>Wat is een moment opname van een Data Warehouse

Een *moment opname van een Data Warehouse* maakt u een herstel punt dat u kunt gebruiken voor het herstellen of kopiëren van uw data warehouse naar een eerdere status.  Omdat de toegewezen SQL-groep een gedistribueerd systeem is, bestaat een moment opname van een Data Warehouse uit veel bestanden die zich in azure Storage bevinden. Met moment opnamen worden incrementele wijzigingen vastgelegd op basis van de gegevens die zijn opgeslagen in uw data warehouse.

Een *Data Warehouse Restore* is een nieuw data warehouse dat is gemaakt op basis van een herstel punt van een bestaand of verwijderd Data Warehouse. Het herstellen van uw data warehouse is een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat de gegevens na een onbedoeld beschadiging of verwijdering opnieuw worden gemaakt. Data Warehouse is ook een krachtig mechanisme voor het maken van kopieën van uw data warehouse voor test-of ontwikkelings doeleinden. De specifieke tarieven voor het herstellen van een SQL-groep kunnen variëren, afhankelijk van de grootte van de data base en de locatie van het bron-en doel Data Warehouse.

## <a name="automatic-restore-points"></a>Automatische herstelpunten

Moment opnamen zijn een ingebouwde functie voor het maken van herstel punten. U hoeft deze mogelijkheid niet in te scha kelen. De toegewezen SQL-groep moet echter een actieve status hebben voor het maken van herstel punten. Als het regel matig wordt onderbroken, kunnen er geen automatische herstel punten worden gemaakt, dus zorg ervoor dat u een door de gebruiker gedefinieerd herstel punt maakt voordat de toegewezen SQL-groep wordt onderbroken. Automatische herstel punten op dit moment kunnen niet door gebruikers worden verwijderd omdat de service deze herstel punten gebruikt om de Sla's voor herstel te onderhouden.

Moment opnamen van uw data warehouse worden gedurende de hele dag genomen om herstel punten te maken die zeven dagen beschikbaar zijn. Deze Bewaar periode kan niet worden gewijzigd. Een toegewezen SQL-groep ondersteunt een Recovery Point Objective van acht uur. U kunt uw data warehouse in de primaire regio herstellen vanuit een van de moment opnamen die in de afgelopen zeven dagen zijn gemaakt.

Als u wilt zien wanneer de laatste moment opname is gestart, voert u deze query uit voor uw online exclusieve SQL-groep.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Door de gebruiker gedefinieerde herstelpunten

Met deze functie kunt u hand matig moment opnamen activeren om herstel punten van uw data warehouse te maken voor en na grote wijzigingen. Deze functionaliteit zorgt ervoor dat herstel punten logisch consistent zijn, wat een extra gegevens bescherming biedt bij onderbrekingen van de werk belasting of gebruikers fouten voor snelle herstel tijd. Door de gebruiker gedefinieerde herstel punten zijn zeven dagen beschikbaar en worden namens u automatisch verwijderd. U kunt de Bewaar periode van door de gebruiker gedefinieerde herstel punten niet wijzigen. **42 door de gebruiker gedefinieerde herstel punten** worden op elk moment gegarandeerd, zodat ze moeten worden [verwijderd](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint) voordat er een nieuw herstel punt wordt gemaakt. U kunt moment opnamen activeren om door de gebruiker gedefinieerde herstel punten te maken via [Power shell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) of de Azure Portal.

> [!NOTE]
> Als u herstel punten van meer dan zeven dagen nodig hebt, kunt u [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)stemmen voor deze functie. U kunt ook een door de gebruiker gedefinieerd herstel punt maken en herstellen vanuit het zojuist gemaakte herstel punt naar een nieuw data warehouse. Nadat u hebt hersteld, hebt u de exclusieve SQL-groep online en kunt u deze voor onbepaalde tijd voor het besparen van reken kosten pauzeren. De gepauzeerde data base maakt kosten per seconde op het Premium Storage tarief van Azure. Als u een actieve kopie van het herstelde data warehouse nodig hebt, kunt u hervatten. dit duurt slechts enkele minuten.

### <a name="restore-point-retention"></a>Bewaar periode van het herstel punt

De volgende lijst bevat Details voor de Bewaar periode van het herstel punt:

1. Een toegewezen SQL-groep verwijdert een herstel punt wanneer het de Bewaar periode van 7 dagen aantreft **en** wanneer er ten minste 42 totale herstel punten zijn (inclusief de door de gebruiker gedefinieerde en automatische).
2. Moment opnamen worden niet uitgevoerd wanneer een toegewezen SQL-groep wordt onderbroken.
3. De leeftijd van een herstel punt wordt gemeten door de absolute kalender dagen vanaf het moment dat het herstel punt wordt gemaakt, inclusief wanneer de SQL-groep wordt gepauzeerd.
4. Op elk moment is een toegewezen SQL-groep gegarandeerd dat er Maxi maal 42 door de gebruiker gedefinieerde herstel punten en 42 automatische herstel punten kunnen worden opgeslagen zolang deze herstel punten de Bewaar periode van 7 dagen niet hebben bereikt
5. Als er een moment opname wordt gemaakt, wordt de toegewezen SQL-groep vervolgens langer dan zeven dagen onderbroken en vervolgens hervat, wordt het herstel punt bewaard totdat er 42 totaal herstel punten zijn (inclusief door de gebruiker gedefinieerde en automatische)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Moment opname bewaren wanneer een SQL-groep wordt verwijderd

Wanneer u een toegewezen SQL-groep neerzet, wordt een laatste moment opname gemaakt en gedurende zeven dagen opgeslagen. U kunt de toegewezen SQL-groep herstellen naar het laatste herstel punt dat bij het verwijderen is gemaakt. Als de toegewezen SQL-groep wordt verwijderd in een onderbroken status, wordt er geen moment opname gemaakt. Zorg er in dat scenario voor dat u een door de gebruiker gedefinieerd herstel punt maakt voordat u de toegewezen SQL-groep verwijdert.

> [!IMPORTANT]
> Als u de server/werk ruimte verwijdert die als host fungeert voor een toegewezen SQL-groep, worden ook alle data bases die deel uitmaken van de server/werk ruimte verwijderd en kunnen deze niet worden hersteld. U kunt een verwijderde server niet herstellen.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-back-ups en herstel na nood gevallen

Er wordt één keer per dag een geo-back-up gemaakt naar een [gekoppeld Data Center](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). De RPO voor een geo-Restore is 24 uur. U kunt de geo-back-up naar een server herstellen in een andere regio waar exclusieve SQL-pool wordt ondersteund. Een geo-back-up zorgt ervoor dat u Data Warehouse kunt herstellen als u geen toegang hebt tot de herstel punten in uw primaire regio.

Als u geen geo-back-ups voor uw toegewezen SQL-groep nodig hebt, kunt u deze uitschakelen en besparen op opslag kosten voor herstel na nood geval. Raadpleeg hiervoor [de hand leiding: Geo-back-ups uitschakelen voor een toegewezen SQL-groep (voorheen SQL DW)](disable-geo-backup.md). Als u geo-back-ups uitschakelt, kunt u uw toegewezen SQL-groep niet herstellen naar uw gekoppelde Azure-regio als uw primaire Azure-Data Center niet beschikbaar is. 

> [!NOTE]
> Als u een korte RPO voor geo-back-ups nodig hebt, stem dan op [deze functie.](https://feedback.azure.com/forums/307516-sql-data-warehouse) U kunt ook een door de gebruiker gedefinieerd herstel punt maken en herstellen vanuit het zojuist gemaakte herstel punt naar een nieuw data warehouse in een andere regio. Nadat u de gegevens hebt hersteld, hebt u het Data Warehouse online en kunt u het voor onbepaalde tijd pauzeren om reken kosten op te slaan. De gepauzeerde data base maakt kosten per seconde op het Premium Storage tarief van Azure. Als u een actieve kopie van het Data Warehouse nodig hebt, kunt u hervatten. dit duurt slechts enkele minuten.

## <a name="data-residency"></a>Gegevenslocatie 

Als uw gekoppelde Data Center zich buiten uw geografische grens bevindt, kunt u ervoor zorgen dat uw gegevens binnen uw geografische grenzen blijven door het afmelden van geografisch redundante opslag. Dit kan worden gedaan bij het inrichten van uw toegewezen SQL-groep (voorheen SQL DW) via de geo-redundante opslag optie bij het maken of herstellen van een toegewezen SQL-groep (voorheen SQL DW). 

Als u wilt controleren of het gekoppelde Data Center zich in een ander land bevindt, raadpleegt u [Azure gekoppelde regio's](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="backup-and-restore-costs"></a>Kosten voor back-up en herstel

U ziet dat de Azure-factuur een regel item bevat voor opslag en een regel item voor nood herstel opslag. De opslag kosten zijn de totale kosten voor het opslaan van uw gegevens in de primaire regio, samen met de incrementele wijzigingen die zijn vastgelegd door moment opnamen. Raadpleeg de  [informatie over het samen voegen](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)van moment opnamen voor een gedetailleerde uitleg over de kosten voor moment opnamen. De geo-redundante kosten omvatten de kosten voor het opslaan van de geo-back-ups.  

De totale kosten voor uw primaire Data Warehouse en zeven dagen aan momentopname wijzigingen worden afgerond op de dichtstbijzijnde TB. Als uw data warehouse bijvoorbeeld 1,5 TB is en de moment opnamen 100 GB worden vastgelegd, worden er twee TB aan gegevens in rekening gebracht op Azure Premium Storage-tarieven.

Als u geografisch redundante opslag gebruikt, ontvangt u afzonderlijke opslag kosten. De geo-redundante opslag wordt gefactureerd op basis van de standaard Read-Access geografisch redundante opslag (RA-GRS).

Zie [prijzen voor Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)voor meer informatie over de prijzen van Azure Synapse. Er worden geen kosten in rekening gebracht voor het afrekenen van gegevens bij het herstellen tussen regio's.

## <a name="restoring-from-restore-points"></a>Herstellen vanaf herstel punten

Elke moment opname maakt een herstel punt dat staat voor de tijd waarop de moment opname is gestart. Als u een Data Warehouse wilt herstellen, kiest u een herstel punt en geeft u een opdracht herstellen op.  

U kunt het herstelde data warehouse en de huidige herstellen, of een van beide verwijderen. Als u het huidige Data Warehouse wilt vervangen door het herstelde data warehouse, kunt u de naam ervan wijzigen met [ALTER data base](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) met de optie naam wijzigen.

Als u een Data Warehouse wilt herstellen, raadpleegt u [een toegewezen SQL-groep herstellen](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Als u een verwijderd of onderbroken Data Warehouse wilt herstellen, kunt u [een ondersteunings ticket maken](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Meerdere abonnementen herstellen

Als u het abonnement direct wilt herstellen, moet u [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)stemmen voor deze functie. Herstel op een andere server en [' Verplaats '](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) de server over abonnementen om een abonnement op meerdere abonnementen uit te voeren.

## <a name="geo-redundant-restore"></a>Geografisch redundant herstel

U kunt [uw toegewezen SQL-groep herstellen](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) naar elke regio die de toegewezen SQL-groep op het gekozen prestatie niveau ondersteunt.

> [!NOTE]
> Als u een geo-redundante terugzet bewerking wilt uitvoeren, moet u deze functie niet gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie door de [gebruiker gedefinieerde herstel punten](sql-data-warehouse-restore-points.md) voor meer informatie over herstel punten