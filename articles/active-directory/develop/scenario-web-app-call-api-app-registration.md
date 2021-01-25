---
title: Een web-app registreren die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het registreren van een web-app die web-Api's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bb9a1ca6c2c81e3b0d5dbeff06f4de012446cf79
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756330"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Een web-app die web-Api's aanroept: app-registratie

Een web-app die web-Api's aanroept, heeft dezelfde registratie als een web-app die gebruikers ondertekent in. Volg de instructies in [een web-app die zich in gebruikers aanmeldt: app-registratie](scenario-web-app-sign-user-app-registration.md).

Echter, omdat de web-app nu ook Web-Api's aanroept, wordt het een vertrouwelijke client toepassing. Daarom is een extra registratie vereist. De app moet client referenties of *geheimen* delen met het micro soft Identity-platform.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-machtigingen

Web apps roepen namens de aangemelde gebruiker Api's aan. Hiervoor moeten ze *gedelegeerde machtigingen* aanvragen. Zie [machtigingen toevoegen voor toegang tot uw web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [code configuratie](scenario-web-app-call-api-app-configuration.md).
