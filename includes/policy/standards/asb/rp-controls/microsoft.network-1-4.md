---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c695a6b4ed213de3cd989a2c3a6718e04ac43a27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590136"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alle internetverkeer moet worden gerouteerd via uw geïmplementeerde Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center heeft vastgesteld dat sommige van uw subnetten niet zijn beveiligd met een firewall van de volgende generatie. Bescherm uw subnetten tegen mogelijke bedreigingen door de toegang tot de subnetten te beperken met Azure Firewall of een ondersteunde firewall van de volgende generatie |AuditIfNotExists, uitgeschakeld |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Azure DDoS-beveiligingsstandaard moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |De standaard-DDoS-beveiliging moet zijn ingeschakeld voor alle virtuele netwerken met een subnet dat deel uitmaakt van een toepassingsgateway met een openbaar IP-adres. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
