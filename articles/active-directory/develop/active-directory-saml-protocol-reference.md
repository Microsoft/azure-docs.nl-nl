---
title: Hoe het micro soft-identiteits platform het SAML-protocol gebruikt
description: In dit artikel vindt u een overzicht van de enkelvoudige Sign-On en single Sign-Out SAML-profielen in Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 4e9d63b8fe7fc281a87deb27ddadd794e14fa04d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755637"
---
# <a name="how-the-microsoft-identity-platform-uses-the-saml-protocol"></a>Hoe het micro soft-identiteits platform het SAML-protocol gebruikt

Het micro soft Identity-platform maakt gebruik van het SAML 2,0-protocol om toepassingen in staat te stellen een eenmalige aanmelding voor hun gebruikers te bieden. Met de SAML-profielen eenmalige [aanmelding](single-sign-on-saml-protocol.md) en [eenmalige afmelding](single-sign-out-saml-protocol.md) van Azure AD wordt uitgelegd hoe SAML-beweringen,-protocollen en-bindingen worden gebruikt in de id-Provider service.

Voor het SAML-protocol zijn de ID-provider (micro soft Identity platform) en de service provider (de toepassing) vereist voor het uitwisselen van informatie over zichzelf.

Wanneer een toepassing is geregistreerd bij Azure AD, registreert de app-ontwikkelaar aan de Federatie gerelateerde informatie met Azure AD. Deze informatie omvat de **omleidings-URI** en de **meta gegevens-URI** van de toepassing.

Het micro soft Identity-platform gebruikt de **meta gegevens-URI** van de Cloud service om de handtekening sleutel en de afmeldings-URI op te halen. De klant kan de app openen in **Azure AD-> app-registratie** en vervolgens in **instellingen-> eigenschappen**, kunnen ze de afmeldings-URL bijwerken. Op die manier kan het micro soft Identity-platform het antwoord naar de juiste URL verzenden. 

Azure Active Directory stelt Tenant-specifieke en algemene (Tenant onafhankelijke) eenmalige aanmelding en eind punten voor eenmalige afmeldingen in. Deze Url's vertegenwoordigen adresseer bare locaties: ze zijn niet alleen id's, dus u kunt naar het eind punt gaan om de meta gegevens te lezen.

* Het Tenant-specifieke eind punt bevindt zich op `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . De *\<TenantDomainName>* tijdelijke aanduiding vertegenwoordigt een geregistreerde domein naam of TenantID-GUID van een Azure AD-Tenant. De federatieve meta gegevens van de contoso.com-Tenant bevindt zich bijvoorbeeld op: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Het Tenant-onafhankelijke eind punt bevindt zich op `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . In dit adres van het eind punt wordt **common** weer gegeven in plaats van de domein naam of-id van een Tenant.

Zie [federatieve meta gegevens](../azuread-dev/azure-ad-federation-metadata.md)voor meer informatie over de federatieve meta gegevens documenten die door Azure AD worden gepubliceerd.
