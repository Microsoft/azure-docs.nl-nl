---
title: OAUTH 2,0-verificatie met Azure Active Directory
description: Architectuur richtlijnen voor het bereiken van OAUTH 2,0-verificatie met Azure Active Directory.
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
ms.openlocfilehash: c1604d79ce5eb9949028cd677b340bf3d4b09f6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172836"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>OAuth 2,0-verificatie met Azure Active Directory

De OAuth 2,0 is het branche protocol voor autorisatie. Hiermee kan een gebruiker beperkte toegang tot de beveiligde bronnen verlenen. OAuth is ontworpen om speciaal te werken met Hypertext Transfer Protocol (HTTP), scheidt de rol van de client van de eigenaar van de resource. De client vraagt om toegang tot de resources die worden beheerd door de resource-eigenaar en worden gehost door de bron server. De resource Server verleent toegangs tokens met de goed keuring van de resource-eigenaar. De client gebruikt de toegangs tokens voor toegang tot de beveiligde resources die worden gehost door de bron server. 

OAuth 2,0 is rechtstreeks gerelateerd aan OpenID Connect Connect (OIDC). Omdat OIDC een verificatie-en autorisatie laag is die is gebouwd op OAuth 2,0, is deze niet achterwaarts compatibel met OAuth 1,0. Azure Active Directory (Azure AD) ondersteunt alle OAuth 2,0-stromen. 

## <a name="use-when"></a>Gebruiken wanneer:

Voor uitgebreide client & moderne app-scenario's en REST Web API-toegang.

![Diagram van architectuur](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: vraagt een service aan bij de webtoepassing (app). De gebruiker is doorgaans de eigenaar van de resource die eigenaar is van de gegevens en heeft de kracht om clients toegang te geven tot de gegevens of de resource. 

* **Webbrowser**: de webbrowser waarmee de gebruiker communiceert, is de OAuth-client. 

* **Web-app**: de web-app of de resource server is waar de resource of gegevens zich bevinden. Het vertrouwt de autorisatie server om de OAuth-client veilig te verifiëren en te autoriseren. 

* **Azure AD**: Azure AD is de autorisatie server, ook wel bekend als de ID-provider (IDP). U kunt alles veilig afhandelen met de gegevens van de gebruiker, hun toegang en de vertrouwens relatie. Het is verantwoordelijk voor het uitgeven van de tokens die toegang tot bronnen verlenen en intrekken.

## <a name="implement-oauth-20-with-azure-ad"></a>OAuth 2,0 implementeren met Azure AD

* [Toepassingen integreren met Azure AD](../saas-apps/tutorial-list.md) 

* [OAuth 2,0 en OpenID Connect Connect protocollen op het micro soft Identity-platform](../develop/active-directory-v2-protocols.md) 

* [Toepassings typen en OAuth2](../develop/v2-app-types.md) 

