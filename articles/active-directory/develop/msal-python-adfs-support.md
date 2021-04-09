---
title: Azure AD FS-ondersteuning (MSAL python)
titleSuffix: Microsoft identity platform
description: Meer informatie over de ondersteuning van Active Directory Federation Services (AD FS) in de micro soft-verificatie bibliotheek voor python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d847e38a6114b0e5f8d56f6e994aa47dfbe929e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583702"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Ondersteuning voor Active Directory Federation Services in MSAL voor python

Met Active Directory Federation Services (AD FS) in Windows Server kunt u OpenID Connect Connect en OAuth 2,0-verificatie en-autorisatie toevoegen aan uw apps met behulp van de micro soft Authentication Library (MSAL) voor python. Met de MSAL voor python-bibliotheek kan uw app gebruikers rechtstreeks verifiëren tegen AD FS. Zie [AD FS scenario's voor ontwikkel aars](/windows-server/identity/ad-fs/ad-fs-development)voor meer informatie over scenario's.

Er zijn meestal twee manieren om te verifiëren met AD FS:

- MSAL python praat met Azure Active Directory, dat zelf is federatief met andere id-providers. De Federatie treedt op via AD FS. MSAL python maakt verbinding met Azure AD, waarmee wordt gemeldd in gebruikers die worden beheerd in azure AD (beheerde gebruikers) of gebruikers die worden beheerd door een andere ID-provider, zoals AD FS (federatieve gebruikers). MSAL python weet niet dat een gebruiker federatief is. Het gaat gewoon naar Azure AD. De [instantie](msal-client-application-configuration.md#authority) die u in dit geval gebruikt, is de gebruikelijke instantie (hostnaam van de autoriteit + Tenant, common of organisaties).
- MSAL python praat rechtstreeks met een AD FS-instantie. Dit wordt alleen ondersteund door AD FS 2019 en hoger.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Verbinding maken met Active Directory federatieve met AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Een token interactief aanschaffen voor een federatieve gebruiker

Het volgende is van toepassing, ongeacht of u rechtstreeks verbinding maakt met Active Directory Federation Services (AD FS) of via Active Directory.

Wanneer u `acquire_token_by_authorization_code` of belt `acquire_token_by_device_flow` , is de gebruikers ervaring als volgt:

1. De gebruiker voert de account-ID in.
2. In azure AD wordt het bericht ' u gaat naar de pagina van uw organisatie ' weer gegeven en de gebruiker wordt omgeleid naar de aanmeldings pagina van de ID-provider. De aanmeldings pagina wordt doorgaans aangepast met het logo van de organisatie.

De ondersteunde AD FS versies in dit federatieve scenario zijn:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Een token verkrijgen via gebruikers naam en wacht woord

Het volgende is van toepassing, ongeacht of u rechtstreeks verbinding maakt met Active Directory Federation Services (AD FS) of via Active Directory.

Wanneer u een token aanschaft met behulp `acquire_token_by_username_password` van, haalt MSAL python de ID-provider contact op op basis van de gebruikers naam. MSAL python haalt een [SAML 1,1-token](reference-saml-tokens.md) van de ID-provider, die vervolgens aan Azure AD wordt verstrekt, waarmee de JSON Web token (JWT) wordt geretourneerd.

## <a name="connecting-directly-to-ad-fs"></a>Rechtstreeks verbinding maken met AD FS

Wanneer u een map verbindt met AD FS, is de instantie die u wilt gebruiken om uw toepassing te maken, iets zoals `https://somesite.contoso.com/adfs/`

MSAL python ondersteunt ADFS 2019.

Er wordt geen rechtstreekse verbinding met ADFS 2016 of ADFS v2 ondersteund. Gebruik de meest recente versie van ADAL python om scenario's te ondersteunen die een directe verbinding met ADFS 2016 vereisen. Wanneer u uw on-premises systeem hebt bijgewerkt naar ADFS 2019, kunt u MSAL python gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Zie voor de federatieve situatie [Azure Active Directory aanmeldings gedrag configureren voor een toepassing met behulp van een beleid voor het detecteren van een thuis domein](../manage-apps/configure-authentication-for-federated-users-portal.md)
