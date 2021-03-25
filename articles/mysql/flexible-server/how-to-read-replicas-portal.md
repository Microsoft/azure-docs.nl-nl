---
title: Lees replica's beheren-Azure Portal-Azure Database for MySQL-flexibele server
description: Meer informatie over het instellen en beheren van Lees replica's in Azure Database for MySQL flexibele server met behulp van de Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: fd303804706f9ae210e6714cc8698c94c39ebef6
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106850"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Lees replica's maken en beheren op Azure Database for MySQL flexibele server met behulp van de Azure Portal

> [!IMPORTANT]
> Het lezen van replica's in Azure Database for MySQL-flexibele server is in preview.

In dit artikel vindt u informatie over het maken en beheren van Lees replica's op de Azure Database for MySQL flexibele server met behulp van de Azure Portal.

> [!Note]
> Replica wordt niet ondersteund op een server met hoge Beschik baarheid. 

## <a name="prerequisites"></a>Vereisten

- Een [flexibele server Azure database for mysql server](quickstart-create-server-portal.md) die wordt gebruikt als de bron server.

## <a name="create-a-read-replica"></a>Een leesreplica maken

> [!IMPORTANT]
> Wanneer u een replica maakt voor een bron die geen bestaande replica's heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd dit in overweging en voer deze bewerkingen uit tijdens een rustige periode.

Een lees replica-server kan worden gemaakt met behulp van de volgende stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Selecteer de bestaande Azure Database for MySQL flexibele server die u wilt gebruiken als bron. Met deze actie wordt de pagina **overzicht** geopend.

3. Selecteer **replicatie** in het menu onder **instellingen**.

4. Selecteer **replica toevoegen**.

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL-replicatie":::

5. Voer een naam in voor de replica server.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL-replica naam":::

6. Selecteer **OK** om te bevestigen dat u de replica wilt maken.

> [!NOTE]
> Lees replica's worden gemaakt met dezelfde server configuratie als de bron. De configuratie van de replica server kan worden gewijzigd nadat deze is gemaakt. De replica server wordt altijd gemaakt in dezelfde resource groep, dezelfde locatie en hetzelfde abonnement als de bron server. Als u een replica server wilt maken naar een andere resource groep of een ander abonnement, kunt u [de replica-server verplaatsen](../../azure-resource-manager/management/move-resource-group-and-subscription.md) nadat u deze hebt gemaakt. Het is raadzaam om de configuratie van de replica server te behouden in gelijke of hogere waarden dan de bron om ervoor te zorgen dat de replica de bron kan blijven gebruiken.

Zodra de replica server is gemaakt, kan deze worden weer gegeven op de Blade **replicatie** .

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL-lijst replica's":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica server stoppen

> [!IMPORTANT]
> Het stoppen van de replicatie naar een server is onomkeerbaar. Zodra de replicatie tussen een bron en replica is gestopt, kan deze niet meer ongedaan worden gemaakt. De replica-server wordt vervolgens een zelfstandige server en ondersteunt nu lezen en schrijven. Deze server kan niet opnieuw in een replica worden gemaakt.

Als u de replicatie tussen een bron en een replica server van de Azure Portal wilt stoppen, gebruikt u de volgende stappen:

1. Selecteer uw bron Azure Database for MySQL flexibele server in het Azure Portal. 

2. Selecteer **replicatie** in het menu onder **instellingen**.

3. Selecteer de replica server waarvoor u de replicatie wilt stoppen.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL-replicatie stoppen server selecteren":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. Selecteer **Replicatie stoppen**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL-replicatie stoppen":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. Bevestig dat u de replicatie wilt stoppen door op **OK** te klikken.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL-replicatie stoppen bevestigen":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>Een replica server verwijderen

Voer de volgende stappen uit om een lees replica-server te verwijderen uit de Azure Portal:

1. Selecteer uw bron Azure Database for MySQL flexibele server in het Azure Portal.

2. Selecteer **replicatie** in het menu onder **instellingen**.

3. Selecteer de replica server die u wilt verwijderen.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL-replica verwijderen server selecteren":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. **Replica verwijderen** selecteren

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL-replica verwijderen":::

5. Typ de naam van de replica en klik op **verwijderen** om de verwijdering van de replica te bevestigen.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL-replica verwijderen bevestigen":::

## <a name="delete-a-source-server"></a>Een bron server verwijderen

> [!IMPORTANT]
> Als u een bronserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de bronserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Gebruik de volgende stappen om een bron server te verwijderen uit de Azure Portal:

1. Selecteer uw bron Azure Database for MySQL flexibele server in het Azure Portal.

2. Selecteer **verwijderen** in het **overzicht**.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL-bron verwijderen":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. Typ de naam van de bron server en klik op **verwijderen** om de verwijdering van de bron server te bevestigen.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL-bron verwijderen bevestigen":::

## <a name="monitor-replication"></a>Replicatie controleren

1. Selecteer in de [Azure Portal](https://portal.azure.com/)de replica Azure database for MySQL flexibele server die u wilt bewaken.

2. Onder de sectie **bewaking** van de zijbalk selecteert u **metrische gegevens**:

3. Selecteer **replicatie vertraging in seconden in** de vervolg keuzelijst met beschik bare metrische gegevens.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Replicatie vertraging selecteren":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. Selecteer het tijds bereik dat u wilt weer geven. In de onderstaande afbeelding wordt een tijds bereik van 30 minuten geselecteerd.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Tijds bereik selecteren":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. De replicatie vertraging voor het geselecteerde tijds bereik weer geven. In de onderstaande afbeelding wordt de laatste 30 minuten weer gegeven.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Selecteer een tijds bereik van 30 minuten":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [lezen van replica's](concepts-read-replicas.md)