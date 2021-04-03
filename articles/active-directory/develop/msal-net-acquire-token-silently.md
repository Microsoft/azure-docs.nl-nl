---
title: Een Token ophalen uit de cache (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe u een toegangs token op de achtergrond kunt verkrijgen (uit de token cache) met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 94e4a4d5b80e246ce822e977deafe5c41c9ff4ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064773"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Een Token ophalen uit de token cache met behulp van MSAL.NET

Wanneer u een toegangs token aanschaft met behulp van de micro soft Authentication Library voor .NET (MSAL.NET), wordt het token in de cache opgeslagen. Wanneer de toepassing een token vereist, moet het eerst de methode aanroepen `AcquireTokenSilent` om te controleren of een acceptabel token zich in de cache bevindt. In veel gevallen is het mogelijk om een andere token te verkrijgen met meer scopes op basis van een token in de cache. Het is ook mogelijk om een token te vernieuwen wanneer het bijna is verlopen (omdat de token cache ook een vernieuwings token bevat).

Het aanbevolen patroon is om eerst de methode aan te roepen `AcquireTokenSilent` .  Als dit `AcquireTokenSilent` mislukt, verschaf dan een token met behulp van andere methoden.

In het volgende voor beeld probeert de toepassing eerst een token op te halen uit de token cache.  Als er een `MsalUiRequiredException` uitzonde ring wordt gegenereerd, verkrijgt de toepassing interactief een token. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
