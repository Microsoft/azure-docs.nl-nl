---
title: Een id-provider toevoegen-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het toevoegen van een id-provider aan uw Active Directory B2C-Tenant.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 01/08/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 5c45342524a0300f1c67339f27aa905eb3dc79db
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033368"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Een id-provider toevoegen aan uw Azure Active Directory B2C-Tenant

U kunt Azure AD B2C configureren zodat gebruikers zich kunnen aanmelden bij uw toepassing met referenties van externe id-providers voor sociale netwerken of voor ondernemingen (IdP). Azure AD B2C ondersteunt externe id-providers zoals Facebook, Microsoft-account, Google, Twitter en iedere andere id-provider die ondersteuning biedt voor OAuth 1.0, OAuth 2.0, OpenID Connect en SAML-protocollen.

Met federatie van externe id-providers kunt u uw consumenten de mogelijkheid bieden om zich aan te melden met hun bestaande accounts voor sociale netwerken of onderneming, zonder dat u een nieuw account hoeft te maken voor alleen uw toepassing.

Op de registratie- of aanmeldingspagina wordt door Azure AD B2C een lijst weergegeven met externe id-providers waaruit de gebruiker kan kiezen om zich aan te melden. Zodra ze een van de externe id-providers hebben geselecteerd, worden ze omgeleid naar de website van de geselecteerde provider om daar het aanmeldingsproces te voltooien. Nadat de gebruiker zich heeft aangemeld, keert deze terug naar Azure AD B2C voor verificatie van het account in uw toepassing.

![Voorbeeld van mobiele aanmelding met een account voor sociale netwerken (Facebook)](media/add-identity-provider/external-idp.png)

U kunt met behulp van Azure Portal id-providers aan uw [gebruikers stromen](user-flow-overview.md) toevoegen die worden ondersteund door Azure Active Directory B2C (Azure AD B2C). U kunt ook id-providers toevoegen aan uw [aangepaste beleids regels](custom-policy-get-started.md).

## <a name="select-an-identity-provider"></a>Een id-provider selecteren

Gewoonlijk gebruikt u slechts één id-provider in uw toepassingen, maar u kunt er eventueel meer toevoegen. In de onderstaande artikelen vindt u informatie over het maken van de ID-provider toepassing, het toevoegen van de ID-provider aan uw Tenant en het toevoegen van de ID-provider aan uw gebruikers stroom of aangepast beleid.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Azure AD (één tenant)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (multitenant)](identity-provider-azure-ad-multi-tenant.md)
* [Facebook](identity-provider-facebook.md)
* [Algemene id-providers](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Microsoft-account](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Sales Force (SAML-Protocol)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
