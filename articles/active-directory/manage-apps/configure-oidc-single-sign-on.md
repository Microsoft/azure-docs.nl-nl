---
title: Op OIDC gebaseerde eenmalige aanmelding (SSO) voor apps in Azure Active Directory
description: Op OIDC gebaseerde eenmalige aanmelding (SSO) voor apps in Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: iangithinji
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: 990e0c09f8a49b83bc68d7123f5f8146a3551575
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374536"
---
# <a name="understand-oidc-based-single-sign-on"></a>Op OIDC gebaseerde een aanmelding begrijpen
In de [quickstartreeks over](view-applications-portal.md) toepassingsbeheer hebt u geleerd hoe u Azure AD gebruikt als id-provider (IdP) voor een toepassing. In dit artikel wordt gedetailleerder in op apps die gebruikmaken van de OpenID Connect standard voor het implementeren van een een aanmelding. 

## <a name="before-you-begin"></a>Voordat u begint
Het proces van het toevoegen van een app aan Azure Active Directory tenant is afhankelijk van het type een aanmelding van de toepassing die is geïmplementeerd. Zie Opties voor een aanmelding voor meer informatie over de opties voor een aanmelding die beschikbaar zijn voor apps die Azure AD kunnen gebruiken voor [identiteitsbeheer.](sso-options.md) In dit artikel worden apps op basis van OIDC beschreven.


## <a name="basic-oidc-configuration"></a>OIDC-basisconfiguratie
In de [quickstart-reeks](add-application-portal-setup-oidc-sso.md)staat een artikel over het configureren van een een aanmelding. In dit artikel leert u hoe u een op OIDC gebaseerde app toevoegt aan uw Azure-tenant.

Het mooie van het toevoegen van een app die gebruikmaakt van de OIDC-standaard voor een een aanmelding is dat de configuratie minimaal is. Hier is een korte video waarin wordt getoond hoe u een op OIDC gebaseerde app toevoegt aan uw tenant.

Een OIDC-app toevoegen in Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Raadpleeg [Gebruikers- en beheerderstoestemming begrijpen](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) voor meer informatie over gebruikers- of beheerderstoestemming.

## <a name="next-steps"></a>Volgende stappen

- [Quickstartreeks over toepassingsbeheer](add-application-portal-setup-oidc-sso.md)
- [Opties voor eenmalige aanmelding](sso-options.md)
- [Procedure: Een Azure Active Directory-gebruiker aanmelden met behulp van het patroon voor multitenant-toepassingen](../develop/howto-convert-app-to-be-multi-tenant.md)
