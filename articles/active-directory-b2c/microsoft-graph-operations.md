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
ms.date: 01/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 96772020e70aeb32fa1a8ae18bf3818396887877
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805241"
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

## <a name="user-phone-number-management"></a>Telefoon nummer beheer gebruiker

- [Add](/graph/api/authentication-post-phonemethods)
- [Ophalen](/graph/api/b2cauthenticationmethodspolicy-get)
- [Bijwerken](/graph/api/b2cauthenticationmethodspolicy-update)
- [Verwijderen](/graph/api/phoneauthenticationmethod-delete)

Zie [B2C authentication methods](/graph/api/resources/b2cauthenticationmethodspolicy)(Engelstalig) voor meer informatie over het beheren van het telefoon nummer van de gebruiker.

## <a name="identity-providers-user-flow"></a>Id-providers (gebruikers stroom)

De id-providers beheren die beschikbaar zijn voor uw gebruikers stromen in uw Azure AD B2C-Tenant.

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

De `RunAsync` methode in het _Program.cs_ -bestand:

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

De geïnitialiseerde *GraphServiceClient* wordt vervolgens in _UserService.cs_ gebruikt om de gebruikers beheer bewerkingen uit te voeren. U kunt bijvoorbeeld een lijst ophalen van de gebruikers accounts in de Tenant:

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
