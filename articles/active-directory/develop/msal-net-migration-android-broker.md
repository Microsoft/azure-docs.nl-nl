---
title: Xamarin Android-apps migreren met behulp van Brokers naar MSAL.NET
titleSuffix: Microsoft identity platform
description: Meer informatie over het migreren van Xamarin Android-apps die gebruikmaken van de Microsoft Authenticator of Intune-bedrijfsportal van ADAL.NET naar MSAL.NET.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 01af0e620ecb100839f7b1101e5ff9fcfc874eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206666"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>Android-toepassingen migreren die een Broker gebruiken van ADAL.NET naar MSAL.NET

Als u een Xamarin Android-app hebt die momenteel gebruikmaakt van de Azure Active Directory Authentication Library voor .NET (ADAL.NET) en een [verificatie Broker](msal-android-single-sign-on.md), is het tijd om te migreren naar de [micro soft Authentication Library voor .net ](msal-overview.md) (MSAL.net).

## <a name="prerequisites"></a>Vereisten

* Een Xamarin Android-app is al geïntegreerd met een Broker ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) of [Intune-bedrijfsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) en ADAL.net die u moet migreren naar MSAL.net.

## <a name="step-1-enable-the-broker"></a>Stap 1: de Broker inschakelen

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
In ADAL.NET is Broker-ondersteuning ingeschakeld op basis van context per verificatie.

Als u de Broker wilt aanroepen, moet u `useBroker` in de constructor een op *waar* instellen `PlatformParameters` :

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

In de platformspecifieke pagina renderer code voor Android stelt u de `useBroker` vlag in op True:

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

Neem vervolgens de para meters op in de aanroep voor het verkrijgen van een token:

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
In MSAL.NET is de ondersteuning van Broker ingeschakeld op basis van één PublicClientApplication.

Gebruik de `WithBroker()` para meter (standaard ingesteld op True) om Broker aan te roepen:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

In de AcquireToken-aanroep:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>Stap 2: een activiteit instellen

In ADAL.NET hebt u een activiteit (meestal de MainActivity) door gegeven als onderdeel van de PlatformParameters, zoals wordt weer gegeven in [stap 1: Schakel de Broker](#step-1-enable-the-broker)in.

MSAL.NET maakt ook gebruik van een activiteit, maar is niet vereist in het gebruik van normale Android zonder een Broker. Als u de Broker wilt gebruiken, stelt u de activiteit in voor het verzenden en ontvangen van antwoorden van de Broker.

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
De activiteit wordt door gegeven aan de PlatformParameters in het Android-specifieke platform.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

In MSAL.NET voert u twee dingen uit om de activiteit in te stellen voor Android:

1. Stel in op `MainActivity.cs` `App.RootViewController`  de om te `MainActivity` controleren of er een activiteit is met de aanroep van de Broker.

    Als deze niet correct is ingesteld, wordt deze fout mogelijk weer geven: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. Gebruik in de AcquireTokenInteractive-aanroep de `.WithParentActivityOrWindow(App.RootViewController)` en geef deze door aan de verwijzing naar de activiteit die u gaat gebruiken. In dit voor beeld wordt het MainActivity gebruikt.

**Bijvoorbeeld:**

In *app. cs*:

```CSharp
   public static object RootViewController { get; set; }
```

In *MainActivity. cs*:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

In de AcquireToken-aanroep:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Volgende stappen

Zie [configuratie vereisten en tips voor probleem oplossing voor Xamarin Android met MSAL.net](msal-net-xamarin-android-considerations.md)voor meer informatie over Android-specifieke overwegingen bij het gebruik van MSAL.net met Xamarin.