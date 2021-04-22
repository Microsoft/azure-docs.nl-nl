---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 871a648eada2e710604d5fb42ea83a8559049b6a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876001"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync moet private link gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Als u een privé-eindpunt maakt voor de aangegeven opslagsynchronisatieserviceresource, kunt u de resource van de opslagsynchronisatieservice adresseert vanuit de privé-IP-adresruimte van het netwerk van uw organisatie, in plaats van via het openbare eindpunt dat via internet toegankelijk is. Als u zelf een privé-eindpunt maakt, wordt het openbare eindpunt niet uitgeschakeld. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Een Azure File Sync privé-eindpunten configureren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Er wordt een privé-eindpunt geïmplementeerd voor de aangegeven opslagsynchronisatieserviceresource. Hiermee kunt u uw opslagsynchronisatieserviceresource adresseert vanuit de privé-IP-adresruimte van het netwerk van uw organisatie, in plaats van via het openbare eindpunt dat toegankelijk is via internet. Het bestaan van een of meer privé-eindpunten op zichzelf schakelt het openbare eindpunt niet uit. |DeployIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Wijzigen: configureer Azure File Sync openbare netwerktoegang uit te schakelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Het Azure File Sync het openbare eindpunt dat via internet toegankelijk is, wordt uitgeschakeld door het beleid van uw organisatie. U hebt nog steeds toegang tot de opslagsynchronisatieservice via de privé-eindpunten. |Wijzigen, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Openbare netwerktoegang moet worden uitgeschakeld voor Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Door het openbare eindpunt uit te stellen, kunt u de toegang tot uw opslagsynchronisatieserviceresource beperken tot aanvragen die zijn bestemd voor goedgekeurde privé-eindpunten in het netwerk van uw organisatie. Het toestaan van aanvragen naar het openbare eindpunt is niet inherent onveilig, maar u kunt het uitschakelen om te voldoen aan wettelijke, juridische of organisatiebeleidsvereisten. U kunt het openbare eindpunt voor een opslagsynchronisatieservice uitschakelen door incomingTrafficPolicy van de resource in te stellen op AllowVirtualNetworksOnly. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
