---
title: Open bare toegang tot het netwerk weigeren-Azure Portal-Azure Database for PostgreSQL-één server
description: Meer informatie over het configureren van toegang tot open bare netwerken met behulp van Azure Portal voor uw Azure Database for PostgreSQL enkele server
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: e195c005676df27385e5e00736b04bdb689fafc5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727104"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Open bare netwerk toegang tot Azure Database for PostgreSQL één server weigeren met Azure Portal

In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL één server kunt configureren om alle open bare configuraties te weigeren en alleen verbindingen via persoonlijke eind punten toe te staan om de netwerk beveiliging verder uit te breiden.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

* Een [Azure database for PostgreSQL één server](quickstart-create-server-database-portal.md) met een algemeen of een prijs categorie geoptimaliseerd voor geheugen.

## <a name="set-deny-public-network-access"></a>Instellen van open bare netwerk toegang weigeren

Volg deze stappen voor het instellen van PostgreSQL single server toegang tot open bare netwerken weigeren:

1. Selecteer uw bestaande Azure Database for PostgreSQL enkele server in de [Azure Portal](https://portal.azure.com/).

1. Klik op de pagina PostgreSQL Eén server onder **instellingen** op **verbindings beveiliging** om de pagina verbindings beveiliging configureren te openen.

1. Selecteer in **open bare netwerk toegang weigeren** de optie **Ja** om open bare toegang voor uw postgresql één server in te scha kelen.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access.PNG" alt-text="Azure Database for PostgreSQL afzonderlijke server netwerk toegang weigeren":::

1. Klik op **Opslaan** om de wijzigingen op te slaan.

1. Bij een melding wordt bevestigd dat de instelling verbindings beveiliging is ingeschakeld.

    :::image type="content" source="./media/howto-deny-public-network-access/deny-public-network-access-success.png" alt-text="Azure Database for PostgreSQL één server netwerk toegang is geweigerd":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van waarschuwingen over metrische gegevens](howto-alert-on-metric.md).
