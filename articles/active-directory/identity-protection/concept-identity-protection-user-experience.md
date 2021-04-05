---
title: Gebruikers ervaring met Azure AD Identity Protection
description: Gebruikers ervaring van Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835982"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Gebruikers ervaring met Azure AD Identity Protection

Met Azure Active Directory Identity Protection kunt u het volgende doen:

* Vereisen dat gebruikers zich registreren voor Azure AD Multi-Factor Authentication (MFA)
* Automatisch herstel van Risk ante aanmeldingen en verkraakte gebruikers automatiseren

Alle beleids regels voor identiteits beveiliging hebben gevolgen voor de aanmeldings ervaring voor gebruikers. Gebruikers toestaan zich te registreren voor en gebruik hulpprogram ma's zoals Azure AD MFA en self-service voor het opnieuw instellen van wacht woorden kan de impact beperken. Deze hulpprogram ma's en de juiste beleids opties bieden gebruikers een optie voor automatisch herstel wanneer ze deze nodig hebben.

## <a name="multi-factor-authentication-registration"></a>Multi-factor Authentication-registratie

Het inschakelen van het beleid voor identiteits beveiliging waarbij multi-factor Authentication-registratie vereist is en die gericht is op al uw gebruikers, zorgt ervoor dat ze de mogelijkheid hebben om Azure AD MFA te gebruiken om zichzelf te herstellen in de toekomst. Door dit beleid te configureren, hebben uw gebruikers een periode van 14 dagen waarin ze zich kunnen registreren en aan het eind moeten registreren. De ervaring voor gebruikers wordt hieronder beschreven. Meer informatie vindt u in de documentatie voor eind gebruikers in het artikel [overzicht van twee ledige verificatie en uw werk-of school account](../user-help/multi-factor-authentication-end-user-first-time.md).

### <a name="registration-interrupt"></a>Registratie onderbreking

1. Bij het aanmelden bij een met Azure AD geïntegreerde toepassing krijgt de gebruiker een melding over de vereiste om het account in te stellen voor multi-factor Authentication. Dit beleid wordt ook geactiveerd in de Windows 10 out-box-ervaring voor nieuwe gebruikers met een nieuw apparaat.
   
    ![Meer informatie vereist](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Voltooi de begeleide stappen om u aan te melden voor Azure AD Multi-Factor Authentication en uw aanmelding te volt ooien.

## <a name="risky-sign-in-remediation"></a>Risk ante aanmeldingen bij herstel

Wanneer een beheerder een beleid heeft geconfigureerd voor aanmeldings Risico's, worden de betrokken gebruikers hiervan op de hoogte gesteld wanneer ze zich proberen aan te melden en het risico niveau van het beleid te activeren. 

### <a name="risky-sign-in-self-remediation"></a>Risk ante aanmelding zelf herstel

1. De gebruiker wordt op de hoogte gesteld dat er iets ongebruikelijk is gedetecteerd over hun aanmelding, zoals het aanmelden vanaf een nieuwe locatie, apparaat of app.
   
    ![Een ongewone prompt](./media/concept-identity-protection-user-experience/120.png)

1. De gebruiker is verplicht hun identiteit te bewijzen door Azure AD MFA met een van de eerder geregistreerde methoden te volt ooien. 

### <a name="risky-sign-in-administrator-unblock"></a>Risk ante beheerder voor aanmeldings blokkering

Beheerders kunnen kiezen om gebruikers te blok keren bij het aanmelden, afhankelijk van hun risico niveau. Als u de blok kering wilt opheffen, moeten eind gebruikers contact opnemen met hun IT-mede werkers of kunnen ze zich aanmelden vanaf een bekende locatie of apparaat. Automatisch door voeren door multi-factor Authentication uit te voeren is in dit geval geen optie.

![Geblokkeerd door beleid voor aanmeldings Risico's](./media/concept-identity-protection-user-experience/200.png)

IT-mede werkers kunnen de instructies in de sectie de [blok kering van gebruikers opheffen](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) zodat gebruikers zich weer kunnen aanmelden.

## <a name="risky-user-remediation"></a>Risk ante gebruikers herstel

Wanneer een gebruikers risico beleid is geconfigureerd, moeten gebruikers die voldoen aan de kans op risico niveau van inbreuk de gebruiker door de herstel stroom van het gebruikers recht door lopen voordat ze zich kunnen aanmelden. 

### <a name="risky-user-self-remediation"></a>Door de gebruiker zelf herstelde gebruikers met een Risk ante oplossing

1. De gebruiker wordt op de hoogte gesteld dat hun account beveiliging risico loopt vanwege verdachte activiteiten of gelekte referenties.
   
    ![Herstel](./media/concept-identity-protection-user-experience/101.png)

1. De gebruiker is verplicht hun identiteit te bewijzen door Azure AD MFA met een van de eerder geregistreerde methoden te volt ooien. 
1. Ten slotte wordt de gebruiker gedwongen hun wacht woord te wijzigen met selfservice voor wachtwoord herstel, omdat iemand anders mogelijk toegang heeft gehad tot hun account.

## <a name="risky-sign-in-administrator-unblock"></a>Risk ante beheerder voor aanmeldings blokkering

Beheerders kunnen kiezen om gebruikers te blok keren bij het aanmelden, afhankelijk van hun risico niveau. Als u de blok kering wilt opheffen, moeten eind gebruikers contact opnemen met hun IT-mede werkers. Zelf herstel door multi-factor Authentication uit te voeren en self-service voor het opnieuw instellen van wacht woorden is in dit geval geen optie.

![Geblokkeerd door beleid voor gebruikers Risico's](./media/concept-identity-protection-user-experience/104.png)

IT-mede werkers kunnen de instructies in de sectie de [blok kering van gebruikers opheffen](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) zodat gebruikers zich weer kunnen aanmelden.

## <a name="see-also"></a>Zie ook

- [Risico's herstellen en gebruikers deblokkeren](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)