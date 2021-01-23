---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 01299d4050611f0064aa26abd9903c14c86bbcb5
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704861"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Voor Azure SignalR Service moet Private Link worden gebruikt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Met Azure Private Link kunt u uw virtuele netwerk met services in Azure verbinden zonder een openbaar IP-adres bij de bron of bestemming. Het privékoppelingsplatform zorgt voor de connectiviteit tussen de consument en de services via het Azure-backbonenetwerk. Als u privé-eindpunten aan uw SignalR-resources toewijst in plaats van aan de volledige service, bent u ook beschermd tegen gegevenslekken. Zie voor meer informatie [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink). |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
