---
title: OpenID Connect Connect-verificatie met Azure Active Directory
description: Architectuur richtlijnen voor het bereiken van OpenID Connect Connect-verificatie met Azure Active Directory.
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168658"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>OpenID Connect Connect-verificatie met Azure Active Directory

OpenID Connect Connect (OIDC) is een verificatie protocol op basis van het OAuth2-Protocol (dat wordt gebruikt voor autorisatie). OIDC maakt gebruik van de gestandaardiseerde bericht stromen van OAuth2 om identiteits services te bieden. 

Het ontwerp doel van OIDC is het ' maken van eenvoudige en gecompliceerde dingen mogelijk '. Met OIDC kunnen ontwikkel aars hun gebruikers verifiëren via websites en apps zonder dat ze hun eigen wachtwoord bestanden hoeven te hebben en te beheren. Dit biedt de app-opbouw functie een veilige manier om de identiteit te verifiëren van de persoon die momenteel gebruikmaakt van de browser of de systeem eigen app die is verbonden met de toepassing.

De verificatie van de gebruiker moet plaatsvinden bij een id-provider waar de sessie of referenties van de gebruiker worden gecontroleerd. Hiervoor hebt u een vertrouwde agent nodig. Systeem eigen apps starten doorgaans de System browser voor dat doel. Inge sloten weer gaven worden beschouwd als niet-vertrouwd, omdat er niets is om te voor komen dat de app kan worden gekeken naar het wacht woord van de gebruiker. 

Naast verificatie kan de gebruiker om toestemming wordt gevraagd. Toestemming is de expliciete machtiging van de gebruiker waarmee een toepassing toegang kan krijgen tot beveiligde bronnen. Toestemming wijkt af van authenticatie omdat de toestemming alleen voor een resource hoeft te worden gegeven. De toestemming blijft geldig totdat de gebruiker of beheerder de toekenning hand matig intrekt. 

## <a name="use-when"></a>Wanneer gebruiken

Er is een nood zaak voor toestemming van de gebruiker en voor aanmelden bij het web.

![architectuur diagram](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: vraagt een service aan bij de toepassing.

* **Vertrouwde agent**: het onderdeel waarmee de gebruiker communiceert. Deze vertrouwde agent is doorgaans een webbrowser.

* **Toepassing**: de toepassing of de resource server is waar de resource of gegevens zich bevinden. Hiermee wordt de ID-provider vertrouwd voor het veilig verifiëren en autoriseren van de vertrouwde agent. 

* **Azure AD**: de OIDC-provider, ook wel bekend als de ID-provider, beheert veilig alles met de informatie van de gebruiker, hun toegang en de vertrouwens relaties tussen partijen in een stroom. Hiermee wordt de identiteit van de gebruiker geverifieerd, wordt de toegang tot bronnen verleend en ingetrokken en worden tokens uitgegeven. 

## <a name="implement-oidc-with-azure-ad"></a>OIDC implementeren met Azure AD

* [Toepassingen integreren met Azure AD](../saas-apps/tutorial-list.md) 

* [OAuth 2,0 en OpenID Connect Connect protocollen op het micro soft Identity-platform](../develop/active-directory-v2-protocols.md) 

* [Micro soft Identity platform en OpenID Connect Connect protocol](../develop/v2-protocols-oidc.md) 

* [Webaanmelding met OpenID Connect Connect in Azure Active Directory B2C](../../active-directory-b2c/openid-connect.md) 

* [Uw toepassing beveiligen met OpenID Connect en Azure AD](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

