---
title: Service plannen en quota's voor Azure lente-Cloud
description: Meer informatie over service quota's en service plannen voor Azure lente Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711872"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quota's en service plannen voor Azure lente-Cloud

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Alle Azure-Services hebben standaard limieten en-quota ingesteld voor resources en functies.   Azure lente-Cloud biedt twee prijs Categorieën: Basic en Standard. In dit artikel worden de limieten voor beide lagen weer gegeven.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure lente-Cloud service lagen en-limieten

| Resource | Bereik | Basic | Standard
------- | ------- | -------
vCPU | per app-exemplaar | 1 | 4
Geheugen | per app-exemplaar | 2 GB | 8 GB
Azure lente-Cloud service-instanties | per regio per abonnement | 10 | 10
Totaal aantal app-exemplaren | Cloud service-exemplaar per Azure-veer | 25 | 500
Aangepaste domeinen | Cloud service-exemplaar per Azure-veer | 0 | 25 
Permanente volumes | Cloud service-exemplaar per Azure-veer | 1 GB/app x 10 apps | 50 GB/app x 10-apps

> [!TIP]
> De vermelde limieten voor het totale aantal app-exemplaren per service-exemplaar gelden voor apps en implementaties in elke status, inclusief de status gestopt. Verwijder apps of implementaties die niet in gebruik zijn.

## <a name="next-steps"></a>Volgende stappen

Enkele standaard limieten kunnen worden verhoogd. Als uw installatie een verhoging vereist, [maakt u een ondersteunings aanvraag](../azure-portal/supportability/how-to-create-azure-support-request.md).
