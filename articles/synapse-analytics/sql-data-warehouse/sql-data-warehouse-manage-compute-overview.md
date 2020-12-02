---
title: Reken resource voor een toegewezen SQL-groep beheren (voorheen SQL DW)
description: Meer informatie over mogelijkheden voor het uitbreiden van de prestaties van een exclusieve SQL-groep (voorheen SQL DW) in azure Synapse Analytics. Uitschalen door Dwu's aan te passen of door de kosten te verlagen door de toegewezen SQL-groep te onderbreken.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 300759b4ab6f806c02e748ff4c9a63a6a772bff4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461080"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Compute voor exclusieve SQL-groep (voorheen SQL DW) beheren in azure Synapse Analytics

Meer informatie over het beheren van reken resources toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics. Lagere kosten door de exclusieve SQL-groep te onderbreken of de toegewezen SQL-groep te schalen om te voldoen aan de prestatie vereisten.

## <a name="what-is-compute-management"></a>Wat is reken beheer?

De architectuur van de toegewezen SQL-groep (voorheen SQL DW) scheidt opslag en reken kracht, waardoor elk afzonderlijk kan worden geschaald. Daardoor kunt u de rekenkracht aanpassen om aan prestatievereisten te voldoen zonder dat dit consequenties heeft voor de opslag van gegevens. U kunt ook rekenresources pauzeren en hervatten. Een natuurlijk gevolg van deze architectuur is dat de [facturering](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor reken capaciteit en opslag gescheiden is. Als u uw toegewezen SQL-groep (voorheen SQL DW) voor een tijdje niet nodig hebt, kunt u de reken kosten besparen door Compute te onderbreken.

## <a name="scaling-compute"></a>Berekening schalen

U kunt de schaal omhoog of omlaag schalen door de instelling voor het [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) voor uw toegewezen SQL-groep (voorheen SQL DW) aan te passen. De prestaties voor het laden en voor query's kunnen lineair toenemen als u meer datawarehouse-eenheden wilt toevoegen.

Zie [Azure Portal](quickstart-scale-compute-portal.md), [Power shell](quickstart-scale-compute-powershell.md)of [T-SQL](quickstart-scale-compute-tsql.md) Quick starts (Engelstalig) voor stapsgewijze instructies. U kunt ook uitschaal bewerkingen uitvoeren met een [rest API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Als u een schaal bewerking wilt uitvoeren, worden met de toegewezen SQL-groep (voorheen SQL DW) alle binnenkomende query's eerst gestopt en worden vervolgens de trans acties teruggedraaid om een consistente status te krijgen. Het aanpassen van de schaal vindt alleen plaats als de transactie is teruggedraaid. Voor een schaal bewerking koppelt het systeem de opslaglaag van de reken knooppunten en voegt reken knooppunten toe en koppelt de opslaglaag vervolgens opnieuw aan de compute-laag. Elke toegewezen SQL-groep (voorheen SQL DW) wordt opgeslagen als 60-distributies, die gelijkmatig worden gedistribueerd naar de reken knooppunten. Het toevoegen van meer reken knooppunten voegt meer reken kracht toe. Naarmate het aantal reken knooppunten toeneemt, neemt het aantal verdelingen per reken knooppunt af, waardoor er meer reken kracht is voor uw query's. Evenzo verlagen Data Warehouse-eenheden het aantal reken knooppunten, waardoor de reken resources voor query's worden verminderd.

De volgende tabel laat zien hoe het aantal distributies per Compute-knoop punt verandert naarmate de Data Warehouse-eenheden worden gewijzigd.  DW30000c biedt 60 Compute-knoop punten en behaalt veel hogere query prestaties dan DW100c.

| Datawarehouse-eenheden  | \# van reken knooppunten | \# distributies per knoop punt |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>De juiste grootte van de Data Warehouse-eenheden zoeken

Voor een overzicht van de prestatie voordelen van uitschalen, met name voor grotere Data Warehouse-eenheden, wilt u ten minste één data set van 1 TB gebruiken. Als u het beste aantal data warehouse-eenheden voor uw toegewezen SQL-groep (voorheen SQL DW) wilt zoeken, kunt u omhoog en omlaag schalen. Voer enkele query's uit met verschillende aantallen Data Warehouse-eenheden na het laden van uw gegevens. Omdat schalen snel is, kunt u verschillende prestatie niveaus uitproberen in een uur of minder.

Aanbevelingen voor het zoeken naar het beste aantal data warehouse-eenheden:

- Voor een toegewezen SQL-groep (voorheen SQL DW) in ontwikkeling, begint u met het selecteren van een kleiner aantal data warehouse-eenheden.  Een goed uitgangs punt is DW400c of DW200c.
- Bewaak de prestaties van uw toepassing, waarbij het aantal geselecteerde data warehouse-eenheden wordt geobserveerd vergeleken met de prestaties die u ziet.
- Stel dat er een lineaire schaal is en bepaal hoeveel u nodig hebt om de Data Warehouse-eenheden te verg Roten of te verkleinen.
- Blijf aanpassingen aanbrengen totdat u een optimaal prestatie niveau bereikt voor uw bedrijfs vereisten.

## <a name="when-to-scale-out"></a>Wanneer uitschalen

Het uitschalen van data warehouse-eenheden heeft gevolgen voor de volgende aspecten van prestaties:

- Zorgt voor een lineaire verbetering van de prestaties van het systeem voor scans, aggregaties en CTAS-instructies.
- Verhoogt het aantal lezers en schrijvers voor het laden van gegevens.
- Maximum aantal gelijktijdige query's en gelijktijdigheids sleuven.

Aanbevelingen voor het schalen van data warehouse-eenheden:

- Voordat u een zware gegevens laad-of transformatie bewerking uitvoert, kunt u uitschalen om de gegevens sneller beschikbaar te maken.
- Uitschalen tijdens de piek uren om grotere aantallen gelijktijdige query's toe te passen.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Wat gebeurt er als de prestaties niet worden verbeterd door het uitschalen?

Het toevoegen van data warehouse-eenheden verhoogt de parallelle factor. Als het werk gelijkmatig wordt verdeeld tussen de reken knooppunten, verbetert de extra parallelle uitvoering query prestaties. Als u de prestaties niet wijzigt, zijn er enkele redenen waarom dit kan gebeuren. Het kan zijn dat uw gegevens over de distributies worden schuingetrokken, of query's kunnen een grote hoeveelheid gegevens verplaatsing introduceren. Zie [prestaties oplossen](sql-data-warehouse-troubleshoot.md#performance)voor meer informatie over het onderzoeken van prestatie problemen.

## <a name="pausing-and-resuming-compute"></a>Onderbreken en hervatten van rekenactiviteiten

Als u de reken kracht onderbreekt, ontkoppelt u de opslaglaag van de reken knooppunten. De reken resources worden vrijgegeven uit uw account. Er worden geen kosten in rekening gebracht voor de reken kracht terwijl de reken kracht wordt onderbroken. Als u de compute hervat, wordt de opslag opnieuw gekoppeld aan de reken knooppunten en worden de kosten voor Compute hervat.
Wanneer u een toegewezen SQL-groep onderbreekt (voorheen SQL DW):

- Reken-en geheugen bronnen worden geretourneerd naar de groep beschik bare resources in het Data Center
- De kosten voor de eenheid van het Data Warehouse zijn nul voor de duur van de onderbreking.
- Gegevens opslag wordt niet beïnvloed en uw gegevens blijven intact.
- Alle actieve of in de wachtrij geplaatste bewerkingen worden geannuleerd.

Wanneer u een toegewezen SQL-groep hervat (voorheen SQL DW):

- De toegewezen SQL-groep (voorheen SQL DW) verkrijgt reken-en geheugen resources voor de instellingen van uw data warehouse-eenheden.
- Reken kosten voor uw data warehouse-eenheden hervatten.
- Uw gegevens worden nu beschikbaar.
- Nadat de toegewezen SQL-groep (voorheen SQL DW) online is, moet u de query's voor de werk belasting opnieuw opstarten.

Als u altijd wilt dat uw toegewezen SQL-groep (voorheen SQL DW) toegankelijk is, kunt u deze omlaag schalen naar de kleinste grootte in plaats van te onderbreken.

Raadpleeg de [Azure Portal](pause-and-resume-compute-portal.md)of [Power shell](pause-and-resume-compute-powershell.md) -Quick starts voor instructies voor onderbreken en hervatten. U kunt ook de [pause-rest API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) of de [hervattings rest API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)gebruiken.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Transacties stoppen voor onderbreken of schalen

We raden aan om bestaande trans acties te volt ooien voordat u een pauze of schaal bewerking initieert.

Wanneer u uw toegewezen SQL-groep pauzeert of schaalt (voorheen SQL DW), worden de query's achter de schermen geannuleerd wanneer u de aanvraag voor onderbreken of schalen initieert. Een eenvoudige SELECT-query annuleren is een snelle bewerking en heeft zo goed als geen invloed op de duur van het onderbreken of schalen van uw instantie.  Maar transactiequery’s, die uw gegevens of de structuur van uw gegevens wijzigen, kunnen mogelijk niet snel worden stopgezet. **Transactiequery’s moeten per definitie volledig worden voltooid of hun wijzigingen volledig terugdraaien.** Het kan even lang of langer duren om het werk dat door een transactiequery is voltooid, terug te draaien, als het uitvoeren van de oorspronkelijke opdracht van de query. Als u bijvoorbeeld een query annuleert voor het verwijderen van rijen die al een uur wordt uitgevoerd, kan het systeem er een uur over doen om de verwijderde rijen terug te plaatsen. Als u onderbreken of schalen uitvoert terwijl er transacties bezig zijn, kan het schalen of onderbreken lang lijken te duren omdat het schalen of onderbreken moet wachten op het terugdraaien van de transacties voordat het kan worden voortgezet.

Zie ook [informatie over trans acties](sql-data-warehouse-develop-transactions.md)en het [optimaliseren van trans acties](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Reken beheer automatiseren

Zie [Compute-functies beheren met Azure functions](manage-compute-with-azure-functions.md)voor het automatiseren van de bewerkingen voor Compute management.

Het kan enkele minuten duren voordat elk van de bewerkingen scale-out, Pause en resume is voltooid. Als u automatisch wilt schalen, onderbreken of hervatten, wordt u aangeraden logica te implementeren om ervoor te zorgen dat bepaalde bewerkingen zijn voltooid voordat u doorgaat met een andere actie. Door de status van de toegewezen SQL-groep (voorheen SQL DW) via verschillende eind punten te controleren, kunt u automatisering van dergelijke bewerkingen op de juiste manier implementeren.

Zie de Snelstartgids [Power shell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) of [T-SQL](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) om de toegewezen status van de SQL-groep (voorheen SQL DW) te controleren. U kunt ook de status van de toegewezen SQL-groep (voorheen SQL DW) controleren met een [rest API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Machtigingen

Voor het schalen van de toegewezen SQL-groep (voorheen SQL DW) zijn de machtigingen vereist die worden beschreven in [ALTER data base](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Voor onderbreken en hervatten is de machtiging [SQL DB-Inzender](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) vereist, met name micro soft. SQL/servers/data bases/action.

## <a name="next-steps"></a>Volgende stappen

Zie de hand leiding voor het [beheren van reken kracht](manage-compute-with-azure-functions.md) een ander aspect van het beheren van reken resources is het toewijzen van verschillende reken bronnen voor afzonderlijke query's. Zie [resource klassen voor workload Management](resource-classes-for-workload-management.md)voor meer informatie.
