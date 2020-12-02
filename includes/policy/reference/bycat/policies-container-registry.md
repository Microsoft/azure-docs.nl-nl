---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b413a5ad82fa464fa0fe698812dd36a7375cfead
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007697"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Controleer of weiger containerregisters waarvoor geen versleuteling is ingeschakeld met door klanten beheerde sleutels (CMK). In Azure wordt registerinhoud at-rest automatisch versleuteld met door de service beheerde sleutels. Naast de standaard versleuteling kunt u ook gebruikmaken van een extra versleutelingslaag met behulp van een sleutel die u maakt en beheert in Azure Key Vault. Voor meer informatie bezoekt u: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Controleren, Weigeren, Uitgeschakeld |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Containerregisters mogen geen onbeperkte netwerktoegang toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Controleer containerregisters waarvoor geen netwerkregels of firewallregels (IP) zijn geconfigureerd en die dus niet standaard netwerktoegang toestaan. Het beperken van netwerktoegang beschermt containerregisters tegen mogelijke bedreigingen. Containerregisters met minstens één geconfigureerde IP-/firewallregel of virtueel netwerk, worden beschouwd als conform. Ga voor meer informatie over netwerkregels van containerregisters naar: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) en [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Containerregisters moeten gebruikmaken van persoonlijke koppelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Controleer containerregisters die niet minstens één goedgekeurde privé-eindpuntverbinding hebben. Clients in een virtueel netwerk hebben beveiligde toegang tot resources met privé-eindpuntverbindingen door middel van privékoppelingen. Openbare toegang kan vervolgens worden uitgeschakeld om ervoor te zorgen dat alleen privékoppelingen kunnen worden gebruikt om verbinding te maken met het register. Voor meer informatie gaat u naar: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
