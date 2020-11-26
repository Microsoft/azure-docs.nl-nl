---
title: Een beleid voor voorwaardelijke toegang bouwen-Azure Active Directory
description: Wat zijn de beschik bare opties voor het bouwen van een beleid voor voorwaardelijke toegang en wat betekenen ze?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d58c476a805b672a6ec8b4d8ec465eba17e559
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169678"
---
# <a name="building-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang bouwen

Zoals beschreven in het artikel [Wat is voorwaardelijke toegang](overview.md), is een beleid voor voorwaardelijke toegang een instructie if-then, van **toewijzingen** en **toegangs beheer**. Een beleid voor voorwaardelijke toegang brengt signalen samen, om beslissingen te nemen en organisatie beleid af te dwingen.

Hoe maakt een organisatie dit beleid? Wat is vereist? Hoe worden deze toegepast?

![Voorwaardelijke toegang (signalen + beslissingen + afdwinging = beleid)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

Er kunnen op elk moment meerdere beleids regels voor voorwaardelijke toegang van toepassing zijn op een afzonderlijke gebruiker. In dit geval moet aan alle beleids regels worden voldaan. Als voor het ene beleid bijvoorbeeld multi-factor Authentication (MFA) is vereist en er voor een andere beleids regel een compatibel apparaat is vereist, moet u MFA volt ooien en een compatibel apparaat gebruiken. Alle toewijzingen zijn logisch **ANDed**. Als u meer dan één toewijzing hebt geconfigureerd, moeten aan alle toewijzingen worden voldaan om een beleid te activeren.

Alle beleids regels worden in twee fasen afgedwongen:

- Fase 1: sessie details verzamelen 
   - Details van de sessie verzamelen, zoals de netwerk locatie en de apparaat-id die nodig is voor de beleids evaluatie. 
   - Fase 1 van de beleids evaluatie vindt plaats voor ingeschakelde beleids regels en beleids regels in de [modus alleen rapport](concept-conditional-access-report-only.md).
- Fase 2: afdwingen 
   - Gebruik de sessie details die in fase 1 zijn verzameld om te bepalen of er niet is voldaan aan de vereisten. 
   - Als er een beleid is geconfigureerd voor het blok keren van de toegang, wordt afdwinging door de blok kering van het besturings element geblokkeerd en wordt de gebruiker geblokkeerd. 
   - De gebruiker wordt gevraagd aanvullende vereisten voor de toekennings controle uit te voeren die tijdens fase 1 in de volgende volg orde niet aan het beleid zijn voldaan:  
      - Multi-Factor Authentication 
      - Goedgekeurde client-app/beveiligings beleid voor apps 
      - Beheerd apparaat (compatibel of hybride Azure AD-deelname) 
      - Gebruiksvoorwaarden 
      - Aangepaste besturingselementen  
   - Als aan alle machtigings besturings elementen is voldaan, kunt u de sessie besturings elementen Toep assen (app afgedwongen, Microsoft Cloud App Security en levens duur van het token) 
   - Fase 2 van de beleids evaluatie wordt uitgevoerd voor alle ingeschakelde beleids regels. 

## <a name="assignments"></a>Toewijzingen

In het gedeelte toewijzingen bepaalt u de wie, wat en waar van het beleid voor voorwaardelijke toegang.

### <a name="users-and-groups"></a>Gebruikers en groepen

[Gebruikers en groepen](concept-conditional-access-users-groups.md) geven aan wie het beleid moet worden opgenomen of uitgesloten. Deze toewijzing kan alle gebruikers, specifieke groepen gebruikers, Directory rollen of externe gast gebruikers bevatten. 

### <a name="cloud-apps-or-actions"></a>Cloud-apps of acties

[Cloud-apps of-acties](concept-conditional-access-cloud-apps.md) kunnen Cloud toepassingen of gebruikers acties die voor het beleid gelden, opnemen of uitsluiten.

### <a name="conditions"></a>Voorwaarden

Een beleid kan meerdere [voor waarden](concept-conditional-access-conditions.md)bevatten.

#### <a name="sign-in-risk"></a>Aanmeldingsrisico

Voor organisaties met [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)kunnen de gegenereerde risico detecties van invloed zijn op uw beleid voor voorwaardelijke toegang.

#### <a name="device-platforms"></a>Apparaatplatformen

Organisaties met meerdere besturingssysteem platforms kunnen specifieke beleids regels afdwingen op verschillende platformen. 

De gegevens die worden gebruikt om het platform te berekenen, zijn afkomstig van niet-geverifieerde bronnen, zoals teken reeksen van gebruikers agenten die kunnen worden gewijzigd.

#### <a name="locations"></a>Locaties

Locatie gegevens worden verschaft door gegevens over de geolocatie van het IP-adres. Beheerders kunnen kiezen om locaties te definiëren en ervoor te kiezen om ze te markeren als vertrouwd, zoals die voor de netwerk locaties van hun organisatie.

#### <a name="client-apps"></a>Client-apps

Beleid voor voorwaardelijke toegang is standaard van toepassing op browser-apps, mobiele apps en desktop-clients die ondersteuning bieden voor moderne verificatie. 

Deze toewijzings voorwaarde staat beleids regels voor voorwaardelijke toegang toe om specifieke client toepassingen te richten die niet gebruikmaken van moderne verificatie. Deze toepassingen zijn onder andere Exchange ActiveSync-clients, oudere Office-toepassingen die geen gebruik maken van moderne verificatie en e-mail protocollen zoals IMAP, MAPI, POP en SMTP.

#### <a name="device-state"></a>Apparaatstatus

Dit besturings element wordt gebruikt om apparaten uit te sluiten die zijn toegevoegd aan hybride Azure AD of die compatibel zijn met intune. Deze uitsluiting kan worden uitgevoerd om onbeheerde apparaten te blok keren. 

## <a name="access-controls"></a>Besturingselementen voor toegang

In het gedeelte toegangs beheer van het beleid voor voorwaardelijke toegang wordt bepaald hoe een beleid wordt afgedwongen.

### <a name="grant"></a>Verlenen

[Grant](concept-conditional-access-grant.md) biedt beheerders een methode voor het afdwingen van beleid waarbij ze toegang kunnen blok keren of verlenen.

#### <a name="block-access"></a>Toegang blokkeren

Toegang blok keren alleen dat houdt in dat de toegang onder de opgegeven toewijzingen wordt geblokkeerd. De blok kering van het besturings element is krachtig en moet worden beheerd met de juiste kennis.

#### <a name="grant-access"></a>Toegang verlenen

De granting Control kan de afdwinging van een of meer besturings elementen activeren. 

- Multi-factor Authentication (Azure AD Multi-Factor Authentication) vereisen
- Vereisen dat het apparaat wordt gemarkeerd als compatibel (intune)
- Hybride Azure AD-gekoppeld apparaat is vereist
- Goedgekeurde client-apps vereisen
- Beleid voor app-beveiliging vereisen

Beheerders kunnen ervoor kiezen om een van de vorige besturings elementen of alle geselecteerde besturings elementen te vereisen met behulp van de volgende opties. De standaard instelling voor meerdere besturings elementen is alle nood zakelijk.

- Alle geselecteerde besturings elementen vereisen (besturings element en besturings element)
- Een van de geselecteerde besturings elementen vereisen (besturings element of besturings element)

### <a name="session"></a>Sessie

[Sessie besturings elementen](concept-conditional-access-session.md) kunnen de ervaring beperken 

- Door apps afgedwongen beperkingen gebruiken
   - Werkt momenteel alleen met Exchange Online en share point online.
      - Hiermee worden apparaatgegevens door gegeven om het beheer van de ervaring met volledige of beperkte toegang toe te staan.
- App-beheer voor voorwaardelijke toegang gebruiken
   - Gebruikt signalen van Microsoft Cloud App Security om dingen als: 
      - Het downloaden, knippen, kopiëren en afdrukken van gevoelige documenten blok keren.
      - Het gedrag van Risk ante sessies bewaken.
      - Labels van gevoelige bestanden vereisen.
- Aanmeldings frequentie
   - De mogelijkheid om de standaard aanmeldings frequentie voor moderne verificatie te wijzigen.
- Permanente browser sessie
   - Hiermee kunnen gebruikers zich blijven aanmelden nadat ze het browser venster hebben gesloten en opnieuw hebben geopend.

## <a name="simple-policies"></a>Eenvoudig beleid

Een beleid voor voorwaardelijke toegang moet mini maal het volgende bevatten om af te dwingen:

- De **naam** van het beleid.
- **Toewijzingen**
   - **Gebruikers en/of groepen** waarop het beleid moet worden toegepast.
   - **Cloud-apps of-acties** waarop het beleid moet worden toegepast.
- **Besturingselementen voor toegang**
   - Besturings elementen **toekennen** of **blok keren**

![Leeg beleid voor voorwaardelijke toegang](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Het artikel [common Conditional Access policies](concept-conditional-access-policy-common.md) bevat enkele beleids regels die van belang zijn voor de meeste organisaties.

## <a name="next-steps"></a>Volgende stappen

[Beleid voor voorwaardelijke toegang maken](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json#create-a-conditional-access-policy)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Een implementatie van Azure AD Multi-Factor Authentication in de Cloud plannen](../authentication/howto-mfa-getstarted.md)

[Apparaatcompatibiliteit beheren met intune](/intune/device-compliance-get-started)

[Microsoft Cloud App Security en voorwaardelijke toegang](/cloud-app-security/proxy-intro-aad)