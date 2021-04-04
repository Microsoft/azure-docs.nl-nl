---
title: Verificatie op basis van wacht woorden met Azure Active Directory
description: Architectuur richtlijnen voor het realiseren van verificatie op basis van wacht woorden met Azure Active Directory.
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
ms.openlocfilehash: 5bd6a5c8af117bf6cb39969a5f1b1f17ff08681c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172819"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Verificatie op basis van wacht woorden met Azure Active Directory

Op wacht woord gebaseerde single Sign-On (SSO) maakt gebruik van het bestaande verificatie proces voor de toepassing. Wanneer u SSO op basis van wacht woorden inschakelt, worden gebruikers referenties in de Directory verzameld, versleuteld en beveiligd door Azure Active Directory (Azure AD). Azure AD levert de gebruikers naam en het wacht woord voor de toepassing wanneer de gebruiker zich probeert aan te melden.

Kies SSO op basis van wacht woorden wanneer een toepassing wordt geverifieerd met een gebruikers naam en wacht woord in plaats van toegangs tokens en headers. Eenmalige aanmelding op basis van wacht woorden ondersteunt elke Cloud toepassing die een op HTML gebaseerde aanmeldings pagina bevat. 

## <a name="use-when"></a>Wanneer gebruiken

U moet beveiligen met pre-authenticatie en eenmalige aanmelding via wachtwoord kluizen bieden aan Web-apps.

![architectuur diagram](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: er wordt vanuit mijn apps toegang tot een samengestelde toepassing uitgevoerd of door de site rechtstreeks te bezoeken. 

* **Webbrowser**: het onderdeel waarmee de gebruiker communiceert om toegang te krijgen tot de externe URL van de toepassing. De gebruiker opent de op formulieren gebaseerde toepassing via de MyApps-extensie. 

* **MyApps-extensie**: identificeert de geconfigureerde op wacht woord gebaseerde SSO-toepassing en levert de referenties aan het aanmeldings formulier. De uitbrei ding MyApps wordt geïnstalleerd op de webbrowser. 

* **Azure AD**: verifieert de gebruiker.

## <a name="implement-password-based-sso-with-azure-ad"></a>Eenmalige aanmelding op basis van wacht woorden implementeren met Azure AD

* [Wat is SSO op basis van een wacht woord](../manage-apps/what-is-single-sign-on.md) 

* [Eenmalige aanmelding op basis van een wacht woord configureren voor Cloud toepassingen ](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [Eenmalige aanmelding op basis van een wacht woord configureren voor on-premises toepassingen met toepassings proxy](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)

