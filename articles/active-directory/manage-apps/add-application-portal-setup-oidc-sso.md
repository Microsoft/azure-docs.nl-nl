---
title: 'Quickstart: Eenmalige aanmelding (SSO) op basis van OIDC instellen voor een toepassing in uw Azure Active Directory-tenant (Azure AD)'
description: In deze quickstart wordt het proces voor het instellen van eenmalige aanmelding (SSO) op basis van OIDC voor een toepassing in uw Azure Active Directory-tenant (Azure AD) besproken.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: e5695d5ded92e9bb41d39a082790cfbb456dbc38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258351"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Eenmalige aanmelding (SSO) op basis van OIDC instellen voor een toepassing in uw Azure Active Directory-tenant (Azure AD)

Ga aan de slag met vereenvoudigde gebruikersaanmeldingen door eenmalige aanmelding (SSO) in te stellen voor een toepassing die u hebt toegevoegd aan uw Azure Active Directory-tenant (Azure AD). Nadat u SSO hebt ingesteld, kunnen uw gebruikers zich aanmelden bij een toepassing met behulp van hun Azure AD-referenties. SSO is opgenomen in de gratis versie van Azure AD.

Raadpleeg [Eenmalige aanmelding op basis van OIDC begrijpen](configure-oidc-single-sign-on.md) voor meer informatie over SSO op basis van OIDC.

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

Wanneer u een app toevoegt die gebruikmaakt van de OIDC-standaard voor SSO, krijgt u een installatieknop te zien. Wanneer u deze knop selecteert, gaat u naar de site van de toepassing om het registratieproces voor de app te voltooien. Het proces voor het toevoegen van een app wordt behandeld in de quickstart Een app toevoegen eerder in deze serie. Als u een toepassing wilt configureren die al is toegevoegd, bekijk dan de eerste quickstart. U krijgt een overzicht te zien van de toepassingen die al aan uw tenant zijn toegevoegd. 

Eenmalige aanmelding voor een toepassing instellen:

1. In de quickstart eerder in deze serie hebt u geleerd hoe u een app kunt toevoegen die uw Azure AD-Tenant gebruikt voor identiteitsbeheer. Als de app-ontwikkelaar de OIDC-standaard heeft gebruikt om SSO te implementeren, wordt er een registratieknop weergegeven wanneer u de app toevoegt. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Schermopname toont de optie voor eenmalige aanmelding en de registratieknop." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Selecteer **Registreren** om naar de aanmeldingspagina van de app-ontwikkelaar te gaan. Meld u aan met uw aanmeldingsreferenties voor Azure Active Directory. 

   > [!IMPORTANT]
    > Als u al een abonnement voor de toepassing hebt, worden de gebruikersgegevens en tenant/directory-gegevens gevalideerd. Als de toepassing de gebruiker niet kan verifiëren, wordt u omgeleid naar de registratiepagina van de toepassingsservice of krijgt u een foutpagina te zien.

3. Na een geslaagde verificatie wordt een dialoogvenster weer gegeven waarin u wordt gevraagd om toestemming van de beheerder. Selecteer **Toestemming namens uw organisatie** en selecteer **Accepteren**. Raadpleeg [Gebruikers- en beheerderstoestemming begrijpen](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) voor meer informatie over gebruikers- of beheerderstoestemming.

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Schermopname toont het toestemmingsscherm voor een app." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. De toepassing wordt toegevoegd aan uw tenant en de startpagina van de toepassing wordt weergegeven.


> [!TIP]
> U kunt het beheer van apps automatiseren met behulp van de Graph API. Zie [App-beheer automatiseren met de Microsoft Graph API](/graph/application-saml-sso-configure-api).

Hier is een video die u stapsgewijs meeneemt door wat aanvullende details over het toevoegen van een OIDC-app aan Azure AD.

Een OIDC-app toevoegen in Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze quickstart-reeks hebt voltooid, kunt u de app verwijderen om uw testtenant op te schonen. Zie [Een app verwijderen](delete-application-portal.md) in de laatste quickstart in deze reeks voor meer informatie over het verwijderen van een app.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om te lezen hoe u een app verwijdert.
> [!div class="nextstepaction"]
> [Een app verwijderen](delete-application-portal.md)
