---
title: Beheerde identiteiten gebruiken in communicatie Services
titleSuffix: An Azure Communication Services quickstart
description: Met beheerde identiteiten kunt u toegang verlenen tot Azure Communication Services vanuit toepassingen die worden uitgevoerd in azure Vm's, functie-apps en andere resources.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e4b71cc889615fd4784f11c9edd77b44ef421d9e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110727"
---
# <a name="use-managed-identities"></a>Beheerde identiteiten gebruiken
Ga aan de slag met Azure Communication Services door beheerde identiteiten te gebruiken. De communicatie Services identiteit en SMS-Sdk's ondersteunen Azure Active Directory-verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md).

Deze Quick Start laat zien hoe u toegang tot de identiteits-en SMS-Sdk's kunt verlenen vanuit een Azure-omgeving die beheerde identiteiten ondersteunt. Ook wordt beschreven hoe u uw code in een ontwikkel omgeving kunt testen.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over toegangs beheer op basis van rollen](../../../articles/role-based-access-control/index.yml)
- [Meer informatie over de Azure-identiteits bibliotheek voor .NET](/dotnet/api/overview/azure/identity-readme)
- [Tokens voor gebruikerstoegang maken](../quickstarts/access-tokens.md)
- [Een sms-bericht verzenden](../quickstarts/telephony-sms/send.md)
- [Meer informatie over sms](../concepts/telephony-sms/concepts.md)
