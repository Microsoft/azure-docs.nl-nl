---
title: Een tolerantie bouwen met behulp van apparaatstatus in Azure Active Directory
description: Een hand leiding voor architecten en IT-beheerders om een tolerantie te maken met behulp van apparaatstatus
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
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724607"
---
# <a name="build-resilience-with-device-states"></a>Een tolerantie bouwen met de status van het apparaat

Door [Apparaatstatus](../devices/overview.md) in te scha kelen met Azure AD, kunnen beheerders [beleids regels voor voorwaardelijke toegang](../conditional-access/overview.md) maken die de toegang tot toepassingen beheren op basis van de Apparaatstatus. Het extra voor deel van apparaten is dat het voldoet aan sterke verificatie vereisten voor toegang tot resources, waardoor extra MFA-verificatie aanvragen worden verminderd en de tolerantie wordt verbeterd. 

In het volgende diagram ziet u de verschillende manieren om apparaten in azure AD in te scha kelen waarmee de status van apparaten wordt ingeschakeld. U kunt meer dan één in uw organisatie gebruiken.

![stroom diagram voor het kiezen van apparaatstatus](./media/resilience-with-device-states/admin-resilience-devices.png)

Wanneer u [Apparaatstatus](../devices/overview.md)gebruikt, hebben gebruikers in de meeste gevallen eenmalige aanmelding bij bronnen via een [primair vernieuwings token](../devices/concept-primary-refresh-token.md) (PRT). De PRT bevat claims over de gebruiker en het apparaat en kan worden gebruikt voor het verkrijgen van verificatie tokens voor toegang tot toepassingen vanaf het apparaat. De PRT is 14 dagen geldig en wordt continu vernieuwd zolang de gebruiker het apparaat actief gebruikt, waardoor gebruikers een robuuste ervaring hebben. Een PRT kan ook een multi-factor Authentication-claim op verschillende manieren verkrijgen. Zie [Wanneer heeft een PRT een MFA-claim ontvangen](../devices/concept-primary-refresh-token.md)voor meer informatie.

## <a name="how-do-device-states-help"></a>Hoe kunnen de apparaats staten helpen?

Wanneer een PRT wordt gebruikt om toegang aan te vragen voor een toepassing, worden het apparaat, de sessie en MFA-claims vertrouwd door Azure AD. Wanneer beheerders beleids regels maken waarvoor een besturings element op basis van een apparaat of een multi-factor Authentication-besturings element vereist is, kan aan de beleids voorwaarde worden voldaan door middel van de apparaatstatus zonder multi-factor Authentication. Gebruikers zien geen aanvullende multi-factor Authentication-prompts op hetzelfde apparaat. Dit verhoogt de flexibiliteit tot een onderbreking van de Azure MFA-service of de afhankelijkheden ervan zoals lokale telecommunicatie providers.

## <a name="how-do-i-implement-device-states"></a>Hoe kan ik de status van de apparaten worden geïmplementeerd?

* Schakel [hybride Azure AD](../devices/hybrid-azuread-join-plan.md) en [Azure AD join](../devices/azureadjoin-plan.md) in voor Windows-apparaten die eigendom zijn van het bedrijf, en vraag deze indien mogelijk toe te voegen. Als dat niet mogelijk is, moeten ze worden geregistreerd.

  Als er oudere versies van Windows in uw organisatie aanwezig zijn, moet u deze apparaten upgraden om Windows 10 te gebruiken.

* Gebruikers browser standaardiseren om [micro soft Edge](/deployedge/microsoft-edge-security-identity) of Google Chrome te gebruiken met [ondersteunde](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) [uitbrei dingen](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) die naadloze SSO op webtoepassingen met behulp van de PRT hebben ingeschakeld.

* Voor persoonlijke of bedrijfs eigendom iOS-en Android-apparaten wordt de [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md)geïmplementeerd. Naast de mogelijkheden voor multi-factor Authentication en wacht woord-minder aanmelding biedt de Microsoft Authenticator-app eenmalige aanmelding mogelijk via een systeem eigen toepassing via [brokered Authentication](../develop/msal-android-single-sign-on.md) met minder verificatie vragen voor eind gebruikers.

* Voor persoonlijk of bedrijfs eigendom iOS-en Android-apparaten gebruiken [Mobile Application Management](/mem/intune/apps/app-management) om veilig toegang te krijgen tot bedrijfs resources met minder verificatie aanvragen. 

* [Gebruik de micro soft Enter PRISE SSO-invoeg toepassing voor Apple-apparaten (preview)](../develop/apple-sso-plugin.md). Hiermee wordt het apparaat geregistreerd en vindt u SSO via browser-en systeem eigen Azure AD-toepassingen. 

## <a name="next-steps"></a>Volgende stappen
Flexibiliteits bronnen voor beheerders en architecten
 
* [Tolerantie bouwen met referentie beheer](resilience-in-credentials.md)

* [Een tolerantie bouwen met behulp van voortdurende toegang (CAE)](resilience-with-continuous-access-evaluation.md)

* [Flexibiliteit in externe gebruikers verificatie maken](resilience-b2b-authentication.md)

* [Flexibiliteit in uw hybride verificatie maken](resilience-in-hybrid.md)

* [Tolerantie in toepassings toegang bouwen met toepassings proxy](resilience-on-premises-access.md)


Flexibiliteits bronnen voor ontwikkel aars

* [IAM-tolerantie bouwen in uw toepassingen](resilience-app-development-overview.md)

* [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)