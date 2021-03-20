---
title: Hoge Beschik baarheid configureren-grootschalige (Citus)-Azure Database for PostgreSQL
description: Hoge Beschik baarheid in-of uitschakelen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907401"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Hoge Beschik baarheid van grootschalige (Citus) configureren

Azure Database for PostgreSQL-grootschalige (Citus) biedt hoge Beschik baarheid (HA) om uitval tijd van de data base te voor komen. Als HA is ingeschakeld, ontvangt elk knoop punt in een server groep een stand-by. Als het oorspronkelijke knoop punt een slechte status krijgt, wordt de stand-by wordt gepromoveerd om het te vervangen.

> [!IMPORTANT]
> Omdat HA het aantal servers in de groep verdubbelt, worden ook de kosten verdubbeld.

Het inschakelen van HA kan tijdens het maken van de Server groep, of later op het tabblad **Compute + Storage** voor uw server groep in de Azure Portal. De gebruikers interface ziet er in beide gevallen ongeveer als volgt uit. Sleep de schuif regelaar voor **hoge Beschik baarheid** van Nee naar Ja:

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="ha-schuif regelaar":::

Klik op de knop **Opslaan** om uw selectie toe te passen. Het inschakelen van HA kan enige tijd in beslag nemen, omdat de Server groep stand-by staat en gegevens streamt naar deze.

Op het tabblad Overzicht voor de Server groep worden alle knoop punten en de bijbehorende stand-by weer **gegeven** , samen met een kolom met **hoge Beschik baarheid** , die aangeeft of ha voor elk knoop punt is ingeschakeld.

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="de kolom ha in het overzicht van de Server groep":::

### <a name="next-steps"></a>Volgende stappen

Meer informatie over [hoge Beschik baarheid](concepts-hyperscale-high-availability.md).
