---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3c98a13cf97dc9bef3120aa0782c33da4623e0d3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512142"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Controleer elke SQL Managed Instance zonder Advanced Data Security. |AuditIfNotExists, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Advanced Data Security moet zijn ingeschakeld op uw SQL-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-servers zonder Advanced Data Security controleren |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Gevoelige gegevens in uw SQL-databases moeten worden geclassificeerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Met Azure Security Center worden de scanresultaten voor gegevensdetectie en -classificatie van uw SQL-databases bewaakt en worden aanbevelingen geleverd om de gevoelige gegevens in uw databases te classificeren voor betere bewaking en beveiliging |AuditIfNotExists, uitgeschakeld |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |
