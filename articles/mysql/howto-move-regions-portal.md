---
title: Azure-regio's verplaatsen-Azure Portal-Azure Database for MySQL
description: Verplaats een Azure Database for MySQL-server van de ene Azure-regio naar de andere met behulp van een lees replica en de Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.openlocfilehash: 87c44e5d56e59b0bf003e8e7d25f3351f58a8984
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065477"
---
# <a name="move-an-azure-database-for-mysql-server-to-another-region-by-using-the-azure-portal"></a>Een Azure Database for MySQL-server naar een andere regio verplaatsen met behulp van de Azure Portal

Er zijn verschillende scenario's voor het verplaatsen van een bestaande Azure Database for MySQL-server van de ene regio naar een andere. Het is bijvoorbeeld mogelijk dat u een productie server naar een andere regio wilt verplaatsen als onderdeel van de planning voor herstel na nood gevallen.

U kunt een Azure Database for MySQL [Kruis regio lezen replica](concepts-read-replicas.md#cross-region-replication) gebruiken om de overgang naar een andere regio te volt ooien. Als u dit wilt doen, maakt u eerst een lees replica in de doel regio. Stop vervolgens de replicatie naar de server voor het lezen van replica's om een zelfstandige server te maken die zowel lees-als schrijf verkeer accepteert. 

> [!NOTE]
> Dit artikel richt zich op het verplaatsen van uw server naar een andere regio. Als u uw server wilt verplaatsen naar een andere resource groep of een ander abonnement, raadpleegt u het artikel over [verplaatsen](../azure-resource-manager/management/move-resource-group-and-subscription.md) . 

## <a name="prerequisites"></a>Vereisten

- De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MySQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de bron server zich in een van deze prijs categorieën bevindt.

- Zorg ervoor dat uw Azure Database for MySQL-bron server zich in de Azure-regio bevindt waaruit u wilt verplaatsen.

## <a name="prepare-to-move"></a>Voorbereiden om te verplaatsen

Als u in de doel regio een cross-Region replica server wilt maken met behulp van de Azure Portal, gebruikt u de volgende stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
1. Selecteer de bestaande Azure Database for MySQL-server die u wilt gebruiken als de bron server. Met deze actie wordt de pagina **overzicht** geopend.
1. Selecteer **replicatie** in het menu onder **instellingen**.
1. Selecteer **replica toevoegen**.
1. Voer een naam in voor de replica server.
1. Selecteer de locatie voor de replica server. De standaard locatie is dezelfde als die van de bron server. Controleer of u de doel locatie hebt geselecteerd waarop u de replica wilt implementeren.
1. Selecteer **OK** om te bevestigen dat u de replica wilt maken. Tijdens het maken van de replica worden gegevens van de bron server naar de replica gekopieerd. Het maken van een tijd kan een paar minuten of meer duren, in verhouding tot de grootte van de bron server.

>[!NOTE]
> Wanneer u een replica maakt, neemt deze de VNet-service-eind punten van de bron server niet over. Deze regels moeten onafhankelijk worden ingesteld voor de replica.

## <a name="move"></a>Verplaatsen

> [!IMPORTANT]
> De zelfstandige server kan niet opnieuw in een replica worden gemaakt.
> Voordat u de replicatie op een lees replica stopt, moet u ervoor zorgen dat de replica over alle gegevens beschikt die u nodig hebt.

De replicatie naar de replica server wordt gestopt, waardoor deze een zelfstandige server wordt. Voer de volgende stappen uit om de replicatie naar de replica vanuit het Azure Portal te stoppen:

1. Wanneer de replica is gemaakt, zoekt en selecteert u uw Azure Database for MySQL-bron server. 
1. Selecteer **replicatie** in het menu onder **instellingen**.
1. Selecteer de replica server.
1. Selecteer **Replicatie stoppen**.
1. Bevestig dat u de replicatie wilt stoppen door op **OK** te klikken.

## <a name="clean-up-source-server"></a>Bron server opschonen

Mogelijk wilt u de bron Azure Database for MySQL server verwijderen. Volg hiervoor de volgende stappen:

1. Wanneer de replica is gemaakt, zoekt en selecteert u uw Azure Database for MySQL-bron server.
1. Selecteer in het venster **overzicht** de optie **verwijderen**.
1. Typ de naam van de bron server om te bevestigen dat u wilt verwijderen.
1. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure Database for MySQL server van de ene naar de andere regio verplaatst met behulp van de Azure Portal en vervolgens de overbodige bron bronnen opschoont. 

- Meer informatie over het [lezen van replica's](concepts-read-replicas.md)
- Meer informatie over [het beheren van Lees replica's in de Azure Portal](howto-read-replicas-portal.md)
- Meer informatie over opties voor [bedrijfs continuïteit](concepts-business-continuity.md)