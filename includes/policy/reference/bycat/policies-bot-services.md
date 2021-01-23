---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e884d11aa3d86a2d9e156aeb14cf4d53a301f2b2
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738571"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot-service-eindpunt moet een geldige HTTPS-URI zijn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Tijdens het verzenden kunnen gegevens onrechtmatig worden gewijzigd. Er bestaan protocollen die versleuteling bieden om problemen met misbruik en manipulatie van gegevens te voorkomen. Om ervoor te zorgen dat uw bots alleen communiceren via versleutelde kanalen, stelt u het eindpunt in op een geldige HTTPS-URI. Dit zorgt ervoor dat het HTTPS-protocol wordt gebruikt voor het versleutelen van uw data-in-transit, en is ook een vereiste voor naleving met regelgeving of industriestandaarden. Ga naar: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |controleren, weigeren, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
