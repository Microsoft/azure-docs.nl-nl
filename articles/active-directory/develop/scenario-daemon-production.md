---
title: Een daemon-app verplaatsen die web-Api's aanroept voor productie | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het verplaatsen van een daemon-app die web-Api's aanroept voor productie
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8dc9bff86a07f3d4a0ec6fd224de6d5633165a6d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582838"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-app voor het aanroepen van web-Api's-verplaatsen naar productie

Nu u weet hoe u een token kunt verkrijgen en gebruiken voor een service-naar-service-oproep, leert u hoe u uw app naar productie kunt verplaatsen.

## <a name="deployment---multitenant-daemon-apps"></a>Implementatie-multi tenant-daemon-apps

Als u een ISV-toepassing maakt die in meerdere tenants kan worden uitgevoerd, moet u ervoor zorgen dat de Tenant beheerder:

- Richt een Service-Principal in voor de toepassing.
- Verleent toestemming voor de toepassing.

U moet uitleggen wat uw klanten zijn om deze bewerkingen uit te voeren. Zie [toestemming vragen voor een hele Tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)voor meer informatie.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Codevoorbeelden

# <a name="net"></a>[.NET](#tab/dotnet)

- Referentie documentatie voor:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)instantiëren.
  - [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)aanroepen.
- Andere voor beelden/zelf studies:
  - [micro soft-Identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) bevat een kleine .net core daemon-console toepassing die de gebruikers bevat van een tenant query Microsoft Graph.

    ![Voor beeld van daemon-app-topologie](media/scenario-daemon-app/daemon-app-sample.svg)

    Hetzelfde voor beeld illustreert ook een variatie met certificaten:

    ![Voor beeld van daemon-app-topologie-certificaten](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [micro soft-Identity-platform-ASPNET-webapp](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) bevat een ASP.NET MVC-webtoepassing die gegevens van Microsoft Graph synchroniseert met behulp van de identiteit van de toepassing in plaats van namens een gebruiker. Dit voor beeld illustreert ook het toestemming proces voor beheerders.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

---

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele koppelingen om meer te weten te komen over:

# <a name="python"></a>[Python](#tab/python)

Probeer de Snelstartgids [een token te verkrijgen en Microsoft Graph-API aan te roepen vanuit een python-console-app met behulp van de identiteit van de app](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Probeer de Snelstartgids [een token te verkrijgen en Microsoft Graph-API aan te roepen vanuit een Java-Console-app met behulp van de identiteit van de app](./quickstart-v2-java-daemon.md).

---
