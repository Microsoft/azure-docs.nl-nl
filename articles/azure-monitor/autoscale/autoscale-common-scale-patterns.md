---
title: Overzicht algemene patronen voor automatische schaalaanpassing
description: Meer informatie over de algemene patronen voor het automatisch schalen van uw resource in Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100610944"
---
# <a name="overview-of-common-autoscale-patterns"></a>Overzicht algemene patronen voor automatische schaalaanpassing
In dit artikel worden enkele van de algemene patronen beschreven om uw resource in azure te schalen.

Azure Monitor automatisch schalen is alleen van toepassing op [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web apps](https://azure.microsoft.com/services/app-service/web/)en [API Management Services](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Hiermee kunt u aan de slag

In dit artikel wordt ervan uitgegaan dat u bekend bent met automatisch schalen. U kunt [hier aan de slag om uw resource te schalen][1]. Hier volgen enkele van de algemene schaal patronen.

## <a name="scale-based-on-cpu"></a>Schalen op basis van CPU

U hebt een web-app (/VMSS/cloud) en

- U wilt uitschalen/schalen op basis van de CPU.
- Daarnaast wilt u er zeker van zijn dat er een minimum aantal exemplaren is.
- U wilt er ook voor zorgen dat u een maximum limiet instelt voor het aantal exemplaren dat u kunt schalen naar.

![Schalen op basis van CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Anders schalen op week dagen versus weekends

U hebt een web-app (/VMSS/cloud) en

- U wilt standaard 3 exemplaren (op werk dagen)
- U verwacht geen verkeer in het weekend en daarom wilt u omlaag schalen naar 1 exemplaar in het weekend.

![Anders schalen op week dagen versus weekends][3]

## <a name="scale-differently-during-holidays"></a>Verschillend schalen tijdens feest dagen

U hebt een web-app (/VMSS/cloud) en

- U wilt omhoog/omlaag schalen op basis van het CPU-gebruik standaard
- Tijdens het kerst seizoen (of specifieke dagen die belang rijk zijn voor uw bedrijf), wilt u echter de standaard instellingen overschrijven en hebt u meer capaciteit ter beschikking.

![Anders schalen op feest dagen][4]

## <a name="scale-based-on-custom-metric"></a>Schalen op basis van aangepaste metrische gegevens

U hebt een web-front-end en een API-laag die communiceert met de back-end.

- U wilt de API-laag schalen op basis van aangepaste gebeurtenissen in de front-end (voor beeld: u wilt het afhandelings proces schalen op basis van het aantal items in de winkel wagen)

![Schalen op basis van aangepaste metrische gegevens][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png
