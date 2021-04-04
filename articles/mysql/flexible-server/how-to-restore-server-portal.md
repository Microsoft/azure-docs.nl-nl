---
title: Restore-Azure Portal-Azure Database for MySQL-flexibele server
description: In dit artikel wordt beschreven hoe u herstel bewerkingen kunt uitvoeren in Azure Database for MySQL via de Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 062d53fcb122ebacd004d7dca5e11f5a883354cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241953"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview"></a>Herstel naar een bepaald tijdstip van een Azure Database for MySQL-flexibele server (preview-versie)


> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel vindt u stapsgewijze procedures voor het uitvoeren van herstel naar een bepaald tijdstip in een flexibele server met behulp van back-ups.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een Azure Database for MySQL flexibele server hebben.

## <a name="restore-to-the-latest-restore-point"></a>Herstellen naar het meest recente herstel punt

Volg deze stappen om uw flexibele server te herstellen met een eerste bestaande back-up.

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server waarvan u de back-up wilt terugzetten.

2.  Klik op **overzicht** in het linkerdeel venster.

3.  Klik op de pagina overzicht op **herstellen**.

    Plaatsaanduidingstekst

4.  Herstellen pagina wordt weer gegeven met een optie om te kiezen tussen het **meest recente herstel punt** en het aangepaste herstel punt.

5.  Selecteer het **meest recente herstel punt**.


6.  Geef een nieuwe server naam op in het veld **herstellen naar nieuwe server** .

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Vroegste herstel tijd":::

8.  Klik op **OK**.

9.  Er wordt een melding weer gegeven dat de herstel bewerking is gestart.

## <a name="restoring-to-a-custom-restore-point"></a>Herstellen naar een aangepast herstel punt

Volg deze stappen om uw flexibele server te herstellen met een eerste bestaande back-up.

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server waarvan u de back-up wilt terugzetten.

2.  Klik op de pagina overzicht op **herstellen**.

    Plaatsaanduidingstekst

3.  Herstellen pagina wordt weer gegeven met een optie om te kiezen tussen het oudste herstel punt en het aangepaste herstel punt.

4.  Kies **aangepast herstel punt**.

5.  Selecteer datum en tijd.

6.  Geef een nieuwe server naam op in het veld **herstellen naar nieuwe server** .

6.  Geef een nieuwe server naam op in het veld **herstellen naar nieuwe server** . 
   
    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="overzicht van weer gave":::
 
7.  Klik op **OK**.

8.  Er wordt een melding weer gegeven dat de herstel bewerking is gestart.

## <a name="next-steps"></a>Volgende stappen

Tijdelijke aanduiding
