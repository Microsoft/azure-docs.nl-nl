---
title: Automatisch opslag laten groeien - Azure Portal - Azure Database for PostgreSQL - Enkele server
description: In dit artikel wordt beschreven hoe u opslag automatisch kunt laten groeien met behulp van de Azure Portal in Azure Database for PostgreSQL - Enkele server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 21cdde40730a037b9d535dfe3c608d6bbc90276f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366127"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatisch opslag laten groeien met de Azure Portal in Azure Database for PostgreSQL - Enkele server
In dit artikel wordt beschreven hoe u een serveropslag Azure Database for PostgreSQL om te groeien zonder dat dit van invloed is op de werkbelasting.

Wanneer een server de toegewezen opslaglimiet bereikt, wordt de server gemarkeerd als alleen-lezen. Als u automatische groei van opslag inschakelen, neemt de serveropslag echter toe om de groeiende gegevens te kunnen verwerken. Voor servers met minder dan 100 GB inrichtende opslag wordt de inrichtende opslag met 5 GB verhoogd zodra de gratis opslag lager is dan 1 GB of 10% van de inrichtende opslag. Voor servers met meer dan 100 GB inrichtende opslag wordt de inrichtende opslag met 5% verhoogd wanneer de vrije opslagruimte kleiner is dan 10 GB van de inrichtende opslaggrootte. De maximale opslaglimieten die hier worden [opgegeven, zijn](./concepts-pricing-tiers.md#storage) van toepassing.

## <a name="prerequisites"></a>Vereisten
Als u deze handleiding wilt voltooien, hebt u het volgende nodig:
- Een [Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Automatisch groeien van opslag inschakelen 

Volg deze stappen om automatische groei van PostgreSQL-serveropslag in te stellen:

1. Selecteer in [Azure Portal](https://portal.azure.com/)de bestaande Azure Database for PostgreSQL server.

2. Klik op de pagina PostgreSQL-server onder **Instellingen** op **Prijscategorie** om de pagina prijscategorie te openen.

3. Selecteer ja **in de sectie** Automatische groei **om** automatisch groeien van opslag in teschakelen.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL - Settings_Pricing_tier - Automatische groei":::

4. Klik op **OK** om de wijzigingen op te slaan.

5. Er wordt een melding ontvangen dat automatisch groeien is ingeschakeld.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL- succes bij automatische groei":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen voor metrische gegevens.](howto-alert-on-metric.md)
