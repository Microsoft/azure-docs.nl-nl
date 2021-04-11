---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4c54b7f2bcad81b8113e35b115dd66e395c333da
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284623"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Bescherm uw virtuele machines tegen mogelijke bedreigingen door de toegang tot de VM te beperken met een netwerkbeveiligingsgroep (Network Security Group/NSG). U vindt meer informatie over het beheren van verkeer met NSG's op [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Doorsturen via IP op uw virtuele machine moet zijn uitgeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |Door doorsturen via IP in te schakelen op de NIC van een virtuele machine kan de computer verkeer ontvangen dat is geadresseerd aan andere bestemmingen. Doorsturen via IP is zelden vereist (bijvoorbeeld wanneer de VM wordt gebruikt als een virtueel netwerkapparaat). Daarom moet dit worden gecontroleerd door het netwerkbeveiligingsteam. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Beheerpoorten moeten gesloten zijn op uw virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Open poorten voor extern beheer stellen uw virtuele machine bloot aan een verhoogd risico op aanvallen via internet. Deze aanvallen proberen de aanmeldingsgegevens voor de beheerderstoegang tot de computer te verkrijgen. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
