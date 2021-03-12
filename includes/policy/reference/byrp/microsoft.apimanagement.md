---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9b4727b34bba563e9f1235771fee34273264726b
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614247"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management-service moet een SKU gebruiken die virtuele netwerken ondersteunt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Met ondersteunde Sku's van API Management is het implementeren van een service in een virtueel netwerk het vergren delen van geavanceerde API Management netwerk-en beveiligings functies die u meer controle over de configuratie van uw netwerk beveiliging bieden. Zie voor meer informatie: [https://aka.ms/apimvnet](https://aka.ms/apimvnet). |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management-services moeten een virtueel netwerk gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network-implementatie biedt verbeterde beveiliging, isolatie en maakt het u mogelijk uw API Management-service te plaatsen in een niet-Internet routeerbaar netwerk waarmee u de toegang tot beheert. Deze netwerken kunnen vervolgens worden verbonden met uw on-premises netwerken met behulp van verschillende VPN-technologieën, waarmee toegang tot uw back-end-services in het netwerk en/of on-premises mogelijk wordt gemaakt. De ontwikkelaars Portal en API-gateway kunnen zodanig worden geconfigureerd dat ze toegankelijk zijn vanaf internet of alleen binnen het virtuele netwerk. |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
