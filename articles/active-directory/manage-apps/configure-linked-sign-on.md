---
title: Informatie over gekoppelde aanmelding in Azure Active Directory
description: Meer informatie over gekoppelde aanmelding in Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 3e2d3ab6a23ce588c3aa393e5096ac75e88a5365
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255278"
---
# <a name="understand-linked-sign-on"></a>Informatie over gekoppelde aanmelding

In de [Quick](view-applications-portal.md) start-serie op toepassings beheer hebt u geleerd hoe u Azure AD als id-provider (IDP) gebruikt voor een toepassing. In de hand leiding Snelstartgids configureert u op SAML gebaseerde SSO-of OIDC. Een andere optie is **gekoppeld**. In dit artikel vindt u meer informatie over de gekoppelde optie.

Met de optie **gekoppeld** kunt u de doel locatie configureren wanneer een gebruiker de app selecteert in [mijn Apps](https://myapps.microsoft.com/) of Office 365-portal van uw organisatie.

Enkele veelvoorkomende scenario's waarbij de koppelings optie waardevol is:
- Voeg een koppeling toe naar een aangepaste webtoepassing die momenteel gebruikmaakt van Federatie, zoals Active Directory Federation Services (AD FS).
- Voeg diepe koppelingen toe aan specifieke share point-pagina's of andere webpagina's die u alleen op de toegangs Vensters van uw gebruiker wilt weer geven.
- Voeg een koppeling toe aan een app waarvoor geen verificatie is vereist. 
 
 De optie **gekoppeld** biedt geen aanmeldings functionaliteit via Azure AD-referenties. Maar u kunt nog steeds een deel van de andere functies van **bedrijfs toepassingen** gebruiken. U kunt bijvoorbeeld audit Logboeken gebruiken en een aangepast logo en de naam van een app toevoegen.

## <a name="before-you-begin"></a>Voordat u begint

Volg de Quick Start- [serie](view-applications-portal.md) op toepassings beheer om kennis snel te vinden. In de Quick Start, waar u eenmalige aanmelding configureert, vindt u ook de optie **gekoppeld** . 

De optie **gekoppeld** biedt geen aanmeldings functionaliteit via Azure AD. Met deze optie stelt u alleen de locatie gebruikers worden verzonden naar wanneer ze de app selecteren in [mijn apps](https://myapps.microsoft.com/) of het start programma voor apps van de Microsoft 365.  Omdat het aanmelden geen aanmeldings functionaliteit biedt via Azure AD, is voorwaardelijke toegang niet beschikbaar voor toepassingen die zijn geconfigureerd met een gekoppelde eenmalige aanmelding.

> [!IMPORTANT] 
> Er zijn enkele scenario's waarbij de optie voor **eenmalige aanmelding** niet voor komt in de navigatie voor een toepassing in **bedrijfs toepassingen**. 
>
> Als de toepassing is geregistreerd met behulp van **app-registraties** , is de functionaliteit voor eenmalige aanmelding standaard ingesteld op het gebruik van OIDC OAuth. In dit geval wordt de optie **voor eenmalige aanmelding** niet weer gegeven in de navigatie onder **bedrijfs toepassingen**. Wanneer u **app-registraties** gebruikt om uw aangepaste app toe te voegen, configureert u de opties in het manifest bestand. Zie [Azure Active Directory app-manifest](../develop/reference-app-manifest.md)voor meer informatie over het manifest bestand. Zie [verificatie en autorisatie met behulp van micro soft Identity platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)voor meer informatie over SSO-standaarden. 
>
> Andere scenario's waarin **eenmalige aanmelding** ontbreekt in de navigatie, zijn, wanneer een toepassing wordt gehost in een andere Tenant of als uw account niet over de vereiste machtigingen (globale beheerder, Cloud toepassings beheerder, toepassings beheerder of eigenaar van de service-principal) beschikt. Machtigingen kunnen ook leiden tot een scenario waarin u **eenmalige aanmelding** kunt openen, maar niet kunt opslaan. Zie (voor meer informatie over Azure AD-beheerders rollen https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Koppeling configureren

Als u een koppeling voor een app wilt instellen, selecteert u **gekoppeld** op de pagina voor **eenmalige aanmelding** . Vervolgens voert u de koppeling in en selecteert u **Opslaan**. Hebt u een herinnering nodig om deze opties te vinden? Bekijk de Quick Start- [serie](view-applications-portal.md).
 
Nadat u een app hebt geconfigureerd, kunt u er gebruikers en groepen aan toewijzen. Wanneer u gebruikers toewijst, kunt u bepalen wanneer de toepassing wordt weer gegeven in [mijn apps](https://myapps.microsoft.com/) of het start programma voor apps van Microsoft 365.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](./assign-user-or-group-access-portal.md)
- [Automatische inrichting van gebruikersaccounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
