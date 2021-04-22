---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1d7df6224dcd6533772734060f4c1cf32e3c972f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866469"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot-service-eindpunt moet een geldige HTTPS-URI zijn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Tijdens het verzenden kunnen gegevens onrechtmatig worden gewijzigd. Er bestaan protocollen die versleuteling bieden om problemen met misbruik en manipulatie van gegevens te voorkomen. Om ervoor te zorgen dat uw bots alleen communiceren via versleutelde kanalen, stelt u het eindpunt in op een geldige HTTPS-URI. Dit zorgt ervoor dat het HTTPS-protocol wordt gebruikt voor het versleutelen van uw data-in-transit, en is ook een vereiste voor naleving met regelgeving of industriestandaarden. Ga naar: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |controleren, weigeren, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service moeten worden versleuteld met een door de klant beheerde sleutel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service versleutelt uw resource automatisch om uw gegevens te beschermen en te voldoen aan de beveiligings- en nalevingsverplichtingen van de organisatie. Standaard worden door Microsoft beheerde versleutelingssleutels gebruikt. Voor meer flexibiliteit bij het beheren van sleutels of het beheren van de toegang tot uw abonnement, selecteert u door de klant beheerde sleutels, ook wel bring your own key (BYOK) genoemd. Meer informatie over Azure Bot Service versleuteling: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |controleren, weigeren, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
