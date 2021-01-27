---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6a5c86dc1b10ed79e5dabbf5be7bf3ee2b677dee
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807680"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot-service-eindpunt moet een geldige HTTPS-URI zijn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Tijdens het verzenden kunnen gegevens onrechtmatig worden gewijzigd. Er bestaan protocollen die versleuteling bieden om problemen met misbruik en manipulatie van gegevens te voorkomen. Om ervoor te zorgen dat uw bots alleen communiceren via versleutelde kanalen, stelt u het eindpunt in op een geldige HTTPS-URI. Dit zorgt ervoor dat het HTTPS-protocol wordt gebruikt voor het versleutelen van uw data-in-transit, en is ook een vereiste voor naleving met regelgeving of industriestandaarden. Ga naar: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |controleren, weigeren, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
