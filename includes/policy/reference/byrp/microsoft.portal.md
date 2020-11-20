---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3c9e698aeddbe4818e7b77596eed81e60cb38e5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "95002149"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gedeelde Dash boards mogen geen geprijsde tegels met inline-inhoud hebben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Het maken van een gedeeld dash board met inline-inhoud in geprijsde tegels niet toestaan en afdwingen dat de inhoud moet worden opgeslagen als een afkortings bestand dat online wordt gehost. Als u inline-inhoud gebruikt in de tegel prijs verlaging, kunt u de versleuteling van de inhoud niet beheren. Door uw eigen opslag te configureren, kunt u versleutelen, dubbel versleutelen en zelfs uw eigen sleutels meenemen. Als u dit beleid inschakelt, worden gebruikers beperkt tot het gebruik van 2020-09-01-preview of de bovenstaande versie van gedeelde Dash boards REST API. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
