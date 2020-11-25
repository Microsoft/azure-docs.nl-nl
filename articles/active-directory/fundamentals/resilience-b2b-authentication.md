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
ms.openlocfilehash: ae8e24341c321fbfffb84d9b072abc4cf925aff3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919673"
---
# <a name="build-resilience-in-external-user-authentication"></a>Flexibiliteit in externe gebruikers verificatie maken

[Azure Active Directory B2B-samen werking](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (Azure AD B2B) is een functie van [externe identiteiten](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) die samen werking met andere organisaties en personen mogelijk maakt. Hiermee wordt het veilig voorbereiden van gast gebruikers in uw Azure AD-Tenant mogelijk zonder dat ze hun referenties hoeven te beheren. Externe gebruikers geven hun identiteit en referenties met hen van een externe ID-provider (IdP), zodat ze geen nieuwe referentie hoeven te onthouden. 

## <a name="ways-to-authenticate-external-users"></a>Manieren om externe gebruikers te verifiëren

U kunt de methoden van externe gebruikers verificatie voor uw directory kiezen. U kunt micro soft id of andere id gebruiken.

Bij elke externe IdP maakt u een afhankelijkheid van de beschik baarheid van die IdP. Met een aantal methoden om verbinding te maken met id, zijn er dingen die u kunt doen om uw tolerantie te verg Roten.

> [!NOTE] 
> Azure AD B2B heeft de ingebouwde mogelijkheid om elke gebruiker te verifiëren bij een [Azure Active Directory](https://docs.microsoft.com/azure/active-directory) Tenant of met een persoonlijk [micro soft-account](https://account.microsoft.com/account). U hoeft geen configuratie met deze ingebouwde opties uit te voeren.

### <a name="considerations-for-resilience-with-other-idps"></a>Overwegingen voor tolerantie met andere id

Wanneer u externe ID gebruikt voor de verificatie van gast gebruikers, zijn er bepaalde configuraties die u moet garanderen om onderbrekingen te voor komen.

| Verificatiemethode| Overwegingen met betrekking tot tolerantie |
| - | - |
| Federatie met sociale id zoals [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) of [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation).| U moet uw account onderhouden met de IdP en de client-ID en het client geheim configureren. |
| [Directe Federatie met SAML-en WS-Federation-id-providers](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| U moet samen werken met de IdP-eigenaar om toegang te krijgen tot hun eind punten, waarop u afhankelijk bent. <br>U moet de meta gegevens onderhouden die de certificaten en eind punten bevatten. |
| [E-mail met eenmalige wachtwoord code](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| Met deze methode bent u afhankelijk van het e-mail systeem van micro soft, het e-mail systeem van de gebruiker en de e-mailclient van de gebruiker. |


 

## <a name="self-service-sign-up-preview"></a>Aanmelden via self-service (preview-versie)

Als alternatief voor het verzenden van uitnodigingen of koppelingen kunt u [Aanmelden via self-service](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview)inschakelen.  Hierdoor kunnen externe gebruikers toegang aanvragen tot een toepassing. U moet een [API-connector](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) maken en deze koppelen aan een gebruikers stroom. U koppelt gebruikers stromen die de gebruikers ervaring definiëren met een of meer toepassingen. 

Het is mogelijk API- [connectors](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) te gebruiken voor het integreren van uw Self-service voor het registreren van de gebruikers stroom met api's van externe systemen. Deze API-integratie kan worden gebruikt voor [aangepaste goedkeurings werk stromen](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals), het [uitvoeren van identiteits verificatie](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up)en andere taken, zoals het overschrijven van gebruikers kenmerken. Als u Api's wilt gebruiken, moet u de volgende afhankelijkheden beheren.

* **Verificatie van API-connector**: voor het instellen van een connector zijn een eind punt-URL, een gebruikers naam en een wacht woord vereist. Stel een proces in voor het onderhouden van deze referenties en werk met de API-eigenaar om te controleren of u een verloop schema kent.

* **Reactie van API-connector**: API-connectors ontwerpen in de registratie stroom om op een correcte wijze te mislukken als de API niet beschikbaar is. Bekijk en bied uw API-Ontwikkel aars deze [voor beeld-API-antwoorden](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) en de [Aanbevolen procedures voor het oplossen van problemen](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector). Werk samen met het API-Ontwikkel team om alle mogelijke reactie scenario's te testen, met inbegrip van voortzetting, validatie fout en blok keren van reacties. 

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
 
