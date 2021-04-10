---
title: Azure Network-latentie van round-trip-statistieken | Microsoft Docs
description: Meer informatie over de latentie statistieken voor de retour tussen Azure-regio's.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/07/2020
ms.author: kumud
ms.openlocfilehash: bc2d7bb7ba17a4a47fecf2144157f79f5367fca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059163"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistieken voor round-trip-latentie van Azure Network

Azure bewaakt voortdurend de latentie (snelheid) van de kern gebieden van het netwerk met behulp van interne controle hulpprogramma's en metingen die door [ThousandEyes](https://thousandeyes.com)worden verzameld, een synthetische bewakings service van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentie metingen worden verzameld van ThousandEyes-agents die wereld wijd worden gehost in azure-Cloud regio's, waardoor voortdurend netwerk tests tussen zichzelf worden verzonden binnen een interval van 1 minuut. De statistieken voor de maandelijkse latentie worden afgeleid van de verzamelde voor beelden voor de maand.

## <a name="december-2020-round-trip-latency-figures"></a>Cijfers van de retour latentie van december 2020

De maandelijkse gemiddelde retour tijden tussen Azure-regio's voor de afgelopen 30 dagen (eindigend op 31 december 2020) worden hieronder weer gegeven. De volgende metingen worden aangedreven door [ThousandEyes](https://thousandeyes.com).

[![Latentie statistieken tussen Azure-regio's](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
