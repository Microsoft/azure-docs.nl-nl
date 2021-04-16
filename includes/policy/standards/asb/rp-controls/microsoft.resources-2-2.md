---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 21a0a62a2ed6afe2e0b45732144fc79bb2ec38d5
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510988"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatisch inrichten van de Log Analytics-agent moet zijn ingeschakeld voor uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Om te controleren op beveiligingsproblemen en bedreigingen verzamelt Azure Security Center gegevens van uw Azure-VM's. De gegevens worden verzameld met behulp van de Log Analytics-agent, voorheen bekend als de Microsoft Monitoring Agent (MMA), die verschillende configuraties en gebeurtenislogboeken met betrekking tot beveiliging van de machine leest en de gegevens kopieert naar uw Log Analytics-werkruimte voor analyse. U wordt aangeraden automatische inrichting in te schakelen om de agent automatisch te implementeren op alle ondersteunde Azure-VM‘s en eventuele nieuwe virtuele machines die worden gemaakt. |AuditIfNotExists, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Met het Azure Monitor-logboekprofiel moeten logboeken worden verzameld voor de categorieën 'schrijven', 'verwijderen' en 'actie'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Met dit beleid wordt ervoor gezorgd dat in een logboekprofiel logboeken worden verzameld voor de categorieën 'schrijven', 'verwijderen' en 'actie' |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Azure Monitor moet activiteitenlogboeken uit alle regio's verzamelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Met dit beleid wordt het Azure Monitor-logboekprofiel gecontroleerd waarbij geen activiteiten worden geëxporteerd uit alle door Azure ondersteunde regio's, inclusief wereldwijd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
