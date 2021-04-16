---
title: Inzicht in gekoppelde aanmelding in Azure Active Directory
description: Inzicht in gekoppelde aanmelding in Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 6ed6f6b69326157573ea043457dbc8d8a3079146
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374571"
---
# <a name="understand-linked-sign-on"></a>Inzicht in gekoppelde aanmelding

In de [quickstartreeks over](view-applications-portal.md) toepassingsbeheer hebt u geleerd hoe u Azure AD gebruikt als id-provider (IdP) voor een toepassing. In de quickstart configureert u eenmalige aanmelding op basis van SAML of OIDC. Een andere optie is **Gekoppeld.** In dit artikel wordt de gekoppelde optie uitgebreider besproken.

Met **de** optie Gekoppeld kunt u de doellocatie configureren wanneer [](https://myapps.microsoft.com/) een gebruiker de app selecteert in de Mijn apps- of Office 365-portal van uw organisatie.

Enkele veelvoorkomende scenario's waarbij de koppelingsoptie waardevol is, zijn:
- Voeg een koppeling toe naar een aangepaste webtoepassing die momenteel gebruikmaakt van federatie, zoals Active Directory Federation Services (AD FS).
- Voeg dieptekoppelingen toe naar specifieke SharePoint-pagina's of andere webpagina's die u alleen wilt zien op de toegangspanels van uw gebruiker.
- Voeg een koppeling toe aan een app waarvoor geen verificatie is vereist. 
 
 De **optie** Gekoppeld biedt geen aanmeldingsfunctionaliteit via Azure AD-referenties. U kunt echter nog steeds enkele van de andere functies van **Enterprise-toepassingen gebruiken.** U kunt bijvoorbeeld auditlogboeken gebruiken en een aangepast logo en een aangepaste app-naam toevoegen.

## <a name="before-you-begin"></a>Voordat u begint

Als u snel kennis wilt op doen, doorloop dan de [quickstartreeks](view-applications-portal.md) over toepassingsbeheer. In de quickstart, waarin u een een aanmelding configureert, vindt u ook de **optie** Gekoppeld. 

De **optie** Gekoppeld biedt geen aanmeldingsfunctionaliteit via Azure AD. Met de optie stelt u in naar welke locatie [](https://myapps.microsoft.com/) gebruikers worden verzonden wanneer ze de app op Mijn apps of het start Microsoft 365 app starten.  Omdat de aanmelding geen aanmeldingsfunctionaliteit biedt via Azure AD, is voorwaardelijke toegang niet beschikbaar voor toepassingen die zijn geconfigureerd met Gekoppelde een aanmelding.

> [!IMPORTANT] 
> Er zijn enkele scenario's waarin de optie **voor een een aanmelding** niet in de navigatie voor een toepassing in **Bedrijfstoepassingen staat.** 
>
> Als de toepassing is geregistreerd met **behulp App-registraties** is de mogelijkheid voor een enkele aanmelding standaard ingesteld om OIDC OAuth te gebruiken. In dit geval wordt **de optie Voor een aanmelding** niet in de navigatie onder **Bedrijfstoepassingen weer geven.** Wanneer u **App-registraties** aangepaste app toevoegt, configureert u opties in het manifestbestand. Zie voor meer informatie over het manifestbestand [Azure Active Directory app-manifest.](../develop/reference-app-manifest.md) Zie Verificatie en autorisatie met Microsoft Identity Platform voor meer informatie over [SSO-standaarden.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Andere scenario's waarbij een een aanmelding ontbreekt in de navigatie, zijn onder andere wanneer een toepassing wordt gehost in een andere tenant of als uw account niet over de vereiste machtigingen (globale beheerder, Cloud Application Administrator, toepassingsbeheerder of eigenaar van de **service-principal)** is. Machtigingen kunnen ook een scenario veroorzaken waarin u een **een aanmelding kunt** openen, maar niet kunt opslaan. Zie voor meer informatie over Azure AD-beheerdersrollen. https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

### <a name="configure-link"></a>Koppeling configureren

Als u een koppeling voor een app wilt instellen, selecteert **u Gekoppeld** op de pagina **Een aanmelding.** Voer vervolgens de koppeling in en selecteer **Opslaan.** Wilt u weten waar u deze opties kunt vinden? Bekijk de [quickstart-serie](view-applications-portal.md).
 
Nadat u een app hebt geconfigureerd, wijst u er gebruikers en groepen aan toe. Wanneer u gebruikers toewijst, kunt u bepalen wanneer de toepassing wordt weergegeven [op](https://myapps.microsoft.com/) Mijn apps of Microsoft 365 starter van de app.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](./assign-user-or-group-access-portal.md)
- [Automatische inrichting van gebruikersaccounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
