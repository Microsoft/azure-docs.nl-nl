---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2a20898586e49b0bdad97f8c33936a484558dd0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590121"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alle internetverkeer moet worden gerouteerd via uw geïmplementeerde Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center heeft vastgesteld dat sommige van uw subnetten niet zijn beveiligd met een firewall van de volgende generatie. Bescherm uw subnetten tegen mogelijke bedreigingen door de toegang tot de subnetten te beperken met Azure Firewall of een ondersteunde firewall van de volgende generatie |AuditIfNotExists, uitgeschakeld |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Subnets moeten worden gekoppeld aan een netwerkbeveiligingsgroep](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Bescherm uw subnet tegen mogelijke bedreigingen door de toegang te beperken met een netwerkbeveiligingsgroep (Network Security Group/NSG). NSG's bevatten een lijst met ACL-regels (Access Control List) waarmee netwerkverkeer naar uw subnet wordt toegestaan of geweigerd. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Virtuele machines moeten zijn verbonden met een goedgekeurd virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Met dit beleid worden virtuele machines gecontroleerd die zijn verbonden met een niet-goedgekeurd virtueel netwerk. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Virtuele netwerken moeten gebruikmaken van de opgegeven virtuele-netwerkgateway](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |Met dit beleid worden alle virtuele netwerken gecontroleerd als de standaardroute niet verwijst naar de opgegeven virtuele-netwerkgateway. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
