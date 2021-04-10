---
title: Resources beheren met Microsoft Graph
titleSuffix: Azure AD B2C
description: Resources in een Azure AD B2C-Tenant beheren door de Microsoft Graph-API aan te roepen en een toepassings-id te gebruiken om het proces te automatiseren.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 41336d59d51685d5daf78a1809ce6c0df2cd6124
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781310"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Azure AD B2C beheren met Microsoft Graph

Met Microsoft Graph kunt u resources in uw Azure AD B2C Directory beheren. De volgende Microsoft Graph API-bewerkingen worden ondersteund voor het beheer van Azure AD B2C resources, waaronder gebruikers, id-providers, gebruikers stromen, aangepaste beleids regels en beleids sleutels. Elke koppeling in de volgende secties is gericht op de corresponderende pagina binnen de Microsoft Graph API-verwijzing voor die bewerking. 

## <a name="prerequisites"></a>Vereisten

Als u MS Graph API wilt gebruiken en wilt communiceren met resources in uw Azure AD B2C-Tenant, moet u een toepassings registratie hebben die de machtigingen verleent. Volg de stappen in het artikel [Manage Azure AD B2C with Microsoft Graph](microsoft-graph-get-started.md) om een toepassings registratie te maken die uw beheer toepassing kan gebruiken. 

## <a name="user-management"></a>Gebruikersbeheer

- [Gebruikers weergeven](/graph/api/user-list)
- [Een consument gebruiker maken](/graph/api/user-post-users)
- [Een gebruiker ophalen](/graph/api/user-get)
- [Een gebruiker bijwerken](/graph/api/user-update)
- [Een gebruiker verwijderen](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Telefoon nummer beheer van de gebruiker (bèta)

Een telefoon nummer dat door een gebruiker kan worden gebruikt om u aan te melden met behulp van [SMS-of telefoon gesprekken](identity-provider-local.md#phone-sign-in-preview)of [multi-factor Authentication](multi-factor-authentication.md). Zie [Azure AD-verificatie methoden API](/graph/api/resources/phoneauthenticationmethod)voor meer informatie.

- [Add](/graph/api/authentication-post-phonemethods)
- [List](/graph/api/authentication-list-phonemethods)
- [Ophalen](/graph/api/phoneauthenticationmethod-get)
- [Bijwerken](/graph/api/phoneauthenticationmethod-update)
- [Verwijderen](/graph/api/phoneauthenticationmethod-delete)

Houd er rekening mee dat de [lijst](/graph/api/authentication-list-phonemethods) bewerking alleen ingeschakelde telefoon nummers retourneert. Het volgende telefoon nummer moet zijn ingeschakeld voor gebruik met de lijst bewerkingen. 

![Aanmelding via telefoon inschakelen](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>E-mail adres van de selfservice voor wacht woord opnieuw instellen (bèta)

Een e-mail adres dat kan worden gebruikt door een [gebruikers naam aanmeldings account](identity-provider-local.md#username-sign-in) om het wacht woord opnieuw in te stellen. Zie [Azure AD-verificatie methoden API](/graph/api/resources/emailauthenticationmethod)voor meer informatie.

- [Add](/graph/api/emailauthenticationmethod-post)
- [List](/graph/api/emailauthenticationmethod-list)
- [Ophalen](/graph/api/emailauthenticationmethod-get)
- [Bijwerken](/graph/api/emailauthenticationmethod-update)
- [Verwijderen](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Id-providers

De [id-providers](add-identity-provider.md) beheren die beschikbaar zijn voor uw gebruikers stromen in uw Azure AD B2C-Tenant.

- [Id-providers weer geven die zijn geregistreerd in de Azure AD B2C Tenant](/graph/api/identityprovider-list)
- [Een id-provider maken](/graph/api/identityprovider-post-identityproviders)
- [Een id-provider ophalen](/graph/api/identityprovider-get)
- [ID-provider bijwerken](/graph/api/identityprovider-update)
- [Een id-provider verwijderen](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Gebruikersstroom

Vooraf gemaakte beleids regels configureren voor aanmelding, aanmelden, gecombineerde registratie en aanmelding, wacht woord opnieuw instellen en profiel update.

- [Gebruikers stromen weer geven](/graph/api/identitycontainer-list-b2cuserflows)
- [Een gebruikersstroom maken](/graph/api/identitycontainer-post-b2cuserflows)
- [Een gebruikers stroom ophalen](/graph/api/b2cidentityuserflow-get)
- [Een gebruikers stroom verwijderen](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Verificatie methoden voor gebruikers stromen (bèta)

Kies een mechanisme om gebruikers te laten registreren via lokale accounts. Lokale accounts zijn de accounts waar Azure AD de identiteits verklaring doet. Zie [b2cAuthenticationMethodsPolicy resource type](/graph/api/resources/b2cauthenticationmethodspolicy)voor meer informatie.

- [Ophalen](/graph/api/b2cauthenticationmethodspolicy-get)
- [Bijwerken](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Aangepast beleid

Met de volgende bewerkingen kunt u uw Azure AD B2C Trust Framework-beleid beheren, ook wel [aangepast beleid](custom-policy-overview.md)genoemd.

- [Alle beleids regels voor vertrouwens relaties die zijn geconfigureerd in een Tenant weer geven](/graph/api/trustframework-list-trustframeworkpolicies)
- [Vertrouwens raamwerk beleid maken](/graph/api/trustframework-post-trustframeworkpolicy)
- [Eigenschappen van een bestaand vertrouwens raamwerk beleid lezen](/graph/api/trustframeworkpolicy-get)
- [Het vertrouwens raamwerk beleid bijwerken of maken.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Een bestaand vertrouwens raamwerk beleid verwijderen](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Beleidssleutels

In het Framework voor identiteits ervaring worden de geheimen opgeslagen waarnaar wordt verwezen in een aangepast beleid om de vertrouwens relatie tussen onderdelen tot stand te brengen. Deze geheimen kunnen symmetrische of asymmetrische sleutels/waarden zijn. In de Azure Portal worden deze entiteiten als **beleids sleutels** weer gegeven.

De resource op het hoogste niveau voor beleids sleutels in de Microsoft Graph-API is de [betrouw bare Framework sleutel](/graph/api/resources/trustframeworkkeyset). Elke **sleutelset** bevat ten minste één **sleutel**. Als u een sleutel wilt maken, moet u eerst een lege sleutelset maken en vervolgens een sleutel genereren in de sleutelset. U kunt een hand matig geheim maken, een certificaat uploaden of een PKCS12/pfx-profiel-sleutel. De sleutel kan een gegenereerd geheim zijn, een teken reeks (zoals het Facebook-toepassings geheim) of een certificaat dat u uploadt. Als een sleutelset meerdere sleutels heeft, is slechts een van de sleutels actief.

### <a name="trust-framework-policy-keyset"></a>Sleutelset van vertrouwens raamwerk beleid

- [De set vertrouwens raamwerk-Series weer geven](/graph/api/trustframework-list-keysets)
- [Een vertrouwens raamwerk-Series maken](/graph/api/trustframework-post-keysets)
- [Een sleutelset ophalen](/graph/api/trustframeworkkeyset-get)
- [Een vertrouwens raamwerk-Series bijwerken](/graph/api/trustframeworkkeyset-update)
- [Een vertrouwens raamwerk-Series verwijderen](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Beleids sleutel vertrouwens raamwerk

- [Momenteel actieve sleutel in de sleutelset ophalen](/graph/api/trustframeworkkeyset-getactivekey)
- [Een sleutel in sleutelset genereren](/graph/api/trustframeworkkeyset-generatekey)
- [Een geheim op basis van een teken reeks uploaden](/graph/api/trustframeworkkeyset-uploadsecret)
- [Een X. 509-certificaat uploaden](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Een certificaat voor PKCS12/pfx-profiel-indeling uploaden](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Toepassingen

- [Toepassingen weergeven](/graph/api/application-list)
- [Een app maken](/graph/api/resources/application)
- [Toepassing bijwerken](/graph/api/application-update)
- [ServicePrincipal maken](/graph/api/resources/serviceprincipal)
- [Oauth2Permission-toekenning maken](/graph/api/resources/oauth2permissiongrant)
- [Toepassing verwijderen](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Eigenschappen van toepassings uitbreiding

- [Uitbrei ding-eigenschappen weer geven](/graph/api/application-list-extensionproperty)

Azure AD B2C biedt een directory die 100 aangepaste kenmerken per gebruiker kan bevatten. Voor gebruikers stromen worden deze extensie-eigenschappen [beheerd met behulp van de Azure Portal](user-flow-custom-attributes.md). Azure AD B2C maakt voor aangepaste beleids regels de eigenschap voor u, de eerste keer dat het beleid een waarde naar de extensie-eigenschap schrijft.

## <a name="audit-logs"></a>Auditlogboeken

- [Audit logboeken weer geven](/graph/api/directoryaudit-list)

Zie [toegang tot Azure AD B2C audit logboeken](view-audit-logs.md)voor meer informatie over het openen van Azure AD B2C controle Logboeken.

## <a name="conditional-access"></a>Voorwaardelijke toegang

- [Alle beleids regels voor voorwaardelijke toegang weer geven](/graph/api/conditionalaccessroot-list-policies?view=graph-rest-beta&tabs=http)
- [Eigenschappen en relaties van beleid voor voorwaardelijke toegang lezen](/graph/api/conditionalaccesspolicy-get)
- [Nieuw beleid voor voorwaardelijke toegang maken](/graph/api/resources/application)
- [Beleid voor voorwaardelijke toegang bijwerken](/graph/api/conditionalaccesspolicy-update)
- [Beleid voor voorwaardelijke toegang verwijderen](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Code voorbeeld: gebruikers accounts programmatisch beheren

Dit code voorbeeld is een .NET core-console toepassing die gebruikmaakt van de [Microsoft Graph SDK](/graph/sdks/sdks-overview) om te communiceren met Microsoft Graph-API. De code laat zien hoe u de API aanroept om gebruikers programmatisch te beheren in een Azure AD B2C-Tenant.
U kunt [het voorbeeld archief](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip) downloaden, [door de opslag plaats](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) in github bladeren of de opslag plaats klonen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Nadat u het code voorbeeld hebt verkregen, configureert u dit voor uw omgeving en bouwt u vervolgens het project:

1. Open het project in [Visual Studio](https://visualstudio.microsoft.com) of [Visual Studio code](https://code.visualstudio.com).
1. Open `src/appsettings.json`.
1. Vervang in de `appSettings` sectie door `your-b2c-tenant` de naam van uw Tenant en `Application (client) ID` en `Client secret` met de waarden voor de registratie van uw beheer toepassing. Zie [een Microsoft Graph-toepassing registreren](microsoft-graph-get-started.md)voor meer informatie.
1. Open een console venster in uw lokale kloon van de opslag plaats, schakel over naar de `src` map en bouw het project:

    ```console
    cd src
    dotnet build
    ```
    
1. De toepassing uitvoeren met de opdracht `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

In de toepassing wordt een lijst weer gegeven met opdrachten die u kunt uitvoeren. U kunt bijvoorbeeld alle gebruikers ophalen, één gebruiker ophalen, een gebruiker verwijderen, het wacht woord van een gebruiker bijwerken en bulksgewijs importeren.

### <a name="code-discussion"></a>Code discussie

De voorbeeld code maakt gebruik van de [Microsoft Graph SDK](/graph/sdks/sdks-overview), die is ontworpen om het bouwen van hoogwaardige, efficiënte en flexibele toepassingen te vereenvoudigen die toegang hebben tot Microsoft Graph.

Voor elke aanvraag voor de Microsoft Graph-API is een toegangs token voor verificatie vereist. De oplossing maakt gebruik van het NuGet-pakket [micro soft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) dat een op een verificatie gebaseerd op een op een manier gebaseerde wrapper van de micro soft Authentication Library (MSAL) biedt voor gebruik met de SDK van Microsoft Graph.

De `RunAsync` methode in het bestand _Program. cs_ :

1. Hiermee leest u de toepassings instellingen van de _appsettings.jsvoor_ het bestand
1. Initialiseert de authenticatie provider met [OAuth 2,0-client referenties toekenning](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) stroom. Met de client referenties toekenning stroom kan de app een toegangs token verkrijgen om de Microsoft Graph-API aan te roepen.
1. Hiermee stelt u de Microsoft Graph-serviceclient in met de verificatie provider:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

De geïnitialiseerde *GraphServiceClient* wordt vervolgens gebruikt in _UserService. cs_ om de gebruikers beheer bewerkingen uit te voeren. U kunt bijvoorbeeld een lijst ophalen van de gebruikers accounts in de Tenant:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[API-aanroepen maken met behulp van de Microsoft Graph sdk's](/graph/sdks/create-requests) bevat informatie over het lezen en schrijven van gegevens van Microsoft Graph, `$select` het beheren van de eigenschappen die worden geretourneerd, het opgeven van aangepaste query parameters en het gebruik van de- `$filter` en- `$orderBy` query parameters.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
