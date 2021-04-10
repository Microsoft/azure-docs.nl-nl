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
ms.date: 03/23/2021
ms.openlocfilehash: 9d7ab0498673ad7006087b66575eea9371b96d11
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565876"
---
# <a name="maintenance-window-preview"></a>Onderhouds venster (preview-versie)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Met de functie onderhouds venster kunt u onderhouds planning voor [Azure SQL database](sql-database-paas-overview.md) en [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) -resources configureren die invloed hebben op voorspel bare onderhouds gebeurtenissen en die minder storend zijn voor uw werk belasting. 

> [!Note]
> De functie onderhouds venster is niet beveiligd tegen niet-geplande gebeurtenissen, zoals hardwarestoringen, waardoor korte verbindings onderbrekingen kunnen ontstaan.

## <a name="overview"></a>Overzicht

Azure voert periodiek [gepland onderhoud](planned-maintenance.md) uit van SQL database en SQL Managed instance-resources. Tijdens de onderhouds gebeurtenis van Azure SQL zijn de data bases volledig beschikbaar, maar kunnen ze onderhevig zijn aan korte herconfiguraties binnen de verschillende beschikbaarheids-Sla's voor [SQL database](https://azure.microsoft.com/support/legal/sla/sql-database) en [SQL Managed instance](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance).

Onderhouds venster is bedoeld voor productie werkbelastingen die niet kunnen worden geconfigureerd voor het opnieuw configureren van een Data Base of het exemplaar van exemplaren, en kan geen snelle onderbrekingen in de verbinding opvangen door geplande onderhouds gebeurtenissen. Als u een onderhouds venster kiest, kunt u de gevolgen van gepland onderhoud minimaliseren, omdat deze buiten uw kantoor uren worden uitgevoerd. Flexibele workloads en niet-productiewerk belastingen kunnen afhankelijk zijn van het standaard onderhouds beleid van Azure SQL.

Het onderhouds venster kan worden geconfigureerd bij het maken of voor bestaande Azure SQL-resources. Het kan worden geconfigureerd met behulp van de Azure Portal, Power shell, CLI of de Azure-API.

> [!Important]
> Het configureren van het onderhouds venster is een langlopende asynchrone bewerking, vergelijkbaar met het wijzigen van de servicelaag van de Azure SQL-resource. De resource is beschikbaar tijdens de bewerking, met uitzonde ring van een korte herconfiguratie die aan het einde van de bewerking plaatsvindt, en die doorgaans tot 8 seconden duurt, zelfs in het geval van langdurige langlopende trans acties. Als u de gevolgen van de herconfiguratie wilt beperken, moet u de bewerking buiten de piek uren uitvoeren.

### <a name="gain-more-predictability-with-maintenance-window"></a>Meer voorspel baarheid krijgen met het onderhouds venster

Het Azure SQL-onderhouds beleid blokkeert standaard updates tijdens de periode **8 a.m. om de lokale tijd elke dag te tot 17:00 uur** om onderbrekingen te voor komen tijdens typische piek uren. De lokale tijd wordt bepaald door de locatie van de [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) die als host fungeert voor de resource en kan de zomer tijd op basis van de lokale tijd zone definitie observeren. 

U kunt de onderhouds updates verder aanpassen aan een tijd die geschikt is voor uw Azure SQL-resources door te kiezen uit twee extra onderhouds venster sleuven:
 
* Venster weekdag, 10PM naar 06.00 lokale tijd maandag-donderdag
* Weekend Window 10PM to 06.00 local time vrijdag-zondag

Zodra de selectie van het onderhouds venster is gemaakt en de service configuratie is voltooid, wordt gepland onderhoud alleen uitgevoerd tijdens uw keuze.   

> [!Important]
> In zeldzame gevallen waarbij elke uitstel van een actie ernstige gevolgen kan veroorzaken, zoals het Toep assen van een essentiële beveiligings patch, kan het geconfigureerde onderhouds venster tijdelijk worden overschreven. 

### <a name="cost-and-eligibility"></a>Kosten en geschiktheid

Het configureren en gebruiken van het onderhouds venster is gratis voor alle typen in aanmerking komende [aanbiedingen](https://azure.microsoft.com/support/legal/offer-details/): betalen per gebruik, Cloud Solution Provider (CSP), micro soft Enterprise overeenkomst of micro soft-klant overeenkomst.

> [!Note]
> Een Azure-aanbieding is het type Azure-abonnement dat u hebt. Een abonnement met [betalen per gebruik-tarieven](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in open](https://azure.microsoft.com/offers/ms-azr-0111p/)en [Visual Studio Enter prise](https://azure.microsoft.com/offers/ms-azr-0063p/) is bijvoorbeeld allemaal Azure-aanbiedingen. Elke aanbieding of elk plan heeft verschillende voor delen. Uw aanbieding of abonnement wordt weer gegeven in het overzicht van het abonnement. Zie [uw Azure-abonnement wijzigen in een andere aanbieding](../../cost-management-billing/manage/switch-azure-offer.md)voor meer informatie over het overschakelen op een ander abonnement.

## <a name="advance-notifications"></a>Voorafgaande meldingen

Onderhouds meldingen kunnen worden geconfigureerd om u te waarschuwen over toekomstige geplande onderhouds gebeurtenissen voor uw Azure SQL Database 24 uur vooraf, op het moment van onderhoud en wanneer het onderhoud is voltooid. Zie [Advance Notifications](advance-notifications.md)(Engelstalig) voor meer informatie.

## <a name="availability"></a>Beschikbaarheid

### <a name="supported-service-level-objectives"></a>Ondersteunde serviceniveau doelstellingen

Het kiezen van een ander onderhouds venster dan de standaard instelling is beschikbaar op alle Slo's **, met uitzonde ring van**:
* Hyperscale 
* Exemplaargroepen
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
- Azië - oost
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

* In Azure SQL Database kunnen alle verbindingen die gebruikmaken van het beleid voor proxy verbindingen, worden beïnvloed door het gekozen onderhouds venster en een onderhouds venster voor gateway knooppunten. Client verbindingen die gebruikmaken van het aanbevolen verbindings beleid voor omleiding worden echter niet beïnvloed door de herconfiguratie van een gateway knooppunt onderhoud. 

* In Azure SQL Managed instance worden de gateway knooppunten gehost [in het virtuele cluster](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) en hebben hetzelfde onderhouds venster als het beheerde exemplaar, maar het gebruik van het verbindings beleid omleiden wordt nog steeds aanbevolen om het aantal onderbrekingen tijdens de onderhouds gebeurtenis te beperken.

Zie [Azure SQL database verbindings beleid](../database/connectivity-architecture.md#connection-policy)voor meer informatie over het client verbindings beleid in Azure SQL database. 

Zie [Azure SQL Managed instance Connect types](../../azure-sql/managed-instance/connection-types-overview.md)(Engelstalig) voor meer informatie over het client verbindings beleid in Azure SQL Managed instance.

## <a name="considerations-for-azure-sql-managed-instance"></a>Overwegingen voor Azure SQL Managed instance

Azure SQL Managed instance bestaat uit service onderdelen die worden gehost op een speciale set van geïsoleerde virtuele machines die worden uitgevoerd in het subnet van het virtuele netwerk van de klant. Met deze virtuele machines kunt u [virtuele clusters (s)](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture) maken die meerdere beheerde instanties kunnen hosten. Het onderhouds venster dat op exemplaren van één subnet is geconfigureerd, kan invloed hebben op het aantal virtuele clusters binnen het subnet en de distributie van exemplaren tussen virtuele clusters. Dit kan vereisen dat er weinig effecten zijn.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>Configuratie van onderhouds venster is een langlopende bewerking 
Alle exemplaren die worden gehost in een virtueel cluster, delen het onderhouds venster. Standaard worden alle beheerde exemplaren gehost in het virtuele cluster met het standaard onderhouds venster. Als u een ander onderhouds venster voor een beheerd exemplaar opgeeft tijdens het maken of later, betekent dit dat het in een virtueel cluster met het bijbehorende onderhouds venster moet worden geplaatst. Als er zich geen virtueel cluster in het subnet bevindt, moet het eerst worden gemaakt om aan het exemplaar te kunnen voldoen. Voor het maken van extra exemplaren in het bestaande virtuele cluster is het mogelijk dat het cluster kleiner wordt. Beide bewerkingen dragen bij aan de duur van het configureren van het onderhouds venster voor een beheerd exemplaar.
De verwachte duur van het configureren van het onderhouds venster op een beheerd exemplaar kan worden berekend op basis [van de geschatte duur van beheer bewerkingen voor instanties](../managed-instance/management-operations-overview.md#duration).

> [!Important]
> Een korte herconfiguratie vindt plaats aan het einde van de onderhouds bewerking en duurt doorgaans tot 8 seconden, zelfs in het geval van langdurige langlopende trans acties. Als u de gevolgen van de herconfiguratie wilt beperken, moet u de bewerking buiten de piek uren plannen.

### <a name="ip-address-space-requirements"></a>Vereisten voor de IP-adres ruimte
Voor elk nieuw virtueel cluster in het subnet zijn extra IP-adressen vereist volgens de toewijzing van het [IP-adres van het virtuele cluster](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size). Het wijzigen van het onderhouds venster voor een bestaand beheerd exemplaar vereist ook een [tijdelijke extra IP-capaciteit](../managed-instance/vnet-subnet-determine-size.md#address-requirements-for-update-scenarios) , zoals bij het schalen van het vCores-scenario voor de bijbehorende servicelaag.

### <a name="ip-address-change"></a>IP-adreswijziging
Het configureren en wijzigen van het onderhouds venster veroorzaakt een wijziging van het IP-adres van de instantie binnen het IP-adres bereik van het subnet.

> [!Important]
>  Zorg ervoor dat de NSG-en firewall regels geen gegevens verkeer blok keren nadat het IP-adres is gewijzigd. 

## <a name="next-steps"></a>Volgende stappen

* [Voorafgaande meldingen](advance-notifications.md)
* [Onderhoudsvenster configureren](maintenance-window-configure.md)

## <a name="learn-more"></a>Lees meer

* [Veelgestelde vragen over onderhouds Vensters](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL-beheerd exemplaar](../managed-instance/sql-managed-instance-paas-overview.md)
* [Azure-onderhouds gebeurtenissen plannen in Azure SQL Database en Azure SQL Managed instance](planned-maintenance.md)