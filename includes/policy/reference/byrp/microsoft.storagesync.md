---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ce13484aa085cef89ba34151824ee843dd3bd90e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "103419886"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure File Sync moet een persoonlijke koppeling gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |Als u een persoonlijk eind punt maakt voor de aangegeven opslag synchronisatie service resource, kunt u de bron van de opslag synchronisatie service verpakken vanuit de privé-IP-adres ruimte van het netwerk van uw organisatie, in plaats van via het open bare eind punt Internet toegankelijk. Als u een persoonlijk eind punt op zichzelf maakt, wordt het open bare eind punt niet uitgeschakeld. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure File Sync met persoonlijke eind punten configureren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |Er wordt een persoonlijk eind punt geïmplementeerd voor de aangegeven opslag synchronisatie service resource. Zo kunt u uw resource voor opslag synchronisatie service adresseren vanuit de privé-IP-adres ruimte van het netwerk van uw organisatie, in plaats van via het open bare eind punt Internet toegankelijk. Het bestaan van een of meer privé-eind punten maakt het open bare eind punt niet uitgeschakeld. |DeployIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[Wijzigen-Azure File Sync configureren om open bare netwerk toegang uit te scha kelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |Het open bare eind punt voor Internet toegang van Azure File Sync is uitgeschakeld door het beleid van uw organisatie. U kunt nog steeds toegang krijgen tot de opslag synchronisatie service via een of meer persoonlijke eind punten. |Wijzigen, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Open bare netwerk toegang moet zijn uitgeschakeld voor Azure File Sync](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |Als u het open bare eind punt uitschakelt, kunt u de toegang tot de bron van de opslag synchronisatie service beperken tot aanvragen die bestemd zijn voor goedgekeurde privé-eind punten op het netwerk van uw organisatie. Het toestaan van aanvragen voor het open bare eind punt is niets onveilig, maar u kunt het echter uitschakelen om te voldoen aan wettelijke, wettelijke of organisatorische beleids vereisten. U kunt het open bare eind punt voor een opslag synchronisatie service uitschakelen door de incomingTrafficPolicy van de resource in te stellen op AllowVirtualNetworksOnly. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
