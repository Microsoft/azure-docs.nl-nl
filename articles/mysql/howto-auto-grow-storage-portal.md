---
title: Automatisch opslag laten groeien - Azure Portal - Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u automatisch groter worden van opslag voor Azure Database for MySQL kunt Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: afa62d6e3ec93c18596ee5870361bf3b657619d1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365022"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automatisch de opslag in Azure Database for MySQL met behulp van de Azure Portal
In dit artikel wordt beschreven hoe u een serveropslag Azure Database for MySQL te laten groeien zonder dat dit van invloed is op de werkbelasting.

Wanneer een server de toegewezen opslaglimiet bereikt, wordt de server gemarkeerd als alleen-lezen. Als u de opslag echter automatisch laat groeien, neemt de serveropslag toe om tegemoet te komen aan de groeiende gegevens. Voor servers met minder dan 100 GB inrichtende opslag wordt de inrichtende opslag met 5 GB verhoogd zodra de gratis opslag lager is dan 1 GB of 10% van de inrichtende opslag. Voor servers met meer dan 100 GB inrichtende opslag wordt de inrichtende opslaggrootte met 5% verhoogd wanneer de vrije opslagruimte kleiner is dan 10 GB van de inrichtende opslaggrootte. De maximale opslaglimieten die hier worden [opgegeven, zijn](./concepts-pricing-tiers.md#storage) van toepassing.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze handleiding te voltooien:
- Een [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Automatisch groeien van opslag inschakelen 

Volg deze stappen om automatische groei van MySQL-serveropslag in te stellen:

1. Selecteer in [Azure Portal](https://portal.azure.com/)de bestaande Azure Database for MySQL server.

2. Klik op de pagina MySQL-server onder **de** kop Instellingen op **Prijscategorie** om de pagina Prijscategorie te openen.

3. Selecteer ja in de sectie Automatische groei **om** automatisch groeien van opslag in te stellen.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for MySQL - Settings_Pricing_tier - Automatische groei":::

4. Klik op **OK** om de wijzigingen op te slaan.

5. Er wordt een melding ontvangen om te bevestigen dat automatisch groeien is ingeschakeld.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL- succes van automatische groei":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen voor metrische gegevens.](howto-alert-on-metric.md)
