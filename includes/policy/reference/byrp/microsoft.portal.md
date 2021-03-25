---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0df4035c75c124b33e299e1ccb2258ef41caebf6
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105037734"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gedeelde dashboards mogen geen Markdown-tegels met inline-inhoud bevatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Hiermee staat u niet toe dat een gedeeld dashboard met inline-inhoud op Markdown-tegels kan worden gemaakt en dwingt u af dat de inhoud moet worden opgeslagen als een Markdown-bestand dat online wordt gehost. Als u in de Markdown-tegel inline-inhoud gebruikt, kunt u de versleuteling van de inhoud niet beheren. Door uw eigen opslag te configureren, kunt u versleuteling, dubbele versleuteling en zelfs uw eigen sleutels gebruiken. Als u dit beleid inschakelt, worden gebruikers beperkt tot het gebruik van de versie 2020-09-01-preview of een hogere versie van de REST API voor gedeelde dashboards. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
