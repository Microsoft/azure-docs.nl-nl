---
title: Functies van Synapse-werk ruimte inschakelen op een toegewezen SQL-groep (voorheen SQL DW)
description: In dit document wordt beschreven hoe een klant in de werk ruimte het bestaande SQL DW zelfstandige exemplaar kan openen en gebruiken.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694611"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Functies van Synapse-werk ruimte inschakelen op een bestaande toegewezen SQL-groep (voorheen SQL DW)

Alle SQL Data Warehouse-klanten hebben nu toegang tot en gebruik van een bestaande toegewezen SQL-groep (voorheen SQL DW)-exemplaar via de Synapse Studio en de werk ruimte, zonder dat dit van invloed is op automatisering, verbindingen of hulp middelen. In dit artikel wordt uitgelegd hoe een bestaande Azure Synapse Analytics-klant hun bestaande analyse oplossing kan bouwen en uitbreiden door gebruik te maken van de nieuwe functie-uitgebreide mogelijkheden die nu beschikbaar zijn via de Synapse-werk ruimte en Studio.   

## <a name="experience"></a>Ervaring
 
Nu de Synapse-werk ruimte beschikbaar is in de overzichts sectie van DW Portal, waarmee u een Synapse-werk ruimte kunt maken voor uw bestaande toegewezen SQL-groep (voorheen SQL DW)-exemplaren. Met deze nieuwe mogelijkheid kunt u de logische server die als host fungeert voor uw bestaande Data Warehouse-instanties verbinden met een nieuwe Synapse-werk ruimte. De verbinding zorgt ervoor dat alle data warehouses die worden gehost op die server toegankelijk worden gemaakt vanuit de werk ruimte en Studio en kunnen worden gebruikt in combi natie met de Synapse-partner Services (serverloze SQL-pool, Apache Spark pool en ADF). U kunt beginnen met het openen en gebruiken van uw resources zodra de inrichtings stappen zijn voltooid en de verbinding tot stand is gebracht met de zojuist gemaakte werk ruimte.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Verbonden Synapse-werk ruimte":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Synapse-werk ruimte en studio-functies gebruiken om toegang te krijgen tot en gebruik te maken van een toegewezen SQL-groep (voorheen SQL DW)
 
De volgende informatie is van toepassing wanneer u een speciale SQL-DW (voorheen SQL DW) gebruikt en de functies van de Synapse-werk ruimte ingeschakeld zijn: 
- **SQL-mogelijkheden** Alle SQL-mogelijkheden blijven met de logische SQL-Server, nadat de Synapse-werkruimte functie is ingeschakeld. Toegang tot de server via de SQL-resource provider is nog steeds mogelijk nadat de werk ruimte is ingeschakeld. Alle beheer functies kunnen worden gestart via de werk ruimte en de bewerking wordt uitgevoerd op de logische SQL Server die als host fungeert voor uw SQL-groepen. Er worden geen bestaande automatisering, hulpprogram ma's of verbindingen verbroken of onderbroken wanneer een werk ruimte is ingeschakeld.  
- **Resource verplaatsen**  Het initiëren van een resource verplaatsing op een server waarop de functie Synapse-werk ruimte is ingeschakeld, zorgt ervoor dat de koppeling tussen de server en de werk ruimte wordt verbroken en dat u geen toegang meer hebt tot uw bestaande toegewezen SQL-groep (voorheen SQL DW)-exemplaren van de werk ruimte. Om ervoor te zorgen dat de verbinding wordt behouden, is het raadzaam dat beide resources binnen hetzelfde abonnement en dezelfde resource groep blijven. 
- **Bewaking** SQL-aanvragen die via de Synapse Studio worden verzonden in een toegewezen SQL-groep (voorheen SQL DW), kunnen worden weer gegeven in de monitor hub. Voor alle andere controleactiviteiten kunt u de controlefunctie voor toegewezen SQL-pools (voorheen SQL DW) van de Azure-portal gebruiken. 
- **Beveiligings** -en **toegangs** beheer zoals hierboven is vermeld, blijven alle management functies voor uw SQL Server-en toegewezen SQL-groepen (voorheen SQL DW) aanwezig op de logische SQL-Server. Deze functies omvatten, firewall regel beheer, het instellen van de Azure AD-beheerder van de server en alle toegangs beheer voor de gegevens in uw toegewezen SQL-groep (voorheen SQL DW). De volgende stappen moeten worden uitgevoerd om ervoor te zorgen dat uw specifieke SQL-groep (voorheen SQL DW) toegankelijk is en kan worden gebruikt via de werk ruimte Synapse. De lidmaatschappen van de werk ruimte geven gebruikers geen machtigingen voor de gegevens in een toegewezen SQL-groep (voorheen SQL DW-exemplaren). Volg uw normale [SQL-verificatie](sql-data-warehouse-authentication.md) beleid om ervoor te zorgen dat gebruikers toegang hebben tot de toegewezen SQL-groep (voorheen SQL DW)-exemplaren op de logische server. Als er al een beheerde identiteit is toegewezen aan de toegewezen SQL-groep (voorheen SQL DW), is de naam van de beheerde identiteit hetzelfde als die van de werk ruimte die wordt beheerd met een identiteit die automatisch wordt gemaakt ter ondersteuning van de werkruimte partner Services (bijvoorbeeld ADF-pijp lijnen).  Er kunnen twee beheerde identiteiten met dezelfde naam bestaan in een verbonden scenario. De beheerde identiteiten kunnen worden onderscheiden door hun Azure AD-object-Id's, functionaliteit voor het maken van SQL-gebruikers met behulp van object-Id's binnenkort beschikbaar.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > In de verbonden werk ruimte Synapse Studio worden de namen weer gegeven van toegewezen Pools op basis van de machtigingen die de gebruiker heeft in Azure. Objecten onder de Pools zijn niet toegankelijk als de gebruiker geen machtigingen heeft voor de SQL-groepen. 

- **Netwerk beveiliging** Als de Synapse-werk ruimte die u hebt ingeschakeld voor uw bestaande toegewezen SQL-groep (voorheen SQL DW) is ingeschakeld voor beveiliging tegen gegevens infiltratie. Een beheerde persoonlijke eindpunt verbinding maken vanuit de werk ruimte naar de logische SQL-Server. De verbindings aanvraag van het particuliere eind punt goed keuren om communicatie tussen de server en de werk ruimte mogelijk te maken.
- **Studio** SQL-groepen in de **Data hub** een toegewezen SQL-groep die is ingeschakeld voor een werk ruimte (voorheen SQL DW), kunnen worden geïdentificeerd via de knop info in de data hub. 
- **Een nieuwe toegewezen SQL-groep maken (voorheen SQL DW)** Nieuwe toegewezen SQL-groepen kunnen worden gemaakt via de Synapse-werk ruimte en Studio nadat de werkruimte functie is ingeschakeld en de inrichting van een nieuwe groep wordt uitgevoerd op de logische SQL-Server. De nieuwe resources worden weer gegeven in de portal en Studio wanneer het inrichten is voltooid.      

## <a name="next-steps"></a>Volgende stappen
[Functies van Synapse-werk ruimte](workspace-connected-create.md) inschakelen in uw bestaande toegewezen SQL-groep (voorheen SQL DW)
