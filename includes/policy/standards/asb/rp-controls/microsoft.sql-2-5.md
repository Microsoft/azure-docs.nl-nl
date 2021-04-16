---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1c61f7bbe9f926ad186710cc6e804060d1e30950
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512070"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-servers met controle naar opslagaccountbestemming moeten worden geconfigureerd met een bewaarperiode van 90 dagen of hoger](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Voor onderzoek naar incidenten raden we u aan de gegevensretentie voor de controle van uw SQL Server in te stellen op de opslagaccountbestemming op ten minste 90 dagen. Controleer of u aan de benodigde bewaarregels voor de regio's waar u werkt, na komt. Dit is soms vereist voor naleving van regelgevingsstandaarden. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
