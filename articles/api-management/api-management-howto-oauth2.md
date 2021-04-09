---
title: Ontwikkelaars accounts met OAuth 2,0 in API Management autoriseren
titleSuffix: Azure API Management
description: Meer informatie over het autoriseren van gebruikers met OAuth 2,0 in API Management. OAuth 2,0 beveiligt de API zodat gebruikers alleen toegang hebben tot bronnen waarvoor ze recht hebben.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2020
ms.author: apimpm
ms.openlocfilehash: fae4e349d46425c0c2b2b923d6a61e2e588708c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077248"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Ontwikkelaars accounts met OAuth 2,0 in azure API Management autoriseren

Veel Api's ondersteunen [OAuth 2,0](https://oauth.net/2/) om de API te beveiligen en ervoor te zorgen dat alleen geldige gebruikers toegang hebben, en ze hebben alleen toegang tot bronnen waarvoor ze recht hebben. Als u de interactieve ontwikkelaars console van Azure API Management met dergelijke Api's wilt gebruiken, kunt u met de service uw service-exemplaar configureren om te werken met uw OAuth 2,0 ingeschakelde API.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Vereisten

In deze hand leiding wordt beschreven hoe u uw API Management service-exemplaar configureert voor het gebruik van OAuth 2,0-autorisatie voor ontwikkelaars accounts, maar wordt niet weer gegeven hoe u een OAuth 2,0-provider configureert. De configuratie voor elke OAuth 2,0-provider verschilt, hoewel de stappen vergelijkbaar zijn, en de vereiste gegevens die worden gebruikt voor het configureren van OAuth 2,0 in uw API Management service-exemplaar zijn hetzelfde. In dit onderwerp worden voor beelden van het gebruik van Azure Active Directory als een OAuth 2,0-Provider weer gegeven.

> [!NOTE]
> Zie het voor beeld van de [webapp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] voor meer informatie over het configureren van OAuth 2,0 met behulp van Azure Active Directory.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Een OAuth 2,0-autorisatie server configureren in API Management

> [!NOTE]
> Als u nog geen API Management service-exemplaar hebt gemaakt, raadpleegt u [een API Management service-exemplaar maken][Create an API Management service instance].

1. Klik op het tabblad OAuth 2,0 in het menu aan de linkerkant en klik op **+ toevoegen**.

    ![OAuth 2,0-menu](./media/api-management-howto-oauth2/oauth-01.png)

2. Voer een naam en een optionele beschrijving in de velden **naam** en **Beschrijving** in.

    > [!NOTE]
    > Deze velden worden gebruikt voor het identificeren van de OAuth 2,0-autorisatie server binnen het huidige API Management service-exemplaar en hun waarden zijn niet afkomstig van de OAuth 2,0-server.

3. Voer de **URL voor de registratie pagina** van de client in. Op deze pagina kunnen gebruikers hun accounts maken en beheren. Dit is afhankelijk van de gebruikte OAuth 2,0-provider. De **URL voor de registratie pagina** van de client verwijst naar de pagina die gebruikers kunnen gebruiken om hun eigen accounts te maken en te configureren voor OAuth 2,0-providers die gebruikers beheer van accounts ondersteunen, bijvoorbeeld `https://contoso.com/login` . Sommige organisaties configureren of gebruiken deze functionaliteit, zelfs niet als de OAuth 2,0-provider dit ondersteunt. Als uw OAuth 2,0-provider geen gebruikers beheer van accounts heeft geconfigureerd, voert u hier een URL voor de tijdelijke aanduiding in, zoals de URL van uw bedrijf of een URL, zoals `https://placeholder.contoso.com` .

    ![OAuth 2,0-nieuwe server](./media/api-management-howto-oauth2/oauth-02.png)

4. De volgende sectie van het formulier bevat de **autorisatie subsidie typen**, de **autorisatie-eind punt-URL** en de instellingen voor de **Autorisatie aanvraag** .

    Geef de **typen autorisatie subsidie** op door de gewenste typen te controleren. **Autorisatie code** is standaard opgegeven.

    Voer de **URL van het autorisatie-eind punt** in. Voor Azure Active Directory is deze URL vergelijkbaar met de volgende URL, waar `<tenant_id>` wordt vervangen door de id van uw Azure AD-Tenant.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    De **methode autorisatie aanvraag** geeft aan hoe de autorisatie aanvraag wordt verzonden naar de OAuth 2,0-server. **Get** is standaard geselecteerd.

5. Vervolgens moet u de URL van het **token-eind punt**, de **client verificatie methoden**, de **Verzend methode voor het toegangs token** en het **standaard bereik** opgeven.

    ![Scherm opname van het venster OAuth2-service toevoegen.](./media/api-management-howto-oauth2/oauth-03.png)

    Voor een Azure Active Directory OAuth 2,0-server heeft de **eind punt-URL** van het token de volgende indeling, waarbij `<TenantID>`  de indeling heeft `yourapp.onmicrosoft.com` .

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    De standaard instelling voor **client verificatie methoden** is **Basic** en de  **methode voor het verzenden van toegangs tokens** is **autorisatie-header**. Deze waarden worden geconfigureerd in deze sectie van het formulier, samen met het **standaard bereik**.

6. De sectie **client referenties** bevat de **client-id** en het **client geheim** die tijdens het maken en configureren van uw OAuth 2,0-server worden verkregen. Zodra de **client-id** en het **client geheim** zijn opgegeven, wordt de **redirect_uri** voor de **autorisatie code** gegenereerd. Deze URI wordt gebruikt voor het configureren van de antwoord-URL in uw OAuth 2,0-server configuratie.

    In de nieuwe ontwikkelaars Portal is het URI-achtervoegsel de vorm:

    - `/signin-oauth/code/callback/{authServerName}` voor de overdracht van autorisatie code toewijzen
    - `/signin-oauth/implicit/callback` voor impliciete toekennings stroom

    ![Scherm opname van de locatie waar de client referenties voor de nieuwe OAuth2-service worden toegevoegd.](./media/api-management-howto-oauth2/oauth-04.png)

    Als **type autorisatie toekenning** is ingesteld op **wacht woord** van de resource-eigenaar, wordt de sectie referenties van de **resource-eigenaar wacht woord** gebruikt om die referenties op te geven. zo niet, dan kunt u dit veld leeg laten.

    Zodra het formulier is voltooid, klikt u op **maken** om de API Management OAuth 2,0-autorisatie server configuratie op te slaan. Zodra de configuratie van de server is opgeslagen, kunt u Api's configureren voor het gebruik van deze configuratie, zoals wordt weer gegeven in de volgende sectie.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Een API configureren voor het gebruik van OAuth 2,0-gebruikers autorisatie

1. Klik op **api's** in het menu **API Management** aan de linkerkant.

    ![OAuth 2,0-Api's](./media/api-management-howto-oauth2/oauth-05.png)

2. Klik op de naam van de gewenste API en klik op **instellingen**. Ga naar de sectie **beveiliging** en schakel het selectie vakje **OAuth 2,0** in.

    ![OAuth 2,0-instellingen](./media/api-management-howto-oauth2/oauth-06.png)

3. Selecteer de gewenste **autorisatie server** in de vervolg keuzelijst en klik op **Opslaan**.

    ![Scherm opname van de geselecteerde autorisatie server en de knop Opslaan.](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Verouderde ontwikkelaars portal: de OAuth 2,0-gebruikers autorisatie testen

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Nadat u uw OAuth 2,0-autorisatie server hebt geconfigureerd en uw API hebt geconfigureerd voor het gebruik van die server, kunt u deze testen door naar de ontwikkelaars portal te gaan en een API aan te roepen. Klik op **ontwikkelaars Portal (verouderd)** in het bovenste menu van de pagina **overzicht** van Azure API Management-instantie.

Klik op **api's** in het bovenste menu en selecteer **echo-API**.

![Echo-API][api-management-apis-echo-api]

> [!NOTE]
> Als er slechts één API is geconfigureerd of zichtbaar is voor uw account, gaat u wanneer u op API's klikt rechtstreeks naar de bewerkingen voor die API.

Selecteer de bewerking **resource ophalen** , klik op **console openen** en selecteer vervolgens **autorisatie code** in de vervolg keuzelijst.

![Console openen][api-management-open-console]

Wanneer **autorisatie code** is geselecteerd, wordt er een pop-upvenster weer gegeven met de aanmeldings vorm van de OAuth 2,0-provider. In dit voor beeld wordt het aanmeldings formulier verschaft door Azure Active Directory.

> [!NOTE]
> Als u pop-ups hebt uitgeschakeld, wordt u gevraagd deze in te scha kelen door de browser. Nadat u ze hebt ingeschakeld, selecteert u de **autorisatie code** opnieuw en wordt het aanmeldings formulier weer gegeven.

![Aanmelden][api-management-oauth2-signin]

Wanneer u bent aangemeld, worden de **aanvraag headers** gevuld met een `Authorization : Bearer` koptekst die de aanvraag toestaat.

![Token aanvraag header][api-management-request-header-token]

Op dit moment kunt u de gewenste waarden voor de overige para meters configureren en de aanvraag indienen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het volgende video en het bijbehorende [artikel](api-management-howto-protect-backend-with-aad.md)voor meer informatie over het gebruik van OAuth 2,0 en API management.

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
