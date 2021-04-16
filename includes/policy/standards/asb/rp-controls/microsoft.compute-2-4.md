---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 08284f67f5d1010d0df92f2df181e4d75e4af945
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511927"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-machines controleren waarop de Log Analytics-agent niet is verbonden zoals verwacht](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Hiertoe moeten vereiste onderdelen worden geïmplementeerd in het bereik van de beleidstoewijzing. Zie [https://aka.ms/gcpol](https://aka.ms/gcpol) voor meer informatie. Machines voldoen niet als de agent niet is geïnstalleerd, of als deze wel is geïnstalleerd, maar door het COM-object AgentConfigManager.MgmtSvcCfg wordt geretourneerd dat de agent is geregistreerd bij een andere werkruimte dan de ID die is opgegeven in de beleidsparameter. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |
|[De Log Analytics-agent moet worden geïnstalleerd op virtuele-machineschaalsets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Met dit beleid worden alle virtuele-machineschaalsets van Windows/Linux gecontroleerd als de Log Analytics-agent niet is geïnstalleerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[De Log Analytics-agent moet worden geïnstalleerd op virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Met dit beleid worden alle virtuele machines van Windows/Linux gecontroleerd als de Log Analytics-agent niet is geïnstalleerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |
