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
ms.openlocfilehash: 99fd79fb6c51f577d9b62d15ac006b068a685bcf
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756539"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-app voor het aanroepen van web-Api's-verplaatsen naar productie

Nu u weet hoe u een token kunt verkrijgen en gebruiken voor een service-naar-service-oproep, leert u hoe u uw app naar productie kunt verplaatsen.

## <a name="deployment---multitenant-daemon-apps"></a>Implementatie-multi tenant-daemon-apps

Als u een ISV-toepassing maakt die in meerdere tenants kan worden uitgevoerd, moet u ervoor zorgen dat de Tenant beheerder:

- Richt een Service-Principal in voor de toepassing.
- Verleent toestemming voor de toepassing.

U moet uitleggen wat uw klanten zijn om deze bewerkingen uit te voeren. Zie [toestemming vragen voor een hele Tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)voor meer informatie.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele koppelingen om meer te weten te komen over:

# <a name="net"></a>[.NET](#tab/dotnet)

- Quick Start: [een token verkrijgen en Microsoft Graph-API aanroepen vanuit een console-app met behulp van de identiteit van de app](./quickstart-v2-netcore-daemon.md).
- Referentie documentatie voor:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)instantiëren.
  - [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)aanroepen.
- Andere voor beelden/zelf studies:
  - [micro soft-Identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) bevat een eenvoudige .net core daemon-console toepassing die de gebruikers van een tenant query Microsoft Graph.

    ![Voor beeld van daemon-app-topologie](media/scenario-daemon-app/daemon-app-sample.svg)

    Hetzelfde voor beeld illustreert ook een variatie met certificaten:

    ![Voor beeld van daemon-app-topologie-certificaten](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [micro soft-Identity-platform-ASPNET-webapp](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) bevat een ASP.NET MVC-webtoepassing die gegevens van Microsoft Graph synchroniseert met behulp van de identiteit van de toepassing in plaats van namens een gebruiker. Dit voor beeld illustreert ook het toestemming proces voor beheerders.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Probeer de Snelstartgids [een token te verkrijgen en Microsoft Graph-API aan te roepen vanuit een python-console-app met behulp van de identiteit van de app](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

MSAL Java is momenteel beschikbaar als open bare preview. Zie MSAL voor beelden van [Java-Ontwikkel aars](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)voor meer informatie.

---