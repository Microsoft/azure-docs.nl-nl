---
title: Actieve geo-replicatie voor Azure-cache voor bedrijven configureren voor redis-instanties
description: Meer informatie over het repliceren van uw Azure-cache voor redis Enter prise-instanties in azure-regio's
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: edf7a7cdbd24249205fedb4654aa092755700910
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035650"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Actieve geo-replicatie voor redis-exemplaren van Azure in Enter prise configureren (preview)

In dit artikel leert u hoe u een actieve Azure-cache met geo-replicatie kunt configureren met behulp van de Azure Portal.

Actieve geo-replicatie groepen twee of meer Enter prise Azure-cache voor redis-instanties in één cache die over Azure-regio's loopt. Alle exemplaren fungeren als de lokale Primaries. Een toepassing bepaalt welke exemplaren moeten worden gebruikt voor lees-en schrijf aanvragen.

## <a name="create-or-join-an-active-geo-replication-group"></a>Een actieve geo-replicatie groep maken of eraan toevoegen

> [!IMPORTANT]
> Actieve geo-replicatie moet zijn ingeschakeld op het moment dat een Azure-cache voor redis wordt gemaakt.
>
>

1. Klik in de **nieuwe** gebruikers interface voor het maken van redis cache op **configureren** om **actieve geo-replicatie** in te stellen op het tabblad **Geavanceerd** .

    ![Actieve geo-replicatie configureren](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Maak een nieuwe replicatie groep voor een eerste cache-exemplaar of selecteer een bestaande in de lijst.

    ![Caches koppelen](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Klik op **configureren** om te volt ooien.

    ![Actieve geo-replicatie geconfigureerd](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Herhaal de bovenstaande stappen voor elk extra cache-exemplaar in de groep geo-replicatie.

## <a name="remove-from-an-active-geo-replication-group"></a>Verwijderen uit een actieve geo-replicatie groep

Als u een cache-exemplaar uit een actieve geo-replicatie groep wilt verwijderen, verwijdert u gewoon het exemplaar. De overige instanties worden automatisch opnieuw geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis-service lagen](cache-overview.md#service-tiers)
* [Hoge Beschik baarheid voor Azure cache voor redis](cache-high-availability.md)
