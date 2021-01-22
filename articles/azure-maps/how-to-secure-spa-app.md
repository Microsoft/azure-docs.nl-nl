---
title: Een toepassing met één pagina beveiligen met niet-interactieve aanmelding
titleSuffix: Azure Maps
description: Het configureren van een toepassing met één pagina met niet-interactief Azure op rollen gebaseerd toegangs beheer (Azure RBAC) en Azure Maps Web-SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 3b647a02731a4fa404aab4bb648853eea766d92e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684069"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Een toepassing met één pagina beveiligen met niet-interactieve aanmelding

De volgende hand leiding heeft betrekking op een toepassing die Azure Active Directory (Azure AD) gebruikt om een toegangs token te bieden voor het Azure Maps van toepassingen wanneer de gebruiker zich niet kan aanmelden bij Azure AD. Deze stroom vereist hosting van een webservice die moet worden beveiligd om alleen te worden geopend door de webtoepassing met één pagina. Er zijn meerdere implementaties die verificatie kunnen uitvoeren voor Azure AD. In deze hand leiding wordt gebruikgemaakt van het product, de Azure-functie voor het verkrijgen van toegangs tokens.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps biedt ondersteuning voor toegangs tokens van aanmeldings-en interactieve stromen van gebruikers. Interactieve stromen maken een meer beperkt bereik van toegang tot intrekking en geheim beheer mogelijk.

## <a name="create-azure-function"></a>Een Azure-functie maken

Maak een beveiligde web service-toepassing die verantwoordelijk is voor verificatie bij Azure AD. 

1. Maak een functie in de Azure Portal. Zie [Azure function maken](../azure-functions/functions-get-started.md)voor meer informatie.

2. Configureer het CORS-beleid voor de Azure-functie zodat deze toegankelijk is voor de webtoepassing met één pagina. Hiermee worden browser-clients beveiligd tot de toegestane oorsprong van uw webtoepassing. Zie [CORS-functionaliteit toevoegen](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. [Voeg een door het systeem toegewezen identiteit toe](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) aan de Azure-functie om het maken van een Service-Principal in te scha kelen voor verificatie bij Azure AD.  

4. Ken op rollen gebaseerde toegang toe voor de door het systeem toegewezen identiteit aan het Azure Maps-account. Zie [toegang verlenen op basis van rollen](#grant-role-based-access) voor meer informatie.

5. Schrijf code voor de Azure-functie om Azure Maps toegangs tokens te verkrijgen met behulp van een door het systeem toegewezen identiteit met een van de ondersteunde mechanismen of het REST-protocol. Zie [tokens voor Azure-resources verkrijgen](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    Voor beeld van een voor beeld van een REST-Protocol:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Voorbeeld antwoord:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Beveiliging configureren voor de Azure function-http trigger

   * [Een functie toegangs sleutel maken](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * [SSL-eind punt](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) voor de Azure-functie in productie.
   
7. Webtoepassing Web Application Azure Maps Web-SDK configureren. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Op rollen gebaseerde toegang verlenen

U verleent op *rollen gebaseerd toegangs beheer voor Azure (Azure RBAC)* door de door het systeem toegewezen identiteit toe te wijzen aan een of meer Azure-functie definities. Als u de definities van Azure-functies wilt weer geven die beschikbaar zijn voor Azure Maps, gaat u naar **toegangs beheer (IAM)**. Selecteer **rollen** en zoek vervolgens naar rollen die beginnen met *Azure Maps*.

1. Ga naar uw **Azure Maps-account**. Selecteer de roltoewijzing van **toegangs beheer (IAM)**  >  .

    > [!div class="mx-imgBorder"]
    > ![Toegang verlenen met behulp van Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Selecteer op **het tabblad roltoewijzingen,** onder **rol**, een ingebouwde Azure Maps roldefinitie, zoals **Azure Maps gegevens lezer** of **Azure Maps gegevensinzender**. Selecteer **functie-app** onder **toegang toewijzen aan**. Selecteer de principal op naam. Selecteer vervolgens **Opslaan**.

   * Zie de Details voor het [toevoegen of verwijderen van roltoewijzingen](../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Azure Maps ingebouwde roldefinities bieden een zeer grote autorisatie toegang tot veel Azure Maps REST-Api's. Zie [een aangepaste roldefinitie maken en de door het systeem toegewezen identiteit toewijzen](../role-based-access-control/custom-roles.md) aan de definitie van de aangepaste rol om api's toegang tot een minimum te beperken. Hiermee wordt de mini maal benodigde bevoegdheden voor toegang tot Azure Maps door de toepassing ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over een toepassings scenario met één pagina:
> [!div class="nextstepaction"]
> [Toepassing met één pagina](../active-directory/develop/scenario-spa-overview.md)

Zoek de metrische gegevens over het API-gebruik voor uw Azure Maps account:
> [!div class="nextstepaction"]
> [Metrische gegevens over het gebruik weergeven](how-to-view-api-usage.md)

Verken andere voor beelden die laten zien hoe u Azure AD integreert met Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps-voor beelden](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)