---
title: RADIUS-verificatie met Azure Active Directory
description: Architectuur richtlijnen voor het bereiken van RADIUS-verificatie met Azure Active Directory.
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
ms.openlocfilehash: 97c524e1f4c05787f1dd61dea5a463e8fa83511a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168624"
---
# <a name="radius-authentication-with-azure-active-directory"></a>RADIUS-verificatie met Azure Active Directory

Remote Authentication Dial-In User Service (RADIUS) is een netwerk protocol dat een netwerk beveiligt door gecentraliseerde verificatie en autorisatie van inbel gebruikers in te scha kelen. Veel toepassingen zijn nog steeds afhankelijk van het RADIUS-protocol om gebruikers te verifiëren.

Micro soft Windows Server heeft een rol met de naam Network Policy Server (NPS), die als RADIUS-server kan fungeren en RADIUS-verificatie kan ondersteunen.

Azure Active Directory (Azure AD) maakt multi-factor Authentication mogelijk met RADIUS-systemen. Als een klant Azure AD Multi-Factor Authentication wil Toep assen op een van de eerder genoemde RADIUS-workloads, kunnen ze de Azure AD Multi-Factor Authentication NPS-extensie installeren op hun Windows NPS-server. 

De Windows NPS-server verifieert de referenties van een gebruiker aan de hand van Active Directory en stuurt de Multi-Factor Authentication-aanvraag vervolgens naar Azure. De gebruiker ontvangt vervolgens een uitdaging voor hun mobiele verificator. Als de client toepassing is geslaagd, mag deze verbinding maken met de service. 

## <a name="use-when"></a>Gebruiken wanneer: 

U moet Multi-Factor Authentication toevoegen aan toepassingen zoals
* een virtueel particulier netwerk (VPN)
* Wi-Fi-toegang
* Extern bureaublad-gateway (RDG)
* Virtual Desktop Infrastructure (VDI)
* Andere personen die afhankelijk zijn van het RADIUS-protocol om gebruikers in de service te verifiëren. 

> [!NOTE]
> In plaats van de RADIUS-en Azure AD Multi-Factor Authentication NPS-extensie te gebruiken voor het Toep assen van Azure AD-Multi-Factor Authentication op VPN-workloads, raden wij u aan om uw VPN om te zetten naar SAML en uw VPN rechtstreeks te verrijken met Azure AD. Dit geeft uw VPN het volledige scala van Azure AD-beveiliging, zoals voorwaardelijke toegang, Multi-Factor Authentication, apparaatcompatibiliteit en identiteits beveiliging.

![architectuur diagram](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Onderdelen van het systeem 

* **Client toepassing (VPN-client)**: stuurt verificatie aanvragen naar de RADIUS-client.

* **RADIUS-client**: converteert aanvragen van een client toepassing en verzendt deze naar de RADIUS-server waarop de NPS-extensie is geïnstalleerd.

* **RADIUS-server**: maakt verbinding met Active Directory om de primaire verificatie voor de RADIUS-aanvraag uit te voeren. Wanneer dit is gelukt, wordt de aanvraag door gegeven aan de NPS-extensie van Azure AD Multi-Factor Authentication.

* **NPS-extensie**: activeert een aanvraag voor Azure AD multi-factor Authentication voor een secundaire authenticatie. Als deze is geslaagd, wordt de verificatie aanvraag door de NPS-extensie voltooid door de RADIUS-server te voorzien van beveiligings tokens die Multi-Factor Authentication claim bevatten, uitgegeven door de beveiligings token service van Azure.

* **Azure ad multi-factor Authentication**: communiceert met Azure AD om de details van de gebruiker op te halen en voert een secundaire verificatie uit met behulp van een verificatie methode die door de gebruiker is geconfigureerd.

## <a name="implement-radius-with-azure-ad"></a>RADIUS implementeren met Azure AD 

* [Azure AD Multi-Factor Authentication-mogelijkheden bieden met NPS](../authentication/howto-mfa-nps-extension.md) 

* [De Azure AD Multi-Factor Authentication NPS-extensie configureren](../authentication/howto-mfa-nps-extension-advanced.md) 

* [VPN met Azure AD Multi-Factor Authentication de NPS-extensie gebruiken](../authentication/howto-mfa-nps-extension-vpn.md) 

  
‎ 

