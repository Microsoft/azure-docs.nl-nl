---
title: Azure AD-Multi-Factor Authentication voor uw organisatie-Azure Active Directory
description: Meer informatie over de beschik bare functies van Azure AD Multi-Factor Authentication voor uw organisatie op basis van uw licentie model
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: daveba
author: daveba
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e7f02f8cbda6218396bf4a9a4654a113b7817c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836739"
---
# <a name="overview-of-azure-ad-multi-factor-authentication-for-your-organization"></a>Overzicht van Azure AD-Multi-Factor Authentication voor uw organisatie

Er zijn meerdere manieren om Azure AD Multi-Factor Authentication in te scha kelen voor uw Azure Active Directory (AD)-gebruikers op basis van de licenties die uw organisatie bezit. 

![Signalen onderzoeken en MFA afdwingen, indien nodig](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Op basis van onze studies is uw account meer dan 99,9% minder risico als u gebruikmaakt van multi-factor Authentication (MFA).

Hoe schakelt uw organisatie MFA zelfs gratis in voordat u een statistiek krijgt?

## <a name="free-option"></a>Gratis optie

Klanten die gebruikmaken van de gratis voor delen van Azure AD, kunnen gebruikmaken van de [standaard instellingen](../fundamentals/concept-fundamentals-security-defaults.md) om multi-factor Authentication in hun omgeving in te scha kelen.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 of E5

Voor klanten met Microsoft 365 zijn er twee opties:

* Azure AD Multi-Factor Authentication is ingeschakeld of uitgeschakeld voor alle gebruikers, voor alle aanmeldings gebeurtenissen. Het is niet mogelijk om multi-factor Authentication in te scha kelen voor een subset van gebruikers of alleen onder bepaalde scenario's. Beheer gaat via de Office 365-Portal. 
* Voor een betere gebruikers ervaring kunt u een upgrade uitvoeren naar Azure AD Premium P1 of P2 en voorwaardelijke toegang gebruiken. Zie voor meer informatie Secure Microsoft 365 resources with multi-factor Authentication (Engelstalig).

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Voor klanten met Azure AD Premium P1 of soort gelijke licenties die deze functionaliteit bevatten, zoals Enterprise Mobility + Security E3, Microsoft 365 F1 of Microsoft 365 E3: 

Gebruik [voorwaardelijke toegang van Azure AD](../authentication/tutorial-enable-azure-mfa.md) om gebruikers te vragen om multi-factor Authentication tijdens bepaalde scenario's of gebeurtenissen die aan uw bedrijfs vereisten voldoen.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Voor klanten met Azure AD Premium P2 of vergelijk bare licenties die deze functionaliteit bevatten, zoals Enterprise Mobility + Security E5 of Microsoft 365 E5: 

Biedt de sterkste beveiligings positie en verbeterde gebruikers ervaring. Voegt [op Risico's gebaseerde voorwaardelijke toegang](../conditional-access/howto-conditional-access-policy-risk.md) toe aan de Azure AD Premium P1-functies die worden aangepast aan de patronen van gebruikers en minimaliseert multi-factor Authentication-prompts.

## <a name="authentication-methods"></a>Verificatiemethoden

| Methode | Standaardinstellingen voor de beveiliging | Alle andere methoden |
| --- | --- | --- |
| Melding via mobiele app | X | X |
| Verificatie code van de mobiele app of het hardware-token |   | X |
| SMS-bericht naar telefoon |   | X |
| Bellen naar telefoon |   | X |

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, raadpleegt u de zelf studie voor het [beveiligen van aanmeldings gebeurtenissen van gebruikers met Azure AD multi-factor Authentication](../authentication/tutorial-enable-azure-mfa.md).

Zie voor meer informatie over licenties de [functies en licenties voor Azure AD multi-factor Authentication](../authentication/concept-mfa-licensing.md).
