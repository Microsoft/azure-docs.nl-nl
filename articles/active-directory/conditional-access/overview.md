---
title: Wat is voorwaardelijke toegang in Azure Active Directory?
description: Meer informatie over hoe voorwaardelijke toegang de kern vormt van het nieuwe besturingsvlak op basis van identiteiten.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4, azuread-video-2020
ms.openlocfilehash: f0a91a4d3b479d1243907951481e5a10f7085bbc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837427"
---
# <a name="what-is-conditional-access"></a>Wat is voorwaardelijke toegang?

De moderne beveiligingsperimeter gaat nu verder dan het netwerk van een organisatie en omvat ook de identiteit van gebruikers en apparaten. Organisaties kunnen deze identiteitssignalen gebruiken als onderdeel van hun beslissingen over toegangsbeheer. 

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-AD-Conditional-Access/player]

Voorwaardelijke toegang is de tool die door Azure Active Directory wordt gebruikt om signalen samen te brengen, beslissingen te nemen en organisatiebeleid af te dwingen. Voorwaardelijke toegang vormt de kern van het nieuwe besturingsvlak op basis van identiteiten.

![Conceptueel voorwaardelijk signaal plus beslissing om afdwinging in te schakelen](./media/overview/conditional-access-signal-decision-enforcement.png)

De eenvoudigste beleidsregels voor voorwaardelijke toegang zijn als/dan-instructies. Als een gebruiker toegang wil krijgen tot een resource, moet deze een actie uitvoeren. Voorbeeld: Een salarisbeheerder wil toegang krijgen tot de salaristoepassing en moet hiervoor Multi-Factor Authentication uitvoeren.

Beheerders hebben twee hoofddoelen:

- Geef gebruikers de mogelijkheid overal en altijd productief te zijn
- De bedrijfsactiva beschermen

Door het beleid voor voorwaardelijke toegang te gebruiken, kunt u waar nodig de juiste toegangscontroles toepassen zodat uw organisatie veilig blijft en u de gebruiker niet onnodig in de weg zit.

![Processtroom Conceptuele voorwaardelijke toegang](./media/overview/conditional-access-overview-how-it-works.png)

> [!IMPORTANT]
> Beleid voor voorwaardelijke toegang wordt afgedwongen nadat verificatie van de eerste factor is voltooid. Voorwaardelijke toegang is niet zozeer bedoeld als de eerste verdedigingslinie van een organisatie tegen bijvoorbeeld DoS-aanvallen (Denial of Service), maar kan gebruikmaken van signalen van deze gebeurtenissen om wel of geen toegang te verlenen.

## <a name="common-signals"></a>Algemene signalen

Via voorwaardelijke toegang kan rekening worden gehouden met de volgende algemene signalen bij het nemen van een beleidsbeslissing:

- Gebruikers- of groepslidmaatschap
   - Beleid kan worden afgestemd op specifieke gebruikers en groepen, waardoor beheerders een nauwkeurige controle hebben over de toegang.
- IP-locatie-informatie
   - Organisaties kunnen vertrouwde IP-adresbereiken maken die kunnen worden gebruikt bij het nemen van beleidsbeslissingen. 
   - Beheerders kunnen IP-bereiken voor landen/regio's opgeven om verkeer te blokkeren of toe te staan.
- Apparaat
   - Gebruikers met apparaten van specifieke platforms of die zijn gemarkeerd met een specifieke status, kunnen worden gebruikt bij het afdwingen van beleid voor voorwaardelijke toegang.
- Toepassing
   - Gebruikers die toegang proberen te krijgen tot specifieke toepassingen, kunnen verschillende beleidsregels voor voorwaardelijke toegang activeren. 
- Realtime en berekende risicodetectie
   - Signaalintegratie met Azure AD Identity Protection maakt voorwaardelijk toegangsbeleid mogelijk om riskant aanmeldingsgedrag te identificeren. Beleid kan gebruikers vervolgens dwingen om wachtwoordwijzigingen of Multi-Factor Authentication uit te voeren om hun risiconiveau te verminderen of de toegang te blokkeren totdat een beheerder handmatige actie onderneemt.
- Microsoft Cloud App Security (MCAS)
   - Hiermee kunnen de toegang en sessies van gebruikerstoepassingen in realtime worden bewaakt en beheerd, waardoor de zichtbaarheid en controle over de toegang en activiteiten in uw cloudomgeving worden vergroot.

## <a name="common-decisions"></a>Algemene beslissingen

- Toegang blokkeren
   - Meest beperkende beslissing
- Toegang verlenen
   - Bij de minst beperkende beslissing kunnen nog steeds een of meer van de volgende opties nodig zijn:
      - Multi-Factor Authentication vereisen
      - Vereisen dat het apparaat moet worden gemarkeerd als compatibel
      - Hybride Azure AD-gekoppeld apparaat is vereist
      - Goedgekeurde client-apps vereisen
      - Beleid voor app-beveiliging vereisen (preview)

## <a name="commonly-applied-policies"></a>Algemeen toegepast beleid

Veel organisaties hebben [algemene toegangsproblemen waarbij het beleid voor voorwaardelijke toegang kan helpen](concept-conditional-access-policy-common.md) zoals:

- Multi-Factor Authentication vereisen voor gebruikers met beheerdersrollen
- Multi-Factor Authentication vereisen voor Azure-beheertaken
- Aanmeldingen blokkeren voor gebruikers die verouderde verificatieprotocollen gebruiken
- Vertrouwde locaties voor registratie van Azure AD Multi-Factor Authentication vereisen
- Toegang vanaf specifieke locaties blokkeren of verlenen
- Riskant aanmeldingsgedrag blokkeren
- Door de organisatie beheerde apparaten vereisen voor specifieke toepassingen

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Klanten met een [Microsoft 365 Business Premium-licentie](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) hebben ook toegang tot de functies voor voorwaardelijke toegang. 

[Aanmeldingsrisico](concept-conditional-access-conditions.md#sign-in-risk) heeft toegang nodig tot [identiteitsbeveiliging](../identity-protection/overview-identity-protection.md)

## <a name="next-steps"></a>Volgende stappen

- [Beleid voor voorwaardelijke toegang geleidelijk opbouwen](concept-conditional-access-policies.md)
- [De implementatie van voorwaardelijke toegang plannen](plan-conditional-access.md)
- [Meer informatie over Identity Protection](../identity-protection/overview-identity-protection.md)
- [Meer informatie over Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)
- [Meer informatie over Microsoft Intune](/intune/index)
