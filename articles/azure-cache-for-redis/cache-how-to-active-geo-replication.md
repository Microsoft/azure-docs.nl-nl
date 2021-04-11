---
title: Actieve geo-replicatie voor Azure-cache voor bedrijven configureren voor redis-instanties
description: Meer informatie over het repliceren van uw Azure-cache voor redis Enter prise-instanties in azure-regio's
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 3fe3131263d3cf1984eae1692854d8d6bcd2746a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109485"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Actieve geo-replicatie voor redis-exemplaren van Azure in Enter prise configureren (preview)

In dit artikel leert u hoe u een actieve Azure-cache met geo-replicatie kunt configureren met behulp van de Azure Portal.

Actieve geo-replicatie groepen twee of meer Enter prise Azure-cache voor redis-instanties in één cache die over Azure-regio's loopt. Alle exemplaren fungeren als de lokale Primaries. Een toepassing bepaalt welke exemplaren moeten worden gebruikt voor lees-en schrijf aanvragen.

## <a name="create-or-join-an-active-geo-replication-group"></a>Een actieve geo-replicatie groep maken of eraan toevoegen

> [!IMPORTANT]
> Actieve geo-replicatie moet zijn ingeschakeld op het moment dat een Azure-cache voor redis wordt gemaakt.
>
>

1. Selecteer op het tabblad **Geavanceerd** van de nieuwe gebruikers interface voor het maken van de **redis cache** **Enter prise** voor **clustering beleid**.

    ![Actieve geo-replicatie configureren](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Klik op **configureren** om **actieve geo-replicatie** in te stellen.

1. Maak een nieuwe replicatie groep voor een eerste cache-exemplaar of selecteer een bestaande in de lijst.

    ![Caches koppelen](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Klik op **configureren** om te volt ooien.

    ![Actieve geo-replicatie geconfigureerd](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Wacht totdat de eerste cache is gemaakt. Herhaal de bovenstaande stappen voor elk extra cache-exemplaar in de groep geo-replicatie.

## <a name="remove-from-an-active-geo-replication-group"></a>Verwijderen uit een actieve geo-replicatie groep

Als u een cache-exemplaar uit een actieve geo-replicatie groep wilt verwijderen, verwijdert u gewoon het exemplaar. De overige instanties worden automatisch opnieuw geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis-service lagen](cache-overview.md#service-tiers)
* [Hoge Beschik baarheid voor Azure cache voor redis](cache-high-availability.md)
