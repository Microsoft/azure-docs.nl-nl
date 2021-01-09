---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0688e5e0703f74ee48e37119edcece465e51b5e5
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98043865"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Voor App Configuration moet een sleutel worden gebruikt die door de klant wordt beheerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Door de klant beheerde sleutels bieden verbeterde gegevensbescherming, omdat u uw versleutelingssleutels kunt beheren. Dit is vaak nodig om aan de nalevingsvereisten te voldoen. |Controleren, Weigeren, Uitgeschakeld |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[App-configuratie moet een persoonlijke koppeling gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Met de persoonlijke Azure-koppeling kunt u uw virtuele netwerk verbinden met Azure-Services zonder een openbaar IP-adres op de bron of bestemming. Het persoonlijke koppelings platform zorgt voor de connectiviteit tussen de consumer en de services via het Azure-backbone netwerk. Als u persoonlijke eind punten aan uw app-configuratie-instanties toewijst in plaats van de volledige service, wordt u ook beschermd tegen risico gegevens lekken. Meer informatie vindt u op: [https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) . |AuditIfNotExists, uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
