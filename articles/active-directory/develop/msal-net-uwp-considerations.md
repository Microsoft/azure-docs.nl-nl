---
title: UWP overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Universeel Windows-platform (UWP) met de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8a8aab447007eb574a7a4bc532d8177bd0d8b345
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122473"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Overwegingen voor het gebruik van Universeel Windows-platform met MSAL.NET
Ontwikkel aars van toepassingen die gebruikmaken van Universeel Windows-platform (UWP) met MSAL.NET, moeten rekening houden met de concepten in dit artikel.

## <a name="the-usecorporatenetwork-property"></a>De eigenschap UseCorporateNetwork
Op het Windows Runtime (WinRT)-platform `PublicClientApplication` heeft de Booleaanse eigenschap `UseCorporateNetwork` . Met deze eigenschap kunnen Windows 10-toepassingen en UWP-toepassingen profiteren van geïntegreerde Windows-authenticatie (IWA) als de gebruiker is aangemeld bij een account met een federatieve Azure Active Directory-Tenant (Azure AD). Gebruikers die zijn aangemeld bij het besturings systeem, kunnen ook gebruikmaken van eenmalige aanmelding (SSO). Wanneer u de `UseCorporateNetwork` eigenschap instelt, maakt MSAL.net gebruik van een Web authentication Broker (WAB).

> [!IMPORTANT]
> `UseCorporateNetwork`Als u de eigenschap instelt op True, wordt ervan uitgegaan dat de ontwikkelaar van de toepassing IWA in de toepassing heeft ingeschakeld. IWA inschakelen:
> - `Package.appxmanifest`Schakel op het tabblad **mogelijkheden** van uw UWP-toepassing de volgende mogelijkheden in:
>   - **Ondernemingsverificatie**
>   - **Particuliere netwerken (client en server)**
>   - **Gedeeld gebruikers certificaat**

IWA is niet standaard ingeschakeld omdat Microsoft Store een hoog verificatie niveau vereist voordat het toepassingen accepteert die de mogelijkheden van ondernemings verificatie of gedeelde gebruikers certificaten aanvragen. Niet alle ontwikkel aars willen dit verificatie niveau.

Op het UWP-platform werkt de onderliggende WAB-implementatie niet goed in bedrijfs scenario's waarin voorwaardelijke toegang is ingeschakeld. Gebruikers zien symptomen van dit probleem wanneer ze zich proberen aan te melden met behulp van Windows hello. Wanneer de gebruiker wordt gevraagd een certificaat te kiezen:

- Het certificaat voor de pincode is niet gevonden.
- Nadat de gebruiker een certificaat heeft gekozen, wordt niet gevraagd om de pincode.

U kunt proberen dit probleem te vermijden door gebruik te maken van een alternatieve methode, zoals gebruikers naam en telefoon verificatie, maar de ervaring is niet goed.

## <a name="troubleshooting"></a>Problemen oplossen

Sommige klanten hebben de volgende aanmeldings fout gerapporteerd in specifieke bedrijfs omgevingen waarin ze weten dat ze een Internet verbinding hebben en dat de verbinding met een openbaar netwerk werkt.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

U kunt dit probleem voor komen door ervoor te zorgen dat WAB (het onderliggende Windows-onderdeel) een particulier netwerk toestaat. U kunt dit doen door een register sleutel in te stellen:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Zie voor meer informatie [Web authentication Broker-Fiddler](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Volgende stappen
In de volgende voor beelden vindt u meer informatie.

Voorbeeld | Platform | Beschrijving 
|------ | -------- | -----------|
|[Active-Directory-DotNet-native-UWP-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Een UWP-client toepassing die gebruikmaakt van MSAL.NET. Er wordt gebruikgemaakt van Microsoft Graph voor een gebruiker die zich verifieert met behulp van een Azure AD 2,0-eind punt. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[Active-Directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Een Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om micro soft-persoonlijke accounts en Azure AD te verifiëren via het micro soft-identiteits platform. Ook wordt uitgelegd hoe u toegang krijgt tot Microsoft Graph en hoe het resulterende token wordt weer gegeven. <br>![Diagram waarin wordt getoond hoe u MSAL kunt gebruiken om micro soft-persoonlijke accounts en Azure AD te verifiëren via het micro soft-identiteits platform.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
