---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: edf73e2b5471719c2d2e1c42251341586cfad080
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105068"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Netwerktoegang tot opslagaccounts moet zijn beperkt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Netwerktoegang tot opslagaccounts moet worden beperkt. Configureer netwerkregels zo dat alleen toepassingen van toegestane netwerken toegang hebben tot het opslagaccount. Om verbindingen van specifieke internet-of on-premises clients toe te staan, kan toegang worden verleend aan verkeer van specifieke Azure Virtual Networks of voor open bare IP-adresbereiken voor Internet |Controleren, Weigeren, Uitgeschakeld |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Opslagaccounts moeten gebruikmaken van een  service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Met dit beleid worden alle opslagaccounts gecontroleerd die niet zijn geconfigureerd voor het gebruik van een  service-eindpunt voor een virtueel netwerk. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
