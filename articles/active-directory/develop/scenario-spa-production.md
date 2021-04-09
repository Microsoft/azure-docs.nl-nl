---
title: App met één pagina verplaatsen naar productie
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een toepassing met één pagina (verplaatsen naar productie)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954954"
---
# <a name="single-page-application-move-to-production"></a>Toepassing met één pagina: verplaatsen naar productie

Nu u weet hoe u een token kunt verkrijgen om Web-Api's aan te roepen, kunt u overwegen om rekening te houden met de volgende punten wanneer u uw toepassing naar productie gaat verplaatsen.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Uw app implementeren

Bekijk een voor [beeld](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) van de implementatie om te leren hoe u uw spa-en Web-API-projecten kunt implementeren met Azure Storage en Azure-app Services. 

## <a name="code-samples"></a>Codevoorbeelden

In deze code voorbeelden worden verschillende belang rijke bewerkingen voor een app met één pagina gedemonstreerd.
- [Beveiligd-wachtwoord verificatie met een ASP.net-back-end](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): tokens ophalen voor uw eigen back-end web-API (ASP.net core) met behulp van **MSAL.js**.

- [Node.js Web-API (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): toegangs tokens voor uw back-end web-api (Node.js) valideren met behulp van **Pass Port-Azure-AD**.

- [Beveiligd-wachtwoord verificatie met Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): **MSAL.js** gebruiken om gebruikers aan te melden bij een app die is geregistreerd bij **Azure Active Directory B2C** (Azure AD B2C).

- [Node.js Web-API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): **Pass Port-Azure-AD** gebruiken om toegangs tokens te valideren voor apps die zijn geregistreerd met **Azure Active Directory B2C** (Azure AD B2C).

## <a name="next-steps"></a>Volgende stappen

- [Java script-zelf studie](./tutorial-v2-javascript-spa.md): uitgebreide kennis over het aanmelden van gebruikers en het verkrijgen van een toegangs token om de **Microsoft Graph-API** aan te roepen met behulp van **MSAL.js**.