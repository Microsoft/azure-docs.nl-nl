---
title: Maak flexibiliteit in uw IAM-infra structuur met Azure Active Directory
description: Een hand leiding voor architecten en IT-beheerders die geen flexibiliteit hebben om hun IAM-infra structuur te onderbreken.
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
ms.openlocfilehash: 64fe4b8c217ec46cbb6dd046339c3ac65eebb121
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724674"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Maak flexibiliteit in uw infra structuur voor identiteits-en toegangs beheer

Azure Active Directory is een wereld wijde Cloud identiteits-en toegangs beheer systeem dat essentiële services biedt, zoals verificatie en autorisatie voor de resources van uw organisatie. Dit document bevat richt lijnen voor het begrijpen, opnemen en beperken van het risico van de onderbreking van verificatie-of autorisatie Services voor bronnen die afhankelijk zijn van Azure Active Directory (Azure AD). 

De documentenset is ontworpen voor

* Identiteits architecten

* Identiteits service-eigen aren

* Identiteits bewerkings teams

Raadpleeg ook de documentatie voor [toepassings ontwikkelaars](./resilience-app-development-overview.md) en voor [Azure AD B2C systemen](resilience-b2c.md).

## <a name="what-is-resilience"></a>Wat is tolerantie?

In de context van uw infra structuur voor identiteiten is de mogelijkheid om te zorgen voor onderbrekingen van services zoals verificatie en autorisatie, of het mislukken van andere onderdelen, met een minimale of geen gevolgen voor uw bedrijf, gebruikers en bewerkingen. De impact van de onderbreking kan ernstig zijn, en voor de flexibiliteit is een flexibele planning vereist.

## <a name="why-worry-about-disruption"></a>Waarom is er een probleem met de onderbreking?

Elke aanroep van het verificatie systeem is onderhevig aan onderbrekingen als een onderdeel van de aanroep mislukt. Wanneer de authenticatie wordt verstoord door de onderliggende onderdeel fouten, hebben uw gebruikers geen toegang tot hun toepassingen. Daarom is het verminderen van het aantal verificatie aanroepen en het aantal afhankelijkheden in deze aanroepen belang rijk voor uw tolerantie. Ontwikkel aars van toepassingen kunnen bepalen hoe vaak tokens worden aangevraagd. Werk met uw ontwikkel aars bijvoorbeeld om ervoor te zorgen dat ze waar mogelijk Azure AD Managed-identiteiten voor hun toepassingen gebruiken. 

In een verificatie systeem op basis van tokens, zoals Azure AD, moet de toepassing (client) van een gebruiker een beveiligings token van het identiteits systeem verkrijgen voordat het toegang heeft tot een toepassing of een andere bron. Gedurende de geldigheids periode kan een client hetzelfde token meerdere keren aanbieden voor toegang tot de toepassing.

Wanneer het token dat aan de toepassing wordt gepresenteerd, verloopt, wordt het token door de toepassing geweigerd en moet de client een nieuw token van Azure AD verkrijgen. Voor het verkrijgen van een nieuw token is mogelijk gebruikers interactie vereist, zoals referentie prompts of voor andere vereisten van het verificatie systeem. Het verminderen van de frequentie van verificatie aanroepen met langere tokens vermindert onnodige interacties. U moet echter de levens duur van het token balanceren met het risico dat door minder beleids evaluaties is gemaakt. Voor meer informatie over het beheren van de levens duur van tokens raadpleegt u dit artikel over het [optimaliseren van herauthenticatie prompts](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="ways-to-increase-resilience"></a>Manieren om de tolerantie te verhogen
In het volgende diagram ziet u zes concrete manieren waarop u de tolerantie kunt verhogen. Elke methode wordt gedetailleerd uitgelegd in de artikelen die in het gedeelte volgende stappen van dit artikel zijn gekoppeld.
  
![Diagram met een overzicht van de beheer tolerantie](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Volgende stappen
Flexibiliteits bronnen voor beheerders en architecten
 
* [Tolerantie bouwen met referentie beheer](resilience-in-credentials.md)

* [Een tolerantie bouwen met de status van het apparaat](resilience-with-device-states.md)

* [Een tolerantie bouwen met behulp van voortdurende toegang (CAE)](resilience-with-continuous-access-evaluation.md)

* [Flexibiliteit in externe gebruikers verificatie maken](resilience-b2b-authentication.md)

* [Flexibiliteit in uw hybride verificatie maken](resilience-in-hybrid.md)

* [Tolerantie in toepassings toegang bouwen met toepassings proxy](resilience-on-premises-access.md)

Flexibiliteits bronnen voor ontwikkel aars

* [IAM-tolerantie bouwen in uw toepassingen](resilience-app-development-overview.md)

* [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)