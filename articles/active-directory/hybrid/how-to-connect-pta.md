---
title: 'Azure AD Connect: Pass-Through-verificatie | Microsoft Docs'
description: In dit artikel wordt de Pass-Through-verificatie van Azure Active Directory (Azure AD) beschreven en wordt uitgelegd hoe Azure AD-aanmeldingen worden toegestaan door gebruikers wachtwoorden te valideren voor on-premises Active Directory.
services: active-directory
keywords: Wat is Azure AD Connect Pass-Through-verificatie, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8f613cb7c75d9dd6af1fcf62f9d484398072c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279462"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Gebruikersaanmelding met Pass Through-verificatie in Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Wat is Azure Active Directory Pass-Through-verificatie?

Met Pass-Through-verificatie van Azure Active Directory (Azure AD) kunnen uw gebruikers zich aanmelden bij zowel lokale als Cloud toepassingen met dezelfde wacht woorden. Deze functie biedt een betere gebruikerservaring (een wachtwoord minder te onthouden) en het vermindert de kosten voor de IT-helpdesk omdat gebruikers minder vaak een wachtwoord vergeten. Wanneer gebruikers zich aanmelden met Azure AD, valideert deze functie de wacht woorden van gebruikers rechtstreeks op uw on-premises Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Deze functie is een alternatief voor [Azure AD-wachtwoord hash-synchronisatie](how-to-connect-password-hash-synchronization.md), waarmee u hetzelfde voor deel van Cloud verificatie voor organisaties kunt bieden. Bepaalde organisaties die hun on-premises Active Directory beveiligings-en wachtwoord beleid willen afdwingen, kunnen er echter voor kiezen om in plaats daarvan Pass-Through-verificatie te gebruiken. Raadpleeg [deze hand leiding](./choose-ad-authn.md) voor een vergelijking van de verschillende aanmeldings methoden van Azure AD en het kiezen van de juiste aanmeldings methode voor uw organisatie.

![Pass-Through-verificatie van Azure AD](./media/how-to-connect-pta/pta1.png)

U kunt Pass-Through-verificatie combi neren met de functie [naadloze eenmalige aanmelding](how-to-connect-sso.md) . Op deze manier hoeft u niet in hun wacht woord in te voeren als uw gebruikers toegang hebben tot toepassingen op hun bedrijfs computers binnen uw bedrijfs netwerk.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Belangrijkste voor delen van het gebruik van Pass-Through-verificatie van Azure AD

- *Goede gebruikerservaring*
  - Gebruikers gebruiken dezelfde wacht woorden om zich aan te melden bij on-premises en Cloud toepassingen.
  - Gebruikers best Eden minder tijd aan de IT-Help Desk om problemen met wacht woorden op te lossen.
  - Gebruikers kunnen [self-service voor wachtwoord beheer](../authentication/concept-sspr-howitworks.md) uitvoeren in de Cloud.
- *Eenvoudig te implementeren & beheren*
  - Er zijn geen complexe on-premises implementaties of netwerk configuratie nodig.
  - Vereist alleen een licht gewicht agent voor on-premises installatie.
  - Geen beheer overhead. De agent ontvangt automatisch verbeteringen en oplossingen voor problemen.
- *Beveiligen*
  - On-premises wacht woorden worden nooit in een wille keurige vorm opgeslagen in de Cloud.
  - Beveiligt uw gebruikers accounts door naadloos samen te werken met [beleid voor voorwaardelijke toegang van Azure AD](../conditional-access/overview.md), inclusief multi-factor Authentication (MFA), het [blok keren van verouderde verificatie](../conditional-access/concept-conditional-access-conditions.md) en het filteren van aanvallen op basis van [Brute Force-wacht woorden](../authentication/howto-password-smart-lockout.md).
  - De agent maakt alleen uitgaande verbindingen vanuit uw netwerk. Daarom is er geen vereiste voor het installeren van de agent in een perimeter netwerk, ook wel bekend als een DMZ.
  - De communicatie tussen een agent en Azure AD is beveiligd met verificatie op basis van certificaten. Deze certificaten worden elke paar maanden automatisch door Azure AD verlengd.
- *Zeer beschikbaar*
  - Er kunnen extra agents op meerdere on-premises servers worden geïnstalleerd om te voorzien in hoge Beschik baarheid van aanmeldings aanvragen.

## <a name="feature-highlights"></a>Functie hoogtepunten

- Biedt ondersteuning voor aanmelding door gebruikers in alle webtoepassingen en Microsoft Office client toepassingen die [moderne verificatie](https://aka.ms/modernauthga)gebruiken.
- Gebruikers namen voor aanmelden kunnen de on-premises standaard gebruikersnaam ( `userPrincipalName` ) of een ander kenmerk zijn dat is geconfigureerd in azure AD Connect (ook wel bekend als `Alternate ID` ).
- De functie werkt probleemloos met functies voor [voorwaardelijke toegang](../conditional-access/overview.md) , zoals multi-factor Authentication (MFA), waarmee u uw gebruikers kunt beveiligen.
- Geïntegreerd met [wacht woord beheer](../authentication/concept-sspr-howitworks.md)op basis van de Cloud, inclusief het terugschrijven van wacht woorden naar on-premises Active Directory en wachtwoord beveiliging door gang bare verbieden.
- Omgevingen met meerdere forests worden ondersteund als er forest-vertrouwens relaties tussen uw AD-forests bestaan en als de route ring van het naam achtervoegsel correct is geconfigureerd.
- Het is een gratis functie en u hebt geen betaalde versies van Azure AD nodig om deze te gebruiken.
- Het kan worden ingeschakeld via [Azure AD Connect](whatis-hybrid-identity.md).
- Er wordt gebruikgemaakt van een Lightweight on-premises agent die luistert naar en reageert op aanvragen voor wachtwoord validatie.
- Het installeren van meerdere agents biedt een hoge Beschik baarheid van aanmeldings aanvragen.
- Uw on-premises accounts worden [beschermd](../authentication/howto-password-smart-lockout.md) tegen beveiligings aanvallen met een felle aanval in de Cloud.

## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids](how-to-connect-pta-quick-start.md) : Ontvang Azure AD Pass-Through-verificatie en voer deze uit.
- [Migratie van AD FS naar Pass-](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) through-verificatie: een gedetailleerde hand leiding voor het migreren van AD FS (of andere Federatie technologieën) naar Pass-Through-verificatie.
- [Slimme vergren deling](../authentication/howto-password-smart-lockout.md) : Configureer slimme vergrendelings mogelijkheden voor uw Tenant om gebruikers accounts te beveiligen.
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md) : informatie over welke scenario's worden ondersteund en wat niet.
- [Technisch diep gaande](how-to-connect-pta-how-it-works.md) kennis van de werking van deze functie.
- [Veelgestelde vragen](how-to-connect-pta-faq.md) : antwoorden op veelgestelde vragen.
- [Problemen oplossen](tshoot-connect-pass-through-authentication.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.
- [Uitgebreide](how-to-connect-pta-security-deep-dive.md) kennis van beveiliging: extra diep gaande technische informatie over de functie.
- [Azure AD naadloze SSO](how-to-connect-sso.md) -meer informatie over deze complementaire functie.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -voor het indienen van nieuwe functie aanvragen.