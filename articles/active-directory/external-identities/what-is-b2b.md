---
title: Wat is B2B-samenwerking in Azure Active Directory?
description: Azure Active Directory B2B-samenwerking ondersteunt gastgebruikerstoegang, zodat u veilig resources kunt delen en samenwerken met externe partners.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/19/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231d3ab81c2376aa8a2a4bf182e1b5e9799e51d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669777"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Wat is gastgebruikerstoegang in Azure Active Directory B2B?

Samenwerking via Azure Active Directory (Azure AD) Business-to-Business (B2B) is een functie in Externe identiteiten waarmee u gastgebruikers kunt uitnodigen om samen te werken met uw organisatie. Met B2B-samenwerking kunt u de toepassingen en services van uw bedrijf veilig delen met gastgebruikers van andere organisaties, zonder controle te verliezen over uw eigen bedrijfsgegevens. Veilig en zeker werken met externe partners, groot of klein, zelfs als ze niet beschikken over de Azure AD of een IT-afdeling. Een eenvoudig uitnodigings- en inwisselproces laat uw partners hun eigen referenties gebruiken voor toegang tot resources van uw bedrijf. Ontwikkelaars kunnen gebruikmaken van Azure AD business-to-business API's voor het aanpassen van het uitnodigingsproces of toepassingen schrijven, zoals selfservice aanmeldingsportals. Raadpleeg [Prijzen voor Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) voor licentie- en prijsinformatie met betrekking tot gastgebruikers.  

> [!IMPORTANT]
> - **Vanaf 4 januari 2021** wordt [ondersteuning voor WebView-aanmelding afgeschaft](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) in Google. Als u gebruikmaakt van Google-federatie of selfserviceregistratie met Gmail, moet u [de compatibiliteit van uw systeemeigen Line-of-Business-toepassingen testen](google-federation.md#deprecation-of-webview-sign-in-support).
> - **Vanaf 2021 oktober** heeft micro soft geen ondersteuning meer voor het aflossen van uitnodigingen door het maken van niet-beheerde Azure AD-accounts en-tenants voor B2B-samenwerkings scenario's. In de voorbereiding raden wij klanten aan om te kiezen voor de [verificatie van de eenmalige wachtwoordcode e-mailen](one-time-passcode.md). We waarderen uw feedback over deze openbare preview-functie en willen graag nog meer manieren te maken om samen te werken.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Samenwerken met een partner met behulp van hun identiteit

Met Azure AD B2B gebruikt de partner hun eigen oplossing voor identiteitsbeheer, zonder externe administratieve overhead voor uw organisatie. Gastgebruikers melden zich met hun eigen werk-, school- of sociale identiteiten aan bij uw apps en services.

- De partner maakt gebruik van hun eigen identiteiten en referenties; Azure AD is niet vereist.
- U hoeft te beheren externe accounts of wachtwoorden.
- U hoeft accounts niet te synchroniseren of account levenscycli niet te beheren.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Gastgebruikers gemakkelijk uitnodigen via de Azure AD-portal

Als beheerder kunt u eenvoudig gastgebruikers toevoegen aan uw organisatie in de Azure Portal.

- [Maak een nieuwe gastgebruiker](b2b-quickstart-add-guest-users-portal.md) in Azure AD, vergelijkbaar met hoe u een nieuwe gebruiker zou toevoegen.
- Wijs gastgebruikers toe aan apps of groepen.
- Verzend een e-mailuitnodiging met een inwisselkoppeling of verzend een directe koppeling naar een app die u wilt delen.

![Schermopname van de invoerpagina Uitnodiging voor nieuwe gastgebruiker](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Gastgebruikers volgen een paar eenvoudige [inwisselstappen](redemption-experience.md) om zich aan te melden.

![Schermopname van de pagina Machtigingen controleren](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Gebruik beleidsrichtlijnen voor het veilig delen van uw apps en services

U kunt een autorisatiebeleid voeren om uw zakelijke inhoud te beveiligen. Beleid voor voorwaardelijke toegang zoals meervoudige verificatie kan worden afgedwongen:

- Op tenantniveau.
- Op toepassingsniveau.
- Voor specifieke gastgebruikers om zakelijke apps en gegevens te beschermen.

![Schermopname van de optie Voorwaardelijke toegang](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Toepassings- en groepseigenaars toestaan om hun eigen gastgebruikers te beheren

U kunt beheer van gastgebruikers overdragen aan toepassingseigenaars zodat ze gastgebruikers rechtstreeks kunnen toevoegen aan een toepassing die ze willen delen, ongeacht of het een Microsoft-toepassing is of niet.

- Administrators stellen selfservice app en groepsbeheer in.
- Niet-administrators gebruiken hun [Toegangsvenster](https://myapps.microsoft.com) om gastgebruikers aan toepassingen of groepen toe te voegen.

![Schermopname van het toegangsvenster voor een gastgebruiker](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>De onboardingervaring voor B2B-gastgebruikers aanpassen

Breng uw externe partners aan boord op manieren die zijn aangepast aan de behoeften van uw organisatie.

- Gebruik [Azure AD-rechtenbeheer](../governance/entitlement-management-overview.md) om beleidsregels te configureren die [de toegang voor externe gebruikers beheren](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- Gebruik de [B2B-samenwerkingsuitnodiging API's](/graph/api/resources/invitation) voor het aanpassen van uw onboarding-ervaringen.

## <a name="integrate-with-identity-providers"></a>Integreren met id-providers

Azure AD biedt ondersteuning voor externe ID-providers, zoals Facebook, Microsoft-accounts, Google- of zakelijke id-providers. U kunt federatie met id-providers instellen zodat uw externe gebruikers zich kunnen aanmelden met hun bestaande sociale of zakelijke accounts in plaats van een nieuw account te maken voor uw toepassing. Meer informatie over [id-providers voor externe identiteiten](identity-providers.md).

![Schermopname van de pagina Id-providers](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>Een self-service-aanmeldings stroom maken

Met een selfservice voor het registreren van gebruikers kunt u een aanmeldingservaring maken voor externe gebruikers die toegang willen ontvangen tot uw apps. Als onderdeel van de registratiestroom kunt u opties bieden voor verschillende sociale of zakelijke id-providers, en informatie over de gebruiker verzamelen. Meer informatie over [selfserviceregistratie en hoe u deze kunt instellen](self-service-sign-up-overview.md).

U kunt ook [API-connectors](api-connectors-overview.md) gebruiken om uw gebruikersstromen voor selfserviceregistratie te integreren met externe cloudsystemen. U kunt onder andere verbinding maken met aangepaste goedkeuringswerkstromen, identiteitsverificatie uitvoeren, door de gebruiker verstrekte informatie valideren.

![Schermopname van de pagina Gebruikersstromen](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Volgende stappen

- [Prijzen van externe identiteiten](external-identities-pricing.md)
- [Gastgebruikers voor B2B-samenwerking toevoegen in de portal](add-users-administrator.md)
- [Het inwisselproces van de uitnodiging begrijpen](redemption-experience.md)