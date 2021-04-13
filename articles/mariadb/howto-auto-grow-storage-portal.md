---
title: Automatisch opslag laten groeien - Azure Portal - Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u automatisch groter worden van opslag voor Azure Database for MariaDB kunt Azure Portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 49b87648a425277f29ef2b3ebd8752e01019d3ad
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366110"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatisch de opslag in Azure Database for MariaDB met behulp van de Azure Portal
In dit artikel wordt beschreven hoe u een serveropslag Azure Database for MariaDB om te groeien zonder dat dit van invloed is op de werkbelasting.

Wanneer een server de toegewezen opslaglimiet bereikt, wordt de server gemarkeerd als alleen-lezen. Als u de opslag echter automatisch laat groeien, neemt de serveropslag toe om ruimte te bieden aan de groeiende gegevens. Voor servers met minder dan 100 GB inrichtende opslag wordt de inrichtende opslag met 5 GB verhoogd zodra de gratis opslag lager is dan 1 GB of 10% van de inrichtende opslag. Voor servers met meer dan 100 GB inrichtende opslag wordt de inrichtende opslaggrootte met 5% verhoogd wanneer de vrije opslagruimte kleiner is dan 10 GB van de inrichtende opslaggrootte. De maximale opslaglimieten die hier worden [opgegeven, zijn](concepts-pricing-tiers.md#storage) van toepassing.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze handleiding te voltooien:
- Een [Azure Database for MariaDB server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Automatisch groeien van opslag inschakelen 

Volg deze stappen om automatische groei van MariaDB-serveropslag in te stellen:

1. Selecteer in [Azure Portal](https://portal.azure.com/)de bestaande Azure Database for MariaDB server.

2. Klik op de pagina MariaDB-server onder **de** kop Instellingen op **Prijscategorie** om de pagina prijscategorie te openen.

3. Selecteer ja in de sectie Automatische groei **om** automatisch groeien van opslag in te stellen.

    ![Azure Database for MariaDB - Settings_Pricing_tier - Automatische groei](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klik op **OK** om de wijzigingen op te slaan.

5. Er wordt een melding ontvangen om te bevestigen dat automatisch groeien is ingeschakeld.

    ![Azure Database for MariaDB- succes van automatische groei](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen voor metrische gegevens.](howto-alert-metric.md)
