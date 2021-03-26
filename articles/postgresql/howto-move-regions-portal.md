---
title: Azure-regio's verplaatsen-Azure Portal-Azure Database for PostgreSQL-één server
description: Verplaats een Azure Database for PostgreSQL-server van de ene Azure-regio naar de andere met behulp van een lees replica en de Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: 204815d36baf9fe6e7fe8c0e194ac7acc14f72e4
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606467"
---
# <a name="move-an-azure-database-for-azure-database-for-postgresql---single-server-to-another-region-by-using-the-azure-portal"></a>Een Azure-Data Base voor Azure Database for PostgreSQL-één server naar een andere regio verplaatsen met behulp van de Azure Portal

Er zijn verschillende scenario's voor het verplaatsen van een bestaande Azure Database for PostgreSQL-server van de ene regio naar een andere. Het is bijvoorbeeld mogelijk dat u een productie server naar een andere regio wilt verplaatsen als onderdeel van de planning voor herstel na nood gevallen.

U kunt een Azure Database for PostgreSQL [Kruis regio lezen replica](concepts-read-replicas.md#cross-region-replication) gebruiken om de overgang naar een andere regio te volt ooien. Als u dit wilt doen, maakt u eerst een lees replica in de doel regio. Stop vervolgens de replicatie naar de server voor het lezen van replica's om een zelfstandige server te maken die zowel lees-als schrijf verkeer accepteert. 

> [!NOTE]
> Dit artikel richt zich op het verplaatsen van uw server naar een andere regio. Als u uw server wilt verplaatsen naar een andere resource groep of een ander abonnement, raadpleegt u het artikel over [verplaatsen](../azure-resource-manager/management/move-resource-group-and-subscription.md) . 

## <a name="prerequisites"></a>Vereisten

- De functie voor het lezen van replica's in meerdere regio's is alleen beschikbaar voor Azure Database for PostgreSQL-één server in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de bron server zich in een van deze prijs categorieën bevindt.

- Zorg ervoor dat uw Azure Database for PostgreSQL-bron server zich in de Azure-regio bevindt waaruit u wilt verplaatsen.

## <a name="prepare-to-move"></a>Voorbereiden om te verplaatsen

Gebruik de volgende stappen om de bron server voor te bereiden voor replicatie met behulp van de Azure Portal: 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
1. Selecteer de bestaande Azure Database for PostgreSQL-server die u wilt gebruiken als de bron server. Met deze actie wordt de pagina **overzicht** geopend.
1. Selecteer in het menu van de server de optie **replicatie**. Als ondersteuning voor Azure-replicatie is ingesteld op ten minste een **replica**, kunt u lees replica's maken. 
1. Als Azure-replicatie ondersteuning niet is ingesteld op ten minste een **replica**, stelt u deze in. Selecteer **Opslaan**.
1. Start de server opnieuw op om de wijziging toe te passen door **Ja** te selecteren.
1. Zodra de bewerking is voltooid, ontvangt u twee meldingen over Azure Portal. Er is één melding voor het bijwerken van de server parameter. Er is een andere melding voor de herstart van de server die onmiddellijk volgt.
1. Vernieuw de Azure Portal pagina om de werk balk replicatie bij te werken. U kunt nu lees replica's maken voor deze server.

Als u in de doel regio een cross-Region replica server wilt maken met behulp van de Azure Portal, gebruikt u de volgende stappen:

1. Selecteer de bestaande Azure Database for PostgreSQL-server die u wilt gebruiken als de bron server.
1. Selecteer **replicatie** in het menu onder **instellingen**.
1. Selecteer **replica toevoegen**.
1. Voer een naam in voor de replica server.
1. Selecteer de locatie voor de replica server. De standaard locatie is dezelfde als die van de primaire server. Controleer of u de doel locatie hebt geselecteerd waarop u de replica wilt implementeren.
1. Selecteer **OK** om te bevestigen dat u de replica wilt maken. Tijdens het maken van de replica worden gegevens van de bron server naar de replica gekopieerd. Het maken van een tijd kan een paar minuten of meer duren, in verhouding tot de grootte van de bron server.

>[!NOTE]
> Wanneer u een replica maakt, neemt deze de firewall regels en de VNet-service-eind punten van de primaire server niet over. Deze regels moeten onafhankelijk worden ingesteld voor de replica.

## <a name="move"></a>Verplaatsen

> [!IMPORTANT]
> De zelfstandige server kan niet opnieuw in een replica worden gemaakt.
> Voordat u de replicatie op een lees replica stopt, moet u ervoor zorgen dat de replica over alle gegevens beschikt die u nodig hebt.

Voer de volgende stappen uit om de replicatie naar de replica vanuit het Azure Portal te stoppen:

1. Wanneer de replica is gemaakt, zoekt en selecteert u uw Azure Database for PostgreSQL-bron server. 
1. Selecteer **replicatie** in het menu onder **instellingen**.
1. Selecteer de replica server.
1. Selecteer **Replicatie stoppen**.
1. Bevestig dat u de replicatie wilt stoppen door op **OK** te klikken.

## <a name="clean-up-source-server"></a>Bron server opschonen

Mogelijk wilt u de bron Azure Database for PostgreSQL server verwijderen. Volg hiervoor de volgende stappen:

1. Wanneer de replica is gemaakt, zoekt en selecteert u uw Azure Database for PostgreSQL-bron server.
1. Selecteer in het venster **overzicht** de optie **verwijderen**.
1. Typ de naam van de bron server om te bevestigen dat u wilt verwijderen.
1. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure Database for PostgreSQL server van de ene naar de andere regio verplaatst met behulp van de Azure Portal en vervolgens de overbodige bron bronnen opschoont. 

- Meer informatie over het [lezen van replica's](concepts-read-replicas.md)
- Meer informatie over [het beheren van Lees replica's in de Azure Portal](howto-read-replicas-portal.md)
- Meer informatie over opties voor [bedrijfs continuïteit](concepts-business-continuity.md)