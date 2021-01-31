---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7c2c9c6ca7badcc432139d8bf898cf4e96a68707
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220299"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache voor Redis moet zich in een virtueel netwerk bevinden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure VNet-implementatie (Virtual Network) biedt uitgebreide beveiliging en isolatie voor Azure Cache voor Redis, evenals subnetten, beleid voor toegangsbeheer, en andere functies om de toegang verder te beperken. Wanneer een Azure Cache voor Redis-exemplaar is geconfigureerd met een VNet, is dit niet openbaar adresseerbaar en kan het exemplaar alleen worden bereikt vanaf virtuele machines en toepassingen binnen het VNet. |Controleren, Weigeren, Uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Alleen beveiligde verbindingen met uw Azure Cache voor Redis moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inschakeling van alleen verbindingen via SSL met Azure Cache voor Redis controleren. Het gebruik van beveiligde verbindingen zorgt voor verificatie tussen de server en de service en beveiligt gegevens tijdens de overdracht tegen netwerklaagaanvallen, zoals man-in-the-middle, meeluisteren en sessie-hijacking |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
