---
title: Informatie over eenmalige aanmelding op basis van OIDC (SSO) voor apps in Azure Active Directory
description: Informatie over eenmalige aanmelding op basis van OIDC voor apps in Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: d1acdc47d5a702faf7d5dbd5f2a4ea6826e97981
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033235"
---
# <a name="understand-oidc-based-single-sign-on"></a>Informatie over eenmalige aanmelding op basis van OIDC
In de [Quick](view-applications-portal.md) start-serie op toepassings beheer hebt u geleerd hoe u Azure AD als id-provider (IDP) gebruikt voor een toepassing. In dit artikel vindt u meer informatie over apps die gebruikmaken van de OpenID Connect Connect-standaard voor het implementeren van eenmalige aanmelding. 

## <a name="before-you-begin"></a>Voordat u begint
Het proces voor het toevoegen van een app aan uw Azure Active Directory-Tenant is afhankelijk van het type eenmalige aanmelding van de toepassing die is geïmplementeerd. Zie [Opties voor eenmalige aanmelding](sso-options.md)voor meer informatie over de opties voor eenmalige aanmelding die beschikbaar zijn voor apps waarvoor Azure AD kan worden gebruikt voor identiteits beheer. Dit artikel heeft betrekking op OIDC-apps.


## <a name="basic-oidc-configuration"></a>Basis configuratie van OIDC
In de [Quick Start serie](add-application-portal-setup-oidc-sso.md)is er een artikel over het configureren van eenmalige aanmelding. Hierin leert u hoe u een op OIDC gebaseerde app kunt toevoegen aan uw Azure-Tenant.

Het mooie van het toevoegen van een app die gebruikmaakt van de OIDC-standaard voor eenmalige aanmelding is dat de configuratie mini maal is. Hier volgt een korte video waarin wordt weer gegeven hoe u een OIDC-app toevoegt aan uw Tenant.

Een op OIDC gebaseerde app toevoegen in Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Zie [Wat is toestemming van gebruikers en beheerders](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)? voor meer informatie over de toestemming van de gebruiker en de beheerder.

## <a name="next-steps"></a>Volgende stappen

- [Quickstartreeks over toepassingsbeheer](add-application-portal-setup-oidc-sso.md)
- [Opties voor eenmalige aanmelding](sso-options.md)
- [Procedure: Een Azure Active Directory-gebruiker aanmelden met behulp van het patroon voor multitenant-toepassingen](../develop/howto-convert-app-to-be-multi-tenant.md)
