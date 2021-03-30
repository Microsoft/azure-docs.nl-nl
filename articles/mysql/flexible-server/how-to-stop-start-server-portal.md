---
title: Stoppen/starten-Azure Portal-Azure Database for MySQL flexibele server
description: In dit artikel wordt beschreven hoe u bewerkingen in Azure Database for MySQL kunt stoppen/starten via de Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 15b08ea67afe0d307470b5a4fb0f7d26e0f4ea82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241919"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Een Azure Database for MySQL-flexibele server stoppen/starten (preview-versie)

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel wordt stapsgewijs beschreven hoe u de flexibele server kunt stoppen en starten.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een Azure Database for MySQL flexibele server hebben.

## <a name="stop-a-running-server"></a>Een actieve server stoppen

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u wilt stoppen.

2.  Klik op de pagina **overzicht** op de knop **stoppen** op de werk balk.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Flexibele server stoppen."::: 

3.  Klik op **Ja** om het stoppen van de server te bevestigen.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Stoppen van de flexibele server bevestigen."::: 

> [!NOTE]
> Zodra de server is gestopt, zijn de andere beheer bewerkingen niet beschikbaar voor de flexibele server.

## <a name="start-a-stopped-server"></a>Een gestopt server starten

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server die u wilt starten.

2.  Klik op de pagina **overzicht** op de knop **Start** op de werk balk.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="De flexibele server starten.":::  

> [!NOTE]
> Zodra de server is gestart, zijn alle beheer bewerkingen nu beschikbaar voor de flexibele server.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [netwerken in azure database for MySQL flexibele server](./concepts-networking.md)
- [Virtueel netwerk met Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-virtual-network-portal.md).

