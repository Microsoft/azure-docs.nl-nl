---
title: Flexibiliteit in externe gebruikers authenticatie maken met Azure Active Directory
description: Een hand leiding voor IT-beheerders en architecten voor het ontwikkelen van flexibele verificatie voor externe gebruikers
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
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724956"
---
# <a name="build-resilience-in-external-user-authentication"></a>Flexibiliteit in externe gebruikers verificatie maken

[Azure Active Directory B2B-samen werking](../external-identities/what-is-b2b.md) (Azure AD B2B) is een functie van [externe identiteiten](../external-identities/delegate-invitations.md) die samen werking met andere organisaties en personen mogelijk maakt. Hiermee wordt het veilig voorbereiden van gast gebruikers in uw Azure AD-Tenant mogelijk zonder dat ze hun referenties hoeven te beheren. Externe gebruikers geven hun identiteit en referenties met hen van een externe ID-provider (IdP), zodat ze geen nieuwe referentie hoeven te onthouden. 

## <a name="ways-to-authenticate-external-users"></a>Manieren om externe gebruikers te verifiëren

U kunt de methoden van externe gebruikers verificatie voor uw directory kiezen. U kunt micro soft id of andere id gebruiken.

Bij elke externe IdP maakt u een afhankelijkheid van de beschik baarheid van die IdP. Met een aantal methoden om verbinding te maken met id, zijn er dingen die u kunt doen om uw tolerantie te verg Roten.

> [!NOTE] 
> Azure AD B2B heeft de ingebouwde mogelijkheid om elke gebruiker te verifiëren bij een [Azure Active Directory](../index.yml) Tenant of met een persoonlijk [micro soft-account](https://account.microsoft.com/account). U hoeft geen configuratie met deze ingebouwde opties uit te voeren.

### <a name="considerations-for-resilience-with-other-idps"></a>Overwegingen voor tolerantie met andere id

Wanneer u externe ID gebruikt voor de verificatie van gast gebruikers, zijn er bepaalde configuraties die u moet garanderen om onderbrekingen te voor komen.

| Verificatiemethode| Overwegingen met betrekking tot tolerantie |
| - | - |
| Federatie met sociale id zoals [Facebook](../external-identities/facebook-federation.md) of [Google](../external-identities/google-federation.md).| U moet uw account onderhouden met de IdP en de client-ID en het client geheim configureren. |
| [Directe Federatie met SAML-en WS-Federation-id-providers](../external-identities/direct-federation.md)| U moet samen werken met de IdP-eigenaar om toegang te krijgen tot hun eind punten, waarop u afhankelijk bent. <br>U moet de meta gegevens onderhouden die de certificaten en eind punten bevatten. |
| [E-mail met eenmalige wachtwoord code](../external-identities/one-time-passcode.md)| Met deze methode bent u afhankelijk van het e-mail systeem van micro soft, het e-mail systeem van de gebruiker en de e-mailclient van de gebruiker. |


 

## <a name="self-service-sign-up-preview"></a>Aanmelden via self-service (preview-versie)

Als alternatief voor het verzenden van uitnodigingen of koppelingen kunt u [Aanmelden via self-service](../external-identities/self-service-sign-up-overview.md)inschakelen.  Hierdoor kunnen externe gebruikers toegang aanvragen tot een toepassing. U moet een [API-connector](../external-identities/self-service-sign-up-add-api-connector.md) maken en deze koppelen aan een gebruikers stroom. U koppelt gebruikers stromen die de gebruikers ervaring definiëren met een of meer toepassingen. 

Het is mogelijk API- [connectors](../external-identities/api-connectors-overview.md) te gebruiken voor het integreren van uw Self-service voor het registreren van de gebruikers stroom met api's van externe systemen. Deze API-integratie kan worden gebruikt voor [aangepaste goedkeurings werk stromen](../external-identities/self-service-sign-up-add-approvals.md), het [uitvoeren van identiteits verificatie](../external-identities/code-samples-self-service-sign-up.md)en andere taken, zoals het overschrijven van gebruikers kenmerken. Als u Api's wilt gebruiken, moet u de volgende afhankelijkheden beheren.

* **Verificatie van API-connector**: voor het instellen van een connector zijn een eind punt-URL, een gebruikers naam en een wacht woord vereist. Stel een proces in voor het onderhouden van deze referenties en werk met de API-eigenaar om te controleren of u een verloop schema kent.

* **Reactie van API-connector**: API-connectors ontwerpen in de registratie stroom om op een correcte wijze te mislukken als de API niet beschikbaar is. Bekijk en bied uw API-Ontwikkel aars deze [voor beeld-API-antwoorden](../external-identities/self-service-sign-up-add-api-connector.md) en de [Aanbevolen procedures voor het oplossen van problemen](../external-identities/self-service-sign-up-add-api-connector.md). Werk samen met het API-Ontwikkel team om alle mogelijke reactie scenario's te testen, met inbegrip van voortzetting, validatie fout en blok keren van reacties. 

## <a name="next-steps"></a>Volgende stappen
Flexibiliteits bronnen voor beheerders en architecten
 
* [Tolerantie bouwen met referentie beheer](resilience-in-credentials.md)

* [Een tolerantie bouwen met de status van het apparaat](resilience-with-device-states.md)

* [Een tolerantie bouwen met behulp van voortdurende toegang (CAE)](resilience-with-continuous-access-evaluation.md)

* [Flexibiliteit in uw hybride verificatie maken](resilience-in-hybrid.md)

* [Tolerantie in toepassings toegang bouwen met toepassings proxy](resilience-on-premises-access.md)

Flexibiliteits bronnen voor ontwikkel aars

* [IAM-tolerantie bouwen in uw toepassingen](resilience-app-development-overview.md)

* [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)
