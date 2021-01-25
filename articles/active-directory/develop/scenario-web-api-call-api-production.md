---
title: Web-API voor het aanroepen van web-Api's naar productie verplaatsen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het verplaatsen van een web-API die web-Api's aanroept voor productie.
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
ms.openlocfilehash: 370bedf04dc61e2a637f735580cd4df14061264a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753343"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Een web-API die web-Api's aanroept: verplaatsen naar productie

Nadat u een token hebt aangeschaft om Web-Api's aan te roepen, kunt u uw app naar productie verplaatsen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Meer informatie

Nu u de basis principes kent van het aanroepen van web-Api's vanuit uw eigen web-API, bent u mogelijk geïnteresseerd in de volgende zelf studie, waarmee de code wordt beschreven die wordt gebruikt voor het bouwen van een beveiligde web-API die web-Api's aanroept.

| Voorbeeld | Platform | Beschrijving |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-zelf studie-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) hoofd stuk 1 | ASP.NET Core Web-API, bureau blad (WPF) | ASP.NET Core Web API-aanroepen Microsoft Graph, die u aanroept vanuit een WPF-toepassing met behulp van het micro soft Identity-platform. |
