---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bf0ba60985d784b765734783289a59e7f8518d9e
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99220279"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot-service-eindpunt moet een geldige HTTPS-URI zijn](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Tijdens het verzenden kunnen gegevens onrechtmatig worden gewijzigd. Er bestaan protocollen die versleuteling bieden om problemen met misbruik en manipulatie van gegevens te voorkomen. Om ervoor te zorgen dat uw bots alleen communiceren via versleutelde kanalen, stelt u het eindpunt in op een geldige HTTPS-URI. Dit zorgt ervoor dat het HTTPS-protocol wordt gebruikt voor het versleutelen van uw data-in-transit, en is ook een vereiste voor naleving met regelgeving of industriestandaarden. Ga naar: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |controleren, weigeren, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot-service moet worden versleuteld met een door de klant beheerde sleutel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service versleutelt uw resource automatisch om uw gegevens te beschermen en te voldoen aan de beveiligings-en nalevings verplichtingen van de organisatie. Door micro soft beheerde versleutelings sleutels worden standaard gebruikt. Voor meer flexibiliteit bij het beheren van sleutels of het beheren van toegang tot uw abonnement, selecteert u door de klant beheerde sleutels, ook wel bekend als uw eigen sleutel (BYOK). Meer informatie over Azure Bot Service versleuteling: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |controleren, weigeren, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
