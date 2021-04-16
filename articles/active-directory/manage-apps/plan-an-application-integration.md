---
title: Aan de slag met het integreren Azure Active Directory apps
description: Dit artikel is een aan de slag-handleiding voor het integreren van Azure Active Directory (AD) met on-premises toepassingen en cloudtoepassingen.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 37916e5e356e7c5ad6e685ac0838363fe2579496
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374978"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Aan de slag Azure Active Directory integreren met toepassingen

In dit onderwerp wordt het proces voor het integreren van toepassingen met Azure Active Directory (AD) samengevat. Elk van de onderstaande secties bevat een korte samenvatting van een gedetailleerder onderwerp, zodat u kunt bepalen welke onderdelen van deze introductiehandleiding relevant zijn voor u.

Zie Volgende stappen voor het downloaden van uitgebreide [implementatieplannen.](#next-steps)

## <a name="take-inventory"></a>Inventariseren
Voordat u toepassingen integreert met Azure AD, is het belangrijk om te weten waar u bent en waar u naartoe wilt gaan.  De volgende vragen zijn bedoeld om u te helpen nadenken over uw Azure AD-toepassingsintegratieproject.

### <a name="application-inventory"></a>Toepassingsinventaris
* Waar zijn al uw toepassingen? Wie is de eigenaar?
* Wat voor soort verificatie is vereist voor uw toepassingen?
* Wie heeft toegang nodig tot welke toepassingen?
* Wilt u een nieuwe toepassing implementeren?
  * Gaat u deze zelf bouwen en implementeren op een Azure Compute-exemplaar?
  * Gebruikt u een galerie die beschikbaar is in Azure-toepassing Gallery?

### <a name="user-and-group-inventory"></a>Inventaris van gebruikers en groepen
* Waar bevinden uw gebruikersaccounts zich?
  * On-premises Active Directory
  * Azure AD
  * Binnen een afzonderlijke toepassingsdatabase die u bezit
  * In niet-geanctioneerde toepassingen
  * Alle hierboven genoemde antwoorden
* Welke machtigingen en roltoewijzingen hebben afzonderlijke gebruikers momenteel? Moet u de toegang controleren of weet u zeker dat uw gebruikerstoegang en roltoewijzingen nu geschikt zijn?
* Zijn er al groepen in uw on-premises Active Directory?
  * Hoe zijn uw groepen ingedeeld?
  * Wie zijn de groepsleden?
  * Welke machtigingen/roltoewijzingen hebben de groepen momenteel?
* Moet u gebruikers-/groepsdatabases ops schonen voordat u gaat integreren?  (Dit is een belangrijke vraag. Garbage in, garbage out.)

### <a name="access-management-inventory"></a>Inventaris van toegangsbeheer
* Hoe beheert u momenteel gebruikerstoegang tot toepassingen? Moet dat veranderen?  Hebt u andere manieren overwogen om toegang te beheren, bijvoorbeeld met [Azure RBAC?](../../role-based-access-control/role-assignments-portal.md)
* Wie heeft toegang nodig tot wat?

Misschien hebt u niet van te voren de antwoorden op al deze vragen, maar dat is geen probleem.  Deze handleiding kan u helpen een aantal van deze vragen te beantwoorden en een aantal weloverwogen beslissingen te nemen.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Niet-geanctioneerde cloudtoepassingen zoeken met Cloud Discovery

Zoals eerder vermeld, zijn er mogelijk toepassingen die tot nu toe niet door uw organisatie zijn beheerd.  Als onderdeel van het inventarisatieproces is het mogelijk om niet-geanctioneerde cloudtoepassingen te vinden. Zie [Set up Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Toepassingen integreren met Azure AD
In de volgende artikelen worden de verschillende manieren besproken waarop toepassingen kunnen worden geïntegreerd met Azure AD en worden enkele richtlijnen gegeven.

* [Bepalen welke Active Directory moet worden gebruikt](../fundamentals/active-directory-whatis.md)
* [Toepassingen gebruiken in de Azure-toepassingsgalerie](what-is-single-sign-on.md)
* [Lijst met zelfstudies over het integreren van SaaS-toepassingen](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Mogelijkheden voor apps die niet worden vermeld in de Azure AD-galerie

U kunt elke toepassing toevoegen die al bestaat in uw organisatie, of een toepassing van derden van een leverancier die nog geen deel uitmaakt van de Azure AD-galerie. Afhankelijk van uw [licentieovereenkomst](https://azure.microsoft.com/pricing/details/active-directory/)zijn de volgende mogelijkheden beschikbaar:

- Selfservice-integratie van elke toepassing die ondersteuning biedt [voor Security Assertion Markup Language (SAML) 2.0-id-providers](https://wikipedia.org/wiki/SAML_2.0) (door SP of IdP geïnitieerd)
- Selfservice-integratie van een webtoepassing met een aanmeldingspagina op basis van HTML met eenmalige [aanmelding op basis van een wachtwoord](sso-options.md#password-based-sso)
- Selfserviceverbinding van toepassingen die gebruikmaken van het [SCIM-protocol (System for Cross-Domain Identity Management)](../app-provisioning/use-scim-to-provision-users-and-groups.md) voor het inrichten van gebruikers
- Mogelijkheid om koppelingen toe te voegen aan elke toepassing [](https://myapplications.microsoft.com/) in het [start-](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) of Mijn apps

Zie Verificatiescenario's voor Azure AD als u op zoek bent naar richtlijnen voor ontwikkelaars over het integreren van aangepaste apps [met Azure AD.](../develop/authentication-vs-authorization.md) Wanneer u een app ontwikkelt die gebruikmaakt van een modern protocol zoals [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) om gebruikers te verifiëren, kunt u deze registreren bij het Microsoft Identity Platform met behulp van de [App-registraties-ervaring](../develop/quickstart-register-app.md) in de Azure Portal.

### <a name="authentication-types"></a>Verificatietypen
Elk van uw toepassingen kan verschillende verificatievereisten hebben. Met Azure AD kunnen handtekeningcertificaten worden gebruikt met toepassingen die gebruikmaken van SAML 2.0, WS-Federation of OpenID Connect Protocols and Password Single Sign On. Zie Managing [Certificates for Federated Single Sign-On in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) and Password based single sign on (Certificaten voor ge federated Single Sign-On beheren in Azure Active Directory en Een wachtwoord gebaseerde een aanmelding ) voor meer informatie over toepassingsverificatietypen. [](what-is-single-sign-on.md)

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Eenmalige aanmelding met Azure AD-app inschakelen
Met Microsoft Azure AD toepassingsproxy kunt u veilig toegang bieden tot toepassingen in uw particuliere netwerk, vanaf elke locatie en elk apparaat. Nadat u een toepassingsproxyconnector in uw omgeving hebt geïnstalleerd, kan deze eenvoudig worden geconfigureerd met Azure AD.

### <a name="integrating-custom-applications"></a>Aangepaste toepassingen integreren
Zie Publish your app to the Azure AD app gallery (Uw app publiceren naar de [Azure AD-appgalerie)](../develop/v2-howto-app-gallery-listing.md)als u uw aangepaste toepassing wilt toevoegen aan Azure-toepassing Gallery.

## <a name="managing-access-to-applications"></a>Toegang tot toepassingen beheren
In de volgende artikelen worden manieren beschreven waarop u de toegang tot toepassingen kunt beheren nadat ze zijn geïntegreerd met Azure AD met behulp van Azure AD-connectors en Azure AD.

* [Toegang tot apps beheren met Azure AD](what-is-access-management.md)
* [Automatiseren met Azure AD-connectors](../app-provisioning/user-provisioning.md)
* [Gebruikers toewijzen aan een toepassing](./assign-user-or-group-access-portal.md)
* [Groepen toewijzen aan een toepassing](./assign-user-or-group-access-portal.md)
* [Accounts delen](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Volgende stappen
Voor uitgebreide informatie kunt u de implementatieplannen Azure Active Directory [gitHub downloaden.](../fundamentals/active-directory-deployment-plans.md) Voor galerietoepassingen kunt u implementatieplannen voor een aanmelding, voorwaardelijke toegang en het inrichten van gebruikers downloaden via [de Azure Portal](https://portal.azure.com).

Een implementatieplan downloaden van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Bedrijfstoepassingen**  |  **Kies een app-implementatieplan.**  |  
