---
title: Restore-Azure Portal-Azure Database for PostgreSQL-flexibele server
description: In dit artikel wordt beschreven hoe u herstel bewerkingen kunt uitvoeren in Azure Database for PostgreSQL via de Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90935894"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Herstel naar een bepaald tijdstip van een flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In dit artikel vindt u stapsgewijze procedures voor het uitvoeren van herstel naar een bepaald tijdstip in een flexibele server met behulp van back-ups. U kunt naar een vroeg herstel punt of een aangepast herstel punt binnen de Bewaar periode uitvoeren.

## <a name="pre-requisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een Azure Database for PostgreSQL-flexibele server hebben. Dezelfde procedure is ook van toepassing op een flexibele server die is geconfigureerd met zone redundantie.

## <a name="restoring-to-the-earliest-restore-point"></a>Herstellen naar het oudste herstel punt

Volg deze stappen om uw flexibele server te herstellen met een eerste bestaande back-up.

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server waarvan u de back-up wilt terugzetten.

2.  Klik op **overzicht** in het linkerdeel venster en klik op **herstellen**
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Overzicht van herstellen":::

3.  Herstellen pagina wordt weer gegeven met een optie om te kiezen tussen het oudste herstel punt en het aangepaste herstel punt.

4.  Selecteer het **oudste herstel punt** en geef een nieuwe server naam op in het veld **herstellen naar nieuwe server** . De vroegste tijds tempel waarnaar u kunt herstellen, wordt weer gegeven. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="Vroegste herstel tijd":::

5.  Klik op **OK**.

6.  Er wordt een melding weer gegeven dat de herstel bewerking is gestart.

## <a name="restoring-to-a-custom-restore-point"></a>Herstellen naar een aangepast herstel punt

Volg deze stappen om uw flexibele server te herstellen met een eerste bestaande back-up.

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server waarvan u de back-up wilt terugzetten.

2.  Klik op de pagina overzicht op **herstellen**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Overzicht van herstellen":::
    
3.  Herstellen pagina wordt weer gegeven met een optie om te kiezen tussen het oudste herstel punt en het aangepaste herstel punt.

4.  Kies **aangepast herstel punt**.

5.  Selecteer datum en tijd en geef een nieuwe server naam op in het veld **herstellen naar nieuwe server** . 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="Aangepaste herstel tijd":::
 
6.  Klik op **OK**.

7.  Er wordt een melding weer gegeven dat de herstel bewerking is gestart.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
-   Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
-   Meer informatie over [back-up en herstel](./concepts-backup-restore.md)
