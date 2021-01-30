---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bea4fac7455a8a99e126622c495852338ade413b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091079"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnostische instellingen implementeren voor Zoekservices naar Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d5da587-71bd-41f5-ac95-dd3330c2d58d) |Hiermee worden de diagnostische instellingen voor Zoekservices geïmplementeerd en naar een regionale Event Hub gestreamd wanneer een Zoekservice waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnostische instellingen implementeren voor Zoekservices naar Log Analytics-werkruimte](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08ba64b8-738f-4918-9686-730d2ed79c7d) |Hiermee worden de diagnostische instellingen voor Zoekservices geïmplementeerd en naar een regionale Log Analytics-werkruimte gestreamd wanneer een Zoekservice waarvoor deze diagnostische instellingen ontbreken, wordt gemaakt of bijgewerkt. |DeployIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Diagnostische logboeken in zoekservices moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Inschakeling van diagnostische logboeken controleren. Hiermee kunt u een activiteitenspoor opnieuw maken om te gebruiken voor onderzoeksdoeleinden wanneer een beveiligingsincident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
