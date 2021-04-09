---
title: Snel reageren op beveiligde identiteiten met Azure Active Directory
description: Snel reageren op bedreigingen met Azure AD cloud-gebaseerde identiteiten
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c4301a61a79ab2351c18af0c76cccc3d07dd202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836679"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Snel op beveiligde identiteiten reageren met Azure AD

Het lijkt erop dat u uw werk nemers in de hedendaagse wereld kunt beveiligen, met name wanneer u snel moet reageren en tegelijkertijd toegang tot veel services moet bieden. Dit artikel is bedoeld om een beknopt overzicht te geven van alle acties die u moet uitvoeren, zodat u kunt bepalen welke volg orde de Azure AD-functies moeten implementeren op basis van het licentie type dat u hebt. Azure AD biedt veel functies en biedt veel beveiliging voor uw identiteiten, waardoor u kunt navigeren welke functie relevant is. Veel organisaties bevinden zich al in de Cloud of verplaatsen snel naar de Cloud. dit document is bedoeld om u snel services te laten implementeren, waarbij uw identiteiten worden beveiligd als primaire overweging. 

Elke tabel biedt een consistente beveiligings aanbeveling, waardoor beheerders-en gebruikers identiteiten kunnen worden beveiligd tegen de belangrijkste beveiligings aanvallen (inbreuk opnieuw afspelen, phishing en wachtwoord spray), terwijl de gebruikers ervaring wordt geminimaliseerd. 

Met de richt lijnen kunnen beheerders ook toegang tot SaaS-en on-premises toepassingen op een veilige en beveiligde manier configureren en van toepassing zijn op Cloud-of Hybrid (gesynchroniseerde) identiteiten en van toepassing zijn op gebruikers die extern of op kantoor werken.

Deze controle lijst helpt u bij het snel implementeren van kritieke aanbevolen acties om uw organisatie onmiddellijk te beschermen door te verklaren hoe u:

- Versterk uw referenties.
- Verminder de aanvals surface area.
- Het antwoord op bedreigingen automatiseren.
- Gebruik Cloud Intelligence.
- Self-service voor eind gebruikers inschakelen.

## <a name="prerequisites"></a>Vereisten

In deze hand leiding wordt ervan uitgegaan dat uw Cloud of hybride identiteiten al in azure AD zijn gevestigd. Voor hulp bij het kiezen van uw identiteits type raadpleegt u het artikel, [kiest u de juiste verificatie methode voor uw Azure Active Directory hybride identiteits oplossing](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>Samenvatting

Er zijn veel aspecten van een veilige identiteits infrastructuur, maar deze controle lijst is gericht op een veilige en veilige identiteits infrastructuur waarmee gebruikers op afstand kunnen werken. Het beveiligen van uw identiteit maakt gewoon deel uit van uw beveiligings verhaal, waarbij u ook gegevens, toepassingen en apparaten kunt beveiligen.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Richt lijnen voor Azure AD Free-, Office 365-of Microsoft 365-klanten.

Er zijn een aantal aanbevelingen die Azure AD Free, Office 365-of Microsoft 365 app-klanten moeten ondernemen om hun gebruikers identiteiten te beschermen, de volgende tabel is bedoeld om de belangrijkste acties voor de volgende licentie abonnementen te markeren:

- Office 365 (Office 365 E1, E3, E5, F1, a1, a3, A5)
- Microsoft 365 (Business Basic, apps voor bedrijven, Business Standard, Business Premium, a1)
- Azure AD Free (opgenomen in azure, Dynamics 365, intune en energie platform)

| Aanbevolen actie | Detail |
| --- | --- |
| [Standaard instellingen voor beveiliging inschakelen](concept-fundamentals-security-defaults.md) | Alle gebruikers identiteiten en toepassingen beveiligen door MFA in te scha kelen en verouderde verificatie te blok keren |
| [Wachtwoord-hash-synchronisatie inschakelen](../hybrid/how-to-connect-password-hash-synchronization.md) (als hybride identiteiten worden gebruikt) | Geef redundantie voor verificatie en verbeter de beveiliging (inclusief slimme vergren deling, IP-vergren deling en de mogelijkheid om gelekte referenties te detecteren). |
| [AD FS Smart lock-out inschakelen](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (indien van toepassing) | Hiermee wordt voor komen dat uw gebruikers een extranet account vergren delen van schadelijke activiteiten. |
| [Inschakelen van Azure Active Directory slim vergren delen](../authentication/howto-password-smart-lockout.md) (als beheerde identiteiten worden gebruikt) | Slimme vergren deling helpt bij het vergren delen van ongeldige Actors die de wacht woorden van uw gebruikers proberen te raden of methoden voor het afwijzen van brute kracht gebruiken om aan de slag te gaan. |
| [Toestemming voor eind gebruikers voor toepassingen uitschakelen](../manage-apps/configure-user-consent.md) | De beheerder toestemming werk stroom biedt beheerders een veilige manier om toegang te verlenen tot toepassingen waarvoor beheerders goed keuring is vereist, zodat eind gebruikers geen bedrijfs gegevens bloot stellen. Micro soft adviseert toekomstige acties voor de gebruikers toestemming uit te scha kelen om uw surface area te verminderen en dit risico te beperken. |
| [Ondersteunde SaaS-toepassingen in de galerie integreren in azure AD en eenmalige aanmelding inschakelen](../manage-apps/add-application-portal.md) | Azure AD bevat een galerie met duizenden vooraf geïntegreerde toepassingen. Sommige van de toepassingen die door uw organisatie worden gebruikt, zijn waarschijnlijk rechtstreeks vanuit de Azure Portal toegankelijk in de galerie. Externe en veilige toegang tot zakelijke SaaS-toepassingen bieden met verbeterde gebruikers ervaring (SSO) |
| [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-toepassingen automatiseren](../app-provisioning/user-provisioning.md) (indien van toepassing) | Automatisch gebruikers identiteiten en-rollen maken in de Cloud toepassingen (SaaS) waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikers identiteiten, omvat automatische inrichting het onderhoud en verwijderen van gebruikers identiteiten als status of rollen wijzigen, waardoor de beveiliging van uw organisatie wordt verhoogd. |
| [Beveiligde hybride toegang inschakelen: beveiligde verouderde apps met bestaande app-bezorgings controllers en-netwerken](../manage-apps/secure-hybrid-access.md) (indien van toepassing) | Publiceer en beveilig uw lokale en Cloud toepassingen voor verouderde verificatie door deze te verbinden met Azure AD met de bestaande controller of het netwerk van de bezorgings voorziening van de toepassing. |
| [Self-service voor wachtwoord herstel inschakelen](../authentication/tutorial-enable-sspr.md) (alleen van toepassing op accounts in de Cloud) | Deze mogelijkheid vermindert helpdesk oproepen en productiviteits verlies wanneer een gebruiker zich niet kan aanmelden bij hun apparaat of een toepassing. |
| [Indien mogelijk niet-globale beheerders rollen gebruiken](../roles/permissions-reference.md) | Geef uw beheerders alleen de toegang die ze nodig hebben tot de gebieden waartoe ze toegang moeten hebben. Niet alle beheerders moeten globale beheerders zijn. |
| [De richt lijnen voor het micro soft-wacht woord inschakelen](https://www.microsoft.com/research/publication/password-guidance/) | Stop het vereisen van gebruikers om hun wacht woord te wijzigen voor een set-schema, om complexiteits vereisten uit te scha kelen en uw gebruikers zijn meer apt om hun wacht woorden te onthouden en ze iets veilig te houden. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Richt lijnen voor klanten van Azure AD Premium plan 1.

De volgende tabel is bedoeld om de belangrijkste acties voor de volgende licentie abonnementen te markeren:

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, a3, F1, F3)

| Aanbevolen actie | Detail |
| --- | --- |
| [Gecombineerde registratie-ervaring inschakelen voor Azure AD MFA en SSPR om gebruikers registratie te vereenvoudigen](../authentication/howto-registration-mfa-sspr-combined.md) | Sta uw gebruikers toe om zich te registreren bij een algemene ervaring voor zowel Azure AD Multi-Factor Authentication als selfservice voor wachtwoord herstel. |
| [MFA-instellingen voor uw organisatie configureren](../authentication/howto-mfa-getstarted.md) | Controleren of accounts beveiligd zijn met multi-factor Authentication |
| [Selfservice voor wachtwoordherstel inschakelen](../authentication/tutorial-enable-sspr.md) | Deze mogelijkheid vermindert helpdesk oproepen en productiviteits verlies wanneer een gebruiker zich niet kan aanmelden bij het apparaat of een toepassing |
| Het [terugschrijven van wacht woorden implementeren](../authentication/tutorial-enable-sspr-writeback.md) (als hybride identiteiten worden gebruikt) | Toestaan dat wacht woorden worden gewijzigd in de cloud om terug te schrijven naar een on-premises Windows Server-Active Directory omgeving. |
| Beleid voor voorwaardelijke toegang maken en inschakelen | [MFA voor beheerders om accounts te beveiligen waaraan beheerders rechten zijn toegewezen.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Verouderde verificatie protocollen als gevolg van het verhoogde risico dat is gekoppeld aan verouderde verificatie protocollen.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [MFA voor alle gebruikers en toepassingen voor het maken van een evenwichtige MFA-beleid voor uw omgeving, het beveiligen van uw gebruikers en toepassingen.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Vereis MFA vereist voor Azure-beheer om uw geprivilegieerde resources te beschermen door multi-factor Authentication te vereisen voor elke gebruiker die toegang probeert te krijgen tot Azure-resources.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Wachtwoord-hash-synchronisatie inschakelen](../hybrid/how-to-connect-password-hash-synchronization.md) (als hybride identiteiten worden gebruikt) | Geef redundantie voor verificatie en verbeter de beveiliging (inclusief slimme vergren deling, IP-vergren deling en de mogelijkheid om gelekte referenties te detecteren). |
| [AD FS Smart lock-out inschakelen](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (indien van toepassing) | Hiermee wordt voor komen dat uw gebruikers een extranet account vergren delen van schadelijke activiteiten. |
| [Inschakelen van Azure Active Directory slim vergren delen](../authentication/howto-password-smart-lockout.md) (als beheerde identiteiten worden gebruikt) | Slimme vergren deling helpt bij het vergren delen van ongeldige Actors die de wacht woorden van uw gebruikers proberen te raden of methoden voor het afwijzen van brute kracht gebruiken om aan de slag te gaan. |
| [Toestemming voor eind gebruikers voor toepassingen uitschakelen](../manage-apps/configure-user-consent.md) | De beheerder toestemming werk stroom biedt beheerders een veilige manier om toegang te verlenen tot toepassingen waarvoor beheerders goed keuring is vereist, zodat eind gebruikers geen bedrijfs gegevens bloot stellen. Micro soft adviseert toekomstige acties voor de gebruikers toestemming uit te scha kelen om uw surface area te verminderen en dit risico te beperken. |
| [Externe toegang tot on-premises oudere toepassingen met toepassings proxy inschakelen](../manage-apps/application-proxy-add-on-premises-application.md) | Schakel Azure AD-toepassingsproxy in en integreer met verouderde apps voor gebruikers om veilig toegang te krijgen tot on-premises toepassingen door u aan te melden met hun Azure AD-account. |
| [Schakel beveiligde hybride toegang in: beveiligde verouderde apps met bestaande app-bezorgings controllers en netwerken](../manage-apps/secure-hybrid-access.md) (indien van toepassing). | Publiceer en beveilig uw lokale en Cloud toepassingen voor verouderde verificatie door deze te verbinden met Azure AD met de bestaande controller of het netwerk van de bezorgings voorziening van de toepassing. |
| [Ondersteunde SaaS-toepassingen in de galerie integreren in azure AD en eenmalige aanmelding inschakelen](../manage-apps/add-application-portal.md) | Azure AD bevat een galerie met duizenden vooraf geïntegreerde toepassingen. Sommige van de toepassingen die door uw organisatie worden gebruikt, zijn waarschijnlijk rechtstreeks vanuit de Azure Portal toegankelijk in de galerie. Bied externe en veilige toegang tot zakelijke SaaS-toepassingen met verbeterde gebruikers ervaring (SSO). |
| [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-toepassingen automatiseren](../app-provisioning/user-provisioning.md) (indien van toepassing) | Automatisch gebruikers identiteiten en-rollen maken in de Cloud toepassingen (SaaS) waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikers identiteiten, omvat automatische inrichting het onderhoud en verwijderen van gebruikers identiteiten als status of rollen wijzigen, waardoor de beveiliging van uw organisatie wordt verhoogd. |
| [Voorwaardelijke toegang inschakelen – op apparaat gebaseerd](../conditional-access/require-managed-devices.md) | Verbeter de beveiliging en gebruikers ervaringen met voorwaardelijke toegang op basis van een apparaat. Deze stap zorgt ervoor dat gebruikers alleen toegang hebben tot apparaten die voldoen aan uw normen voor beveiliging en naleving. Deze apparaten worden ook wel beheerde apparaten genoemd. Beheerde apparaten kunnen compatibel zijn met intune of aan hybride Azure AD gekoppelde apparaten. |
| [Wachtwoord beveiliging inschakelen](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Gebruikers beveiligen tegen het gebruik van zwakke en gemakkelijk te raden wacht woorden. |
| [Meer dan één globale beheerder aanwijzen](../roles/security-emergency-access.md) | Wijs ten minste twee permanente globale beheerders accounts toe die u kunt gebruiken als er sprake is van een nood geval. Deze accounts worden niet dagelijks gebruikt en moeten lange en complexe wacht woorden bevatten. Als u de accounts verbreekt, kunt u in een nood geval toegang krijgen tot de service. |
| [Indien mogelijk niet-globale beheerders rollen gebruiken](../roles/permissions-reference.md) | Geef uw beheerders alleen de toegang die ze nodig hebben tot de gebieden waartoe ze toegang moeten hebben. Niet alle beheerders moeten globale beheerders zijn. |
| [De richt lijnen voor het micro soft-wacht woord inschakelen](https://www.microsoft.com/research/publication/password-guidance/) | Stop het vereisen van gebruikers om hun wacht woord te wijzigen voor een set-schema, om complexiteits vereisten uit te scha kelen en uw gebruikers zijn meer apt om hun wacht woorden te onthouden en ze iets veilig te houden. |
| [Een plan maken voor toegang tot gast gebruikers](../external-identities/what-is-b2b.md) | Samen werken met gast gebruikers door hen te laten aanmelden bij uw apps en services met hun eigen werk, school of sociale identiteiten. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Richt lijnen voor klanten van Azure AD Premium plan 2.

De volgende tabel is bedoeld om de belangrijkste acties voor de volgende licentie abonnementen te markeren:

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, A5)

| Aanbevolen actie | Detail |
| --- | --- |
| [Gecombineerde registratie-ervaring inschakelen voor Azure AD MFA en SSPR om gebruikers registratie te vereenvoudigen](../authentication/howto-registration-mfa-sspr-combined.md) | Sta uw gebruikers toe om zich te registreren bij een algemene ervaring voor zowel Azure AD Multi-Factor Authentication als selfservice voor wachtwoord herstel. |
| [MFA-instellingen voor uw organisatie configureren](../authentication/howto-mfa-getstarted.md) | Controleren of accounts beveiligd zijn met multi-factor Authentication |
| [Selfservice voor wachtwoordherstel inschakelen](../authentication/tutorial-enable-sspr.md) | Deze mogelijkheid vermindert helpdesk oproepen en productiviteits verlies wanneer een gebruiker zich niet kan aanmelden bij het apparaat of een toepassing |
| Het [terugschrijven van wacht woorden implementeren](../authentication/tutorial-enable-sspr-writeback.md) (als hybride identiteiten worden gebruikt) | Toestaan dat wacht woorden worden gewijzigd in de cloud om terug te schrijven naar een on-premises Windows Server-Active Directory omgeving. |
| [Beleid voor identiteits beveiliging inschakelen voor het afdwingen van MFA-registratie](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | De implementatie van Azure AD Multi-Factor Authentication (MFA) beheren. |
| [Gebruikers-en aanmeldings risico beleid voor identiteits beveiliging inschakelen](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Gebruikers-en aanmeldings beleid voor identiteits beveiliging inschakelen. Het aanbevolen aanmeldings beleid is de aanmeldingen voor middel grote Risico's te richten en MFA verplicht te stellen. Voor gebruikers beleid moet het worden gericht op gebruikers met een hoog risico dat de actie voor het wijzigen van het wacht woord vereist. |
| Beleid voor voorwaardelijke toegang maken en inschakelen | [MFA voor beheerders om accounts te beveiligen waaraan beheerders rechten zijn toegewezen.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Verouderde verificatie protocollen als gevolg van het verhoogde risico dat is gekoppeld aan verouderde verificatie protocollen.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Vereis MFA vereist voor Azure-beheer om uw geprivilegieerde resources te beschermen door multi-factor Authentication te vereisen voor elke gebruiker die toegang probeert te krijgen tot Azure-resources.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Wachtwoord-hash-synchronisatie inschakelen](../hybrid/how-to-connect-password-hash-synchronization.md) (als hybride identiteiten worden gebruikt) | Geef redundantie voor verificatie en verbeter de beveiliging (inclusief slimme vergren deling, IP-vergren deling en de mogelijkheid om gelekte referenties te detecteren). |
| [AD FS Smart lock-out inschakelen](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (indien van toepassing) | Hiermee wordt voor komen dat uw gebruikers een extranet account vergren delen van schadelijke activiteiten. |
| [Inschakelen van Azure Active Directory slim vergren delen](../authentication/howto-password-smart-lockout.md) (als beheerde identiteiten worden gebruikt) | Slimme vergren deling helpt bij het vergren delen van ongeldige Actors die de wacht woorden van uw gebruikers proberen te raden of methoden voor het afwijzen van brute kracht gebruiken om aan de slag te gaan. |
| [Toestemming voor eind gebruikers voor toepassingen uitschakelen](../manage-apps/configure-user-consent.md) | De beheerder toestemming werk stroom biedt beheerders een veilige manier om toegang te verlenen tot toepassingen waarvoor beheerders goed keuring is vereist, zodat eind gebruikers geen bedrijfs gegevens bloot stellen. Micro soft adviseert toekomstige acties voor de gebruikers toestemming uit te scha kelen om uw surface area te verminderen en dit risico te beperken. |
| [Externe toegang tot on-premises oudere toepassingen met toepassings proxy inschakelen](../manage-apps/application-proxy-add-on-premises-application.md) | Schakel Azure AD-toepassingsproxy in en integreer met verouderde apps voor gebruikers om veilig toegang te krijgen tot on-premises toepassingen door u aan te melden met hun Azure AD-account. |
| [Schakel beveiligde hybride toegang in: beveiligde verouderde apps met bestaande app-bezorgings controllers en netwerken](../manage-apps/secure-hybrid-access.md) (indien van toepassing). | Publiceer en beveilig uw lokale en Cloud toepassingen voor verouderde verificatie door deze te verbinden met Azure AD met de bestaande controller of het netwerk van de bezorgings voorziening van de toepassing. |
| [Ondersteunde SaaS-toepassingen in de galerie integreren in azure AD en eenmalige aanmelding inschakelen](../manage-apps/add-application-portal.md) | Azure AD bevat een galerie met duizenden vooraf geïntegreerde toepassingen. Sommige van de toepassingen die door uw organisatie worden gebruikt, zijn waarschijnlijk rechtstreeks vanuit de Azure Portal toegankelijk in de galerie. Bied externe en veilige toegang tot zakelijke SaaS-toepassingen met verbeterde gebruikers ervaring (SSO). |
| [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-toepassingen automatiseren](../app-provisioning/user-provisioning.md) (indien van toepassing) | Automatisch gebruikers identiteiten en-rollen maken in de Cloud toepassingen (SaaS) waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikers identiteiten, omvat automatische inrichting het onderhoud en verwijderen van gebruikers identiteiten als status of rollen wijzigen, waardoor de beveiliging van uw organisatie wordt verhoogd. |
| [Voorwaardelijke toegang inschakelen – op apparaat gebaseerd](../conditional-access/require-managed-devices.md) | Verbeter de beveiliging en gebruikers ervaringen met voorwaardelijke toegang op basis van een apparaat. Deze stap zorgt ervoor dat gebruikers alleen toegang hebben tot apparaten die voldoen aan uw normen voor beveiliging en naleving. Deze apparaten worden ook wel beheerde apparaten genoemd. Beheerde apparaten kunnen compatibel zijn met intune of aan hybride Azure AD gekoppelde apparaten. |
| [Wachtwoord beveiliging inschakelen](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Gebruikers beveiligen tegen het gebruik van zwakke en gemakkelijk te raden wacht woorden. |
| [Meer dan één globale beheerder aanwijzen](../roles/security-emergency-access.md) | Wijs ten minste twee permanente globale beheerders accounts toe die u kunt gebruiken als er sprake is van een nood geval. Deze accounts worden niet dagelijks gebruikt en moeten lange en complexe wacht woorden bevatten. Als u de accounts verbreekt, kunt u in een nood geval toegang krijgen tot de service. |
| [Indien mogelijk niet-globale beheerders rollen gebruiken](../roles/permissions-reference.md) | Geef uw beheerders alleen de toegang die ze nodig hebben tot de gebieden waartoe ze toegang moeten hebben. Niet alle beheerders moeten globale beheerders zijn. |
| [De richt lijnen voor het micro soft-wacht woord inschakelen](https://www.microsoft.com/research/publication/password-guidance/) | Stop het vereisen van gebruikers om hun wacht woord te wijzigen voor een set-schema, om complexiteits vereisten uit te scha kelen en uw gebruikers zijn meer apt om hun wacht woorden te onthouden en ze iets veilig te houden. |
| [Een plan maken voor toegang tot gast gebruikers](../external-identities/what-is-b2b.md) | Samen werken met gast gebruikers door hen te laten aanmelden bij uw apps en services met hun eigen werk, school of sociale identiteiten. |
| [Privileged Identity Management inschakelen](../privileged-identity-management/pim-configure.md) | Hiermee kunt u toegang tot belang rijke resources in uw organisatie beheren, controleren en bewaken, zodat beheerders alleen toegang hebben wanneer ze nodig zijn en met goed keuring |

## <a name="next-steps"></a>Volgende stappen

- Voor gedetailleerde implementatie richtlijnen voor afzonderlijke functies van Azure AD raadpleegt u de [Azure AD-implementatie plannen](active-directory-deployment-plans.md)voor het project.

- Voor een end-to-end implementatie controlelijst voor Azure AD raadpleegt u het artikel [Azure Active Directory Deployment Guide van feature](active-directory-deployment-checklist-p2.md)