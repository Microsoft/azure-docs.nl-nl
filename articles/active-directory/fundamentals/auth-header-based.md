---
title: Verificatie op basis van een header met Azure Active Directory
description: Richt lijnen voor architectuur over het bereiken van verificatie op basis van een header met Azure Active Directory.
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
ms.openlocfilehash: 69e87fc919893a544f5d0b1b615a110f25486e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168743"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Verificatie op basis van een header met Azure Active Directory

Oudere toepassingen gebruiken vaak authenticatie op basis van een koptekst. In dit scenario wordt een gebruiker (of bericht starter) geverifieerd bij een id-oplossing tussen de identiteit. De intermediaire oplossing verifieert de gebruiker en geeft de vereiste Hypertext Transfer Protocol (HTTP)-headers door aan de webservice. Azure Active Directory (AD) ondersteunt dit patroon via de service Application proxy en integraties met andere netwerk controller oplossingen. 

In onze oplossing biedt toepassings proxy externe toegang tot de toepassing, verifieert de gebruiker en worden de door de toepassing vereiste headers door gegeven. 

## <a name="use-when"></a>Wanneer gebruiken

Externe gebruikers moeten zich op een veilige manier aanmelden voor on-premises toepassingen waarvoor op headers gebaseerde verificatie is vereist.

![Op de architectuur gebaseerde verificatie van de installatie kopie](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: toegang tot verouderde toepassingen die worden bediend door de toepassings proxy.

* **Webbrowser**: het onderdeel waarmee de gebruiker communiceert om toegang te krijgen tot de externe URL van de toepassing.

* **Azure AD**: verifieert de gebruiker. 

* **Application proxy-service**: fungeert als omgekeerde proxy voor het verzenden van een aanvraag van de gebruiker naar de on-premises toepassing. Het bevindt zich in azure AD en kan ook beleids regels voor voorwaardelijke toegang afdwingen.

* **Application proxy-connector**: on-premises op Windows-servers geïnstalleerd om verbinding te kunnen maken met de toepassingen. Er worden alleen uitgaande verbindingen gebruikt. Retourneert het antwoord naar Azure AD.

* **Verouderde toepassingen**: toepassingen die gebruikers aanvragen ontvangen van de toepassings proxy. De verouderde toepassing ontvangt de vereiste HTTP-headers voor het instellen van een sessie en het retour neren van een antwoord. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Op headers gebaseerde verificatie implementeren met Azure AD

* [Een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in azure AD](../manage-apps/application-proxy-add-on-premises-application.md)  

* [Op headers gebaseerde verificatie voor eenmalige aanmelding met de toepassingsproxy en PingAccess](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) 

* [Verouderde apps beveiligen met app-leveringscontrollers en -netwerken](../manage-apps/secure-hybrid-access.md)