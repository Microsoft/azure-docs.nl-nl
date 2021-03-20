---
title: Stoppen/starten-Azure Portal-Azure Database for MySQL-server
description: In dit artikel wordt beschreven hoe u bewerkingen in Azure Database for MySQL kunt stoppen/starten.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516869"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Een Azure Database for MySQL stoppen/starten

> [!IMPORTANT]
>  Wanneer u de server **stopt** , blijft deze voor de volgende zeven dagen in een stretch-status. Als u deze niet hand matig **Start** , wordt de server aan het eind van 7 dagen automatisch gestart. U kunt ervoor kiezen om het opnieuw te **stoppen** als u de-server niet gebruikt.

In dit artikel vindt u stapsgewijze procedures voor het uitvoeren van stoppen en starten van één server.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een Azure Database for MySQL enkele server hebben.

> [!NOTE]
> Raadpleeg de beperking van het gebruik van [stoppen/starten](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>De Azure Database for MySQL stoppen/starten met Azure Portal

### <a name="stop-a-running-server"></a>Een actieve server stoppen

1.  Kies in het [Azure Portal](https://portal.azure.com/)de mysql-server die u wilt stoppen.

2.  Klik op de pagina **overzicht** op de knop **stoppen** op de werk balk.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Server Azure Database for MySQL stoppen":::

    > [!NOTE]
    > Zodra de server is gestopt, zijn de andere beheer bewerkingen niet beschikbaar voor de afzonderlijke server.

### <a name="start-a-stopped-server"></a>Een gestopt server starten

1.  Kies in het [Azure Portal](https://portal.azure.com/)uw enkele server die u wilt starten.

2.  Klik op de pagina **overzicht** op de knop **Start** op de werk balk.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Server Azure Database for MySQL starten":::

    > [!NOTE]
    > Zodra de server is gestart, zijn alle beheer bewerkingen nu beschikbaar voor één server.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>De Azure Database for MySQL stoppen/starten met CLI

### <a name="stop-a-running-server"></a>Een actieve server stoppen

1.  Kies in het [Azure Portal](https://portal.azure.com/)de mysql-server die u wilt stoppen.

2.  Klik op de pagina **overzicht** op de knop **stoppen** op de werk balk.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Zodra de server is gestopt, zijn de andere beheer bewerkingen niet beschikbaar voor de afzonderlijke server.

### <a name="start-a-stopped-server"></a>Een gestopt server starten

1.  Kies in het [Azure Portal](https://portal.azure.com/)uw enkele server die u wilt starten.

2.  Klik op de pagina **overzicht** op de knop **Start** op de werk balk.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Zodra de server is gestart, zijn alle beheer bewerkingen nu beschikbaar voor één server.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).
