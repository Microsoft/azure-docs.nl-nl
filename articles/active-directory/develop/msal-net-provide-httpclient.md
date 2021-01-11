---
title: Geef een httpclient maakt-& proxy op (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bieden van uw eigen httpclient maakt en proxy om verbinding te maken met Azure AD met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6139fd1b081c69f037ec9cd3313e4a6499c39543
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064620"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Uw eigen httpclient maakt en proxy bieden met behulp van MSAL.NET
Wanneer u [een open bare client toepassing initialiseert](msal-net-initializing-client-applications.md), kunt u de gebruiken `.WithHttpClientFactory method` om uw eigen httpclient maakt te bieden.  Door uw eigen httpclient maakt te bieden, kunt u geavanceerde scenario's maken met een dergelijk nauw keurig beheer van een HTTP-proxy, het aanpassen van de headers van de gebruikers agent of het afdwingen van MSAL om een specifieke httpclient maakt te gebruiken (bijvoorbeeld in ASP.NET Core web apps/Api's).

## <a name="initialize-with-httpclientfactory"></a>Initialiseren met HttpClientFactory
In het volgende voor beeld ziet u hoe u een `HttpClientFactory` open bare client toepassing maakt en deze vervolgens initialiseert:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>Httpclient maakt en Xamarin iOS
Wanneer u Xamarin iOS gebruikt, wordt het aanbevolen een `HttpClient` expliciete handler te maken `NSURLSession` voor IOS 7 en hoger. MSAL.NET maakt automatisch een `HttpClient` die gebruik maakt `NSURLSessionHandler` van Ios 7 en hoger. Raadpleeg de [Xamarin IOS-documentatie voor httpclient maakt](/xamarin/cross-platform/macios/http-stack)voor meer informatie.