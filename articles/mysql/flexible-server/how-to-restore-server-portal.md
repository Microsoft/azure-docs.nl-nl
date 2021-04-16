---
title: Herstel een Azure Database for MySQL Flexibele server met Azure Portal.
description: In dit artikel wordt beschreven hoe u herstelbewerkingen in Azure Database for MySQL Flexibele server kunt uitvoeren via de Azure Portal
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502042"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Herstel naar een bepaald tijdstip van een Azure Database for MySQL - Flexible Server (preview) met behulp van Azure Portal


> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Dit artikel bevat stapsgewijs procedure voor het uitvoeren van herstel naar een bepaald tijdstip op flexibele servers met behulp van back-ups.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze handleiding te voltooien:

-   U moet een flexibele Azure Database for MySQL hebben.

## <a name="restore-to-the-latest-restore-point"></a>Herstellen naar het meest recente herstelpunt

Volg deze stappen om uw flexibele server te herstellen met behulp van een eerste bestaande back-up.

1.  Kies in [Azure Portal](https://portal.azure.com/)flexibele server van waar u de back-up wilt herstellen.

2.  Klik **in het** linkerpaneel op Overzicht.

3.  Klik op de overzichtspagina op **Herstellen.**

4.  De pagina Herstellen wordt weergegeven met een optie om te kiezen tussen **Laatste herstelpunt** en Aangepast herstelpunt.

5.  Selecteer **Laatste herstelpunt.**

6.  Geef een nieuwe servernaam op in **het veld Herstellen naar nieuwe server.**

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="Vroegste hersteltijd":::

8.  Klik op **OK**.

9.  Er wordt een melding weergegeven dat de herstelbewerking is gestart.

## <a name="restoring-to-a-custom-restore-point"></a>Herstellen naar een aangepast herstelpunt

Volg deze stappen om uw flexibele server te herstellen met behulp van een eerste bestaande back-up.

1.  Kies in [Azure Portal](https://portal.azure.com/)flexibele server van waar u de back-up wilt herstellen.

2.  Klik op de overzichtspagina op **Herstellen.**

3.  De pagina Herstellen wordt weergegeven met een optie om te kiezen tussen Eerste herstelpunt en Aangepast herstelpunt.

4.  Kies **Aangepast herstelpunt.**

5.  Selecteer datum en tijd.

6.  Geef een nieuwe servernaam op in het **veld Herstellen naar nieuwe server.**

6.  Geef een nieuwe servernaam op in het **veld Herstellen naar nieuwe server.**

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="overzicht weergeven":::

7.  Klik op **OK**.

8.  Er wordt een melding weergegeven dat de herstelbewerking is gestart.


## <a name="perform-post-restore-tasks"></a>Taken na herstel uitvoeren
Nadat het herstellen is voltooid, moet u de volgende taken uitvoeren om uw gebruikers en toepassingen weer actief te maken:

- Als de nieuwe server is bedoeld om de oorspronkelijke server te vervangen, moet u clients en clienttoepassingen omleiden naar de nieuwe server.
- Zorg ervoor dat er geschikte VNet-regels zijn om gebruikers verbinding te laten maken. Deze regels worden niet gekopieerd van de oorspronkelijke server.
- Zorg ervoor dat de juiste aanmeldingen en machtigingen op databaseniveau zijn gebruikt.
- Configureer waarschuwingen die geschikt zijn voor de zojuist herstelde server.


## <a name="next-steps"></a>Volgende stappen
Meer informatie over [bedrijfscontinuïteit](concepts-business-continuity.md)
