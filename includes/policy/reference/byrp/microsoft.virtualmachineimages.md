---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9ab85ba65f4cb9dc61f91b794496c7d8ab4cef99
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044858"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VM Image Builder-sjablonen moeten een private link gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Controleer VM Image Builder-sjablonen waarvoor geen virtueel netwerk is geconfigureerd. Wanneer een virtueel netwerk niet is geconfigureerd, wordt er een openbaar IP-adres gemaakt en gebruikt, in plaats van dat er direct bronnen beschikbaar worden gesteld op internet en de mogelijke kwets baarheid kan worden verhoogd. |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
