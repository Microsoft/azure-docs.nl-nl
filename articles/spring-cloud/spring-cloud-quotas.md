---
title: Service plannen en quota's voor Azure lente-Cloud
description: Meer informatie over service quota's en service plannen voor Azure lente Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20ebeb23fe09ba4fd70a724828afadfaa3901abd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095674"
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
> De vermelde tarieven voor het totale aantal app-exemplaren per service-exemplaar gelden voor apps/implementaties met de status gestopt. Verwijder apps/implementaties die niet worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

Enkele standaard limieten kunnen worden verhoogd. Als uw installatie een verhoging vereist, [maakt u een ondersteunings aanvraag](../azure-portal/supportability/how-to-create-azure-support-request.md).
