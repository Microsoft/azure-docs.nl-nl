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
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675869"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Een web-API die web-Api's aanroept: verplaatsen naar productie

Nadat u een token hebt aangeschaft om Web-Api's aan te roepen, moet u rekening houden met de volgende punten wanneer u uw toepassing naar productie gaat verplaatsen.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes kent van het aanroepen van web-Api's vanuit uw eigen web-API, bent u mogelijk geïnteresseerd in de volgende zelf studie, waarmee de code wordt beschreven die wordt gebruikt voor het bouwen van een beveiligde web-API die web-Api's aanroept.

| Voorbeeld | Platform | Beschrijving |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-zelf studie-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) hoofd stuk 1 | ASP.NET Core Web-API, bureau blad (WPF) | ASP.NET Core Web API-aanroepen Microsoft Graph, die u aanroept vanuit een WPF-toepassing met behulp van het micro soft Identity-platform. |
