---
title: Gebruikers beheren met de Microsoft Graph-API
titleSuffix: Azure AD B2C
description: Gebruikers in een Azure AD B2C-Tenant beheren door de Microsoft Graph-API aan te roepen en een toepassings-id te gebruiken om het proces te automatiseren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178871"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Azure AD B2C gebruikers accounts beheren met Microsoft Graph

Met Microsoft Graph kunt u gebruikers accounts in uw Azure AD B2C Directory beheren door methoden voor maken, lezen, bijwerken en verwijderen op te geven in de Microsoft Graph-API. U kunt een bestaande gebruikers opslag migreren naar een Azure AD B2C Tenant en andere beheer bewerkingen voor gebruikers accounts uitvoeren door de Microsoft Graph-API aan te roepen.

In de volgende secties worden de belangrijkste aspecten van Azure AD B2C gebruikers beheer met de Microsoft Graph-API weer gegeven. De Microsoft Graph-API-bewerkingen, typen en eigenschappen die hier worden weer gegeven, zijn een subset van die in de naslag documentatie over de Microsoft Graph-API wordt vermeld.

## <a name="register-a-management-application"></a>Een beheer toepassing registreren

Voordat u een toepassing voor gebruikers beheer of een door u geschreven script kunt gebruiken om te werken met de resources in uw Azure AD B2C Tenant, hebt u een toepassings registratie nodig die de machtigingen verleent.

Volg de stappen in dit artikel om een toepassings registratie te maken die uw beheer toepassing kan gebruiken:

[Azure AD B2C beheren met Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Microsoft Graph bewerkingen voor gebruikers beheer

De volgende gebruikers beheer bewerkingen zijn beschikbaar in de [Microsoft Graph-API](/graph/api/resources/user):

- [Een lijst met gebruikers ophalen](/graph/api/user-list)
- [Een gebruiker maken](/graph/api/user-post-users)
- [Een gebruiker ophalen](/graph/api/user-get)
- [Een gebruiker bijwerken](/graph/api/user-update)
- [Een gebruiker verwijderen](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Code voorbeeld: gebruikers accounts programmatisch beheren

Dit code voorbeeld is een .NET core-console toepassing die gebruikmaakt van de [Microsoft Graph SDK](/graph/sdks/sdks-overview) om te communiceren met Microsoft Graph-API. De code laat zien hoe u de API aanroept om gebruikers programmatisch te beheren in een Azure AD B2C-Tenant.
U kunt [het voorbeeld archief](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip) downloaden, [door de opslag plaats](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) in github bladeren of de opslag plaats klonen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Nadat u het code voorbeeld hebt verkregen, configureert u dit voor uw omgeving en bouwt u vervolgens het project:

1. Open het project in [Visual Studio](https://visualstudio.microsoft.com) of [Visual Studio code](https://code.visualstudio.com).
1. Open `src/appsettings.json`.
1. Vervang in de `appSettings` sectie door `your-b2c-tenant` de naam van uw Tenant en `Application (client) ID` en `Client secret` met de waarden voor de registratie van uw beheer toepassing (Zie de sectie [een beheer toepassing registreren](#register-a-management-application) in dit artikel).
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

## <a name="next-steps"></a>Volgende stappen

Zie [Microsoft Graph beschik bare bewerkingen voor Azure AD B2C](microsoft-graph-operations.md)voor een volledige index van de API-bewerkingen voor Microsoft Graph die worden ondersteund voor Azure AD B2C resources.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
