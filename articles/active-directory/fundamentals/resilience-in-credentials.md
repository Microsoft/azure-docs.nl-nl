---
title: Een tolerantie bouwen met referentie beheer in Azure Active Directory
description: Een hand leiding voor architecten en IT-beheerders bij het bouwen van een flexibele referentie strategie.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399d2f71fa20d63dce89cf3be5c12ffd63264895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724707"
---
# <a name="build-resilience-with-credential-management"></a>Tolerantie bouwen met referentie beheer

Wanneer een referentie wordt weer gegeven aan Azure AD in een token aanvraag, zijn er meerdere afhankelijkheden die voor validatie beschikbaar moeten zijn. De eerste verificatie factor is afhankelijk van Azure AD-verificatie en in sommige gevallen in een on-premises infra structuur. Zie voor meer informatie over hybride-verificatie architecturen [tolerantie bouwen in uw hybride infra structuur](resilience-in-hybrid.md). 

Als u een tweede factor implementeert, worden de afhankelijkheden voor de tweede factor toegevoegd aan de afhankelijkheden voor de eerste. Als uw eerste factor bijvoorbeeld is via PTA en uw tweede factor is SMS, zijn uw afhankelijkheden:

* Azure AD-verificatie services

* Azure MFA-service

* Lokale infrastructuur

* Telefoon provider

* Het apparaat van de gebruiker (niet afbeelding)

 
![Afbeelding van verificatie methoden en-afhankelijkheden](./media/resilience-in-credentials/admin-resilience-credentials.png)

De referentie strategie moet rekening houden met de afhankelijkheden van elk verificatie type en het inrichten van methoden waarmee een Single Point of Failure wordt voor komen. 

Omdat verificatie methoden verschillende afhankelijkheden hebben, is het een goed idee om gebruikers in te scha kelen voor zoveel mogelijk opties van de tweede factor. Zorg ervoor dat u, indien mogelijk, een tweede factor met verschillende afhankelijkheden opneemt. Een voor beeld: een telefoon oproep en SMS als tweede factor delen dezelfde afhankelijkheden, dus als de enige opties het risico niet beperken.

De meest flexibele referentie strategie is het gebruik van verificatie met een wacht woord. De beveiligings sleutels van Windows hello voor bedrijven en FIDO 2,0 hebben minder afhankelijkheden dan sterke verificatie met twee afzonderlijke factoren. De beveiligings sleutels voor de Microsoft Authenticator-app, Windows hello voor bedrijven en Fido 2,0 zijn het meest veilig. 

Voor de tweede factoren hebben de Microsoft Authenticator-app of andere verificator-apps die gebruikmaken van op tijd gebaseerde eenmalige wachtwoord code (mobiele TOTP) of OATH-hardware-tokens de minste afhankelijkheden en zijn daarom robuuster.

## <a name="how-do-multiple-credentials-help-resilience"></a>Hoe kunnen meerdere referenties de flexibiliteit verbeteren?

Het inrichten van meerdere referentie typen biedt gebruikers opties die voldoen aan hun voor keuren en omgevings beperkingen. Als gevolg hiervan is interactieve verificatie waar gebruikers naar multi-factor Authentication wordt gevraagd, meer flexibiliteit voor specifieke afhankelijkheden die niet beschikbaar zijn op het moment van de aanvraag. U kunt de [herverificaties prompts optimaliseren voor multi-factor Authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Naast de hierboven beschreven tolerantie voor individuele gebruikers, moeten ondernemingen voor het plannen van problemen met grootschalige onderbrekingen, zoals operationele fouten die een onjuiste configuratie veroorzaken, een natuur ramp of een bedrijfs wijde storing in de onderneming voor een on-premises Federation service (vooral wanneer deze wordt gebruikt voor multi-factor Authentication). 

## <a name="how-do-i-implement-resilient-credentials"></a>Hoe kan ik flexibele referenties implementeren?

* Implementeer [referenties met een wacht woord](../authentication/howto-authentication-passwordless-deployment.md) , zoals Windows hello voor bedrijven, telefonische verificatie en FIDO2 om de afhankelijkheden te reduceren.

* Implementeer de [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md) als een tweede factor.

* [Wachtwoord hash-synchronisatie](../hybrid/whatis-phs.md) inschakelen voor hybride accounts die zijn gesynchroniseerd vanuit Windows Server Active Directory. Deze optie kan naast Federation Services worden ingeschakeld, zoals AD FS en biedt een terugvallen voor het geval de Federation service mislukt.

* [Analyseer het gebruik van multi-factor Authentication-methoden](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) om gebruikers ervaring te verbeteren.

* [Een flexibele strategie voor toegangs beheer implementeren](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Volgende stappen
Flexibiliteits bronnen voor beheerders en architecten
 
* [Een tolerantie bouwen met de status van het apparaat](resilience-with-device-states.md)

* [Een tolerantie bouwen met behulp van voortdurende toegang (CAE)](resilience-with-continuous-access-evaluation.md)

* [Flexibiliteit in externe gebruikers verificatie maken](resilience-b2b-authentication.md)

* [Flexibiliteit in uw hybride verificatie maken](resilience-in-hybrid.md)

* [Tolerantie in toepassings toegang bouwen met toepassings proxy](resilience-on-premises-access.md)

Flexibiliteits bronnen voor ontwikkel aars

* [IAM-tolerantie bouwen in uw toepassingen](resilience-app-development-overview.md)

* [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)