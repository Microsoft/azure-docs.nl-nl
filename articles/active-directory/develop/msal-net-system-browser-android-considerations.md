---
title: Xamarin Android-systeem browser overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van systeem browsers op Xamarin Android met de micro soft-verificatie bibliotheek voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4230a194fb18587a209c100a39b0924e6170502d
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063464"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android-systeem browser overwegingen voor het gebruik van MSAL.NET

In dit artikel wordt beschreven wat u moet overwegen wanneer u de systeem browser op Xamarin Android gebruikt met de micro soft Authentication Library voor .NET (MSAL.NET).

Vanaf MSAL.NET 2.4.0 Preview ondersteunt MSAL.NET andere browsers dan Chrome. Het is niet meer nodig om Chrome te installeren op het Android-apparaat voor authenticatie.

U wordt aangeraden browsers te gebruiken die aangepaste tabbladen ondersteunen. Hier volgen enkele voor beelden van deze browsers:

| Browsers met aangepaste tabbladen ondersteunen | Naam van het pakket |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. micro soft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwi | com. kiwibrowser. browser|
|Brave | com. Brave. browser|

Naast het identificeren van browsers die ondersteuning bieden voor aangepaste tabbladen, wordt met onze tests aangegeven dat een aantal browsers die geen aangepaste tabbladen ondersteunen ook voor verificatie werkt. Deze browsers bevatten Opera, Opera Mini-, inbrowser-en Maxthon. 

## <a name="tested-devices-and-browsers"></a>Geteste apparaten en browsers
De volgende tabel geeft een lijst van de apparaten en browsers die zijn getest voor verificatie compatibiliteit.

| Apparaat | Browser     |  Resultaat  | 
| ------------- |:-------------:|:-----:|
| Huawei/één + | Chrome\* | Geslaagd|
| Huawei/één + | Edge\* | Geslaagd|
| Huawei/één + | Firefox\* | Geslaagd|
| Huawei/één + | Brave\* | Geslaagd|
| Eén + | Ecosia\* | Geslaagd|
| Eén + | Kiwi\* | Geslaagd|
| Huawei/één + | Opera | Geslaagd|
| Huawei | OperaMini | Geslaagd|
| Huawei/één + | Inbrowser | Geslaagd|
| Eén + | Maxthon | Geslaagd|
| Huawei/één + | DuckDuckGo | Door de gebruiker geannuleerde authenticatie|
| Huawei/één + | UC-browser | Door de gebruiker geannuleerde authenticatie|
| Eén + | Dolfijnen | Door de gebruiker geannuleerde authenticatie|
| Eén + | CM-browser | Door de gebruiker geannuleerde authenticatie|
| Huawei/één + | Geen geïnstalleerd | AndroidActivityNotFound-uitzonde ring|

\* Ondersteunt aangepaste tabbladen

## <a name="known-issues"></a>Bekende problemen

Als de gebruiker geen browser op het apparaat heeft ingeschakeld, wordt door MSAL.NET een `AndroidActivityNotFound` uitzonde ring gegenereerd.  
  - **Risico beperking**: vraag de gebruiker een browser op het apparaat in te scha kelen. U kunt het beste een browser aanbevelen die aangepaste tabbladen ondersteunt.

Als verificatie mislukt (bijvoorbeeld als verificatie wordt gestart met DuckDuckGo), wordt door MSAL.NET geretourneerd `AuthenticationCanceled MsalClientException` . 
  - **Hoofd probleem**: een browser die ondersteuning biedt voor aangepaste tabbladen is niet ingeschakeld op het apparaat. Verificatie gestart met een browser die de verificatie niet kan volt ooien. 
  - **Risico beperking**: vraag de gebruiker een browser op het apparaat in te scha kelen. U kunt het beste een browser aanbevelen die aangepaste tabbladen ondersteunt.

## <a name="next-steps"></a>Volgende stappen
Zie [kiezen tussen een Inge sloten webbrowser en een systeem browser op Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) en [embedded versus systeem webinterface](msal-net-web-browsers.md#embedded-vs-system-web-ui)voor meer informatie en voor beelden van code.  