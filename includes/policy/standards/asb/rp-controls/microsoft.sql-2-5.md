---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0e6aaab50b549f0bf5c971c0e05c83b84a8b379b
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105859"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-servers met controle naar het opslag account doel moeten worden geconfigureerd met een retentie van 90 dagen of hoger](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Voor het onderzoeken van incidenten wordt aangeraden om de gegevens retentie voor uw SQL Server ' auditing to Storage account Destination ' in te stellen op ten minste 90 dagen. Bevestig dat u voldoet aan de vereiste Bewaar regels voor de regio's waarin u werkt. Dit is soms vereist om te voldoen aan de regelgevings normen. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
