---
title: Opslag Azure Portal automatisch uitbreiden-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u de automatische groei van opslag kunt configureren met behulp van de Azure Portal in Azure Database for PostgreSQL-enkele server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: dd6effff2fa38276729e2609e115ef72c7af4066
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228075"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Opslag automatisch uitbreiden met de Azure Portal in Azure Database for PostgreSQL-enkele server
In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL Server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

Wanneer een server de toegewezen opslag limiet bereikt, is de server gemarkeerd als alleen-lezen. Als u automatisch verg Roten van opslag inschakelt, neemt de server opslag echter toe om de groeiende gegevens te verwerken. Voor servers met een ingerichte opslag van minder dan 100 GB wordt de ingerichte opslag grootte met 5 GB verhoogd zodra de beschik bare opslag onder het hoogste van 1 GB of 10% van de ingerichte opslag ligt. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslag grootte verhoogd met 5% wanneer de beschik bare opslag ruimte lager is dan 5% van de ingerichte opslag grootte. De maximale opslag limieten die [hier](./concepts-pricing-tiers.md#storage) zijn opgegeven, zijn van toepassing.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om deze hand leiding te volt ooien:
- Een [Azure database for postgresql server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Opslag automatisch verg Roten inschakelen 

Voer de volgende stappen uit om PostgreSQL-Server opslag automatisch uitbreiden in te stellen:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)uw bestaande Azure database for postgresql-server.

2. Klik op de pagina PostgreSQL-server onder **instellingen** op **prijs categorie** om de pagina prijs categorie te openen.

3. In de sectie **automatische groei** selecteert u **Ja** om automatische groei van opslag in te scha kelen.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL-Settings_Pricing_tier-automatische groei":::

4. Klik op **OK** om de wijzigingen op te slaan.

5. Bij een melding wordt bevestigd dat automatisch uitbreiden is ingeschakeld.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL-automatisch groei geslaagd":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).