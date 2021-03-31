---
title: 'Snelstart: Eenmalige aanmelding (SSO) op basis van SAML instellen voor een toepassing in uw Azure Active Directory-tenant (Azure AD)'
description: In deze snelstart wordt het proces voor het instellen van eenmalige aanmelding (SSO) op basis van SAML voor een toepassing in uw Azure Active Directory-tenant (Azure AD) besproken.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 44a838bdce4721d24bd0bdbb45ac7b36ceb34e71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102488366"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snelstart: Eenmalige aanmelding (SSO) op basis van SAML instellen voor een toepassing in uw Azure Active Directory-tenant (Azure AD)

Ga aan de slag met vereenvoudigde gebruikersaanmeldingen door eenmalige aanmelding (SSO) in te stellen voor een toepassing die u hebt toegevoegd aan uw Azure Active Directory-tenant (Azure AD). Nadat u SSO hebt ingesteld, kunnen uw gebruikers zich aanmelden bij een toepassing met behulp van hun Azure AD-referenties. SSO is opgenomen in de gratis versie van Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u SSO wilt instellen voor een toepassing die u hebt toegevoegd aan uw Azure AD-tenant, hebt u het volgende nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een van de volgende rollen: Globale beheerder, Cloudtoepassingsbeheerder, Toepassingsbeheerder of eigenaar van de service-principal.
- Een toepassing die ondersteuning biedt voor SSO en die al vooraf is geconfigureerd en toegevoegd aan de Azure AD-galerie. De meeste apps kunnen Azure AD gebruiken SSO. De apps in de Azure AD-galerie zijn vooraf geconfigureerd. Als uw app niet wordt vermeld, of een aangepaste ontwikkelde app is, kunt u deze nog steeds gebruiken met Azure AD. Bekijk de zelfstudies en andere documentatie in de inhoudsopgave. Deze quickstart is gericht op apps die vooraf zijn geconfigureerd voor SSO en die zijn toegevoegd aan de Azure AD-galerie door de app-ontwikkelaars.
- Optioneel: Voltooiing van [Uw apps weergeven](view-applications-portal.md).
- Optioneel: Voltooiing van [Een app toevoegen](add-application-portal.md).
- Optioneel: Voltooiing van [Een app configureren](add-application-portal-configure.md).
- Optioneel: Voltooiing van [Gebruikers toewijzen aan een app](add-application-portal-assign-users.md).


>[!IMPORTANT]
>Gebruik een niet-productieomgeving om de stappen in deze quickstart te testen.

## <a name="enable-single-sign-on-for-an-app"></a>Eenmalige aanmelding inschakelen voor een app

Nadat u klaar bent met het toevoegen van een toepassing aan uw Azure AD-tenant, verschijnt de overzichtspagina. Als u een toepassing wilt configureren die al is toegevoegd, bekijk dan de eerste quickstart. U krijgt een overzicht te zien van de toepassingen die aan uw tenant zijn toegevoegd.

Eenmalige aanmelding voor een toepassing instellen:

1. Selecteer in het Azure AD-portal **Bedrijfstoepassingen**. Zoek en selecteer de toepassing die u wilt instellen voor eenmalige aanmelding.
1. Selecteer **Eenmalige aanmelding** in de sectie **Beheren** om het deelvenster **Eenmalige aanmelding** te openen voor bewerken.

    > [!IMPORTANT]
    > Als de app gebruikmaakt van de standaard OpenID Connect (OIDC) voor eenmalige aanmelding, ziet u geen optie voor eenmalige aanmelding in de navigatie. Raadpleeg de snelstart over eenmalige aanmelding op basis van OIDC voor meer informatie over hoe u deze kunt instellen.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Schermopname toont de configuratiepagina voor eenmalige aanmelding in het Azure AD-portal.":::

1. Selecteer **SAML** om de SSO-configuratiepagina te openen. In dit voorbeeld is GitHub de toepassing die wordt geconfigureerd voor eenmalige aanmelding. Nadat GitHub is ingesteld, kunnen uw gebruikers zich aanmelden bij GitHub met behulp van hun referenties van onze Azure AD-tenant.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Schermopname toont de configuratiepagina voor eenmalige aanmelding op GitHub.":::

1. Het proces voor het configureren van een toepassing om Azure AD te gebruiken voor SSO op basis van SAML is afhankelijk van de toepassing. Er staat koppeling naar de richtlijnen voor GitHub. Zie [Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](/azure/active-directory/saas-apps/) voor handleidingen voor andere apps.
1. Volg de handleiding om SSO in te stellen voor de toepassing. Veel toepassingen hebben specifieke abonnementsvereisten voor SSO-functionaliteit. GitHub vereist bijvoorbeeld een Enterprise-abonnement.
    > [!TIP]
    > Raadpleeg [eenmalige aanmelding op basis van SAML configureren](configure-saml-single-sign-on.md) voor meer informatie over de configuratieopties voor SAML.

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Schermopname toont de optie voor eenmalige aanmelding in het Enterprise-abonnement op de pagina met GitHub-prijzen.":::

> [!TIP]
> U kunt het beheer van apps automatiseren met behulp van de Graph API. Zie [App-beheer automatiseren met de Microsoft Graph API](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Resources opschonen

Als u deze quickstart-reeks hebt voltooid, kunt u de app verwijderen om uw testtenant op te schonen. Zie [Een app verwijderen](delete-application-portal.md) in de laatste quickstart in deze reeks voor meer informatie over het verwijderen van een app.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om te lezen hoe u een app verwijdert.
> [!div class="nextstepaction"]
> [Een app verwijderen](delete-application-portal.md)
