---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0478e606cd5eb6652b992b647d5f69b347753341
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504268"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync moet private link gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Als u een privé-eindpunt maakt voor de aangegeven opslagsynchronisatieserviceresource, kunt u uw opslagsynchronisatieserviceresource adresseert vanuit de privé-IP-adresruimte van het netwerk van uw organisatie, in plaats van via het openbare eindpunt dat toegankelijk is via internet. Als u zelf een privé-eindpunt maakt, wordt het openbare eindpunt niet uitgeschakeld. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Een Azure File Sync privé-eindpunten configureren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Er wordt een privé-eindpunt geïmplementeerd voor de aangegeven opslagsynchronisatieserviceresource. Hiermee kunt u uw opslagsynchronisatieserviceresource adresseert vanuit de privé-IP-adresruimte van het netwerk van uw organisatie, in plaats van via het openbare eindpunt dat toegankelijk is via internet. Het bestaan van een of meer privé-eindpunten zelf betekent niet dat het openbare eindpunt wordt uitgeschakeld. |DeployIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Wijzigen: configureer Azure File Sync om openbare netwerktoegang uit te schakelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Het Azure File Sync het openbare eindpunt dat via internet toegankelijk is, wordt uitgeschakeld door het beleid van uw organisatie. U hebt nog steeds toegang tot de opslagsynchronisatieservice via de privé-eindpunten. |Wijzigen, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Openbare netwerktoegang moet worden uitgeschakeld voor Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Door het openbare eindpunt uit te stellen, kunt u de toegang tot uw opslagsynchronisatieserviceresource beperken tot aanvragen die zijn bestemd voor goedgekeurde privé-eindpunten in het netwerk van uw organisatie. Het is niet inherent onveilig om aanvragen naar het openbare eindpunt toe te staan. Mogelijk wilt u dit echter uitschakelen om te voldoen aan wettelijke, wettelijke of organisatorische beleidsvereisten. U kunt het openbare eindpunt voor een opslagsynchronisatieservice uitschakelen door incomingTrafficPolicy van de resource in te stellen op AllowVirtualNetworksOnly. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
