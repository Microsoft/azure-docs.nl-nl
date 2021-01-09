---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f03688012100c31865fb25087f7b4049c087b43e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050902"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Event Grid domeinen moeten een persoonlijke koppeling gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Met de persoonlijke Azure-koppeling kunt u uw virtuele netwerk verbinden met Azure-Services zonder een openbaar IP-adres op de bron of bestemming. Het persoonlijke koppelings platform zorgt voor de connectiviteit tussen de consumer en de services via het Azure-backbone netwerk. Als u persoonlijke eind punten aan uw Event Grid domeinen toewijst in plaats van de volledige service, bent u ook beschermd tegen Risico's voor het lekken van gegevens. Meer informatie vindt u op: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure Event Grid onderwerpen moeten een persoonlijke koppeling gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Met de persoonlijke Azure-koppeling kunt u uw virtuele netwerk verbinden met Azure-Services zonder een openbaar IP-adres op de bron of bestemming. Het persoonlijke koppelings platform zorgt voor de connectiviteit tussen de consumer en de services via het Azure-backbone netwerk. Als u persoonlijke eind punten aan uw onderwerpen toewijst in plaats van de volledige service, wordt u ook beschermd tegen risico gegevens lekkage. Meer informatie vindt u op: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
