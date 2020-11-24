---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: aedfe8783beacfe2e6679848ef4c2defa24d2da0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559923"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](../articles/governance/policy/overview.md#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
U hebt toegang tot Azure Cognitive Services via twee verschillende resources: Een resource voor meerdere services of een voor één service.

* Resource voor meerdere services:
    * Via één sleutel en eindpunt toegang krijgen tot meerdere Azure Cognitive Services.
    * Geconsolideerde facturering van de services die u gebruikt.
* Resource voor één service:
    * U hebt toegang tot één Azure Cognitive Service met een unieke sleutel en een uniek eindpunt voor elke service die is gemaakt. 
    * Gebruik de gratis laag om de service uit te proberen.