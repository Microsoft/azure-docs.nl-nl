---
title: Een Azure API Management-API beveiligen met behulp van Azure Active Directory B2C
description: Meer informatie over het gebruik van toegangs tokens die zijn uitgegeven door Azure Active Directory B2C voor het beveiligen van een Azure API Management API-eind punt.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60b7bb33dfbf29b7e448887ce992d03009133b2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953485"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Een Azure API Management-API beveiligen met Azure AD B2C

Meer informatie over het beperken van toegang tot uw Azure API Management-API (APIM) op clients die zijn geverifieerd met Azure Active Directory B2C (Azure AD B2C). Volg de stappen in dit artikel voor het maken en testen van een inkomend beleid in APIM die de toegang beperkt tot alleen de aanvragen die een geldig door Azure AD B2C verleend toegangs token bevatten.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om door te gaan met de stappen in dit artikel:

* [Azure AD B2C-tenant](tutorial-create-tenant.md)
* [Toepassing](tutorial-register-applications.md) die is geregistreerd in uw tenant
* [Gebruikersstromen die zijn gemaakt](tutorial-create-user-flows.md) in uw tenant
* [Gepubliceerde API](../api-management/import-and-publish.md) in azure API Management
* [Postman](https://www.getpostman.com/) om beveiligde toegang te testen (optioneel)

## <a name="get-azure-ad-b2c-application-id"></a>Azure AD B2C toepassings-ID ophalen

Wanneer u een API in azure API Management met Azure AD B2C beveiligt, hebt u verschillende waarden nodig voor het [inkomende beleid](../api-management/api-management-howto-policies.md) dat u in APIM maakt. Noteer eerst de toepassings-ID van een toepassing die u eerder hebt gemaakt in uw Azure AD B2C-Tenant. Als u de toepassing gebruikt die u in de vereisten hebt gemaakt, gebruikt u de toepassings-ID voor *webbapp1*.

Als u een toepassing wilt registreren in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **app-registraties** en selecteer vervolgens het tabblad **toepassingen in eigendom** .
1. Noteer de waarde in de kolom **Application (client) ID** voor *webapp1* of een andere toepassing die u eerder hebt gemaakt.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer onder **beheren** de optie **toepassingen (verouderd)**.
1. Noteer de waarde in de kolom **toepassings-id** voor *webapp1* of een andere toepassing die u eerder hebt gemaakt.

* * *

## <a name="get-token-issuer-endpoint"></a>Eind punt van de token Uitgever ophalen

Haal vervolgens de bekende configuratie-URL op voor een van uw Azure AD B2C gebruikers stromen. U hebt ook de token uitgever-eind punt-URI nodig die u wilt ondersteunen in azure API Management.

1. Blader naar uw Azure AD B2C-Tenant in de [Azure Portal](https://portal.azure.com).
1. Selecteer onder **Beleid** **Gebruikersstromen**.
1. Selecteer een bestaand beleid, bijvoorbeeld *B2C_1_signupsignin1*, en selecteer vervolgens **gebruikers stroom uitvoeren**.
1. Noteer de URL in de Hyper link die wordt weer gegeven onder de kop **gebruikers stroom uitvoeren** in de buurt van de bovenkant van de pagina. Deze URL is het OpenID Connect Connect-bekende detectie-eind punt voor de gebruikers stroom en u gebruikt dit in de volgende sectie wanneer u het inkomende beleid configureert in azure API Management.

    ![Bekende URI-Hyper link op de pagina nu uitvoeren van de Azure Portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selecteer de Hyper link om te bladeren naar de pagina met de bekende configuratie van de OpenID connect-verbinding.
1. Noteer de waarde op de pagina die in de browser wordt geopend `issuer` , bijvoorbeeld:

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    U gebruikt deze waarde in de volgende sectie bij het configureren van uw API in azure API Management.

Er zijn nu twee Url's vastgelegd voor gebruik in de volgende sectie: de OpenID Connect Connect well-bekende configuratie eind punt-URL en de Issuer-URI. Bijvoorbeeld:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Inkomend beleid configureren in azure API Management

U bent nu klaar om het inkomende beleid toe te voegen aan Azure API Management dat API-aanroepen valideert. Door een [JWT-validatie](../api-management/api-management-access-restriction-policies.md#ValidateJWT) beleid toe te voegen waarmee de doel groep en de verlener in een toegangs token worden geverifieerd, kunt u ervoor zorgen dat alleen API-aanroepen met een geldig token worden geaccepteerd.

1. Blader naar uw Azure API Management-exemplaar in het [Azure Portal](https://portal.azure.com).
1. Selecteer **API's**.
1. Selecteer de API die u met Azure AD B2C wilt beveiligen.
1. Selecteer het tabblad **Ontwerpen**.
1. Onder **inkomende verwerking** selecteert **\</\>** u om de beleids code-editor te openen.
1. Plaats de volgende `<validate-jwt>` tag in het `<inbound>` beleid.

    1. Werk de `url` waarde in het `<openid-config>` element bij met de bekende configuratie-URL van uw beleid.
    1. Werk het `<audience>` element bij met de toepassings-id van de toepassing die u eerder in uw B2C-Tenant hebt gemaakt (bijvoorbeeld *webapp1*).
    1. Werk het `<issuer>` element bij met het token uitgever-eind punt dat u eerder hebt vastgelegd.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Beveiligde API-toegang valideren

Om ervoor te zorgen dat alleen geverifieerde Bellers toegang hebben tot uw API, kunt u uw Azure API Management-configuratie valideren door de API aan te roepen met behulp van [postman](https://www.getpostman.com/).

Als u de API wilt aanroepen, moet u een toegangs token hebben dat is uitgegeven door Azure AD B2C en een APIM-abonnements sleutel.

### <a name="get-an-access-token"></a>Een toegangstoken opvragen

U hebt eerst een token nodig dat is uitgegeven door Azure AD B2C om in de `Authorization` header in postman te gebruiken. U kunt er een ontvangen met behulp van de functie **nu uitvoeren** van uw gebruikers stroom voor aanmelden/aanmelden. u moet een van de vereisten hebben gemaakt.

1. Blader naar uw Azure AD B2C-Tenant in de [Azure Portal](https://portal.azure.com).
1. Selecteer onder **Beleid** **Gebruikersstromen**.
1. Selecteer een bestaande gebruikers stroom voor registreren/aanmelden, bijvoorbeeld *B2C_1_signupsignin1*.
1. Selecteer voor **toepassing** *webapp1*.
1. Kies voor **antwoord**-URL `https://jwt.ms` .
1. Selecteer **Gebruikersstroom uitvoeren**.

    ![De pagina gebruikers stroom uitvoeren voor aanmelding aanmelden gebruikers stroom in Azure Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Voltooi het aanmeldingsproces. U moet worden omgeleid naar `https://jwt.ms` .
1. Registreer de versleutelde token waarde die wordt weer gegeven in uw browser. U gebruikt deze token waarde voor de autorisatie-header in postman.

    ![Versleutelde token waarde wordt weer gegeven op jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Sleutel van API-abonnement ophalen

Een client toepassing (in dit geval Postman) die een gepubliceerde API aanroept, moet een geldige API Management-abonnements sleutel bevatten in de HTTP-aanvragen van de API. Ga als volgt te vraag om een abonnements sleutel op te nemen in uw postman HTTP-aanvraag:

1. Blader naar uw Azure API Management service-exemplaar in de [Azure Portal](https://portal.azure.com).
1. Selecteer **Abonnementen**.
1. Selecteer het beletsel teken voor **product: onbeperkt** en selecteer vervolgens **sleutels weer geven/verbergen**.
1. Registreer de **primaire sleutel** voor het product. U gebruikt deze sleutel voor de `Ocp-Apim-Subscription-Key` header in uw HTTP-aanvraag in postman.

![Pagina abonnements sleutel met de toetsen weer geven/verbergen die zijn geselecteerd in Azure Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Een beveiligde API-aanroep testen

Met het toegangs token en de APIM-abonnements sleutel die u hebt geregistreerd, bent u nu klaar om te testen of u beveiligde toegang tot de API goed hebt geconfigureerd.

1. Maak een nieuwe `GET` aanvraag in het [bericht](https://www.getpostman.com/). Geef voor de aanvraag-URL het eind punt van de lijst met luid sprekers op van de API die u als een van de vereisten hebt gepubliceerd. Bijvoorbeeld:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Voeg vervolgens de volgende headers toe:

    | Sleutel | Waarde |
    | --- | ----- |
    | `Authorization` | Gecodeerde token waarde die u eerder hebt vastgelegd, voorafgegaan door `Bearer ` (de spatie toevoegen na ' Bearer ') |
    | `Ocp-Apim-Subscription-Key` | APIM-abonnements sleutel die u eerder hebt geregistreerd |

    De URL van de **Get** -aanvraag en de **kopteksten** moeten er ongeveer als volgt uitzien:

    ![Postman-gebruikers interface met de URL van de GET-aanvraag en de headers](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selecteer de knop **verzenden** in het bericht om de aanvraag uit te voeren. Als u alles op de juiste manier hebt geconfigureerd, moet u een JSON-antwoord met een verzameling conferentie luidsprekers weer geven (dit is afgekapt):

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Een onveilige API-oproep testen

Nu u een succes volle aanvraag hebt ingediend, test u de fout melding om ervoor te zorgen dat aanroepen naar uw API met een *ongeldig* token worden afgewezen zoals verwacht. Een manier om de test uit te voeren, is door een paar tekens toe te voegen of te wijzigen in de waarde van het token en vervolgens dezelfde `GET` aanvraag als voorheen uit te voeren.

1. Voeg verschillende tekens toe aan de token waarde om een ongeldig token te simuleren. Voeg bijvoorbeeld ' ongeldig ' toe aan de token waarde:

    ![De sectie headers van de gebruikers interface van Postman met ongeldige toevoeging aan token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selecteer de **Verzend** knop om de aanvraag uit te voeren. Met een ongeldig token is het verwachte resultaat een niet- `401` geautoriseerde status code:

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Als u de `401` status code ziet, hebt u gecontroleerd dat alleen bellers met een geldig toegangs token dat is uitgegeven door Azure AD B2C, geslaagde aanvragen kunt indienen voor uw Azure API Management-API.

## <a name="support-multiple-applications-and-issuers"></a>Ondersteuning voor meerdere toepassingen en verleners

Verschillende toepassingen communiceren doorgaans met één REST API. Als u wilt dat uw API tokens accepteert die bedoeld zijn voor meerdere toepassingen, voegt u hun toepassings-Id's toe aan het- `<audiences>` element in het inkomend APIM-beleid.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Als u meerdere token verleners wilt ondersteunen, voegt u hun eind punt-Uri's toe aan het- `<issuers>` element in het APIM-beleid voor binnenkomende verbindingen.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migreren naar b2clogin.com

Als u een APIM-API hebt die tokens valideert die zijn uitgegeven door het verouderde `login.microsoftonline.com` eind punt, moet u de API en de toepassingen die deze aanroepen, gebruiken om tokens te maken die zijn uitgegeven door [b2clogin.com](b2clogin.md).

U kunt dit algemene proces volgen om een gefaseerde migratie uit te voeren:

1. Voeg ondersteuning toe in uw APIM-inkomend beleid voor tokens die zijn uitgegeven door b2clogin.com en login.microsoftonline.com.
1. Werk uw toepassingen een voor een bij om tokens van het b2clogin.com-eind punt te verkrijgen.
1. Wanneer al uw toepassingen tokens op de juiste wijze verkrijgen van b2clogin.com, verwijdert u ondersteuning voor login.microsoftonline.com-uitgegeven tokens uit de API.

In het volgende voor beeld APIM inkomend beleid ziet u hoe tokens kunnen worden geaccepteerd die worden uitgegeven door b2clogin.com en login.microsoftonline.com. Daarnaast ondersteunt de IT API-aanvragen van twee toepassingen.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Volgende stappen

Zie [APIM Policy Reference Index](../api-management/api-management-policies.md)(Engelstalig) voor meer informatie over Azure API management-beleid.

Meer informatie over het migreren van OWIN-gebaseerde web-Api's en hun toepassingen naar b2clogin.com vindt u in [een OWIN-based Web-API migreren naar b2clogin.com](multiple-token-endpoints.md).