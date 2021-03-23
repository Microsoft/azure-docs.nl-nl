---
title: Aan de slag met het integreren van Azure Active Directory met apps
description: Dit artikel is een aan de slag-hand leiding voor het integreren van Azure Active Directory (AD) met on-premises toepassingen en Cloud toepassingen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: de06bb4f97568eaa40b0b09e9bc2b50608424aa8
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775592"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Hand leiding voor het integreren van Azure Active Directory met toepassingen

Dit onderwerp bevat een overzicht van het proces voor het integreren van toepassingen met Azure Active Directory (AD). Elk van de onderstaande secties bevat een korte samen vatting van een gedetailleerd onderwerp, zodat u kunt zien welke onderdelen van deze aan de slag-hand leiding relevant zijn voor u.

Zie [volgende stappen](#next-steps)voor het downloaden van gedetailleerde implementatie plannen.

## <a name="take-inventory"></a>Inventaris maken
Voordat u toepassingen met Azure AD integreert, is het belang rijk om te weten waar u zich bevindt en waar u wilt gaan.  De volgende vragen zijn bedoeld om u te helpen bij het integratie project van Azure AD-toepassingen.

### <a name="application-inventory"></a>Toepassingsinventaris
* Waar zijn al uw toepassingen? Wie is eigenaar van de gebruikers?
* Wat voor soort verificatie vereisen uw toepassingen?
* Wie moet toegang hebben tot de toepassingen?
* Wilt u een nieuwe toepassing implementeren?
  * Maakt u deze intern en implementeert u deze in een Azure Compute-instantie?
  * Gebruikt u een die beschikbaar is in de Azure-toepassing galerie?

### <a name="user-and-group-inventory"></a>Gebruikers-en groeps inventaris
* Waar bevinden uw gebruikers accounts zich?
  * On-premises Active Directory
  * Azure AD
  * Binnen een afzonderlijke toepassings database waarvan u de eigenaar bent
  * In niet-goedgekeurde toepassingen
  * Alle hierboven genoemde antwoorden
* Welke machtigingen en roltoewijzingen hebben momenteel afzonderlijke gebruikers? Moet u de toegang controleren of weet u zeker dat uw gebruikers toegang en roltoewijzingen nu geschikt zijn?
* Worden er al groepen in uw on-premises Active Directory gemaakt?
  * Hoe worden uw groepen ingedeeld?
  * Wie zijn de groeps leden?
  * Welke machtigingen/roltoewijzingen hebben de groepen momenteel?
* Moet u de gebruikers-of groeps databases opschonen voordat u de integratie kunt door gaan?  (Dit is een belang rijke vraag. Opschonen, opschonen.)

### <a name="access-management-inventory"></a>Inventaris van toegangs beheer
* Hoe beheert u momenteel de gebruikers toegang tot toepassingen? Moet dat veranderen?  Hebt u andere manieren om toegang te beheren, zoals met [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) bijvoorbeeld?
* Wie moet er toegang tot hebben?

Misschien hebt u niet de antwoorden op al deze vragen, maar dat is wel goed.  Deze hand leiding kan u helpen bij het beantwoorden van enkele van deze vragen en een aantal weloverwogen beslissingen te nemen.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Niet-goedgekeurde Cloud toepassingen met Cloud Discovery zoeken

Zoals hierboven vermeld, zijn er mogelijk toepassingen die niet zijn beheerd door uw organisatie tot nu toe.  Als onderdeel van het inventaris proces is het mogelijk om niet-goedgekeurde Cloud toepassingen te vinden. Zie [Cloud Discovery instellen](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Toepassingen integreren met Azure AD
In de volgende artikelen vindt u informatie over de verschillende manieren waarop toepassingen kunnen worden geïntegreerd met Azure AD en bieden u enkele richt lijnen.

* [Bepalen welke Active Directory moeten worden gebruikt](../fundamentals/active-directory-whatis.md)
* [Toepassingen gebruiken in de Azure-toepassings galerie](what-is-single-sign-on.md)
* [Lijst met zelf studies voor SaaS-toepassingen integreren](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Mogelijkheden voor apps die niet worden vermeld in de Azure AD-galerie

U kunt elke toepassing toevoegen die al bestaat in uw organisatie of een toepassing van derden van een leverancier die nog geen deel uitmaakt van de Azure AD-galerie. Afhankelijk van uw [gebruiksrecht overeenkomst](https://azure.microsoft.com/pricing/details/active-directory/)zijn de volgende mogelijkheden beschikbaar:

- Selfservice-integratie van elke toepassing die ondersteuning biedt voor services van [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (door SP geïnitieerd of IDP)
- Self-Service-integratie van elke webtoepassing met een op een HTML gebaseerde aanmeldings pagina met [SSO op basis van wacht woorden](sso-options.md#password-based-sso)
- Selfservice verbinding van toepassingen die gebruikmaken van het [systeem voor het scim-Protocol (Cross-Domain Identity Management) voor het inrichten van gebruikers](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Mogelijkheid om koppelingen toe te voegen aan een toepassing in het [Start programma voor Office 365-apps](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) of [mijn apps](sso-options.md#linked-sign-on)

Zie [verificatie scenario's voor Azure AD](../develop/authentication-vs-authorization.md)als u hulp nodig hebt bij het integreren van aangepaste apps met Azure AD. Wanneer u een App ontwikkelt die gebruikmaakt van een modern protocol zoals [OpenID Connect Connect/OAuth](../develop/active-directory-v2-protocols.md) om gebruikers te verifiëren, kunt u dit registreren bij het micro soft Identity-platform met behulp van de [app-registraties](../develop/quickstart-register-app.md) -ervaring in de Azure Portal.

### <a name="authentication-types"></a>Verificatie typen
Elk van uw toepassingen heeft mogelijk andere verificatie vereisten. Met Azure AD kunnen handtekening certificaten worden gebruikt met toepassingen die gebruikmaken van SAML 2,0-, WS-Federation-of OpenID Connect Connect-protocollen en wacht woord eenmalige aanmelding. Zie voor meer informatie over toepassings verificatie typen [certificaten beheren voor federatieve enkelvoudige Sign-On in azure Active Directory](manage-certificates-for-federated-single-sign-on.md) en [op wacht woord gebaseerde eenmalige aanmelding](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Eenmalige aanmelding met Azure AD-app proxy inschakelen
Met Microsoft Azure AD toepassings proxy kunt u vanaf elke locatie en op elk apparaat toegang bieden tot toepassingen die zich in uw particuliere netwerk bevinden. Nadat u een toepassings proxy connector hebt geïnstalleerd in uw omgeving, kunt u deze eenvoudig configureren met Azure AD.

### <a name="integrating-custom-applications"></a>Aangepaste toepassingen integreren
Als u uw aangepaste toepassing wilt toevoegen aan de Azure-toepassing galerie, raadpleegt u [uw app naar de app-galerie van Azure AD publiceren](../develop/v2-howto-app-gallery-listing.md).

## <a name="managing-access-to-applications"></a>Toegang tot toepassingen beheren
In de volgende artikelen worden de manieren beschreven waarop u de toegang tot toepassingen kunt beheren wanneer ze zijn geïntegreerd met Azure AD met behulp van Azure AD-connectors en Azure AD.

* [Toegang tot apps beheren met Azure AD](what-is-access-management.md)
* [Automatiseren met Azure AD-connectors](../app-provisioning/user-provisioning.md)
* [Gebruikers toewijzen aan een toepassing](./assign-user-or-group-access-portal.md)
* [Groepen toewijzen aan een toepassing](./assign-user-or-group-access-portal.md)
* [Accounts delen](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Volgende stappen
Voor gedetailleerde informatie kunt u Azure Active Directory implementatie plannen downloaden van [github](../fundamentals/active-directory-deployment-plans.md). Voor galerie toepassingen kunt u implementatie plannen voor eenmalige aanmelding, voorwaardelijke toegang en gebruikers inrichten via de [Azure Portal](https://portal.azure.com)downloaden.

Een implementatie plan downloaden van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer   |  **een app**-  |  **implementatie plan** voor bedrijfs toepassingen.
