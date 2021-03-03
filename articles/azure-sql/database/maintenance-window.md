---
title: Onderhoudsvenster
description: Begrijpen hoe het onderhouds venster voor de Azure SQL Database en het beheerde exemplaar kan worden geconfigureerd.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 4006cedf5f24ab2fc08e41b58f8acf90c404f668
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679615"
---
# <a name="maintenance-window-preview"></a>Onderhouds venster (preview-versie)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

De functie onderhouds venster kan de configuratie van voorspel bare onderhouds Vensters plannen voor [Azure SQL database](sql-database-paas-overview.md) en [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md). 

Zie voor meer informatie over onderhouds gebeurtenissen [plannen voor Azure-onderhouds gebeurtenissen in Azure SQL database en Azure SQL Managed instance](planned-maintenance.md).

## <a name="overview"></a>Overzicht

Azure voert regel matig geplande onderhouds updates uit op Azure SQL Database en SQL Managed Instance-bronnen, die vaak updates bevatten voor onderliggende hardware, software zoals een onderliggend besturings systeem (OS) en de SQL-engine. Tijdens een onderhouds update zijn resources volledig beschikbaar en toegankelijk, maar sommige onderhouds updates vereisen een failover als Azure exemplaren gedurende korte tijd offline neemt om de onderhouds updates toe te passen (acht seconden in de duur van het gemiddelde).  Geplande onderhouds updates worden eenmaal per 35 dagen uitgevoerd, wat betekent dat de klant ongeveer één gepland onderhouds gebeurtenis per maand per Azure SQL Database of een door SQL beheerd exemplaar kan verwachten en alleen tijdens de door de klant geselecteerde sleuven voor het onderhouds venster.   

Het onderhouds venster is bedoeld voor zakelijke workloads die gevoelig zijn voor de mogelijke connectiviteits onderbrekingen die kunnen voortvloeien uit gepland onderhouds gebeurtenissen tijdens het standaard venster.  

Het onderhouds venster kan worden geconfigureerd met behulp van de Azure Portal, Power shell, CLI of de Azure-API. Het kan worden geconfigureerd bij het maken of voor bestaande SQL-data bases en SQL Managed instances.

### <a name="gain-more-predictability-with-maintenance-window"></a>Meer voorspel baarheid krijgen met het onderhouds venster

Standaard worden alle Azure SQL-data bases en beheerde exemplaar databases alleen bijgewerkt tijdens tot 17:00 uur naar de lokale tijd van 8 A.M. om te voor komen dat er piek onderbrekingen in de kantoor uren zijn. De lokale tijd wordt bepaald door de [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) die als host fungeert voor de resource. U kunt de onderhouds updates verder aanpassen aan een tijd die geschikt is voor uw data base door te kiezen uit twee extra onderhouds venster sleuven:

* **Standaard** venster, tot 17:00 uur naar 8 a.m. lokale tijd van maandag tot zondag 
* Het venster weekdag, 10PM naar 06.00 lokale tijd maandag – donderdag: **vereist de klant opt-in** 
* Weekend Window 10PM to 06.00 local time vrijdag-zondag: **vereist een klant opt-in**  

Zodra het onderhouds venster is geselecteerd, worden alle geplande onderhouds updates alleen uitgevoerd in het venster van uw keuze.   

> [!Note]
> Naast geplande onderhouds updates kunnen ongeplande onderhouds gebeurtenissen niet worden Beschik baarheid veroorzaakt. 

### <a name="cost"></a>Kosten

Het kiezen van een onderhouds venster is gratis voor de volgende [typen abonnements aanbiedingen](https://azure.microsoft.com/support/legal/offer-details/): betalen per gebruik, Cloud Solution Provider (CSP), micro soft Enter prise of micro soft-klant overeenkomst.

> [!Note]
> Een Azure-aanbieding is het type Azure-abonnement dat u hebt. Een abonnement met [betalen per gebruik-tarieven](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)en [Visual Studio Enter prise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) is bijvoorbeeld allemaal Azure-aanbiedingen. Elke aanbieding of elk plan heeft verschillende voor delen. Uw aanbieding of abonnement wordt weer gegeven in het overzicht van het abonnement. Zie [uw Azure-abonnement wijzigen in een andere aanbieding](/azure/cost-management-billing/manage/switch-azure-offer)voor meer informatie over het overschakelen op een ander abonnement.

## <a name="advance-notifications"></a>Voorafgaande meldingen

Onderhouds meldingen kunnen worden geconfigureerd om klanten 24 uur vooraf te waarschuwen over toekomstige geplande onderhouds gebeurtenissen, op het moment van onderhoud en wanneer het onderhouds venster is voltooid. Zie [Advance Notifications](advance-notifications.md)(Engelstalig) voor meer informatie.

## <a name="availability"></a>Beschikbaarheid

### <a name="supported-service-level-objectives"></a>Ondersteunde serviceniveau doelstellingen

Het kiezen van een ander onderhouds venster dan de standaard instelling is beschikbaar op alle Slo's **, met uitzonde ring van**:
* Hyperscale 
* Verouderde Gen4-vCore
* Basic, S0 en S1 
* DC, Fsv2, M-serie

### <a name="azure-region-support"></a>Azure-regio ondersteuning

Het kiezen van een ander onderhouds venster dan de standaard instelling is momenteel beschikbaar in de volgende regio's:

- Australië - oost
- Australië-Zuidoost
- Brazilië - zuid
- Canada - midden
- VS - centraal
- VS - oost
- VS - oost 2
- Japan - oost
- NorthCentral
- Europa - noord
- SouthCentral
- Zuidoost-Azië
- Verenigd Koninkrijk Zuid
- Europa -west
- VS - west
- VS - west 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Gateway onderhoud voor Azure SQL Database

Om het maximale voor deel van onderhouds Vensters te verkrijgen, moet u ervoor zorgen dat uw client toepassingen gebruikmaken van het verbindings beleid omleiden. Omleiding is het aanbevolen verbindings beleid, waarbij clients rechtstreeks verbinding maken met het knoop punt dat als host fungeert voor de data base, waardoor de latentie wordt beperkt en de door Voer is verbeterd.  

* In Azure SQL Database kunnen alle verbindingen die gebruikmaken van het beleid voor proxy verbindingen, worden beïnvloed door het gekozen onderhouds venster en een onderhouds venster voor gateway knooppunten. Client verbindingen die gebruikmaken van het aanbevolen verbindings beleid voor omleiding worden echter niet beïnvloed door een onderhouds failover voor gateway knooppunten. 

* In Azure SQL Managed instance bevinden de gateway knooppunten zich [in het virtuele cluster](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) en hebben hetzelfde onderhouds venster als het beheerde exemplaar, dus als u het beleid voor proxy verbindingen gebruikt, is het niet mogelijk verbindingen met een extra onderhouds venster beschikbaar te maken.

Zie [Azure SQL database verbindings beleid](../database/connectivity-architecture.md#connection-policy)voor meer informatie over het client verbindings beleid in Azure SQL database. 

Zie [Azure SQL Managed instance Connect types](../../azure-sql/managed-instance/connection-types-overview.md)(Engelstalig) voor meer informatie over het client verbindings beleid in Azure SQL Managed instance.


## <a name="next-steps"></a>Volgende stappen

* [Voorafgaande meldingen](advance-notifications.md)
* [Onderhouds venster configureren](maintenance-window-configure.md)

## <a name="learn-more"></a>Lees meer

* [Veelgestelde vragen over onderhouds Vensters](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
* [Azure-onderhouds gebeurtenissen plannen in Azure SQL Database en Azure SQL Managed instance](planned-maintenance.md)




