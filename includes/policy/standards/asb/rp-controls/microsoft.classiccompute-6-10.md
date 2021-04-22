---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 22592adf9d3bb538ba087ff9ab828bd374b4d61e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879994"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Schakel toepassingsregelaars in om de lijst te definiëren met bekende veilige toepassingen die worden uitgevoerd op uw machines en om een waarschuwing te ontvangen wanneer andere toepassingen worden uitgevoerd. Dit helpt uw computers te beschermen tegen schadelijke software. Om het proces van het configureren en onderhouden van uw regels te vereenvoudigen, gebruikt Security Center machine learning om de toepassingen te analyseren die op elke computer worden uitgevoerd en stelt de lijst met bekende veilige toepassingen voor. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
