---
title: Uw toegewezen SQL-groep (voorheen SQL DW) migreren naar Gen2
description: Instructies voor het migreren van een bestaande exclusieve SQL-groep (voorheen SQL DW) naar Gen2 en de migratie planning per regio.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0ce07ff3ca5fbcc9776792129d3bfb4ef54efe7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120118"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>Een upgrade uitvoeren van uw toegewezen SQL-groep (voorheen SQL DW) naar Gen2

Micro soft helpt bij het verminderen van de kosten op instap niveau van het uitvoeren van een toegewezen SQL-groep (voorheen SQL DW).  Er zijn nu lagere reken lagen mogelijk waarmee veeleisende query's kunnen worden verwerkt. Lees de volledige aankondiging [ondersteuning voor de compute-laag voor Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). De nieuwe aanbieding is beschikbaar in de regio's die in de onderstaande tabel worden vermeld. Voor ondersteunde regio's kan bestaande aan gen1 toegewezen SQL-groep (voorheen SQL DW) worden bijgewerkt naar Gen2 via een van de volgende opties:

- **Het automatische upgrade proces:** Automatische upgrades worden niet gestart zodra de service beschikbaar is in een regio.  Wanneer automatische upgrades in een bepaalde regio worden gestart, worden er tijdens de geselecteerde onderhouds planning afzonderlijke Data Warehouse-upgrades uitgevoerd.
- [**Zelf upgrade naar Gen2:**](#self-upgrade-to-gen2) U kunt bepalen wanneer u een upgrade uitvoert door een self-upgrade uit te voeren naar Gen2. Als uw regio nog niet wordt ondersteund, kunt u vanaf een herstel punt rechtstreeks naar een Gen2-exemplaar in een ondersteunde regio herstellen.

## <a name="automated-schedule-and-region-availability-table"></a>Tabel met Beschik baarheid van automatische planning en regio

De volgende tabel bevat een overzicht van de regio's wanneer de lagere Gen2-Compute-laag beschikbaar is en wanneer automatische upgrades worden gestart. De datums zijn onderhevig aan wijzigingen. Ga terug om te zien wanneer uw regio beschikbaar wordt.

\* Hiermee wordt aangegeven dat een specifieke planning voor de regio momenteel niet beschikbaar is.

| **Regio** | **Minder Gen2 beschikbaar** | **Begin van automatische upgrades** |
|:--- |:--- |:--- |
| Canada - oost |1 juni 2020 |1 juli 2020 |
| China East |\* |\* |
| China - noord |\* |\* |
| Duitsland - centraal |\* |\* |
| Duitsland - west-centraal |Beschikbaar |1 mei 2020 |
| India - west |Beschikbaar |1 mei 2020  |

## <a name="automatic-upgrade-process"></a>Automatisch upgrade proces

Op basis van de bovenstaande beschikbaarheids grafiek worden automatische upgrades gepland voor uw gen1-instanties. Om onverwachte onderbrekingen te voor komen voor de beschik baarheid van de toegewezen SQL-groep (voorheen SQL DW), worden de automatische upgrades gepland tijdens de onderhouds planning. De mogelijkheid om een nieuw gen1-exemplaar te maken wordt uitgeschakeld in regio's die automatisch worden bijgewerkt naar Gen2. Gen1 wordt afgeschaft zodra de automatische upgrades zijn voltooid. Zie [een onderhouds planning weer geven](maintenance-scheduling.md#view-a-maintenance-schedule) voor meer informatie over planningen

Bij het upgrade proces geldt een korte daling van de connectiviteit (ongeveer 5 min.) als u uw toegewezen SQL-groep opnieuw opstart (voorheen SQL DW).  Zodra uw toegewezen SQL-groep (voorheen SQL DW) opnieuw is opgestart, is deze volledig beschikbaar voor gebruik. Het is echter mogelijk dat de prestaties worden verminderd terwijl het upgrade proces de gegevens bestanden op de achtergrond bijwerkt. De totale tijdsduur voor afnemende prestaties is afhankelijk van de grootte van uw gegevensbestanden.

U kunt het upgrade proces van het gegevens bestand ook versnellen door [ALTER index Rebuild](sql-data-warehouse-tables-index.md) op alle primaire column Store-tabellen uit te voeren met een grotere SLO en resource klasse nadat de computer opnieuw is opgestart.

> [!NOTE]
> Alter index Rebuild is een offline bewerking en de tabellen zijn pas beschikbaar als het opnieuw opbouwen is voltooid.

## <a name="self-upgrade-to-gen2"></a>Zelf upgrade naar Gen2

U kunt zelf een upgrade uitvoeren door de volgende stappen uit te voeren op een bestaande gen1-toegewezen SQL-pool (voorheen SQL DW). Als u zelf een upgrade wilt uitvoeren, moet u deze volt ooien voordat het automatische upgrade proces in uw regio wordt gestart. Dit zorgt ervoor dat u elk risico op de automatische upgrades voor komt dat een conflict veroorzaakt.

Er zijn twee opties voor het uitvoeren van een self-upgrade.  U kunt uw huidige exclusieve SQL-groep (voorheen SQL DW) in-place upgraden of u kunt een gen1 toegewezen SQL-groep (voorheen SQL DW) herstellen in een Gen2-exemplaar.

- [In-place upgrade uitvoeren](upgrade-to-latest-generation.md) : met deze optie wordt uw bestaande met GEN1 toegewezen SQL-groep (voorheen SQL DW) bijgewerkt naar Gen2. Bij het upgrade proces geldt een korte daling van de connectiviteit (ongeveer 5 min.) als u uw toegewezen SQL-groep opnieuw opstart (voorheen SQL DW).  Zodra de service opnieuw is opgestart, is deze volledig beschikbaar voor gebruik. Als u problemen ondervindt tijdens de upgrade, opent u een [ondersteunings aanvraag](sql-data-warehouse-get-started-create-support-ticket.md) en raadpleegt u de mogelijke oorzaak van de Gen2-upgrade.
- [Upgrade van herstel punt](sql-data-warehouse-restore-points.md) : Maak een door de gebruiker gedefinieerd herstel punt op uw huidige GEN1 toegewezen SQL-groep (voorheen SQL DW) en herstel deze vervolgens rechtstreeks naar een Gen2-exemplaar. De bestaande gereserveerde SQL-groep gen1 (voorheen SQL DW) blijft aanwezig. Nadat het herstel is voltooid, is uw toegewezen SQL-groep met Gen2 (voorheen SQL DW) volledig beschikbaar voor gebruik.  Wanneer u alle test-en validatie processen op het teruggezette Gen2-exemplaar hebt uitgevoerd, kan de oorspronkelijke gen1-instantie worden verwijderd.

  - Stap 1: Maak vanuit het Azure Portal [een door de gebruiker gedefinieerd herstel punt](sql-data-warehouse-restore-active-paused-dw.md).
  - Stap 2: Stel bij het herstellen van een door de gebruiker gedefinieerd herstel punt het prestatie niveau in op de Gen2-laag van uw voor keur.

Er kan tijdelijk sprake zijn van verminderde prestaties terwijl het upgradeproces de gegevensbestanden op de achtergrond blijft bijwerken. De totale tijdsduur voor afnemende prestaties is afhankelijk van de grootte van uw gegevensbestanden.

Als u het migratie proces op de achtergrond wilt versnellen, kunt u de gegevens verplaatsing onmiddellijk afdwingen door [ALTER index Rebuild](sql-data-warehouse-tables-index.md) uit te voeren op alle primaire column Store-tabellen die u wilt opvragen bij een grotere SLO en resource klasse.

> [!NOTE]
> Alter index Rebuild is een offline bewerking en de tabellen zijn pas beschikbaar als het opnieuw opbouwen is voltooid.

Als u problemen ondervindt met uw toegewezen SQL-groep (voorheen SQL DW), maakt u een [ondersteunings aanvraag](sql-data-warehouse-get-started-create-support-ticket.md) en verwijst u naar "Gen2 upgrade" als de mogelijke oorzaak.

Zie [Upgrade naar Gen2](upgrade-to-latest-generation.md) voor meer informatie.

## <a name="migration-frequently-asked-questions"></a>Veelgestelde vragen over migratie

**V: kost Gen2 hetzelfde als gen1?**

- A: Ja.

**V: hoe worden de upgrades toegepast op mijn automatiserings scripts?**

- A: elk Automation-script dat verwijst naar een serviceniveau doelstelling moet worden gewijzigd om overeen te komen met het Gen2-equivalent.  Bekijk [hier](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal)meer informatie.

**V: hoe lang duurt een automatische upgrade?**

- A: u kunt een upgrade uitvoeren op de locatie of een upgrade uitvoeren vanaf een herstel punt.

  - Als u een upgrade naar een andere locatie uitvoert, wordt uw toegewezen SQL-groep (voorheen SQL DW) tijdelijk onderbroken en hervat.  Er wordt een achtergrond proces voortgezet terwijl de toegewezen SQL-groep (voorheen SQL DW) online is.  
  - Het duurt langer als u een upgrade uitvoert via een herstel punt, omdat de upgrade wordt uitgevoerd door het volledige herstel proces.

**V: Hoelang duurt de automatische upgrade?**

- A: de daad werkelijke downtime voor de upgrade is alleen de tijd die nodig is om de service te onderbreken en te hervatten. Dit ligt tussen 5 en tien minuten. Na een korte downtime wordt er op de achtergrond een opslagmigratie uitgevoerd. De tijds duur voor het achtergrond proces is afhankelijk van de grootte van uw toegewezen SQL-groep (voorheen SQL DW).

**V: wanneer wordt deze automatische upgrade uitgevoerd?**

- A: tijdens uw onderhouds planning. Door gebruik te maken van uw gekozen onderhouds planning wordt de onderbreking van uw bedrijf tot een minimum beperkt.

**V: wat moet ik doen als mijn upgrade proces op de achtergrond lijkt te blijven?**

- A: een REINDEX van uw column Store-tabellen starten. Houd er rekening mee dat het opnieuw indexeren van de tabel offline is tijdens deze bewerking.

**V: wat moet ik doen als Gen2 geen serviceniveau doelstelling heeft op gen1?**

- A: als u een DW600 of DW1200 uitvoert op gen1, is het raadzaam DW500c of DW1000c te gebruiken, omdat Gen2 meer geheugen, meer bronnen en hogere prestaties biedt dan gen1.

**V: kan ik geo-backup uitschakelen?**

- A: Nee. Geo-Backup is een zakelijke functie om uw toegewezen SQL-groep (voorheen SQL DW) Beschik baarheid te behouden in het geval dat een regio niet meer beschikbaar is. Open een [ondersteunings aanvraag](sql-data-warehouse-get-started-create-support-ticket.md) als u nog meer problemen hebt.

**V: is er een verschil in T-SQL-syntaxis tussen gen1 en Gen2?**

- A: er is geen wijziging in de syntaxis van de T-SQL-taal van gen1 naar Gen2.

**V: ondersteunt Gen2 onderhouds Vensters?**

- A: Ja.

**V: kan ik een nieuw gen1-exemplaar maken nadat mijn regio is bijgewerkt?**

- A: Nee. Nadat een upgrade van een regio is uitgevoerd, wordt het maken van nieuwe gen1-instanties uitgeschakeld.

## <a name="next-steps"></a>Volgende stappen

- [Upgrade stappen](upgrade-to-latest-generation.md)
- [Onderhoudsvensters](maintenance-scheduling.md)
- [Resource Health Monitor](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Controleren voordat u een migratie start](upgrade-to-latest-generation.md#before-you-begin)
- [Upgrade in-place en upgrade vanaf een herstel punt](upgrade-to-latest-generation.md)
- [Een door de gebruiker gedefinieerd herstel punt maken](sql-data-warehouse-restore-points.md)
- [Meer informatie over het herstellen naar Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Een ondersteunings aanvraag voor Azure Synapse Analytics openen](./sql-data-warehouse-get-started-create-support-ticket.md)