---
title: SAML-verificatie met Azure Active Directory
description: Architectuur richtlijnen voor het bereiken van SAML-verificatie met Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168641"
---
# <a name="saml-authentication-with-azure-active-directory"></a>SAML-verificatie met Azure Active Directory

Security Assertion Markup Language (SAML) is een open standaard voor het uitwisselen van verificatie-en autorisatie gegevens tussen een id-provider en een service provider. SAML is een op XML gebaseerde Markup-taal voor beveiligings verklaringen. Dit zijn instructies die door service providers worden gebruikt om beslissingen voor toegangs beheer te nemen. 

De SAML-specificatie definieert drie rollen:

* De principal, meestal een gebruiker
* De ID-provider (IdP)
* De service provider (SP)


## <a name="use-when"></a>Wanneer gebruiken

U moet een SSO-ervaring (eenmalige aanmelding) bieden voor een Enter prise SAML-toepassing.

Hoewel een van de belangrijkste voor beelden van SAML-adressen SSO is, met name door SSO uit te breiden tussen beveiligings domeinen, zijn er ook andere use-cases (profielen genoemd). 

![architectuur diagram voor SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: vraagt een service aan bij de toepassing.

* **Webbrowser**: het onderdeel waarmee de gebruiker communiceert.

* **Web-app**: bedrijfs toepassing die SAML ondersteunt en Azure AD gebruikt als IDP.

* **Token**: een SAML-verklaring (ook wel SAML-tokens genoemd) die sets van claims bevat die door de IDP over het principe (gebruiker) worden gemaakt. Het bevat verificatie-informatie, kenmerken en beslissings verklaringen voor autorisatie.

* **Azure AD**: Enter prise Cloud IDP die SSO en multi-factor Authentication biedt voor SAML-apps. Hiermee worden identiteits gegevens voor gebruikers gesynchroniseerd, onderhouden en beheerd, terwijl er verificatie services beschikbaar zijn voor het vertrouwen van toepassingen. 

## <a name="implement-saml-authentication-with-azure-ad"></a>SAML-verificatie implementeren met Azure AD

* [Zelf studies voor het integreren van SaaS-toepassingen met Azure Active Directory](../saas-apps/tutorial-list.md) 

* [Eenmalige aanmelding op basis van SAML configureren voor niet-galerie toepassingen](../manage-apps/add-application-portal.md) 

* [Hoe Azure Active Directory het SAML-protocol gebruikt](../develop/active-directory-saml-protocol-reference.md)