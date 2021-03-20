---
title: Ontwikkelaars accounts met behulp van Azure Active Directory B2C autoriseren
titleSuffix: Azure API Management
description: Meer informatie over het autoriseren van gebruikers met behulp van Azure Active Directory B2C in API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7b586edd7adce8bcea61419005a3ce8cfc814fb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013537"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Ontwikkelaarsaccounts autoriseren met behulp van Azure Active Directory B2C in Azure API Management

## <a name="overview"></a>Overzicht

Azure Active Directory B2C is een oplossing voor het beheer van Cloud identiteit voor webtoepassingen en mobiele toepassingen. U kunt deze gebruiken voor het beheren van toegang tot uw ontwikkelaars Portal. In deze hand leiding vindt u de configuratie die vereist is in uw API Management-service om te integreren met Azure Active Directory B2C. Zie voor meer informatie over het inschakelen van toegang tot de ontwikkelaars Portal met klassieke Azure Active Directory, [ontwikkelaars accounts autoriseren met behulp van Azure Active Directory].

> [!NOTE]
> Als u de stappen in deze hand leiding wilt uitvoeren, moet u eerst een Azure Active Directory B2C-Tenant hebben om een toepassing te maken in. U moet ook het aanmeldings-en aanmeldings beleid voor bereid zijn. Zie [Azure Active Directory B2C Overview]voor meer informatie.

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Ontwikkelaars accounts met behulp van Azure Active Directory B2C autoriseren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en zoek uw API Management exemplaar om aan de slag te gaan.

   > [!NOTE]
   > Als u nog geen API Management service-exemplaar hebt gemaakt, raadpleegt u [een API Management service-exemplaar maken][Create an API Management service instance] in de [zelf studie aan de slag met Azure API Management][Get started with Azure API Management].

1. Onder **identiteiten**. Klik bovenaan op **+ toevoegen** .

   Het deel venster **ID-provider toevoegen** wordt aan de rechter kant weer gegeven. Kies **Azure Active Directory B2C**.
    
   ![AAD B2C toevoegen als id-provider][api-management-howto-add-b2c-identity-provider]

1. Kopieer de **omleidings-URL**.

   ![Omleidings-URL van AAD B2C ID-provider][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Ga op een nieuw tabblad naar uw Azure Active Directory B2C-Tenant in de Azure Portal en open de Blade **toepassingen** .

   ![Een nieuwe toepassing registreren 1][api-management-howto-aad-b2c-portal-menu]

1. Klik op de knop **toevoegen** om een nieuwe Azure Active Directory B2C-toepassing te maken.

   ![Een nieuwe toepassing registreren 2][api-management-howto-aad-b2c-add-button]

1. Voer op de Blade **nieuwe toepassing** een naam in voor de toepassing. Kies **Ja** onder **Web-app/Web-API** en kies **Ja** onder **impliciete stroom toestaan**. Plak vervolgens de **omleidings-URL** die u in stap 3 hebt gekopieerd in het tekstvak **antwoord-URL** .

   ![Een nieuwe toepassing registreren 3][api-management-howto-aad-b2c-app-details]

1. Als u de nieuwe ontwikkelaars Portal gebruikt (niet de verouderde ontwikkelaars Portal), neemt u in de toepassings claims de **opgegeven naam** **en de** **object-id** van de gebruiker op.

    ![Toepassingsclaims](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Klik op de knop **Maken**. Wanneer de toepassing wordt gemaakt, wordt deze weer gegeven op de Blade **toepassingen** . Klik op de naam van de toepassing om de details ervan weer te geven.

   ![Een nieuwe toepassing registreren 4][api-management-howto-aad-b2c-app-created]

1. Kopieer de **toepassings-id** van de Blade **Eigenschappen** naar het klem bord.

   ![Toepassings-ID 1][api-management-howto-aad-b2c-app-id]

1. Ga terug naar het API Management deel venster id- **provider toevoegen** en plak de id in het tekstvak **client-id** .
    
1.  Ga terug naar de registratie van de B2C-app, klik op de knop **sleutels** en klik vervolgens op **sleutel genereren**. Klik op **Opslaan** om de configuratie op te slaan en de **app-sleutel** weer te geven. Kopieer de sleutel naar het klem bord.

    ![App-sleutel 1][api-management-howto-aad-b2c-app-key]

1.  Ga terug naar het deel venster API Management **ID-provider toevoegen** en plak de sleutel in het tekstvak **client Secret** .
    
1.  Geef de domein naam van de Azure Active Directory B2C Tenant op in de **signin-Tenant**.

1.  Met het veld **Authority** kunt u de AANMELDINGS-URL van Azure AD B2C voor gebruik beheren. Stel de waarde in op **<your_b2c_tenant_name>. b2clogin.com**.

1. Geef het **aanmeldings beleid** en het aanmeldings **beleid** op uit het Tenant beleid voor B2C. U kunt eventueel ook het beleid voor het **bewerken van profielen** en het beleid voor het **opnieuw instellen van wacht woorden** opgeven.

1. Nadat u de gewenste configuratie hebt opgegeven, klikt u op **Opslaan**.

    Nadat de wijzigingen zijn opgeslagen, kunnen ontwikkel aars nieuwe accounts maken en zich aanmelden bij de ontwikkelaars Portal met behulp van Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Ontwikkelaars Portal-verificatie van Azure AD B2C-account toevoegen

Meld u in de ontwikkelaars portal aan met AAD B2C is mogelijk met de **knop Aanmelden: OAuth** -widget. De widget is al opgenomen op de aanmeldings pagina van de standaard inhoud van de ontwikkelaars Portal.

Hoewel een nieuw account automatisch wordt gemaakt wanneer een nieuwe gebruiker zich aanmeldt met AAD B2C, kunt u overwegen om dezelfde widget toe te voegen aan de registratie pagina.

Het **registratie formulier: OAuth** -widget vertegenwoordigt een formulier dat wordt gebruikt om u aan te melden bij OAuth.

> [!IMPORTANT]
> U moet [de portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de Aad-wijzigingen van kracht te laten worden.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Verouderde ontwikkelaars Portal-aanmelden met Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Als u zich wilt aanmelden voor een ontwikkelaars account met behulp van Azure Active Directory B2C, opent u een nieuw browser venster en gaat u naar de ontwikkelaars Portal. Klik op de knop **registreren** .

   ![Ontwikkelaars Portal 1][api-management-howto-aad-b2c-dev-portal]

2. U kunt zich aanmelden met **Azure Active Directory B2C**.

   ![Ontwikkelaars Portal 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. U wordt omgeleid naar het aanmeldings beleid dat u in de vorige sectie hebt geconfigureerd. U kunt zich aanmelden met uw e-mail adres of een van uw bestaande sociale accounts.

   > [!NOTE]
   > Als Azure Active Directory B2C de enige optie is die is ingeschakeld op het tabblad **identiteiten** in de Publisher-Portal, wordt u rechtstreeks omgeleid naar het beleid voor aanmelden.

   ![ontwikkelaarsportal][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Wanneer de aanmelding is voltooid, wordt u teruggeleid naar de ontwikkelaars Portal. U bent nu aangemeld bij de ontwikkelaars portal voor uw API Management service-exemplaar.

    ![Registratie is voltooid][api-management-registration-complete]

## <a name="next-steps"></a>Volgende stappen

*  [Overzicht van Azure Active Directory B2C]
*  [Azure Active Directory B2C: uitbreidbaar beleids raamwerk]
*  [Een Microsoft-account als een id-provider gebruiken in Azure Active Directory B2C]
*  [Een Google-account gebruiken als een id-provider in Azure Active Directory B2C]
*  [Een LinkedIn-account gebruiken als een id-provider in Azure Active Directory B2C]
*  [Een Facebook-account gebruiken als een id-provider in Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Overzicht van Azure Active Directory B2C]: ../active-directory-b2c/overview.md
[Ontwikkelaars accounts met behulp van Azure Active Directory autoriseren]: ./api-management-howto-aad.md
[Azure Active Directory B2C: uitbreidbaar beleids raamwerk]: ../active-directory-b2c/user-flow-overview.md
[Een Microsoft-account als een id-provider gebruiken in Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Een Google-account gebruiken als een id-provider in Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Een Facebook-account gebruiken als een id-provider in Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[Een LinkedIn-account gebruiken als een id-provider in Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
