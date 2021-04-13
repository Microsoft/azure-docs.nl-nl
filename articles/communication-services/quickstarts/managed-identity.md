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
ms.openlocfilehash: aedf54c8c958e96b2bbfa31652b4861ff452f75a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307431"
---
# <a name="use-managed-identities"></a>Beheerde identiteiten gebruiken
Ga aan de slag met Azure Communication Services door beheerde identiteiten te gebruiken. De communicatie Services identiteit en SMS-Sdk's ondersteunen Azure Active Directory-verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md).

Deze Quick Start laat zien hoe u toegang tot de identiteits-en SMS-Sdk's kunt verlenen vanuit een Azure-omgeving die beheerde identiteiten ondersteunt. Ook wordt beschreven hoe u uw code in een ontwikkel omgeving kunt testen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)
- Een actieve Azure Communication Services-resource, Zie [een communicatie Services-resource maken](./create-communication-resource.md) als u er nog geen hebt.
- Als u een SMS wilt verzenden, hebt u een [telefoon nummer](./telephony-sms/get-phone-number.md)nodig.
- Een door Setup beheerde identiteit voor een ontwikkel omgeving, Zie [toegang verlenen met beheerde identiteit](./managed-identity-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over toegangs beheer op basis van rollen](../../../articles/role-based-access-control/index.yml)
- [Meer informatie over de Azure-identiteits bibliotheek voor .NET](/dotnet/api/overview/azure/identity-readme)
- [Tokens voor gebruikerstoegang maken](../quickstarts/access-tokens.md)
- [Een sms-bericht verzenden](../quickstarts/telephony-sms/send.md)
- [Meer informatie over sms](../concepts/telephony-sms/concepts.md)
