---
title: Geavanceerd gebruik van authn/AuthZ
description: Meer informatie over het aanpassen van de functie voor verificatie en autorisatie in App Service voor verschillende scenario's en het ophalen van gebruikers claims en verschillende tokens.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 50587feff29e1c02a639d63d0c99156dcec4f68e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180867"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Geavanceerd gebruik van verificatie en autorisatie in Azure App Service

In dit artikel wordt beschreven hoe u de ingebouwde [verificatie en autorisatie in app service](overview-authentication-authorization.md)kunt aanpassen en hoe u identiteit kunt beheren vanuit uw toepassing. 

Als u snel aan de slag wilt gaan, raadpleegt u een van de volgende zelf studies:

* [Zelfstudie: Zelfstudie: Gebruikers eind-tot-eind verifiëren en autoriseren in Azure App Service](tutorial-auth-aad.md)
* [Uw app configureren voor aanmelding met Azure Active Directory](configure-authentication-provider-aad.md)
* [Uw app configureren voor aanmelding met Facebook](configure-authentication-provider-facebook.md)
* [Uw app configureren voor aanmelding met Google](configure-authentication-provider-google.md)
* [Uw app configureren voor aanmelding met een Microsoft Account](configure-authentication-provider-microsoft.md)
* [Uw app configureren voor aanmelding met Twitter](configure-authentication-provider-twitter.md)
* [Uw app configureren voor aanmelding met een OpenID Connect Connect provider (preview-versie)](configure-authentication-provider-openid-connect.md)
* [Uw app configureren voor aanmelding met een aanmelding met Apple (preview-versie)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Meerdere aanmeldings providers gebruiken

De Portal configuratie biedt geen mogelijkheid om meerdere aanmeld providers te presen teren aan uw gebruikers (zoals Facebook en Twitter). Het is echter moeilijk om de functionaliteit toe te voegen aan uw app. De stappen worden als volgt beschreven:

Configureer eerst op de pagina **verificatie/autorisatie** in de Azure Portal elke id-provider die u wilt inschakelen.

Selecteer **anonieme aanvragen toestaan (geen actie)** **wanneer de aanvraag niet is geverifieerd in actie om uit te voeren**.

Voeg op de aanmeldings pagina of in de navigatie balk of op een andere locatie van uw app een aanmeldings koppeling toe aan elk van de providers die u hebt ingeschakeld ( `/.auth/login/<provider>` ). Bijvoorbeeld:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Wanneer de gebruiker op een van de koppelingen klikt, wordt de betreffende aanmeldings pagina geopend om zich aan te melden bij de gebruiker.

Als u de aanmelding van een gebruiker naar een aangepaste URL wilt door sturen, gebruikt u de `post_login_redirect_url` query teken reeks parameter (niet met de omleidings-URI in de configuratie van de ID-provider). Gebruik bijvoorbeeld de volgende HTML-code om door de gebruiker te navigeren naar `/Home/Index` na het aanmelden:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Tokens van providers valideren

In een client gerichte aanmelding meldt de toepassing de gebruiker hand matig aan bij de provider en verzendt het verificatie token naar App Service voor validatie (Zie [verificatie stroom](overview-authentication-authorization.md#authentication-flow)). Met deze validatie wordt u geen toegang verleend tot de gewenste app-resources, maar een geslaagde validatie geeft u een sessie token dat u kunt gebruiken voor toegang tot app-resources. 

Als u het provider token wilt valideren, moet App Service app eerst worden geconfigureerd met de gewenste provider. Tijdens runtime, na het ophalen van het verificatie token van uw provider, plaatst u het token voor `/.auth/login/<provider>` validatie. Bijvoorbeeld: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

De token indeling is enigszins afhankelijk van de provider. Raadpleeg de volgende tabel voor meer informatie:

| Provider waarde | Vereist in de hoofd tekst van de aanvraag | Opmerkingen |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | De `expires_in` eigenschap is optioneel. <br/>Bij het aanvragen van het token van Live Services vraagt u het bereik altijd aan `wl.basic` . |
| `google` | `{"id_token":"<id_token>"}` | De `authorization_code` eigenschap is optioneel. Indien opgegeven, kan deze eventueel ook vergezeld gaan van de `redirect_uri` eigenschap. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Gebruik een geldig [token voor gebruikers toegang](https://developers.facebook.com/docs/facebook-login/access-tokens) van Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Als het provider token is gevalideerd, wordt de API geretourneerd met een `authenticationToken` in de antwoord tekst, wat uw sessie token is. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Zodra u dit sessie token hebt, kunt u toegang krijgen tot beveiligde app-bronnen door de header toe te voegen `X-ZUMO-AUTH` aan uw HTTP-aanvragen. Bijvoorbeeld: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Afmelden bij een sessie

Gebruikers kunnen een afmelding initiëren door een `GET` aanvraag naar het eind punt van de app te verzenden `/.auth/logout` . De `GET` aanvraag doet het volgende:

- Hiermee wist u de verificatie cookies van de huidige sessie.
- Hiermee verwijdert u de tokens van de huidige gebruiker uit de token opslag.
- Voor Azure Active Directory en Google voert een afmelding aan de server zijde uit voor de ID-provider.

Dit is een voorbeeld van een eenvoudige afmeldingskoppeling op een webpagina:

```html
<a href="/.auth/logout">Sign out</a>
```

Standaard wordt de client door een geslaagde aanmelding omgeleid naar de URL `/.auth/logout/done` . U kunt de omleidings pagina voor het afmelden van berichten wijzigen door de query parameter toe te voegen `post_logout_redirect_uri` . Bijvoorbeeld:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Het is raadzaam om de [](https://wikipedia.org/wiki/Percent-encoding) waarde van te coderen `post_logout_redirect_uri` .

Wanneer u Fully Qualified Url's gebruikt, moet de URL ofwel worden gehost in hetzelfde domein of worden geconfigureerd als een toegestane externe omleidings-URL voor uw app. In het volgende voor beeld wordt een omleiding naar `https://myexternalurl.com` die niet gehost in hetzelfde domein:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Voer de volgende opdracht uit in de [Azure Cloud shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-fragmenten behouden

Wanneer gebruikers zich aanmelden bij uw app, willen ze meestal worden omgeleid naar hetzelfde gedeelte van dezelfde pagina, zoals `/wiki/Main_Page#SectionZ` . Omdat URL- [fragmenten](https://wikipedia.org/wiki/Fragment_identifier) echter `#SectionZ` nooit naar de server worden verzonden, worden ze niet standaard bewaard nadat de OAuth-aanmelding is voltooid en weer omgeleid naar uw app. Gebruikers krijgen dan een optimale ervaring wanneer ze opnieuw naar het gewenste anker moeten navigeren. Deze beperking is van toepassing op alle verificatie oplossingen aan de server zijde.

Bij App Service-verificatie kunt u URL-fragmenten behouden over de OAuth-aanmelding. Als u dit wilt doen, stelt u een app-instelling `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` in op `true` . U kunt dit doen in de [Azure Portal](https://portal.azure.com)of gewoon de volgende opdracht uitvoeren in de [Azure Cloud shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Gebruikers claims openen

App Service gebruikers claims door gegeven aan uw toepassing met behulp van speciale headers. Externe aanvragen zijn niet gemachtigd om deze headers in te stellen, zodat ze alleen aanwezig zijn als deze zijn ingesteld door App Service. Enkele voor beelden van headers zijn:

* X-MS-CLIENT-PRINCIPAL-NAAM
* X-MS-CLIENT-PRINCIPAL-ID

Code die is geschreven in een wille keurige taal of elk Framework, kan de informatie die nodig is van deze headers ophalen. Voor ASP.NET 4,6-apps wordt de **claimsprincipal is** automatisch ingesteld met de juiste waarden. ASP.NET Core biedt echter geen middleware voor authenticatie die kan worden geïntegreerd met App Service gebruikers claims. Zie [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)voor een tijdelijke oplossing.

Als de [token opslag](overview-authentication-authorization.md#token-store) is ingeschakeld voor uw app, kunt u ook aanvullende details over de geverifieerde gebruiker verkrijgen door aan te roepen `/.auth/me` . De Sdk's van de Mobile Apps server bieden hulp methoden om met deze gegevens te werken. Zie [How to use the Azure Mobile Apps Node.js SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)(Engelstalig) voor meer informatie en [samen werken met de .net back-end-server-sdk voor Azure Mobile apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Tokens ophalen in app-code

Vanuit uw server code worden de providerspecifieke tokens in de aanvraag header ingevoegd, zodat u ze eenvoudig kunt openen. De volgende tabel bevat mogelijke token header-namen:

| Provider | Header namen |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-account | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Verzend vanuit uw client code (zoals een mobiele app of een Java script in de browser) een HTTP- `GET` aanvraag naar `/.auth/me` ([token archief](overview-authentication-authorization.md#token-store) moet zijn ingeschakeld). De geretourneerde JSON heeft de providerspecifieke tokens.

> [!NOTE]
> Toegangs tokens zijn voor toegang tot bronnen van de provider, zodat ze alleen aanwezig zijn als u uw provider configureert met een client geheim. Zie toegangs tokens vernieuwen voor meer informatie over het verkrijgen van vernieuwings tokens.

## <a name="refresh-identity-provider-tokens"></a>ID-provider tokens vernieuwen

Wanneer het toegangs token van uw provider (niet het [sessie token](#extend-session-token-expiration-grace-period)) verloopt, moet u de gebruiker opnieuw verifiëren voordat u het token opnieuw gebruikt. U kunt de verval datum van tokens voor komen door een `GET` aanroep naar het `/.auth/refresh` eind punt van uw toepassing te maken. Wanneer wordt aangeroepen App Service de toegangs tokens in de [token opslag](overview-authentication-authorization.md#token-store) voor de geverifieerde gebruiker automatisch vernieuwd. Bij volgende aanvragen voor tokens door uw app-code worden de vernieuwde tokens opgehaald. Het vernieuwen van tokens werkt echter alleen als de token opslag [vernieuwings tokens](https://auth0.com/learn/refresh-tokens/) voor uw provider bevat. De manier om vernieuwings tokens op te halen, wordt door elke provider gedocumenteerd, maar de volgende lijst is een korte samen vatting:

- **Google**: een `access_type=offline` query reeks parameter toevoegen aan de `/.auth/login/google` API-aanroep. Als u de Mobile Apps SDK gebruikt, kunt u de para meter toevoegen aan een van de `LogicAsync` Overloads (Zie [tokens voor Google-vernieuwing](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: biedt geen vernieuwings tokens. Tokens met een lange levens duur verlopen over 60 dagen (Zie het [verloop van Facebook en de uitbrei ding van toegangs tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: toegangs tokens verlopen niet (Zie [Veelgestelde vragen over Twitter OAuth](https://developer.twitter.com/en/docs/authentication/faq)).
- **Micro soft-account**: Selecteer het bereik bij het [configureren van verificatie-instellingen voor micro soft-accounts](configure-authentication-provider-microsoft.md) `wl.offline_access` .
- **Azure Active Directory**: in [https://resources.azure.com](https://resources.azure.com) voert u de volgende stappen uit:
    1. Selecteer boven aan de pagina **lezen/schrijven**.
    2. Navigeer in de linkernavigatiebalk naar **abonnementen** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **providers**  >  **micro soft.**  >  **websites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 
    3. Klik op **Bewerken**.
    4. Wijzig de volgende eigenschap. Vervang door _\<app\_id>_ de Azure Active Directory-toepassings-id van de service die u wilt openen.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klik op **put**. 

Zodra uw provider is geconfigureerd, kunt u [het vernieuwings token en de verloop tijd voor het toegangs token vinden](#retrieve-tokens-in-app-code) in de token opslag. 

Als u uw toegangs token op elk gewenst moment wilt vernieuwen, kunt u gewoon `/.auth/refresh` een wille keurige taal aanroepen. Het volgende code fragment gebruikt jQuery om uw toegangs tokens van een Java script-client te vernieuwen.

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

Als een gebruiker de machtigingen intrekt die aan uw app zijn verleend, `/.auth/me` kan de aanroep mislukken met een `403 Forbidden` antwoord. Raadpleeg uw toepassings logboeken voor meer informatie over het vaststellen van fouten.

## <a name="extend-session-token-expiration-grace-period"></a>Respijt periode van verloop sessie token uitbreiden

De geverifieerde sessie verloopt na 8 uur. Nadat een geverifieerde sessie is verlopen, is er standaard een respijt periode van 72 uur. Binnen deze respijt periode mag u het sessie token vernieuwen met App Service zonder de gebruiker opnieuw te verifiëren. U kunt alleen bellen `/.auth/refresh` wanneer uw sessie token ongeldig wordt en u hoeft niet zelf het token verloop op te volgen. Nadat de respijt periode van 72 uur is vervallen, moet de gebruiker zich opnieuw aanmelden om een geldig sessie token te verkrijgen.

Als 72 uur niet genoeg tijd voor u is, kunt u dit verloop venster uitbreiden. Het verlengen van de verloop tijd voor een lange periode kan aanzienlijke gevolgen hebben voor de beveiliging (zoals wanneer een verificatie token wordt gelekt of gestolen). Daarom moet u de standaard waarde van 72 uur laten staan of de extensie periode instellen op de kleinste.

Als u het standaard vervaldatum venster wilt uitbreiden, voert u de volgende opdracht uit in de [Cloud shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> De respijt periode geldt alleen voor de App Service geverifieerde sessie, niet de tokens van de id-providers. Er is geen respijt periode voor de verlopen provider tokens. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Het domein van aanmeldings accounts beperken

Met zowel het micro soft-account als de Azure Active Directory kunt u zich vanuit meerdere domeinen aanmelden. Micro soft-account staat bijvoorbeeld _Outlook.com_-, _live.com_-en _Hotmail.com_ -accounts toe. Azure AD biedt een wille keurig aantal aangepaste domeinen voor de aanmeldings accounts. U kunt uw gebruikers echter ook rechtstreeks naar uw eigen Azure AD-aanmeldings pagina (zoals `contoso.com` ) versnellen. Voer de volgende stappen uit om de domein naam van de aanmeldings accounts voor te stellen.

[https://resources.azure.com](https://resources.azure.com)Ga in naar **abonnementen** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **providers**  >  **micro soft.**  >  **websites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 

Klik op **bewerken**, wijzig de volgende eigenschap en klik vervolgens op **put**. Zorg ervoor dat u vervangt door _\<domain\_name>_ het domein dat u wilt.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Met deze instelling wordt de `domain_hint` query teken reeks parameter toegevoegd aan de omleidings-URL voor aanmelding. 

> [!IMPORTANT]
> Het is mogelijk dat de client de `domain_hint` para meter verwijdert nadat de omleidings-URL is ontvangen en u zich vervolgens aanmeldt met een ander domein. Hoewel deze functie handig is, is het geen beveiligings functie.
>

## <a name="authorize-or-deny-users"></a>Gebruikers autoriseren of weigeren

Hoewel App Service de eenvoudigste autorisatie aanvraag verkrijgt (dat wil zeggen niet-geverifieerde aanvragen afwijzen), is voor uw app mogelijk meer verfijnde autorisatie gedrag vereist, zoals het beperken van de toegang tot een specifieke groep gebruikers. In bepaalde gevallen moet u aangepaste toepassings code schrijven om toegang tot de aangemelde gebruiker toe te staan of te weigeren. In andere gevallen kunnen App Service of uw ID-provider u helpen zonder code wijzigingen te hoeven vereisen.

- [Server niveau](#server-level-windows-apps-only)
- [Niveau van ID-provider](#identity-provider-level)
- [Toepassings niveau](#application-level)

### <a name="server-level-windows-apps-only"></a>Server niveau (alleen Windows-apps)

Voor elke Windows-app kunt u autorisatie gedrag van de IIS-webserver definiëren door het *Web.config* -bestand te bewerken. Linux-apps gebruiken IIS niet en kunnen niet worden geconfigureerd via *Web.config*.

1. Ga naar `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. Ga in de browser Verkenner van uw App Service-bestanden naar *site/wwwroot*. Als een *Web.config* niet bestaat, maakt u het door **+**  >  **nieuw bestand** te selecteren. 

1. Selecteer het potlood voor *Web.config* om het te bewerken. Voeg de volgende configuratie code toe en klik op **Opslaan**. Als *Web.config* al bestaat, kunt u het `<authorization>` element gewoon toevoegen met alles. Voeg de accounts toe die u wilt toestaan in het- `<allow>` element.

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

### <a name="identity-provider-level"></a>Niveau van ID-provider

De ID-provider kan bepaalde machtigingen voor een schakel sleutel geven. Bijvoorbeeld:

- Voor [Azure app service](configure-authentication-provider-aad.md)kunt u [toegang op ondernemings niveau](../active-directory/manage-apps/what-is-access-management.md) rechtstreeks in azure AD beheren. Zie [de toegang van een gebruiker tot een toepassing verwijderen](../active-directory/manage-apps/methods-for-removing-user-access.md)voor instructies.
- Google API-projecten die deel uitmaken van een [organisatie](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) kunnen worden geconfigureerd om alleen toegang te verlenen aan gebruikers in uw organisatie (Zie [de pagina **OAuth 2,0** -ondersteuning instellen](https://support.google.com/cloud/answer/6158849?hl=en)) voor [Google](configure-authentication-provider-google.md).

### <a name="application-level"></a>Toepassings niveau

Als een van de andere niveaus niet de autorisatie biedt die u nodig hebt, of als uw platform of ID-provider niet wordt ondersteund, moet u aangepaste code schrijven om gebruikers te autoriseren op basis van de [gebruikers claims](#access-user-claims).

## <a name="updating-the-configuration-version-preview"></a>De configuratie versie bijwerken (preview)

Er zijn twee versies van de beheer-API voor de functie voor verificatie/autorisatie. De preview v2-versie is vereist voor de ervaring authenticatie (preview) in de Azure Portal. Een app die al de V1-API gebruikt, kan een upgrade uitvoeren naar versie v2 wanneer er enkele wijzigingen zijn aangebracht. In het bijzonder moet de geheime configuratie worden verplaatst naar de instellingen van een sleuf-Sticky-toepassing. De configuratie van de micro soft-account provider wordt ook momenteel niet ondersteund in v2.

> [!WARNING]
> Door de migratie naar v2 Preview wordt het beheer van de functie voor App Service verificatie/autorisatie voor uw toepassing via sommige clients, zoals de bestaande ervaring in de Azure Portal, Azure CLI en Azure PowerShell, uitgeschakeld. Dit kan niet worden omgekeerd. Tijdens de preview wordt de migratie van productie werkbelastingen niet aangemoedigd of ondersteund. Volg de stappen in deze sectie alleen voor test toepassingen.

### <a name="moving-secrets-to-application-settings"></a>Geheimen verplaatsen naar toepassings instellingen

1. De bestaande configuratie ophalen met behulp van de V1 API:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   Noteer de geheime waarde die wordt gebruikt voor elke provider die u hebt geconfigureerd in de resulterende JSON-nettolading:

   * Aad `clientSecret`
   * Google `googleClientSecret`
   * Facebook `facebookAppSecret`
   * Twitter `twitterConsumerSecret`
   * Micro soft-account: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > De geheime waarden zijn belang rijke beveiligings referenties en moeten zorgvuldig worden afgehandeld. U kunt deze waarden niet delen of persistent maken op een lokale computer.

1. Maak voor elke geheime waarde een sleuf-Sticky toepassings instellingen. U kunt de naam van elke toepassings instelling kiezen. De waarde moet overeenkomen met wat u in de vorige stap hebt verkregen of [naar een Key Vault geheim](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) dat u met die waarde hebt gemaakt.

   Als u de instelling wilt maken, kunt u de Azure Portal gebruiken of een variant van het volgende voor elke provider uitvoeren:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > De toepassings instellingen voor deze configuratie moeten worden gemarkeerd als sleuf-plak, wat betekent dat ze niet tussen omgevingen worden verplaatst tijdens het wisselen van een [sleuf](./deploy-staging-slots.md). Dit komt doordat uw verificatie configuratie zelf is gekoppeld aan de omgeving. 

1. Maak een nieuw JSON-bestand met de naam `authsettings.json` . Neem de uitvoer die u eerder hebt ontvangen en verwijder elke geheime waarde uit deze. Schrijf de resterende uitvoer naar het bestand en zorg ervoor dat er geen geheim is opgenomen. In sommige gevallen kan de configuratie matrices hebben die lege teken reeksen bevatten. Zorg ervoor dat dat `microsoftAccountOAuthScopes` niet het geval is, en als dat zo is, schakelt u deze waarde in `null` .

1. Voeg een eigenschap toe `authsettings.json` die verwijst naar de naam van de toepassings instelling die u eerder hebt gemaakt voor elke provider:
 
   * Aad `clientSecretSettingName`
   * Google `googleClientSecretSettingName`
   * Facebook `facebookAppSecretSettingName`
   * Twitter `twitterConsumerSecretSettingName`
   * Micro soft-account: `microsoftAccountClientSecretSettingName`

   Een voorbeeld bestand na deze bewerking kan er ongeveer als volgt uitzien, in dit geval alleen geconfigureerd voor AAD:

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
           "clientSecret": null,
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

1. Dien dit bestand in als de nieuwe verificatie/autorisatie configuratie voor uw app:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Controleer of de app nog steeds wordt uitgevoerd zoals verwacht na deze penbeweging.

1. Verwijder het bestand dat u in de vorige stappen hebt gebruikt.

U hebt nu de app gemigreerd voor het opslaan van geheimen van de identiteits provider als toepassings instellingen.

### <a name="support-for-microsoft-account-registrations"></a>Ondersteuning voor Microsoft-account registraties

De v2-API biedt momenteel geen ondersteuning voor micro soft-accounts als een DISTINCT-provider. In plaats daarvan maakt het gebruik van het geconvergeerde [micro soft Identity-platform](../active-directory/develop/v2-overview.md) om gebruikers te registreren met persoonlijke micro soft-accounts. Wanneer u overschakelt naar de v2 API, wordt de configuratie v1 Azure Active Directory gebruikt voor het configureren van de micro soft Identity platform-provider.

Als uw bestaande configuratie een micro soft-account provider bevat en geen Azure Active Directory provider heeft, kunt u de configuratie overschakelen naar de Azure Active Directory provider en vervolgens de migratie uitvoeren. Om dit te doen:

1. Ga naar [**app-registraties**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in het Azure Portal en zoek de registratie die is gekoppeld aan uw micro soft-account provider. Deze kan zich onder de kop ' toepassingen van persoonlijk account ' bevinden.
1. Ga naar de pagina verificatie voor de registratie. Onder omleidings-Uri's moet er een vermelding worden weer gegeven die eindigt op `/.auth/login/microsoftaccount/callback` . Kopieer deze URI.
1. Voeg een nieuwe URI toe die overeenkomt met de URL die u zojuist hebt gekopieerd, behalve in plaats daarvan eindigen op `/.auth/login/aad/callback` . Hiermee staat u toe dat de registratie wordt gebruikt door de configuratie voor App Service verificatie/autorisatie.
1. Ga naar de App Service verificatie/autorisatie configuratie voor uw app.
1. Verzamel de configuratie voor de micro soft-account provider.
1. Configureer de Azure Active Directory provider met behulp van de ' geavanceerde ' beheer modus, waarbij u de client-ID en client Secret-waarden opgeeft die u in de vorige stap hebt verzameld. Gebruik voor de URL van de uitgever gebruik `<authentication-endpoint>/<tenant-id>/v2.0` en vervang door *\<authentication-endpoint>* het [verificatie-eind punt voor uw cloud omgeving](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (bijvoorbeeld: " https://login.microsoftonline.com " voor wereld wijd Azure), vervangen *\<tenant-id>* door de id van uw **Directory (Tenant)**.
1. Nadat u de configuratie hebt opgeslagen, moet u de aanmeldings stroom testen door in uw browser naar het `/.auth/login/aad` eind punt op uw site te navigeren en de aanmeldings stroom te volt ooien.
1. U hebt nu de configuratie gekopieerd over, maar de bestaande configuratie van de micro soft-account provider blijft behouden. Voordat u het verwijdert, moet u ervoor zorgen dat alle onderdelen van uw app naar de Azure Active Directory provider verwijzen via aanmeldings koppelingen, enzovoort. Controleer of alle onderdelen van uw app werken zoals verwacht.
1. Zodra u hebt gevalideerd dat dingen werken aan de AAD-Azure Active Directory provider, kunt u de configuratie van de micro soft-account provider verwijderen.

Sommige apps hebben mogelijk al afzonderlijke registraties voor Azure Active Directory en het micro soft-account. Deze apps kunnen op dit moment niet worden gemigreerd. 

> [!WARNING]
> U kunt de twee registraties convergeren door de [ondersteunde account typen](../active-directory/develop/supported-accounts-validation.md) voor de registratie van de Aad-app te wijzigen. Dit zou er echter toe leiden dat er een nieuwe toestemming wordt gevraagd voor gebruikers van micro soft-accounts en dat de identiteits claims van die gebruikers kunnen verschillen in de structuur, met `sub` name het wijzigen van waarden sinds een nieuwe app-id wordt gebruikt. Deze methode wordt niet aanbevolen, tenzij zorgvuldig is begrepen. U moet in plaats daarvan wachten op ondersteuning voor de twee registraties in het v2 API-Opper vlak.

### <a name="switching-to-v2"></a>Overschakelen naar v2

Zodra de bovenstaande stappen zijn uitgevoerd, gaat u naar de app in de Azure Portal. Selecteer de sectie verificatie (preview). 

U kunt ook een PUT-aanvraag indienen `config/authsettingsv2` bij de resource onder de site resource. Het schema voor de payload is hetzelfde als vastgelegd in de sectie [configureren met een bestand](#config-file) .

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Configureren met behulp van een bestand (preview)

Uw verificatie-instellingen kunnen eventueel worden geconfigureerd via een bestand dat is opgegeven door uw implementatie. Dit kan vereist zijn voor bepaalde preview-mogelijkheden van App Service verificatie/autorisatie.

> [!IMPORTANT]
> Houd er rekening mee dat uw app-Payload, en dus dit bestand, kan worden verplaatst tussen omgevingen, zoals met [sleuven](./deploy-staging-slots.md). Waarschijnlijk wilt u dat er een andere app-registratie wordt vastgemaakt aan elke sleuf. in dergelijke gevallen moet u de standaard configuratie methode blijven gebruiken in plaats van het configuratie bestand te gebruiken.

### <a name="enabling-file-based-configuration"></a>Op bestanden gebaseerde configuratie inschakelen

> [!CAUTION]
> Tijdens de preview-versie wordt door het inschakelen van op bestanden gebaseerde configuratie het beheer van de functie voor App Service verificatie/autorisatie voor uw toepassing via sommige clients, zoals de Azure Portal, Azure CLI en Azure PowerShell, uitgeschakeld.

1. Maak een nieuw JSON-bestand voor uw configuratie in de hoofdmap van uw project (geïmplementeerd in D:\home\site\wwwroot in uw web/function-app). Vul uw gewenste configuratie in op basis [van de op bestanden gebaseerde configuratie verwijzing](#configuration-file-reference). Als u een bestaande Azure Resource Manager configuratie wilt wijzigen, moet u ervoor zorgen dat de eigenschappen die in de verzameling zijn vastgelegd, `authsettings` in uw configuratie bestand worden vertaald.

2. Wijzig de bestaande configuratie, die wordt vastgelegd in de [Azure Resource Manager](../azure-resource-manager/management/overview.md) api's onder `Microsoft.Web/sites/<siteName>/config/authsettings` . Als u dit wilt wijzigen, kunt u een [Azure Resource Manager-sjabloon](../azure-resource-manager/templates/overview.md) of een hulp programma gebruiken, zoals [Azure resource Explorer](https://resources.azure.com/). In de authsettings-verzameling moet u drie eigenschappen instellen (en kunnen andere worden verwijderd):

    1.  Ingesteld `enabled` op ' True '
    2.  Ingesteld `isAuthFromFile` op ' True '
    3.  Stel `authFilePath` in op de naam van het bestand (bijvoorbeeld ' auth.jsop ')

> [!NOTE]
> De notatie voor `authFilePath` varieert tussen platforms. In Windows worden zowel relatieve als absolute paden ondersteund. Relatief wordt aanbevolen. Voor Linux worden alleen absolute paden ondersteund. de waarde van de instelling moet dus '/Home/site/wwwroot/auth.jsop ' of vergelijkbaar zijn.

Zodra u deze configuratie-update hebt gemaakt, wordt de inhoud van het bestand gebruikt voor het definiëren van het gedrag van App Service verificatie/autorisatie voor die site. Als u ooit wilt terugkeren naar Azure Resource Manager configuratie, kunt u dit doen door `isAuthFromFile` terug in te stellen op ' false '.

### <a name="configuration-file-reference"></a>Naslag informatie voor het configuratie bestand

Alle geheimen waarnaar wordt verwezen vanuit uw configuratie bestand, moeten worden opgeslagen als [Toepassings instellingen](./configure-common.md#configure-app-settings). U kunt de instellingen een wille keurige naam hebben. Zorg ervoor dat de verwijzingen van het configuratie bestand dezelfde sleutels gebruiken.

De volgende uitputtende configuratie opties in het bestand:

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

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Uw app vastmaken aan een specifieke runtime versie voor verificatie

Wanneer u verificatie/autorisatie inschakelt, wordt het platform geinjecteerd in uw HTTP request-pijp lijn, zoals beschreven in het [onderdeel overzicht](overview-authentication-authorization.md#how-it-works). Deze middleware van het platform wordt regel matig bijgewerkt met nieuwe functies en verbeteringen als onderdeel van de routine-updates. Uw web-of functie-app wordt standaard uitgevoerd op de meest recente versie van deze middleware van het platform. Deze automatische updates zijn altijd achterwaarts compatibel. In het zeldzame geval dat deze automatische update een runtime probleem voor uw web-of functie-app introduceert, kunt u tijdelijk teruggaan naar de vorige middleware-versie. In dit artikel wordt uitgelegd hoe u tijdelijk een app vastmaakt aan een specifieke versie van de middleware voor authenticatie.

### <a name="automatic-and-manual-version-updates"></a>Automatische en hand matige versie-updates 

U kunt uw app vastmaken aan een specifieke versie van de middleware van het platform door een `runtimeVersion` instelling voor de app in te stellen. Uw app wordt altijd uitgevoerd op de meest recente versie, tenzij u ervoor kiest om deze expliciet terug te koppelen aan een specifieke versie. Er worden een aantal versies tegelijk ondersteund. Als u vastmaakt aan een ongeldige versie die niet meer wordt ondersteund, gebruikt uw app in plaats daarvan de nieuwste versie. Als u de meest recente versie altijd wilt uitvoeren, stelt `runtimeVersion` u in op ~ 1. 

### <a name="view-and-update-the-current-runtime-version"></a>De huidige runtime versie weer geven en bijwerken

U kunt de runtime versie die door uw app wordt gebruikt, wijzigen. De nieuwe runtime versie wordt van kracht nadat de app opnieuw is opgestart. 

#### <a name="view-the-current-runtime-version"></a>De huidige runtime versie weer geven

U kunt de huidige versie van de platform verificatie-middleware weer geven met behulp van de Azure CLI of via een van de ingebouwde versie HTTP-eind punten in uw app.

##### <a name="from-the-azure-cli"></a>Uit de Azure CLI

Gebruik de Azure CLI om de huidige middleware-versie te bekijken met de opdracht [AZ webapp auth show](/cli/azure/webapp/auth#az-webapp-auth-show) .

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Vervang in deze code door `<my_app_name>` de naam van uw app. Vervang ook door `<my_resource_group>` de naam van de resource groep voor uw app.

Het veld wordt weer geven `runtimeVersion` in de CLI-uitvoer. Dit komt overeen met de volgende voorbeeld uitvoer, die is afgekapt voor duidelijkheid: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Van het versie-eind punt

U kunt ook op/.auth/version-eind punt in een app drukken om de huidige middleware-versie te bekijken waarop de app wordt uitgevoerd. Deze ziet eruit als in de volgende voorbeeld uitvoer:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>De huidige runtime versie bijwerken

Met de Azure CLI kunt u de `runtimeVersion` instelling in de app bijwerken met de opdracht [AZ webapp auth update](/cli/azure/webapp/auth#az-webapp-auth-update) .

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Vervang door `<my_app_name>` de naam van uw app. Vervang ook door `<my_resource_group>` de naam van de resource groep voor uw app. Vervang ook door `<version>` een geldige versie van de 1. x-runtime of `~1` voor de meest recente versie. U kunt de release opmerkingen vinden in de verschillende runtime versies [hier] ( https://github.com/Azure/app-service-announcements) om te bepalen welke versie moet worden vastgemaakt aan.

U kunt deze opdracht uitvoeren vanuit de [Azure Cloud shell](../cloud-shell/overview.md) door deze in het voor gaande code voorbeeld **te kiezen.** U kunt de [Azure cli ook lokaal](/cli/azure/install-azure-cli) gebruiken om deze opdracht uit te voeren nadat u [AZ login](/cli/azure/reference-index#az-login) hebt uitgevoerd om u aan te melden.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: gebruikers eind-tot-eind verifiëren en autoriseren](tutorial-auth-aad.md)
