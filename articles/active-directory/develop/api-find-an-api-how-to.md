---
title: Een API zoeken voor een aangepaste app | Azure
description: De machtigingen configureren die u nodig hebt voor toegang tot een bepaalde API in uw aangepaste, ontwikkelde Azure AD-toepassing
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 28cfb3d8b09c9661d16ac6e7146c50e7043d913a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581955"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Een specifieke API vinden die nodig is voor een speciaal ontwikkelde toepassing

Toegang tot Api's vereist configuratie van toegangs scopes en rollen. Als u de Web-Api's voor de resource toepassing beschikbaar wilt maken voor client toepassingen, configureert u de toegangs scopes en rollen voor de API. Als u een-client toepassing toegang wilt geven tot een web-API, configureert u de machtigingen voor toegang tot de API in de app-registratie.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Een resourcetoepassing configureren voor het beschikbaar maken van web-API's

Wanneer u uw web-API beschikbaar maakt, wordt de API weer gegeven in de lijst **een API selecteren** wanneer u machtigingen toevoegt aan een app-registratie. Volg de stappen die worden beschreven in [een toepassing configureren om Web-api's](quickstart-configure-app-expose-web-apis.md)weer te geven om toegangs scopes toe te voegen.

## <a name="configuring-a-client-application-to-access-web-apis"></a>Een client toepassing configureren voor toegang tot Web-Api's

Wanneer u machtigingen toevoegt voor uw app-registratie, kunt u **API-toegang toevoegen** aan weer gegeven Web-api's. Als u toegang wilt krijgen tot Web-Api's, volgt u de stappen in [een client toepassing configureren voor toegang tot Web-api's](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Volgende stappen

- [Informatie over het Azure Active Directory toepassings manifest](./reference-app-manifest.md)