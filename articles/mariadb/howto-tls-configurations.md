---
title: TLS-configuratie-Azure Portal-Azure Database for MariaDB
description: Meer informatie over het instellen van TLS-configuratie met behulp van Azure Portal voor uw Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 55a664ceb3524a0c545e037dcba7a2af90034ad4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664763"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>TLS-instellingen in Azure Database for MariaDB configureren met Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for MariaDB server kunt configureren om de minimale TLS-versie voor verbindingen af te dwingen en alle verbindingen met een lagere TLS-versie dan de geconfigureerde minimale TLS-versie te weigeren, waardoor de netwerk beveiliging wordt verbeterd.

U kunt TLS-versie afdwingen om verbinding te maken met hun Azure Database for MariaDB door de minimale TLS-versie voor de database server in te stellen. Als u bijvoorbeeld de minimale TLS-instellings versie instelt op TLS 1,0, betekent dit dat uw server verbindingen toestaat van clients met behulp van TLS 1,0, 1,1 en 1.2 +. U kunt dit ook instellen op 1,2 zodat u alleen verbindingen van clients met TLS 1.2 + kunt toestaan en alle verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

* Een [Azure database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>TLS-configuraties instellen voor Azure Database for MariaDB

Volg deze stappen voor het instellen van de minimale TLS-versie van de MariaDB-server:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for MariaDB-server.

1. Klik op de pagina MariaDB-server onder **instellingen** op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. Selecteer in **minimale TLS**-versie **1,2** om verbindingen met een TLS-versie kleiner dan TLS 1,2 voor uw MariaDB-server te weigeren.

    ![Azure Database for MariaDB TLS-configuratie](./media/howto-tls-configurations/tls-configurations.png)

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    ![Azure Database for MariaDB TLS-configuratie geslaagd](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-metric.md)