---
title: Functies van Synapse-werk ruimte inschakelen
description: In dit document wordt beschreven hoe een gebruiker de functies van de Synapse-werk ruimte kan inschakelen op een bestaande toegewezen SQL-groep (voorheen SQL DW).
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: a1fbc6eede6c82020b765185602c672c1162fdf8
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548185"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Functies van Synapse-werk ruimte inschakelen voor een toegewezen SQL-groep (voorheen SQL DW)

Alle SQL Data Warehouse-gebruikers hebben nu toegang tot en gebruiken een bestaande toegewezen SQL-groep (voorheen SQL DW)-exemplaar via de Synapse Studio en de werk ruimte. Gebruikers kunnen de Synapse Studio en de werk ruimte gebruiken zonder dat dit van invloed is op automatisering, verbindingen of hulp middelen. In dit artikel wordt uitgelegd hoe een bestaande Azure Synapse Analytics-gebruiker de functies van de Synapse-werk ruimte kan inschakelen voor een bestaande toegewezen SQL-groep (voorheen SQL DW). De gebruiker kan hun bestaande analyse oplossing uitbreiden door gebruik te maken van de nieuwe functies-uitgebreide mogelijkheden die nu beschikbaar zijn via de Synapse-werk ruimte en Studio.   

## <a name="prerequisites"></a>Vereisten
Voordat u de functies van de Synapse-werk ruimte in uw data warehouse inschakelt, moet u ervoor zorgen dat u over het volgende beschikt
- Rechten voor het maken en beheren van de SQL-resources die worden gehost op de logische SQL-Server.
- Rechten voor het maken van Azure Synapse-resources.
- Een Azure Active Directory beheerder geïdentificeerd op de logische server

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Functies van Synapse-werk ruimte inschakelen voor een bestaande toegewezen SQL-groep (voorheen SQL DW)

De functies van de Synapse-werk ruimte kunnen worden ingeschakeld op elke bestaande toegewezen SQL-groep (voorheen SQL DW) in een ondersteunde regio. Deze mogelijkheid is alleen beschikbaar via de Azure Portal.

Volg deze stappen om een Synapse-werk ruimte te maken voor uw bestaande Data Warehouse.
1. Selecteer een bestaande exclusieve SQL-groep (voorheen SQL DW) en open de pagina overzicht.
2. Selecteer in de bovenste menu balk een **nieuwe Synapse-werk ruimte** of het bericht dat hieronder wordt beschreven.
3. Controleer de lijst met data warehouses die beschikbaar worden gesteld via de nieuwe Synapse-werk ruimte op de pagina **nieuwe Azure Synapse-werk ruimte maken** . Selecteer **door gaan** om door te gaan.
4. Op de pagina basis beginselen moet de bestaande toegewezen SQL-groep (de sectie **project** Details vooraf zijn ingevuld met hetzelfde **abonnement** en dezelfde **resource groep** die is geïmplementeerd op de logische server). En onder **werkruimte Details** is de naam van de **werk ruimte** vooraf ingevuld met dezelfde naam en regio van de logische SQL-Server om ervoor te zorgen dat de verbinding tussen de nieuwe Synapse-werk ruimte en de logische server tijdens het inrichtings proces kan worden gemaakt. Het na inrichting van deze verbinding moet worden bewaard om te zorgen voor permanente toegang tot de toegewezen SQL-groep (voorheen SQL DW)-instanties via de werk ruimte en Studio.
5. Navigeer naar Data Lake Storage Gen 2 selecteren.
6. Selecteer **nieuwe maken** of selecteer een bestaande **Data Lake Storage Gen2** voordat u **volgende selecteert: netwerk**.
7. Kies een **SQL-beheerders wachtwoord** voor uw **serverloze exemplaar**. Als u dit wacht woord wijzigt, worden de SQL-referenties van de logische server niet bijgewerkt of gewijzigd. Laat deze velden leeg als u de voor keur geeft aan een door het systeem gedefinieerd wacht woord. Dit wacht woord kan op elk gewenst moment in de nieuwe werk ruimte worden gewijzigd. De naam van de beheerder moet hetzelfde zijn als die in de SQL Server.
8. Selecteer uw voorkeurs **instellingen voor netwerken** en selecteer **controleren + maken** om de inrichting van de werk ruimte te starten.
9. Selecteer **Ga naar resource** om uw nieuwe werk ruimte te openen.

    > [!NOTE]
    > Alle toegewezen SQL-groeps instanties (voorheen SQL DW) die worden gehost op de logische server, zijn beschikbaar via de nieuwe werk ruimte.

## <a name="post-provisioning-steps"></a>Stappen na inrichting
De volgende stappen moeten worden uitgevoerd om ervoor te zorgen dat uw bestaande toegewezen SQL-groep (voorheen SQL DW)-instanties toegankelijk zijn via de Synapse Studio.
1. Selecteer op de pagina overzicht van de Synapse-werk ruimte de optie **verbonden server**. **Met de verbonden server** gaat u naar de verbinding met de logische SQL-Server die als host fungeert voor uw data warehouses. Selecteer **verbonden server** in het menu essentieel.
2. Open **firewalls en virtuele netwerken** en zorg ervoor dat uw client-IP of een vooraf bepaald IP-bereik toegang heeft tot de logische server.
3. Open **Active Directory beheerder** en zorg ervoor dat er een Aad-beheerder is ingesteld op de logische server.
4. Selecteer een van de toegewezen SQL-groeps exemplaren (voorheen SQL DW) die worden gehost op de logische server. Op de pagina Overzicht selecteert u **Synapse Studio starten** of gaat u naar de [Synapse Studio](https://web.azuresynapse.net) en meldt u zich aan bij uw werk ruimte.

5. Open de **Data hub** en vouw de exclusieve SQL-groep in de object Verkenner uit om ervoor te zorgen dat u toegang hebt tot uw data warehouse en query's kunt uitvoeren.

## <a name="next-steps"></a>Volgende stappen
Aan de slag met [Synapse-werk ruimte en Studio](../get-started.md).
