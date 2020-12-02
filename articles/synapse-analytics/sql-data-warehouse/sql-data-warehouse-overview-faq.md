---
title: Veelgestelde vragen over een toegewezen SQL-groep (voorheen SQL DW)
description: In dit artikel vindt u een lijst met veelgestelde vragen over een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics van klanten en ontwikkel aars.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460522"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>Toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics Veelgestelde vragen

## <a name="general"></a>Algemeen

V. Wat is Azure Synapse?

A. Azure Synapse is een analyse service waarmee gegevens opslag en Big data-analyses worden gecombineerd. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens op te nemen, voor te bereiden, te beheren en te verwerken voor BI-en machine learning behoeften. Zie voor meer informatie, [Wat is Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

V. Wat is er gebeurd met Azure SQL Data Warehouse?

A. Azure Synapse wordt Azure SQL Data Warehouse ontwikkeld. We hebben hetzelfde toonaangevende data warehouse gebruikt voor een geheel nieuw niveau van prestaties en mogelijkheden. U kunt door gaan met het uitvoeren van uw bestaande Data Warehouse-workloads in productie met een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse. Zie [Wat is Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md) voor meer informatie.

V. Wat is een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics?

A. De toegewezen SQL-groep (voorheen SQL DW) verwijst naar de functies voor Enter prise data warehousing die algemeen beschikbaar zijn met Azure Synapse. Zie voor meer informatie, [Wat is Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

V. Hoe kan ik aan de slag met Azure Synapse?

A. U kunt aan de slag met een [gratis Azure-account](https://azure.microsoft.com/free/sql-data-warehouse/) of [contact opnemen met de verkoop voor meer informatie](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

V. Wat biedt Azure Synapse voor de beveiliging van gegevens?

A. Azure Synapse biedt verschillende oplossingen voor het beveiligen van gegevens, zoals TDE en controle. Zie [beveiliging](sql-data-warehouse-overview-manage-security.md)voor meer informatie.

V. Waar kan ik zien welke juridische of zakelijke standaarden Azure Synapse compatibel is met?

A. Ga naar de [micro soft-nalevings](https://www.microsoft.com/trustcenter/compliance/complianceofferings) pagina voor diverse nalevings aanbiedingen per product zoals Soc en ISO. Kies eerst op nalevings titel. Vouw vervolgens Azure uit in de sectie Cloud Services van micro soft in het bereik aan de rechter kant van de pagina om te zien welke Services compatibel zijn met Azure Synapse.

V. Kan ik Power BI verbinding maken?

A. Ja. Hoewel Power BI directe query met Azure Synapse ondersteunt, is het niet bedoeld voor een groot aantal gebruikers of realtime-gegevens. Als u de prestaties van Power BI verder wilt optimaliseren, kunt u Power BI gebruiken boven op Azure Analysis Services of Analysis Service IaaS.

V. Wat zijn een toegewezen SQL-groep (voorheen SQL DW) capaciteits limieten?

A. Bekijk onze huidige [capaciteits limieten](sql-data-warehouse-service-capacity-limits.md) pagina.

V. Waarom duurt mijn schaal/onderbreken/hervatten?

A. Verschillende factoren kunnen invloed hebben op de tijd voor reken beheer bewerkingen. Een veelvoorkomend geval voor langlopende bewerkingen is transactioneel terugdraaien. Wanneer een schaal-of onderbrekings bewerking wordt gestart, worden alle binnenkomende sessies geblokkeerd en worden query's leeg gemaakt. Om ervoor te zorgen dat het systeem stabiel blijft, moeten trans acties worden teruggedraaid voordat een bewerking kan worden begonnen. Hoe groter het aantal en hoe groter de logboek grootte van trans acties, hoe langer de bewerking het systeem kan herstellen naar een stabiele status.

## <a name="user-support"></a>Gebruikersondersteuning

V. Ik heb een functie verzoek, waar kan ik het verzenden?

A. Als u een functie aanvraag hebt, moet u deze verzenden op onze [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) -pagina

V. Hoe kan ik x doen?

A. Voor hulp bij het ontwikkelen met Azure Synapse kunt u vragen stellen op onze [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) -pagina.

V. Hoe kan ik een ondersteunings ticket verzenden?

A. [Ondersteunings tickets](sql-data-warehouse-get-started-create-support-ticket.md) kunnen via Azure Portal worden ingediend.

## <a name="sql-languagefeature-support"></a>Ondersteuning voor SQL-taal/-functie

V. Welke gegevens typen worden ondersteund?

A. Zie  [gegevens typen](sql-data-warehouse-tables-data-types.md).

V. Welke tabel functies worden ondersteund?

A. Veel functies worden ondersteund. Functies die niet worden ondersteund, kunnen worden gevonden in [niet-ondersteunde tabel functies](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Hulp middelen en beheer

V. Ondersteunt de exclusieve SQL-groep (voorheen SQL DW) REST-Api's?

A. Ja. De meeste REST-functionaliteit die met SQL Database kan worden gebruikt, is ook beschikbaar met een toegewezen SQL-groep (voorheen SQL DW). U vindt API-informatie binnen REST-documentatie pagina's of [data bases](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="loading"></a>Laden

V. Welke client Stuur Programma's worden ondersteund?

A. Stuur programma-ondersteuning voor toegewezen SQL-groep (voorheen SQL DW) vindt u op de pagina [verbindings reeksen](sql-data-warehouse-connection-strings.md)

V: welke bestands indelingen worden ondersteund door poly base?

A: Orc, RC, Parquet en platte tekst met scheidings tekens

V: met welke gegevens bronnen kan ik verbinding maken met poly base?

A: [Azure data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) -en [Azure Storage-blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

V: is de reken pushdown mogelijk bij het verbinden met Azure Storage blobs of ADLS?

A: Nee, poly base werkt alleen met de opslag onderdelen.

V: kan ik verbinding maken met HDI?

A: HDI kan ADLS of WASB als de HDFS-laag gebruiken. Als u als uw HDFS-laag hebt, kunt u die gegevens in een toegewezen SQL-groep (voorheen SQL DW) laden. U kunt echter geen pushdown berekening genereren voor het HDI-exemplaar.

## <a name="next-steps"></a>Volgende stappen

Zie onze pagina [overzicht](sql-data-warehouse-overview-what-is.md) voor meer informatie over de toegewezen SQL-groep (voorheen SQL DW) in azure Synapse.
