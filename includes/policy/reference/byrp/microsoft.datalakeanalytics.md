---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b47a848e5cbdb849263989a33f2baa1de6d9e04f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105035303"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnostische instellingen implementeren voor Data Lake Analytics naar Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4daddf25-4823-43d4-88eb-2419eb6dcc08) |Hiermee worden de diagnostische instellingen geïmplementeerd waarmee Data Lake Analytics naar een regionale Event Hub worden gestreamd wanneer Data Lake Analytics waarvoor deze diagnostische instellingen ontbreken, worden gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnostische instellingen implementeren voor Data Lake Analytics naar Log Analytics-werkruimte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd56a5a7c-72d7-42bc-8ceb-3baf4c0eae03) |Hiermee worden de diagnostische instellingen voor Data Lake Analytics geïmplementeerd en naar een regionale Log Analytics-werkruimte gestreamd wanneer een Data Lake Analytics waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Bron Logboeken in Data Lake Analytics moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Het inschakelen van bron logboeken controleren. Hiermee kunt u een activiteitenspoor opnieuw maken om te gebruiken voor onderzoeksdoeleinden wanneer een beveiligingsincident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
