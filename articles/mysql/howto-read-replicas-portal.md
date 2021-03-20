---
title: Lees replica's beheren-Azure Portal-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van Lees replica's in Azure Database for MySQL met behulp van de Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 26b503e7d55ed3d2f9bd06837551655e7af05a17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541937"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Lees replica's maken en beheren in Azure Database for MySQL met behulp van de Azure Portal

In dit artikel leert u hoe u in de Azure Database for MySQL-service Lees replica's maakt en beheert met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

- Een [Azure database for mysql-server](quickstart-create-mysql-server-database-using-azure-portal.md) die wordt gebruikt als de bron server.

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MySQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de bron server zich in een van deze prijs categorieën bevindt.

## <a name="create-a-read-replica"></a>Een leesreplica maken

> [!IMPORTANT]
> Wanneer u een replica maakt voor een bron die geen bestaande replica's heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd dit in overweging en voer deze bewerkingen uit tijdens een rustige periode.

Een lees replica-server kan worden gemaakt met behulp van de volgende stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Selecteer de bestaande Azure Database for MySQL-server die u wilt gebruiken als een Master. Met deze actie wordt de pagina **overzicht** geopend.

3. Selecteer **replicatie** in het menu onder **instellingen**.

4. Selecteer **replica toevoegen**.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL-replicatie":::

5. Voer een naam in voor de replica server.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL-replica naam":::

6. Selecteer de locatie voor de replica server. De standaard locatie is dezelfde als die van de bron server.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Azure Database for MySQL-replica locatie":::

   > [!NOTE]
   > Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

7. Selecteer **OK** om te bevestigen dat u de replica wilt maken.

> [!NOTE]
> Lees replica's worden gemaakt met dezelfde server configuratie als de Master. De configuratie van de replica server kan worden gewijzigd nadat deze is gemaakt. De replica server wordt altijd gemaakt in dezelfde resource groep en hetzelfde abonnement als de bron server. Als u een replica server wilt maken naar een andere resource groep of een ander abonnement, kunt u [de replica-server verplaatsen](../azure-resource-manager/management/move-resource-group-and-subscription.md) nadat u deze hebt gemaakt. Het is raadzaam om de configuratie van de replica server te behouden in gelijke of hogere waarden dan de bron om ervoor te zorgen dat de replica kan blijven werken met de Master.

Zodra de replica server is gemaakt, kan deze worden weer gegeven op de Blade **replicatie** .

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL-lijst replica's":::

## <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica server stoppen

> [!IMPORTANT]
> Het stoppen van de replicatie naar een server is onomkeerbaar. Zodra de replicatie tussen een bron en replica is gestopt, kan deze niet meer ongedaan worden gemaakt. De replica-server wordt vervolgens een zelfstandige server en ondersteunt nu lezen en schrijven. Deze server kan niet opnieuw in een replica worden gemaakt.

Als u de replicatie tussen een bron en een replica server van de Azure Portal wilt stoppen, gebruikt u de volgende stappen:

1. Selecteer uw bron Azure Database for MySQL server in het Azure Portal. 

2. Selecteer **replicatie** in het menu onder **instellingen**.

3. Selecteer de replica server waarvoor u de replicatie wilt stoppen.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL-replicatie stoppen server selecteren":::

4. Selecteer **Replicatie stoppen**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL-replicatie stoppen":::

5. Bevestig dat u de replicatie wilt stoppen door op **OK** te klikken.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL-replicatie stoppen bevestigen":::

## <a name="delete-a-replica-server"></a>Een replica server verwijderen

Voer de volgende stappen uit om een lees replica-server te verwijderen uit de Azure Portal:

1. Selecteer uw bron Azure Database for MySQL server in het Azure Portal.

2. Selecteer **replicatie** in het menu onder **instellingen**.

3. Selecteer de replica server die u wilt verwijderen.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL-replica verwijderen server selecteren":::

4. **Replica verwijderen** selecteren

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL-replica verwijderen":::

5. Typ de naam van de replica en klik op **verwijderen** om de verwijdering van de replica te bevestigen.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL-replica verwijderen bevestigen":::

## <a name="delete-a-source-server"></a>Een bron server verwijderen

> [!IMPORTANT]
> Als u een bronserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de bronserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Gebruik de volgende stappen om een bron server te verwijderen uit de Azure Portal:

1. Selecteer uw bron Azure Database for MySQL server in het Azure Portal.

2. Selecteer **verwijderen** in het **overzicht**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL-Master verwijderen":::

3. Typ de naam van de bron server en klik op **verwijderen** om de verwijdering van de bron server te bevestigen.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL-Master verwijderen bevestigen":::

## <a name="monitor-replication"></a>Replicatie controleren

1. Selecteer in de [Azure Portal](https://portal.azure.com/)de replica Azure database for mysql server die u wilt bewaken.

2. Onder de sectie **bewaking** van de zijbalk selecteert u **metrische gegevens**:

3. Selecteer **replicatie vertraging in seconden in** de vervolg keuzelijst met beschik bare metrische gegevens.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Replicatie vertraging selecteren":::

4. Selecteer het tijds bereik dat u wilt weer geven. In de onderstaande afbeelding wordt een tijds bereik van 30 minuten geselecteerd.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Tijds bereik selecteren":::

5. De replicatie vertraging voor het geselecteerde tijds bereik weer geven. In de onderstaande afbeelding wordt de laatste 30 minuten weer gegeven.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Selecteer een tijds bereik van 30 minuten":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [lezen van replica's](concepts-read-replicas.md)