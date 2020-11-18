---
title: Controlelijst voor Azure AD-implementatie
description: Controle lijst voor implementatie van Azure Active Directory-onderdelen
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d1e5b8ac6492b0b1d819431e4616d32a092cc8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836917"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Implementatiehandleiding voor Azure Active Directory-functies

Het kan voor komen dat u Azure Active Directory (Azure AD) implementeert voor uw organisatie en deze veilig kunt houden. In dit artikel worden algemene taken geïdentificeerd die klanten nuttig vinden om in fasen te worden voltooid, in de loop van 30, 60, 90 dagen of meer om hun beveiligings postuur te verbeteren. Zelfs organisaties die Azure AD al hebben geïmplementeerd, kunnen deze hand leiding gebruiken om ervoor te zorgen dat ze het meest uit hun investering halen.

Een goed geplande en uitgevoerde identiteits infrastructuur wordt de manier om de toegang tot uw productiviteits werkbelastingen en gegevens te beveiligen door alleen bekende gebruikers en apparaten.

Daarnaast kunnen klanten hun [identiteits veilige Score](identity-secure-score.md) controleren om te zien hoe ze zijn uitgelijnd op best practices van micro soft. Controleer uw beveiligde score voor en na de implementatie van deze aanbevelingen om te zien hoe goed u presteert in vergelijking met anderen in uw branche en tot andere organisaties van uw omvang.

## <a name="prerequisites"></a>Vereisten

Veel van de aanbevelingen in deze hand leiding kunnen worden geïmplementeerd met Azure AD Free of helemaal geen licentie. Als u licenties nodig hebt, moet u de licentie voor het uitvoeren van de taak aangeven.

Meer informatie over licentie verlening vindt u op de volgende pagina's:

* [Azure AD-licenties](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Zakelijk](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Prijzen voor externe Azure AD-identiteiten](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: bouw een basis van beveiliging

In deze fase scha kelen beheerders basis beveiligings functies in om een beter beveiligde en gebruiks vriendelijke basis te maken in azure AD voordat we normale gebruikers accounts importeren of maken. Deze kern fase zorgt ervoor dat u een veiligere status hebt van het begin en dat uw eind gebruikers alleen maar één keer aan nieuwe concepten moeten worden toegevoegd.

| Taak | Detail | Vereiste licentie |
| ---- | ------ | ---------------- |
| [Meer dan één globale beheerder aanwijzen](../roles/security-emergency-access.md) | Wijs ten minste twee permanente globale beheerders accounts toe die u kunt gebruiken als er sprake is van een nood geval. Deze accounts worden niet dagelijks gebruikt en moeten lange en complexe wacht woorden bevatten. | Azure AD Free |
| [Indien mogelijk niet-globale beheerders rollen gebruiken](../roles/permissions-reference.md) | Geef uw beheerders alleen de toegang die ze nodig hebben tot de gebieden waartoe ze toegang moeten hebben. Niet alle beheerders moeten globale beheerders zijn. | Azure AD Free |
| [Gebruik van Privileged Identity Management voor het bijhouden van rol van beheerdersrol inschakelen](../privileged-identity-management/pim-getting-started.md) | Schakel Privileged Identity Management in om het gebruik van de beheerdersrol te volgen. | Azure AD Premium P2 |
| [Selfservice voor wachtwoordherstel implementeren](../authentication/howto-sspr-deployment.md) | Verminder de helpdesk oproepen voor wacht woord opnieuw instellen door personeel toe te staan hun eigen wacht woord opnieuw in te stellen met behulp van beleids regels die u als beheerder beheert. | |
| [Een specifieke aangepaste lijst met geblokkeerde wacht woorden maken](../authentication/tutorial-configure-custom-password-protection.md) | Voor komen dat gebruikers wacht woorden maken die algemene woorden of zinsdelen bevatten van uw organisatie of gebied. | |
| [On-premises integratie met Azure AD-wachtwoord beveiliging inschakelen](../authentication/concept-password-ban-bad-on-premises.md) | Breid de lijst met geblokkeerde wacht woorden uit naar uw on-premises Directory om ervoor te zorgen dat wacht woorden die on-premises zijn ingesteld ook voldoen aan de algemene en Tenant-specifieke lijst met verboden wacht woorden. | Azure AD Premium P1 |
| [De richt lijnen voor het micro soft-wacht woord inschakelen](https://www.microsoft.com/research/publication/password-guidance/) | Stop het vereisen van gebruikers om hun wacht woord te wijzigen voor een set-schema, om complexiteits vereisten uit te scha kelen en uw gebruikers zijn meer apt om hun wacht woorden te onthouden en ze iets veilig te houden. | Azure AD Free |
| [Periodiek wacht woord opnieuw instellen uitschakelen voor gebruikers accounts op basis van de Cloud](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Periodiek wacht woord opnieuw instellen moedig uw gebruikers aan om hun bestaande wacht woord te verhogen. Gebruik de richt lijnen in het document met wachtwoord richtlijnen van micro soft om uw on-premises beleid te spie gelen aan alleen-Cloud gebruikers. | Azure AD Free |
| [Azure Active Directory slim vergren delen aanpassen](../authentication/howto-password-smart-lockout.md) | Voor komen dat de vergren delingen van Cloud gebruikers worden gerepliceerd naar on-premises Active Directory gebruikers | |
| [Extranet smartcard vergrendeling inschakelen voor AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS extranet vergrendeling beschermt tegen beveiligings aanvallen met een felle wacht woord, terwijl geldige AD FS gebruikers hun accounts blijven gebruiken. | |
| [Verouderde verificatie naar Azure AD blok keren met voorwaardelijke toegang](../conditional-access/block-legacy-authentication.md) | Verouderde verificatie protocollen, zoals POP, SMTP, IMAP en MAPI, die Multi-Factor Authentication niet kunnen afdwingen, waardoor ze een voorkeurs toegangs punt voor aanvallers zijn. | Azure AD Premium P1 |
| [Azure AD-Multi-Factor Authentication implementeren met behulp van beleid voor voorwaardelijke toegang](../authentication/howto-mfa-getstarted.md) | Gebruikers verplichten verificatie in twee stappen uit te voeren bij het openen van gevoelige toepassingen die gebruikmaken van het beleid voor voorwaardelijke toegang. | Azure AD Premium P1 |
| [Azure Active Directory Identity Protection inschakelen](../identity-protection/overview-identity-protection.md) | Tracering inschakelen van Risk ante aanmeldingen en aangetaste referenties voor gebruikers in uw organisatie. | Azure AD Premium P2 |
| [Risico detecties gebruiken om multi-factor Authentication-en wachtwoord wijzigingen te activeren](../authentication/tutorial-risk-based-sspr-mfa.md) | Schakel automatisering in die gebeurtenissen zoals multi-factor Authentication, het opnieuw instellen van wacht woorden en het blok keren van aanmeldingen kan activeren op basis van risico. | Azure AD Premium P2 |
| [Gecombineerde registratie inschakelen voor Self-service voor wachtwoord herstel en Azure AD Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) | Sta uw gebruikers toe om zich te registreren bij een algemene ervaring voor zowel Azure AD Multi-Factor Authentication als selfservice voor wachtwoord herstel. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: gebruikers importeren, synchronisatie inschakelen en apparaten beheren

We voegen vervolgens toe aan de basis die in fase 1 is vastgelegd door de gebruikers te importeren en synchronisatie in te scha kelen, te plannen voor gast toegang en de ondersteuning van aanvullende functionaliteit voor te bereiden.

| Taak | Detail | Vereiste licentie |
| ---- | ------ | ---------------- |
| [Azure AD Connect installeren](../hybrid/how-to-connect-install-select-installation.md) | Bereid u voor op het synchroniseren van gebruikers van uw bestaande on-premises Directory naar de Cloud. | Azure AD Free |
| [Wachtwoord-hash-synchronisatie implementeren](../hybrid/how-to-connect-password-hash-synchronization.md) | Synchroniseer wachtwoord hashes zodat wachtwoord wijzigingen kunnen worden gerepliceerd, ongeldige wachtwoord detectie en herstel en gelekte referentie rapportage. | Azure AD Free |
| [Wacht woord terugschrijven implementeren](../authentication/tutorial-enable-sspr-writeback.md) | Toestaan dat wacht woorden worden gewijzigd in de cloud om terug te schrijven naar een on-premises Windows Server-Active Directory omgeving. | Azure AD Premium P1 |
| [Azure AD Connect Health implementeren](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Schakel de bewaking in van de belangrijkste status statistieken voor uw Azure AD Connect servers, AD FS servers en domein controllers. | Azure AD Premium P1 |
| [Licenties toewijzen aan gebruikers per groepslid maatschap in Azure Active Directory](../enterprise-users/licensing-groups-assign.md) | Bespaar tijd en moeite door licentie groepen te maken die functies in-of uitschakelen in plaats van per gebruiker in te stellen. | |
| [Een plan maken voor toegang tot gast gebruikers](../external-identities/what-is-b2b.md) | Samen werken met gast gebruikers door hen aan te melden bij uw apps en services met hun eigen werk, school of sociale identiteiten. | [Prijzen voor externe Azure AD-identiteiten](../external-identities/external-identities-pricing.md) |
| [Bepaal de strategie voor het beheer van apparaten](../devices/overview.md) | Bepaal wat uw organisatie in staat is met betrekking tot apparaten. Registreren versus samen voegen, uw eigen apparaat en bedrijf meenemen. | |
| [Windows hello voor bedrijven in uw organisatie implementeren](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Voor bereiding voor verificatie met een wacht woord met behulp van Windows hello | |
| [Authenticatie methoden met een wacht woord implementeren voor uw gebruikers](../authentication/concept-authentication-passwordless.md) | Geef uw gebruikers handige verificatie methoden met een wacht woord | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fase 3: toepassingen beheren

Omdat we de vorige fasen blijven ontwikkelen, identificeren we kandidaat-toepassingen voor migratie en integratie met Azure AD en volt ooien we de installatie van deze toepassingen.

| Taak | Detail | Vereiste licentie |
| ---- | ------ | ---------------- |
| Uw toepassingen identificeren | Bepaal welke toepassingen in uw organisatie worden gebruikt: on-premises, SaaS-toepassingen in de Cloud en andere line-of-business-toepassingen. Bepaal of deze toepassingen kunnen en moeten worden beheerd met Azure AD. | Geen licentie vereist |
| [Ondersteunde SaaS-toepassingen integreren in de galerie](../manage-apps/add-application-portal.md) | Azure AD bevat een galerie met duizenden vooraf geïntegreerde toepassingen. Sommige van de toepassingen die door uw organisatie worden gebruikt, zijn waarschijnlijk rechtstreeks vanuit de Azure Portal toegankelijk in de galerie. | Azure AD Free |
| [Toepassings proxy gebruiken voor het integreren van on-premises toepassingen](../manage-apps/application-proxy-add-on-premises-application.md) | Met toepassings proxy kunnen gebruikers toegang krijgen tot on-premises toepassingen door zich aan te melden met hun Azure AD-account. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: bevoegde identiteiten controleren, een toegangs beoordeling volt ooien en de gebruikers levenscyclus beheren

Fase 4 ziet beheerders de mogelijkheid om minimale bevoegdheids principes voor beheer af te dwingen, hun eerste toegangs beoordelingen te volt ooien en automatisering van algemene gebruikers levenscyclus taken in te scha kelen.

| Taak | Detail | Vereiste licentie |
| ---- | ------ | ---------------- |
| [Het gebruik van Privileged Identity Management afdwingen](../privileged-identity-management/pim-security-wizard.md) | Beheer rollen verwijderen van normale dag-naar-dag-gebruikers accounts. Zorg ervoor dat gebruikers met beheerders rechten hun rol kunnen gebruiken nadat ze een multi-factor Authentication-controle hebben uitgevoerd, een zakelijke reden bieden of goed keuring aanvragen van aangewezen goed keurders. | Azure AD Premium P2 |
| [Een toegangs beoordeling voor Azure AD-Directory functies in PIM volt ooien](../privileged-identity-management/pim-how-to-start-security-review.md) | Werk samen met uw beveiligings-en leiderschaps teams om een toegangs beoordelings beleid te maken om beheerders toegang te controleren op basis van het beleid van uw organisatie. | Azure AD Premium P2 |
| [Beleid voor dynamische groepslid maatschappen implementeren](../enterprise-users/groups-dynamic-membership.md) | Gebruik dynamische groepen om automatisch gebruikers toe te wijzen aan groepen op basis van hun kenmerken van HR (of uw bron van waarheid), zoals afdeling, titel, regio en andere kenmerken. |  |
| [Implementatie op basis van groepen implementeren](../manage-apps/what-is-access-management.md) | Gebruik het inrichten van toegangs beheer op basis van groepen om automatisch gebruikers in te richten voor SaaS-toepassingen. |  |
| [Gebruikers inrichting en het ongedaan maken van de inrichting automatiseren](../app-provisioning/user-provisioning.md) | Verwijder hand matige stappen uit de levens duur van uw werknemers account om onbevoegde toegang te voor komen. Identiteiten van uw bron van waarheid (HR-systeem) synchroniseren met Azure AD. |  |

## <a name="next-steps"></a>Volgende stappen

[Azure AD-licentie verlening en prijs informatie](https://azure.microsoft.com/pricing/details/active-directory/)

[Configuraties voor identiteiten en apparaattoegang](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Algemeen aanbevolen identiteits-en toegangs beleid voor apparaten](/microsoft-365/enterprise/identity-access-policies)