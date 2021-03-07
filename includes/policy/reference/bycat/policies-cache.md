---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1c78986a5c2777c0db7cca314d5a49494f733e22
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429197"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache voor Redis moet zich in een virtueel netwerk bevinden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure Virtual Network-implementatie biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor redis, evenals subnetten, Toegangs beheer beleid en andere functies om de toegang verder te beperken. Wanneer een Azure-cache voor redis-exemplaar is geconfigureerd met een virtueel netwerk, kan het niet openbaar worden benaderd en alleen toegankelijk zijn vanaf virtuele machines en toepassingen binnen het virtuele netwerk. |Controleren, Weigeren, Uitgeschakeld |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Alleen beveiligde verbindingen met uw Azure Cache voor Redis moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inschakeling van alleen verbindingen via SSL met Azure Cache voor Redis controleren. Het gebruik van beveiligde verbindingen zorgt voor verificatie tussen de server en de service en beveiligt gegevens tijdens de overdracht tegen netwerklaagaanvallen, zoals man-in-the-middle, meeluisteren en sessie-hijacking |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
