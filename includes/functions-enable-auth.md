---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83648939"
---
#### <a name="enable-app-service-authenticationauthorization"></a>App Service-verificatie/-autorisatie inschakelen

Met het App Service-platform kunt u Azure Active Directory (AAD) en verschillende id-providers van derden gebruiken om clients te verifiëren. U kunt deze strategie gebruiken om aangepaste autorisatie regels voor uw functies te implementeren en u kunt met gebruikers gegevens uit uw functie code werken. Zie [verificatie en autorisatie in azure app service](../articles/app-service/overview-authentication-authorization.md) en [werken met client identiteiten](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)voor meer informatie.

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Azure API Management (APIM) gebruiken om aanvragen te verifiëren

APIM biedt diverse API-beveiligings opties voor inkomende aanvragen. Zie [API Management Authentication policies](../articles/api-management/api-management-authentication-policies.md)(Engelstalig) voor meer informatie. Als APIM is geïmplementeerd, kunt u uw functie-app zodanig configureren dat aanvragen alleen worden geaccepteerd van het IP-adres van uw APIM-exemplaar. Zie [IP-adres beperkingen](../articles/azure-functions/ip-addresses.md#ip-address-restrictions)voor meer informatie.
