---
title: OWIN-based web-Api's migreren naar b2clogin.com of een aangepast domein
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van een .NET-Web-API voor het ondersteunen van tokens die zijn uitgegeven door meerdere token verleners terwijl u uw toepassingen migreert naar b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 860f167913211ee7c511e515937f29ba5bf954cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491556"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom-or-a-custom-domain"></a>Een op OWIN gebaseerde web-API migreren naar b2clogin.com of een aangepast domein

In dit artikel wordt een techniek beschreven voor het inschakelen van ondersteuning voor meerdere token verleners in Web-Api's die de [open web interface voor .net (OWIN)](http://owin.org/)implementeren. Het ondersteunen van meerdere token-eind punten is handig wanneer u Azure Active Directory B2C-Api's (Azure AD B2C) en de bijbehorende toepassingen van het ene domein naar het andere migreert. Bijvoorbeeld van *login.microsoftonline.com* naar *b2clogin.com*, of naar een [aangepast domein](custom-domain.md).

Door ondersteuning toe te voegen aan uw API voor het accepteren van tokens die zijn uitgegeven door b2clogin.com, login.microsoftonline.com of een aangepast domein, kunt u uw webtoepassingen op een gefaseerde manier migreren voordat u ondersteuning voor login.microsoftonline.com-uitgegeven tokens uit de API verwijdert.

De volgende secties bevatten een voor beeld van het inschakelen van meerdere verleners in een web-API die gebruikmaakt van de [micro soft OWIN][katana] middleware Components (Katana). Hoewel de code voorbeelden specifiek zijn voor de micro soft OWIN-middleware, moet de algemene techniek van toepassing zijn op andere OWIN-bibliotheken.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende Azure AD B2C resources nodig om door te gaan met de stappen in dit artikel:

* [Gebruikers stromen](tutorial-create-user-flows.md) of [aangepaste beleids regels](custom-policy-get-started.md) die zijn gemaakt in uw Tenant

## <a name="get-token-issuer-endpoints"></a>Eind punten van token Uitgever ophalen

U moet eerst de eind punt-Uri's van de token Uitgever ophalen voor elke verlener die u wilt ondersteunen in uw API. Als u de *b2clogin.com* -en *login.microsoftonline.com* -eind punten wilt ophalen die worden ondersteund door uw Azure AD B2C-Tenant, gebruikt u de volgende procedure in de Azure Portal.

Begin met het selecteren van een van uw bestaande gebruikers stromen:

1. Navigeer naar uw Azure AD B2C-Tenant in de [Azure Portal](https://portal.azure.com)
1. Selecteer onder **beleids regels** **gebruikers stromen (beleid)**
1. Selecteer een bestaand beleid, bijvoorbeeld *B2C_1_signupsignin1*, en selecteer vervolgens **gebruikers stroom uitvoeren**
1. Selecteer onder de kop **gebruikers stroom uitvoeren** aan de bovenkant van de pagina de Hyper link om naar het OpenID Connect Connect-detectie-eind punt voor die gebruikers stroom te gaan.

    ![Bekende URI-Hyper link op de pagina nu uitvoeren van de Azure Portal](media/multi-token-endpoints/portal-01-policy-link.png)

1. Noteer de waarde op de pagina die in de browser wordt geopend `issuer` , bijvoorbeeld:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Gebruik de vervolg keuzelijst **domein selecteren** om het andere domein te selecteren en voer de vorige twee stappen opnieuw uit en noteer de `issuer` waarde ervan.

Er zijn nu twee Uri's opgenomen die vergelijkbaar zijn met:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Aangepast beleid

Als u aangepaste beleids regels hebt in plaats van gebruikers stromen, kunt u een vergelijkbaar proces gebruiken om de Issuer-Uri's op te halen.

1. Navigeer naar uw Azure AD B2C-Tenant
1. **Identiteits ervaring-Framework** selecteren
1. Selecteer een van de Relying Party-beleids regels, bijvoorbeeld *B2C_1A_signup_signin*
1. Gebruik de vervolg keuzelijst **domein selecteren** om een domein te selecteren, bijvoorbeeld *yourtenant.b2clogin.com*
1. Selecteer de Hyper link die wordt weer gegeven onder **OpenID Connect Connect Discovery-eind punt**
1. De `issuer` waarde vastleggen
1. Voer de stappen 4-6 voor het andere domein uit, bijvoorbeeld *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

Nu u beide token eindpunt-Uri's hebt, moet u de code bijwerken om op te geven dat beide eind punten geldige verleners zijn. Als u een voor beeld wilt door lopen, kunt u de voorbeeld toepassing downloaden of klonen en vervolgens het voor beeld bijwerken zodat beide eind punten als geldige verleners worden ondersteund.

Het archief downloaden: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Meerdere verleners inschakelen in de Web-API

In deze sectie werkt u de code bij om aan te geven dat beide eind punten van de token Uitgever geldig zijn.

1. Open de oplossing **B2C-WebAPI-dotnet. SLN** in Visual Studio
1. Open in het project **TaskService** het bestand *TaskService \\ App_Start \\ * * Startup. auth. cs** * in uw editor
1. Voeg de volgende `using` instructie toe aan het begin van het bestand:

    `using System.Collections.Generic;`
1. Voeg de [`ValidIssuers`][validissuers] eigenschap toe aan de [`TokenValidationParameters`][tokenvalidationparameters] definitie en geef beide uri's op die u in de vorige sectie hebt genoteerd:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"//,
            //"https://your-custom-domain/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` wordt verzorgd door MSAL.NET en wordt gebruikt door de OWIN-middleware in de volgende sectie van de code in *Startup. auth. cs*. Als er meerdere geldige verleners zijn opgegeven, wordt de OWIN-toepassings pijplijn op de hoogte gesteld dat beide token-eind punten geldige verleners zijn.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Zoals eerder vermeld, bieden andere OWIN-bibliotheken doorgaans een vergelijk bare faciliteit voor het ondersteunen van meerdere verleners. Hoewel voor beelden worden gegeven voor elke bibliotheek buiten het bereik van dit artikel, kunt u een soort gelijke techniek gebruiken voor de meeste bibliotheken.

## <a name="switch-endpoints-in-web-app"></a>Scha kelen tussen eind punten in web-app

Wanneer beide Uri's nu door de Web-API worden ondersteund, moet u uw webtoepassing nu bijwerken zodat de tokens worden opgehaald uit het b2clogin.com-eind punt.

U kunt bijvoorbeeld de voor beeld-webtoepassing zodanig configureren dat het nieuwe eind punt wordt gebruikt door de `ida:AadInstance` waarde in het *project taskwebapp \\ **Web.config** _-bestand van het _* project taskwebapp * *-project te wijzigen.

Wijzig de `ida:AadInstance` waarde in de *Web.config* van project taskwebapp zodat deze verwijst naar `{your-b2c-tenant-name}.b2clogin.com` in plaats van `login.microsoftonline.com` .

Voor:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Na (Vervang `{your-b2c-tenant}` door de naam van uw B2C-Tenant):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Wanneer de eindpunt teken reeksen zijn gemaakt tijdens de uitvoering van de web-app, worden de b2clogin.com-eind punten gebruikt bij het aanvragen van tokens.

Wanneer u een aangepast domein gebruikt:

```xml
<!-- Custom domain -->
<add key="ida:AadInstance" value="https://custom-domain/{0}/{1}" />
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel vindt u een methode voor het configureren van een web-API die de micro soft OWIN middleware (Katana) implementeert voor het accepteren van tokens van meerdere issue-eind punten. Zoals u ziet, zijn er verschillende andere teken reeksen in de *Web.Config* bestanden van zowel de TaskService-als project taskwebapp-projecten die moeten worden gewijzigd als u deze projecten wilt bouwen en uitvoeren met uw eigen Tenant. U kunt de projecten op de juiste manier aanpassen als u ze in actie wilt zien, maar een volledig overzicht van het uitvoeren van dit artikel.

Zie [overzicht van tokens in azure Active Directory B2C](tokens-overview.md)voor meer informatie over de verschillende typen beveiligings tokens die worden gegenereerd door Azure AD B2C.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
