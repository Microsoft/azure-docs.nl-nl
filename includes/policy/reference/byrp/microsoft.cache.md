---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8b8623d5fed525372c7335600dfa988cad91bd89
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045276"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache voor Redis moet zich in een virtueel netwerk bevinden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |De implementatie van Azure Virtual Network (VNet) biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor redis, evenals subnetten, Toegangs beheer beleid en andere functies om de toegang verder te beperken. Wanneer een Azure-cache voor redis-exemplaar is geconfigureerd met een VNet, is het niet openbaar adresseerbaar en is deze alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet. |Controleren, Weigeren, Uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Alleen beveiligde verbindingen met uw Azure Cache voor Redis moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inschakeling van alleen verbindingen via SSL met Azure Cache voor Redis controleren. Het gebruik van beveiligde verbindingen zorgt voor verificatie tussen de server en de service en beveiligt gegevens tijdens de overdracht tegen netwerklaagaanvallen, zoals man-in-the-middle, meeluisteren en sessie-hijacking |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
