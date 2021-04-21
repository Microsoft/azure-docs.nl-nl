---
title: Geavanceerd gebruik van AuthN/AuthZ
description: Meer informatie over het aanpassen van de verificatie- en autorisatiefunctie in App Service voor verschillende scenario's en het verkrijgen van gebruikersclaims en verschillende tokens.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 9335bb62e494fab50f7beadf3d7bbc423d80cf14
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775723"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Geavanceerd gebruik van verificatie en autorisatie in Azure App Service

In dit artikel wordt beschreven hoe u de ingebouwde verificatie en autorisatie [in App Service](overview-authentication-authorization.md)kunt aanpassen en identiteit kunt beheren vanuit uw toepassing. 

Zie een van de volgende zelfstudies om snel aan de slag te gaan:

* [Zelfstudie: Zelfstudie: Gebruikers eind-tot-eind verifiëren en autoriseren in Azure App Service](tutorial-auth-aad.md)
* [Uw app configureren voor aanmelding met Microsoft Identity Platform](configure-authentication-provider-aad.md)
* [Uw app configureren voor aanmelding met Facebook](configure-authentication-provider-facebook.md)
* [Uw app configureren voor aanmelding met Google](configure-authentication-provider-google.md)
* [Uw app configureren voor aanmelding met Twitter](configure-authentication-provider-twitter.md)
* [Uw app configureren om u aan te melden met een OpenID Connect provider (preview)](configure-authentication-provider-openid-connect.md)
* [Uw app configureren om u aan te melden met een aanmelding met Apple (preview)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Meerdere aanmeldingsproviders gebruiken

De portalconfiguratie biedt geen gebruikssleutel voor het aanbieden van meerdere aanmeldingsproviders aan uw gebruikers (zoals Facebook en Twitter). Het is echter niet moeilijk om de functionaliteit toe te voegen aan uw app. De stappen worden als volgt beschreven:

Configureer eerst op **de pagina** Verificatie/autorisatie in Azure Portal de identiteitsprovider die u wilt inschakelen.

Selecteer **anonieme aanvragen toestaan (geen actie)** in Actie die moet worden ondernomen wanneer de aanvraag niet is **geverifieerd.**

Voeg op de aanmeldingspagina, de navigatiebalk of een andere locatie van uw app een aanmeldingskoppeling toe aan elk van de providers die u hebt ingeschakeld ( `/.auth/login/<provider>` ). Bijvoorbeeld:

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Wanneer de gebruiker op een van de koppelingen klikt, wordt de desbetreffende aanmeldingspagina geopend om de gebruiker aan te melden.

Als u de gebruiker na aanmelding wilt omleiden naar een aangepaste URL, gebruikt u de querytekenreeksparameter (niet te verwarrend met de omleidings-URI in de configuratie van uw `post_login_redirect_url` id-provider). Als u bijvoorbeeld na het aanmelden naar de gebruiker wilt `/Home/Index` navigeren, gebruikt u de volgende HTML-code:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Tokens van providers valideren

In een clientgestuurde aanmelding meldt de toepassing de gebruiker handmatig aan bij de provider en verstuurt de toepassing het verificatie-token vervolgens naar App Service voor validatie (zie [Verificatiestroom).](overview-authentication-authorization.md#authentication-flow) Deze validatie zelf verleent u geen toegang tot de gewenste app-resources, maar een geslaagde validatie geeft u een sessie-token dat u kunt gebruiken voor toegang tot app-resources. 

Als u het provider-token wilt valideren, App Service de app eerst worden geconfigureerd met de gewenste provider. Nadat u tijdens runtime het verificatie-token van uw provider hebt opgehaald, plaatst u het token op `/.auth/login/<provider>` voor validatie. Bijvoorbeeld: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

De tokenindeling varieert enigszins, afhankelijk van de provider. Zie de volgende tabel voor meer informatie:

| Providerwaarde | Vereist in aanvraag body | Opmerkingen |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | De `expires_in` eigenschap is optioneel. <br/>Wanneer u het token bij liveservices aanvraagt, moet u altijd het bereik `wl.basic` aanvragen. |
| `google` | `{"id_token":"<id_token>"}` | De `authorization_code` eigenschap is optioneel. Wanneer dit is opgegeven, kan deze eventueel ook worden vergezeld van de `redirect_uri` eigenschap . |
| `facebook`| `{"access_token":"<user_access_token>"}` | Gebruik een geldig [token voor gebruikerstoegang](https://developers.facebook.com/docs/facebook-login/access-tokens) van Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Als het token van de provider is gevalideerd, retourneert de API met een in de `authenticationToken` antwoord-body, wat uw sessie-token is. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Zodra u dit sessie-token hebt, hebt u toegang tot beveiligde app-resources door de header toe te `X-ZUMO-AUTH` voegen aan uw HTTP-aanvragen. Bijvoorbeeld: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Meld u af bij een sessie

Gebruikers kunnen een aanmelding initiëren door een aanvraag naar het eindpunt van de `GET` app `/.auth/logout` te verzenden. De `GET` aanvraag doet het volgende:

- Verificatiecookies in de huidige sessie gewed.
- Hiermee verwijdert u de tokens van de huidige gebruiker uit de tokenopslag.
- Voor Azure Active Directory en Google voert een aanmelding aan de serverzijde uit bij de id-provider.

Dit is een voorbeeld van een eenvoudige afmeldingskoppeling op een webpagina:

```html
<a href="/.auth/logout">Sign out</a>
```

Bij een geslaagde aanmelding wordt de client standaard omgeleid naar de URL `/.auth/logout/done` . U kunt de omleidingspagina na de aanmelding wijzigen door de `post_logout_redirect_uri` queryparameter toe te voegen. Bijvoorbeeld:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Het is raadzaam om de [waarde van te](https://wikipedia.org/wiki/Percent-encoding) coderen. `post_logout_redirect_uri`

Wanneer u volledig gekwalificeerde URL's gebruikt, moet de URL worden gehost in hetzelfde domein of worden geconfigureerd als een toegestane externe omleidings-URL voor uw app. In het volgende voorbeeld wordt omgeleid `https://myexternalurl.com` naar die niet wordt gehost in hetzelfde domein:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Voer de volgende opdracht uit in [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-fragmenten behouden

Nadat gebruikers zich bij uw app hebben aanmelden, willen ze meestal worden omgeleid naar dezelfde sectie van dezelfde pagina, zoals `/wiki/Main_Page#SectionZ` . Omdat [URL-fragmenten](https://wikipedia.org/wiki/Fragment_identifier) (bijvoorbeeld ) echter nooit naar de server worden verzonden, worden ze niet standaard bewaard nadat de OAuth-aanmelding is voltooid en teruggeleid naar uw `#SectionZ` app. Gebruikers krijgen vervolgens een suboptimale ervaring wanneer ze opnieuw naar het gewenste anker moeten navigeren. Deze beperking geldt voor alle verificatieoplossingen aan de serverzijde.

In App Service verificatie kunt u URL-fragmenten behouden in de OAuth-aanmelding. Hiervoor stelt u een app-instelling met de naam `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` in op `true` . U kunt dit doen in de [Azure Portal](https://portal.azure.com)of gewoon de volgende opdracht uitvoeren in de [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Toegang tot gebruikersclaims

App Service geeft gebruikersclaims door aan uw toepassing met behulp van speciale headers. Externe aanvragen mogen deze headers niet instellen, dus ze zijn alleen aanwezig als ze zijn ingesteld door App Service. Enkele voorbeeldheaders zijn:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Code die in elke taal of elk framework is geschreven, kan de informatie die nodig is uit deze headers halen. Voor ASP.NET 4.6-apps wordt **ClaimsPrincipal** automatisch ingesteld met de juiste waarden. ASP.NET Core biedt echter geen verificatie-middleware die kan worden geïntegreerd met App Service gebruikersclaims. Zie [MaximeRilir.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)voor een tijdelijke oplossing.

Als de [tokenopslag](overview-authentication-authorization.md#token-store) is ingeschakeld voor uw app, kunt u ook aanvullende informatie over de geverifieerde gebruiker verkrijgen door aan te `/.auth/me` roepen. De Mobile Apps server-SDK's bieden helpermethoden om met deze gegevens te werken. Zie De [Azure Mobile Apps Node.js SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)gebruiken en Werken met de [SDK voor de .NET-back-Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)voor meer informatie.

## <a name="retrieve-tokens-in-app-code"></a>Tokens ophalen in app-code

Vanuit de servercode worden de providerspecifieke tokens in de aanvraagheader geïnjecteerd, zodat u deze eenvoudig kunt openen. De volgende tabel bevat mogelijke tokenheadernamen:

| Provider | Headernamen |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Verzend vanuit uw clientcode (zoals een mobiele app of in-browser JavaScript) een HTTP-aanvraag `GET` naar `/.auth/me` ([token store](overview-authentication-authorization.md#token-store) moet zijn ingeschakeld). De geretourneerde JSON heeft de providerspecifieke tokens.

> [!NOTE]
> Toegangstokens zijn voor toegang tot providerbronnen, dus ze zijn alleen aanwezig als u uw provider configureert met een clientgeheim. Zie Toegangstokens vernieuwen voor meer informatie over het op halen van vernieuwingstokens.

## <a name="refresh-identity-provider-tokens"></a>Tokens van id-provider vernieuwen

Wanneer het toegangsteken van uw provider (niet het [sessie-token](#extend-session-token-expiration-grace-period)) verloopt, moet u de gebruiker opnieuw autoreren voordat u dat token opnieuw gebruikt. U kunt voorkomen dat het token verloopt door het eindpunt van `GET` uw toepassing aan te `/.auth/refresh` roepen. Wanneer deze wordt App Service, worden de toegangstokens in het [tokenopslag](overview-authentication-authorization.md#token-store) voor de geverifieerde gebruiker automatisch vernieuwd. Volgende aanvragen voor tokens door uw app-code ontvangen de vernieuwde tokens. Het tokenopslag moet echter vernieuwingstokens [](https://auth0.com/learn/refresh-tokens/) voor uw provider bevatten om het token te kunnen vernieuwen. De manier om vernieuwingstokens op te halen, wordt door elke provider gedocumenteerd, maar de volgende lijst is een korte samenvatting:

- **Google:** een queryreeksparameter `access_type=offline` toevoegen aan uw `/.auth/login/google` API-aanroep. Als u de Mobile Apps SDK gebruikt, kunt u de parameter toevoegen aan een van de `LogicAsync` overloads (zie [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook:** biedt geen vernieuwingstokens. Tokens met een lange duur verlopen binnen 60 dagen (zie Vervaldatum en uitbreiding van [toegangstokens voor Facebook).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter:** toegangstokens verlopen niet (zie [Veelgestelde vragen over Twitter OAuth](https://developer.twitter.com/en/docs/authentication/faq)).
- **Azure Active Directory:** in [https://resources.azure.com](https://resources.azure.com) gaat u als volgt te werk:
    1. Selecteer bovenaan de pagina **Lezen/Schrijven.**
    2. Navigeer in de linkerbrowser naar abonnementen **>** **_\<subscription\_name_** > **resourceGroups** > ** **_ \<resource\_group\_name> _> **providers**  >  **Microsoft.Web**  >  **sites** > **_ \<app\_name> _** > **config**  >  **authsettings**. 
    3. Klik op **Bewerken**.
    4. Wijzig de volgende eigenschap. Vervang _\<app\_id>_ door de Azure Active Directory toepassings-id van de service die u wilt openen.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klik **op Put.** 

Zodra uw provider is geconfigureerd, kunt u het [vernieuwingstoken](#retrieve-tokens-in-app-code) en de verlooptijd voor het toegangstoken vinden in de tokenopslag. 

Als u uw toegangs token op elk moment wilt vernieuwen, roept u `/.auth/refresh` aan in elke taal. In het volgende codefragment wordt jQuery gebruikt om uw toegangstokens van een JavaScript-client te vernieuwen.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Als een gebruiker de machtigingen ingetrokken die aan uw app zijn verleend, kan uw aanroep `/.auth/me` naar mislukken met een `403 Forbidden` antwoord. Als u fouten wilt diagnosticeren, controleert u de toepassingslogboeken voor meer informatie.

## <a name="extend-session-token-expiration-grace-period"></a>Respijtperiode voor verlopen sessie token uitbreiden

De geverifieerde sessie verloopt na 8 uur. Nadat een geverifieerde sessie is verlopen, is er standaard een respijtperiode van 72 uur. Binnen deze respijtperiode kunt u het sessie token vernieuwen met App Service zonder de gebruiker opnieuw te autoriëren. U kunt gewoon aanroepen wanneer uw sessie-token ongeldig wordt en u de vervaldatum van `/.auth/refresh` het token niet zelf hoeft bij te houden. Zodra de respijtperiode van 72 uur is verstreken, moet de gebruiker zich opnieuw aanmelden om een geldig sessie-token op te halen.

Als 72 uur niet voldoende tijd voor u is, kunt u dit verloopvenster verlengen. Het verlengen van de verlooptijd gedurende een lange periode kan aanzienlijke gevolgen hebben voor de beveiliging (bijvoorbeeld wanneer een verificatie-token wordt gelekt of gestolen). Laat deze dus op de standaardwaarde van 72 uur staan of stel de extensieperiode in op de kleinste waarde.

Als u het standaardverloopvenster wilt uitbreiden, moet u de volgende opdracht uitvoeren in [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> De respijtperiode is alleen van toepassing op App Service geverifieerde sessie, niet op de tokens van de id-providers. Er is geen respijtperiode voor de verlopen providertokens. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Het domein van aanmeldingsaccounts beperken

Met zowel een Microsoft-Azure Active Directory kunt u zich aanmelden vanuit meerdere domeinen. Microsoft-accounts kunnen bijvoorbeeld _outlook.com,_ _live.com_ en hotmail.com accounts.  Azure AD staat een groot aantal aangepaste domeinen toe voor de aanmeldingsaccounts. Mogelijk wilt u uw gebruikers echter versnellen naar uw eigen Azure AD-aanmeldingspagina (zoals `contoso.com` ). Volg deze stappen om de domeinnaam van de aanmeldingsaccounts voor te stellen.

Navigeer in naar abonnementen [https://resources.azure.com](https://resources.azure.com) > **_\<subscription\_name_** > **resourceGroepen** > **_  \<resource\_group\_name> _** **>-providers**  >  **Microsoft.Websites**  >   > **_ \<app\_name> _** >   >  **config authsettings**. 

Klik **op Bewerken,** wijzig de volgende eigenschap en klik vervolgens op **Put.** Zorg ervoor dat u vervangt _\<domain\_name>_ door het domein dat u wilt.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Met deze instelling wordt de queryreeksparameter `domain_hint` aan de omleidings-URL voor aanmeldingen toevoegen. 

> [!IMPORTANT]
> Het is mogelijk dat de client de parameter verwijdert nadat de omleidings-URL is ontvangen en zich vervolgens bij `domain_hint` een ander domein kan aanmelden. Hoewel deze functie handig is, is het geen beveiligingsfunctie.
>

## <a name="authorize-or-deny-users"></a>Gebruikers machtigen of weigeren

Hoewel App Service de eenvoudigste autorisatieaanvraag afwijst (dat wil zeggen niet-niet-geautheseerde aanvragen afwijzen), vereist uw app mogelijk een fijner autorisatiegedrag, zoals het beperken van de toegang tot alleen een specifieke groep gebruikers. In bepaalde gevallen moet u aangepaste toepassingscode schrijven om toegang tot de aangemelde gebruiker toe te staan of te weigeren. In andere gevallen kan App Service of uw id-provider helpen zonder dat er codewijzigingen nodig zijn.

- [Serverniveau](#server-level-windows-apps-only)
- [Niveau van identiteitsprovider](#identity-provider-level)
- [Toepassingsniveau](#application-level)

### <a name="server-level-windows-apps-only"></a>Serverniveau (alleen Windows-apps)

Voor elke Windows-app kunt u het autorisatiegedrag van de IIS-webserver definiëren door hetWeb.config *bewerken.* Linux-apps maken geen gebruik van IIS en kunnen niet worden geconfigureerd via *Web.config*.

1. Ga naar `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. Navigeer in de browserverkenner App Service bestanden naar *site/wwwroot.* Als er *Web.config* bestaat, maakt u deze door **+**  >  **Nieuw bestand te selecteren.** 

1. Selecteer het potlood om *Web.config* te bewerken. Voeg de volgende configuratiecode toe en klik op **Opslaan.** Als *Web.config* al bestaat, voegt u het `<authorization>` element toe met alles er in. Voeg de accounts toe die u wilt toestaan in het `<allow>` -element.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Niveau van identiteitsprovider

De id-provider kan bepaalde gebruikssleutelautorisatie bieden. Bijvoorbeeld:

- Voor [Azure App Service](configure-authentication-provider-aad.md)kunt u [toegang op ondernemingsniveau rechtstreeks](../active-directory/manage-apps/what-is-access-management.md) in Azure AD beheren. Zie How [to remove a user's access to an application (De](../active-directory/manage-apps/methods-for-removing-user-access.md)toegang van een gebruiker tot een toepassing verwijderen) voor instructies.
- Voor [Google](configure-authentication-provider-google.md)kunnen Google API-projecten die deel uitmaken van een organisatie worden geconfigureerd om alleen toegang toe te staan aan gebruikers in uw organisatie (zie de pagina [](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) [ **OAuth 2.0-ondersteuning**](https://support.google.com/cloud/answer/6158849?hl=en)instellen van Google).

### <a name="application-level"></a>Toepassingsniveau

Als een van de andere niveaus niet de autorisatie biedt die u nodig hebt, of als uw platform of id-provider niet wordt ondersteund, moet u aangepaste code schrijven om gebruikers te autorisaties op basis van de [gebruikersclaims.](#access-user-claims)

## <a name="updating-the-configuration-version"></a>De configuratieversie bijwerken

Er zijn twee versies van de beheer-API voor de functie Verificatie/autorisatie. De versie V2 is vereist voor de verificatie-ervaring in Azure Portal. Een app die al gebruik maakt van de V1-API kan upgraden naar de versie V2 zodra er enkele wijzigingen zijn aangebracht. Met name geheime configuratie moet worden verplaatst naar site-sticky toepassingsinstellingen. Dit kan automatisch worden gedaan vanuit de sectie Verificatie van de portal voor uw app.

> [!WARNING]
> Bij migratie naar V2 wordt het beheer van de functie App Service-verificatie/autorisatie voor uw toepassing uitgeschakeld via sommige clients, zoals de bestaande ervaring in de Azure Portal, Azure CLI en Azure PowerShell. Dit kan niet worden omgekeerd.

De V2-API biedt geen ondersteuning voor het maken of bewerken van een Microsoft-account als een afzonderlijke provider, zoals is gedaan in V1. In plaats van het geconvergeerde [Microsoft Identity Platform](../active-directory/develop/v2-overview.md) wordt het gebruikt om gebruikers aan te melden met zowel Azure AD als persoonlijke Microsoft-accounts. Wanneer u overschakelt naar de V2-API, wordt de V1 Azure Active Directory gebruikt om de Microsoft Identity Platform-provider te configureren. De provider van het Microsoft-account van V1 wordt voortgezet in het migratieproces en blijft gewoon werken, maar het wordt aanbevolen om over te stappen op het nieuwere Microsoft Identity Platform-model. Zie [Ondersteuning voor registraties van Microsoft-accountproviders](#support-for-microsoft-account-provider-registrations) voor meer informatie.

Het geautomatiseerde migratieproces verplaatst providergeheimen naar toepassingsinstellingen en converteert vervolgens de rest van de configuratie naar de nieuwe indeling. De automatische migratie gebruiken:

1. Navigeer naar uw app in de portal en selecteer de **menuoptie** Verificatie.
1. Als de app is geconfigureerd met het V1-model, ziet u de **knop Upgraden.**
1. Bekijk de beschrijving in de bevestigingsprompt. Als u klaar bent om de migratie uit te voeren, klikt u in **de** prompt op Upgraden.

### <a name="manually-managing-the-migration"></a>De migratie handmatig beheren

Met de volgende stappen kunt u de toepassing handmatig migreren naar de V2-API als u de hierboven genoemde automatische versie niet wilt gebruiken.

#### <a name="moving-secrets-to-application-settings"></a>Geheimen verplaatsen naar toepassingsinstellingen

1. Haal uw bestaande configuratie op met behulp van de V1-API:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   Noteer in de resulterende JSON-nettolading de geheime waarde die wordt gebruikt voor elke provider die u hebt geconfigureerd:

   * Aad: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Microsoft-account: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > De geheime waarden zijn belangrijke beveiligingsreferenties en moeten zorgvuldig worden verwerkt. Deel deze waarden niet en behoudt ze niet op een lokale computer.

1. Maak site-sticky toepassingsinstellingen voor elke geheime waarde. U kunt de naam van elke toepassingsinstelling kiezen. De waarde moet overeenkomen met wat u in de vorige stap hebt verkregen of verwijzen naar een [Key Vault](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) geheim dat u met die waarde hebt gemaakt.

   Als u de instelling wilt maken, kunt u de Azure Portal of een variant van het volgende uitvoeren voor elke provider:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > De toepassingsinstellingen voor deze configuratie moeten worden gemarkeerd als site-sticky, wat betekent dat ze niet worden verplaatst tussen omgevingen tijdens een [sitewisselingsbewerking.](./deploy-staging-slots.md) Dit komt doordat uw verificatieconfiguratie zelf is gekoppeld aan de omgeving. 

1. Maak een nieuw JSON-bestand met de naam `authsettings.json` . Neem de uitvoer die u eerder hebt ontvangen en verwijder elke geheime waarde ervan. Schrijf de resterende uitvoer naar het bestand en zorg ervoor dat er geen geheim is opgenomen. In sommige gevallen kan de configuratie matrices bevatten die lege tekenreeksen bevatten. Zorg ervoor dat `microsoftAccountOAuthScopes` dat niet gebeurt, en als dit het doet, schakelt u die waarde over naar `null` .

1. Voeg een eigenschap toe `authsettings.json` die naar de naam van de toepassingsinstelling wijst die u eerder voor elke provider hebt gemaakt:
 
   * Aad: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Microsoft-account: `microsoftAccountClientSecretSettingName`

   Een voorbeeldbestand na deze bewerking kan er ongeveer als volgt uitzien, in dit geval alleen geconfigureerd voor AAD:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Verzend dit bestand als de nieuwe verificatie-/autorisatieconfiguratie voor uw app:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Controleer of uw app na deze beweging nog steeds werkt zoals verwacht.

1. Verwijder het bestand dat u in de vorige stappen hebt gebruikt.

U hebt de app nu gemigreerd om geheimen van id-provider op te slaan als toepassingsinstellingen.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Ondersteuning voor registraties van Microsoft-accountproviders

Als uw bestaande configuratie een Microsoft-accountprovider bevat en geen Azure Active Directory-provider bevat, kunt u de configuratie overschakelen naar de Azure Active Directory-provider en vervolgens de migratie uitvoeren. Om dit te doen:

1. Ga naar [**App-registraties**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in de Azure Portal en zoek de registratie die is gekoppeld aan uw Microsoft-accountprovider. Deze staat mogelijk onder de kop Toepassingen van persoonlijk account.
1. Navigeer naar de pagina Verificatie voor de registratie. Onder Omleidings-URI's ziet u een vermelding die eindigt op `/.auth/login/microsoftaccount/callback` . Kopieer deze URI.
1. Voeg een nieuwe URI toe die overeenkomt met de URI die u zojuist hebt gekopieerd, maar laat deze eindigen op `/.auth/login/aad/callback` . Hierdoor kan de registratie worden gebruikt door de configuratie App Service verificatie/autorisatie.
1. Navigeer naar App Service verificatie-/autorisatieconfiguratie voor uw app.
1. Verzamel de configuratie voor de Microsoft-accountprovider.
1. Configureer de Azure Active Directory-provider met behulp van de beheermodus Geavanceerd, door de client-id en clientgeheimwaarden op te geven die u in de vorige stap hebt verzameld. Voor de URL van de vergever gebruikt u Gebruiken en vervangt u door het verificatie-eindpunt voor uw cloudomgeving (bijvoorbeeld " " voor globale Azure), en vervangt u ook door uw `<authentication-endpoint>/<tenant-id>/v2.0` *\<authentication-endpoint>* [](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) https://login.microsoftonline.com *\<tenant-id>* **directory-id (tenant)**.
1. Nadat u de configuratie hebt opgeslagen, test u de aanmeldingsstroom door in uw browser naar het eindpunt op uw site te gaan en `/.auth/login/aad` de aanmeldingsstroom te voltooien.
1. Op dit moment hebt u de configuratie gekopieerd, maar blijft de bestaande configuratie van de Microsoft-accountprovider bestaan. Voordat u deze verwijdert, moet u ervoor zorgen dat alle onderdelen van uw app verwijzen naar Azure Active Directory provider via aanmeldingskoppelingen, enzovoort. Controleer of alle onderdelen van uw app werken zoals verwacht.
1. Nadat u hebt gevalideerd dat dingen werken met de AAD-Azure Active Directory provider, kunt u de configuratie van de Microsoft-accountprovider verwijderen.

> [!WARNING]
> Het is mogelijk om de twee registraties te convergeren door de ondersteunde [accounttypen](../active-directory/develop/supported-accounts-validation.md) voor de AAD-app-registratie te wijzigen. Hierdoor wordt echter een nieuwe toestemmingsprompt geforceert voor microsoft-accountgebruikers. De identiteitsclaims van die gebruikers kunnen echter anders zijn in de structuur, met name het wijzigen van waarden omdat er een nieuwe `sub` app-id wordt gebruikt. Deze aanpak wordt niet aanbevolen, tenzij u deze goed begrijpt. Wacht in plaats daarvan op ondersteuning voor de twee registraties in het V2 API-oppervlak.

#### <a name="switching-to-v2"></a>Overschakelen naar V2

Nadat de bovenstaande stappen zijn uitgevoerd, gaat u naar de app in Azure Portal. Selecteer de sectie Verificatie (preview). 

U kunt ook een PUT-aanvraag indienen voor de `config/authsettingsv2` resource onder de siteresource. Het schema voor de nettolading is hetzelfde als vastgelegd in de [sectie Configureren met behulp van een bestand.](#config-file)

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Configureren met behulp van een bestand (preview)

Uw auth-instellingen kunnen eventueel worden geconfigureerd via een bestand dat wordt geleverd door uw implementatie. Dit kan vereist zijn voor bepaalde preview-mogelijkheden van App Service verificatie/autorisatie.

> [!IMPORTANT]
> Onthoud dat de nettolading van uw app, en dus dit bestand, kan worden verplaatst tussen omgevingen, net als met [sleuven](./deploy-staging-slots.md). Waarschijnlijk wilt u een andere app-registratie vastmaken aan elke site. In dergelijke gevallen moet u de standaardconfiguratiemethode blijven gebruiken in plaats van het configuratiebestand te gebruiken.

### <a name="enabling-file-based-configuration"></a>Configuratie op basis van bestanden inschakelen

> [!CAUTION]
> Tijdens het inschakelen van configuratie op basis van bestanden wordt het beheer van de functie App Service-verificatie/autorisatie voor uw toepassing uitgeschakeld via sommige clients, zoals de Azure Portal, Azure CLI en Azure PowerShell.

1. Maak een nieuw JSON-bestand voor uw configuratie in de hoofdmap van uw project (geïmplementeerd op D:\home\site\wwwroot in uw web-/functie-app). Vul de gewenste configuratie in volgens de [bestandsgebaseerde configuratieverwijzing](#configuration-file-reference). Als u een bestaande Azure Resource Manager wijzigt, moet u de eigenschappen die in de verzameling zijn vastgelegd, vertalen naar `authsettings` uw configuratiebestand.

2. Wijzig de bestaande configuratie, die wordt vastgelegd in de [Azure Resource Manager](../azure-resource-manager/management/overview.md) API's onder `Microsoft.Web/sites/<siteName>/config/authsettings` . Als u dit wilt wijzigen, kunt u een [sjabloon Azure Resource Manager](../azure-resource-manager/templates/overview.md) of een hulpprogramma zoals [Azure Resource Explorer.](https://resources.azure.com/) In de verzameling authsettings moet u drie eigenschappen instellen (en andere verwijderen):

    1.  Ingesteld `enabled` op 'true'
    2.  Ingesteld `isAuthFromFile` op 'true'
    3.  Ingesteld `authFilePath` op de naam van het bestand (bijvoorbeeld 'auth.jsaan')

> [!NOTE]
> De indeling voor `authFilePath` varieert per platform. In Windows worden zowel relatieve als absolute paden ondersteund. Relatief wordt aanbevolen. Voor Linux worden momenteel alleen absolute paden ondersteund, dus de waarde van de instelling moet '/home/site/wwwroot/auth.json' of vergelijkbaar zijn.

Zodra u deze configuratie-update hebt aangebracht, wordt de inhoud van het bestand gebruikt om het gedrag van App Service/autorisatie voor die site te definiëren. Als u ooit wilt terugkeren naar Azure Resource Manager configuratie, kunt u dit doen door weer in te stellen `isAuthFromFile` op 'false'.

### <a name="configuration-file-reference"></a>Naslag voor configuratiebestand

Geheimen waarnaar vanuit uw configuratiebestand wordt verwezen, moeten worden opgeslagen als [toepassingsinstellingen.](./configure-common.md#configure-app-settings) U kunt de instellingen een naam geven die u maar wilt. Zorg ervoor dat de verwijzingen uit het configuratiebestand dezelfde sleutels gebruiken.

De volgende opties zijn uitputtend mogelijke configuratieopties in het bestand:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Uw app vastmaken aan een specifieke versie van de verificatieruntime

Wanneer u Verificatie/autorisatie inschakelen, wordt platform-middleware in uw HTTP-aanvraagpijplijn geïnjecteerd, zoals beschreven in het [functieoverzicht.](overview-authentication-authorization.md#how-it-works) Deze platform-middleware wordt regelmatig bijgewerkt met nieuwe functies en verbeteringen als onderdeel van routinematige platformupdates. Standaard wordt uw web- of functie-app uitgevoerd op de nieuwste versie van deze platform-middleware. Deze automatische updates zijn altijd achterwaarts compatibel. In het zeldzame geval dat deze automatische update echter een runtime-probleem introduceert voor uw web- of functie-app, kunt u tijdelijk terugdraaien naar de vorige middlewareversie. In dit artikel wordt uitgelegd hoe u een app tijdelijk vast kunt maken aan een specifieke versie van de verificatie-middleware.

### <a name="automatic-and-manual-version-updates"></a>Automatische en handmatige versie-updates 

U kunt uw app vastmaken aan een specifieke versie van de platform-middleware door een `runtimeVersion` instelling voor de app in te stellen. Uw app wordt altijd uitgevoerd met de nieuwste versie, tenzij u ervoor kiest om deze expliciet vast te maken aan een specifieke versie. Er worden een aantal versies tegelijk ondersteund. Als u vastgemaakt aan een ongeldige versie die niet meer wordt ondersteund, gebruikt uw app in plaats daarvan de nieuwste versie. Als u altijd de nieuwste versie wilt uitvoeren, stelt `runtimeVersion` u in op ~1. 

### <a name="view-and-update-the-current-runtime-version"></a>De huidige runtimeversie weergeven en bijwerken

U kunt de runtimeversie wijzigen die door uw app wordt gebruikt. De nieuwe runtimeversie moet van kracht worden nadat de app opnieuw is gestart. 

#### <a name="view-the-current-runtime-version"></a>De huidige runtimeversie weergeven

U kunt de huidige versie van de platformverificatie-middleware bekijken met behulp van de Azure CLI of via een van de ingebouwde versie-HTTP-eindpunten in uw app.

##### <a name="from-the-azure-cli"></a>Vanuit de Azure CLI

Gebruik de Azure CLI om de huidige middlewareversie weer te geven met [de opdracht az webapp auth show.](/cli/azure/webapp/auth#az_webapp_auth_show)

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Vervang in deze code `<my_app_name>` door de naam van uw app. Vervang ook `<my_resource_group>` door de naam van de resourcegroep voor uw app.

U ziet het veld `runtimeVersion` in de CLI-uitvoer. Deze lijkt op de volgende voorbeelduitvoer, die voor de duidelijkheid is afgekapt: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Vanaf het versie-eindpunt

U kunt ook het eindpunt /.auth/version in een app raken om ook de huidige middlewareversie te bekijken waarmee de app wordt uitgevoerd. Deze lijkt op de volgende voorbeelduitvoer:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>De huidige runtimeversie bijwerken

Met de Azure CLI kunt u de instelling in de `runtimeVersion` app bijwerken met de opdracht az [webapp auth update.](/cli/azure/webapp/auth#az_webapp_auth_update)

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Vervang `<my_app_name>` door de naam van uw app. Vervang ook `<my_resource_group>` door de naam van de resourcegroep voor uw app. Vervang ook `<version>` door een geldige versie van de 1.x-runtime of voor de nieuwste `~1` versie. U vindt de release-opmerkingen over de verschillende runtimeversies [hier] ( om te bepalen aan welke versie moet https://github.com/Azure/app-service-announcements) worden vastgemaakt.

U kunt deze opdracht uitvoeren vanuit de [Azure Cloud Shell](../cloud-shell/overview.md) door In **het voorgaande** codevoorbeeld Proberen te kiezen. U kunt de Azure CLI ook [lokaal gebruiken om](/cli/azure/install-azure-cli) deze opdracht uit te voeren nadat u az login hebt uitgevoerd [om](/cli/azure/reference-index#az_login) u aan te melden.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: gebruikers eind-tot-eind verifiëren en autoriseren](tutorial-auth-aad.md)
