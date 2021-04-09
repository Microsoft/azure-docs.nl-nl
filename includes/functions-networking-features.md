---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97936754"
---


| Functie |[Verbruiksabonnement](../articles/azure-functions/consumption-plan.md)|[Premium-abonnement](../articles/azure-functions/functions-premium-plan.md)|[Toegewezen abonnement](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Binnenkomende IP-beperkingen en toegang tot persoonlijke sites](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integratie van virtueel netwerk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Ja (regionaal)|✅Ja (regionaal en gateway)|✅Yes| ✅Yes|
|[Virtuele netwerk triggers (niet-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hybride verbindingen](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (alleen Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Uitgaande IP-beperkingen](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Ja|