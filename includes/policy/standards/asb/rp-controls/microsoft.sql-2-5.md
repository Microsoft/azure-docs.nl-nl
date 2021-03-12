---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 60ddf7a90f478fa0b286f14a1cba4d609b02e321
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021199"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-servers moeten controle gegevens gedurende ten minste 90 dagen bewaren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Voor het onderzoeken van incidenten wordt aangeraden de gegevens retentie voor de controle gegevens van uw SQL-servers tot ten minste 90 dagen in te stellen. Controleer of u de benodigde Bewaar regels hebt voor de regio's waarin u werkt. Dit is soms vereist om te voldoen aan de regelgevings normen. |AuditIfNotExists, uitgeschakeld |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
