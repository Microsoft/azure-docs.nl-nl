---
title: Open bare toegang tot het netwerk weigeren-Azure Portal-Azure Database for MariaDB
description: Meer informatie over het configureren van toegang tot open bare netwerken met behulp van Azure Portal voor uw Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 3117ebfd258c72bb97432871c2ea74d30c52f669
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665069"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Open bare netwerk toegang in Azure Database for MariaDB weigeren met Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for MariaDB server kunt configureren om alle open bare configuraties te weigeren en alleen verbindingen via persoonlijke eind punten toe te staan om de netwerk beveiliging verder uit te breiden.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

* Een [Azure database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Instellen van open bare netwerk toegang weigeren

Voer de volgende stappen uit om de MariaDB-server in te stellen voor toegang tot open bare netwerken:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for MariaDB-server.

1. Klik op de pagina MariaDB-server onder **instellingen** op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. Selecteer in open bare netwerk toegang weigeren de optie **Ja** om open bare toegang voor uw MariaDB-server in te scha kelen.

    ![Netwerk toegang Azure Database for MariaDB weigeren](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    ![Azure Database for MariaDB netwerk toegang weigeren](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-metric.md).