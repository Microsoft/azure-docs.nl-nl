---
title: Problemen met TLS/SSL oplossen (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over diverse problemen met TLS/SSL-certificaten met de MSAL. Doel-C-bibliotheek.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80881074"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Procedure: problemen oplossen met MSAL voor iOS-en macOS TLS/SSL-problemen

In dit artikel vindt u informatie over het oplossen van problemen die kunnen optreden tijdens het gebruik [van de micro soft Authentication Library (MSAL) voor IOS en macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Netwerk problemen

**Fout-1200**: er is een SSL-fout opgetreden en een beveiligde verbinding met de server kan niet worden gemaakt.

Deze fout betekent dat de verbinding niet is beveiligd. Deze gebeurtenis treedt op wanneer een certificaat ongeldig is. Raadpleeg `NSURLErrorFailingURLErrorKey` de `userInfo` woorden lijst van het fout object voor meer informatie, waaronder welke server de TLS-controle niet kan uitvoeren.

Deze fout is afkomstig uit de netwerk bibliotheek van Apple. Een volledige lijst met NSURL-fout codes bevindt zich in NSURLError. h in de macOS-en iOS-Sdk's. Zie [URL-systeem fout codes laden](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)voor meer informatie over deze fout.

## <a name="certificate-issues"></a>Certificaat problemen

Als de URL die een ongeldig certificaat levert, verbinding maakt met de server die u wilt gebruiken als onderdeel van de verificatie stroom, is een goed begin om de URL te testen met een SSL-validatie service zoals SSL- [server test](https://www.ssllabs.com/ssltest/analyze.html). Hiermee wordt de server getest op een breed scala aan scenario's en browsers en worden er controles uitgevoerd voor veel bekende beveiligings problemen.

De nieuwe [ATS-functie (app Trans Port Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) van Apple past standaard strengere beveiligings beleidsregels toe op apps die gebruikmaken van TLS/SSL-certificaten. Sommige besturings systemen en webbrowsers zijn standaard begonnen met het afdwingen van enkele van deze beleids regels. Uit veiligheids overwegingen raden wij u aan om ATS niet uit te scha kelen.

Certificaten die gebruikmaken van SHA-1-hashes, hebben bekende beveiligings problemen. Voor de meeste moderne webbrowsers zijn certificaten met SHA-1-hashes niet toegestaan.

## <a name="captive-portals"></a>Portals

Een in bedrijf zijnde portal geeft een webpagina aan een gebruiker wanneer ze voor het eerst toegang hebben tot een Wi-Fi netwerk en nog geen toegang tot dat netwerk hebben gekregen. Het Internet verkeer wordt onderschept totdat de gebruiker voldoet aan de vereisten van de portal. Netwerk fouten omdat de gebruiker geen verbinding kan maken met netwerk bronnen, wordt verwacht totdat de gebruiker verbinding maakt via de portal.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Port alen](https://en.wikipedia.org/wiki/Captive_portal) en de nieuwe [ATS-functie (app Trans Port Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) van Apple.
