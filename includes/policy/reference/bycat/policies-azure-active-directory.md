---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bbd833b840809828cb13cf807a8c150d42283d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866451"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Active Directory Domain Services beheerde domeinen moeten de modus alleen TLS 1.2 gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Gebruik de modus Alleen TLS 1.2 voor uw beheerde domeinen. Standaard schakelt Azure AD Domain Services het gebruik van coderingen zoals NTLM v1 en TLS v1 in. Deze coderingen zijn mogelijk vereist voor sommige oudere toepassingen, maar worden als zwak beschouwd en kunnen worden uitgeschakeld als u ze niet nodig hebt. Wanneer de modus alleen TLS 1.2 is ingeschakeld, mislukt elke client die een aanvraag indient die TLS 1.2 niet gebruikt. Meer informatie op [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain) . |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |
