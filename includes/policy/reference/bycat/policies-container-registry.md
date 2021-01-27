---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4cb4425c32d049a6706424adbdd4b5602ed20fdf
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807220"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Gebruik door de klant beheerde sleutels voor het beheren van de versleuteling van de rest van de inhoud van uw registers. Standaard worden de gegevens in rust versleuteld met door service beheerde sleutels, maar door de klant beheerde sleutels (CMK) zijn doorgaans vereist om te voldoen aan de normen voor naleving van regelgeving. CMK‘s zorgen ervoor dat de gegevens worden versleuteld met een Azure Key Vault-sleutel die u hebt gemaakt en waarvan u eigenaar bent. U hebt de volledige controle en verantwoordelijkheid voor de levenscyclus van de sleutel, met inbegrip van rotatie en beheer. Meer informatie over CMK versleuteling bij [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Controleren, Weigeren, Uitgeschakeld |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Containerregisters mogen geen onbeperkte netwerktoegang toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Azure-containerregisters accepteren standaard verbindingen via Internet van hosts op elk netwerk. Als u uw registers tegen mogelijke dreigingen wilt beveiligen, moet u alleen toegang vanaf specifieke openbare IP-adressen of adresbereiken toestaan. Als uw register geen IP/firewallregel of een geconfigureerd virtueel netwerk heeft, wordt het in de beschadigde resources weergegeven. Meer informatie over Container Registry-netwerkregels vindt u hier: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) en hier [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Containerregisters moeten een privékoppeling gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Met Azure Private Link kunt u uw virtuele netwerk met services in Azure verbinden zonder een openbaar IP-adres bij de bron of bestemming. Het persoonlijke koppelingsplatform zorgt voor de connectiviteit tussen de consument en de services via het Azure-backbonenetwerk. Als u privé-eindpunten aan uw containerregisters toewijst in plaats van aan de volledige service, bent u ook beschermd tegen gegevenslekken. Zie voor meer informatie: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
