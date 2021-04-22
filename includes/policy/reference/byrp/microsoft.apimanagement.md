---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6c54ddf232d16b0555ec8e1f5fee57e1f597488f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866238"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management-service moet een SKU gebruiken die virtuele netwerken ondersteunt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Met ondersteunde SKU's API Management maakt het implementeren van een service in een virtueel netwerk geavanceerde API Management netwerk- en beveiligingsfuncties mogelijk, waardoor u meer controle hebt over uw netwerkbeveiligingsconfiguratie. Zie voor meer informatie: [https://aka.ms/apimvnet](https://aka.ms/apimvnet). |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management-services moeten een virtueel netwerk gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network-implementatie biedt verbeterde beveiliging en isolatie en stelt u in staat om uw API Management-service te plaatsen in een routeerbaar netwerk dat niet via internet kan worden gebruikt. Deze netwerken kunnen vervolgens worden verbonden met uw on-premises netwerken met behulp van verschillende VPN-technologieën, waarmee u toegang krijgt tot uw back-endservices binnen het netwerk en/of on-premises. De ontwikkelaarsportal en API-gateway kunnen zodanig worden geconfigureerd dat ze toegankelijk zijn via internet of alleen binnen het virtuele netwerk. |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
