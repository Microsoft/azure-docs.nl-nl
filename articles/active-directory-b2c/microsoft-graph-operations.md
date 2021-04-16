---
title: Resources beheren met Microsoft Graph
titleSuffix: Azure AD B2C
description: Resources in een Azure AD B2C tenant beheren door de Microsoft Graph-API aan te roepen en een toepassings-id te gebruiken om het proces te automatiseren.
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
ms.openlocfilehash: 884cb0c30bc754366fda79a4b54b977517fbadd3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530527"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Beheer Azure AD B2C met Microsoft Graph

Microsoft Graph kunt u resources in uw Azure AD B2C beheren. De volgende Microsoft Graph API-bewerkingen worden ondersteund voor het beheer van Azure AD B2C-resources, waaronder gebruikers, id-providers, gebruikersstromen, aangepaste beleidsregels en beleidssleutels. Elke koppeling in de volgende secties is gericht op de bijbehorende pagina in Microsoft Graph API-verwijzing voor die bewerking. 

## <a name="prerequisites"></a>Vereisten

Als u MS Graph API wilt gebruiken en wilt communiceren met resources in uw Azure AD B2C-tenant, hebt u een toepassingsregistratie nodig die de machtigingen verleent om dit te doen. Volg de stappen in het artikel [Azure AD B2C Microsoft Graph](microsoft-graph-get-started.md) beheren om een toepassingsregistratie te maken die uw beheertoepassing kan gebruiken. 

## <a name="user-management"></a>Gebruikersbeheer

- [Gebruikers weergeven](/graph/api/user-list)
- [Een consumentgebruiker maken](/graph/api/user-post-users)
- [Een gebruiker krijgen](/graph/api/user-get)
- [Een gebruiker bijwerken](/graph/api/user-update)
- [Een gebruiker verwijderen](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Beheer van telefoonnummers van gebruikers (bèta)

Een telefoonnummer dat door een gebruiker kan worden gebruikt om zich aan te melden met behulp van [sms-](identity-provider-local.md#phone-sign-in-preview)of spraakoproepen, of [meervoudige verificatie.](multi-factor-authentication.md) Zie Api voor [Azure AD-verificatiemethoden voor meer informatie.](/graph/api/resources/phoneauthenticationmethod)

- [Add](/graph/api/authentication-post-phonemethods)
- [List](/graph/api/authentication-list-phonemethods)
- [Ophalen](/graph/api/phoneauthenticationmethod-get)
- [Bijwerken](/graph/api/phoneauthenticationmethod-update)
- [Verwijderen](/graph/api/phoneauthenticationmethod-delete)

Opmerking: de [lijstbewerking](/graph/api/authentication-list-phonemethods) retourneert alleen ingeschakelde telefoonnummers. Het volgende telefoonnummer moet zijn ingeschakeld voor gebruik met de lijstbewerkingen. 

![Aanmelden via telefoon inschakelen](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>E-mailadres voor selfservice voor wachtwoord opnieuw instellen (bèta)

Een e-mailadres dat kan worden gebruikt door een [aanmeldingsaccount voor](identity-provider-local.md#username-sign-in) gebruikersnamen om het wachtwoord opnieuw in te stellen. Zie Api voor [Azure AD-verificatiemethoden voor meer informatie.](/graph/api/resources/emailauthenticationmethod)

- [Add](/graph/api/emailauthenticationmethod-post)
- [List](/graph/api/emailauthenticationmethod-list)
- [Ophalen](/graph/api/emailauthenticationmethod-get)
- [Bijwerken](/graph/api/emailauthenticationmethod-update)
- [Verwijderen](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Id-providers

Beheer de [id-providers die](add-identity-provider.md) beschikbaar zijn voor uw gebruikersstromen in uw Azure AD B2C tenant.

- [Id-providers die zijn geregistreerd in de Azure AD B2C-tenant](/graph/api/identityprovider-list)
- [Een id-provider maken](/graph/api/identityprovider-post-identityproviders)
- [Een id-provider op halen](/graph/api/identityprovider-get)
- [Id-provider bijwerken](/graph/api/identityprovider-update)
- [Een id-provider verwijderen](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Gebruikersstroom

Configureer vooraf samengesteld beleid voor registreren, aanmelden, gecombineerd registreren en aanmelden, wachtwoord opnieuw instellen en profielupdates.

- [Gebruikersstromen op een lijst zetten](/graph/api/identitycontainer-list-b2cuserflows)
- [Een gebruikersstroom maken](/graph/api/identitycontainer-post-b2cuserflows)
- [Een gebruikersstroom krijgen](/graph/api/b2cidentityuserflow-get)
- [Een gebruikersstroom verwijderen](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Verificatiemethoden voor gebruikersstromen (bèta)

Kies een mechanisme waarmee gebruikers zich kunnen registreren via lokale accounts. Lokale accounts zijn de accounts waar Azure AD de identiteitsver bevestigt. Zie voor meer informatie [b2cAuthenticationMethodsPolicy resourcetype.](/graph/api/resources/b2cauthenticationmethodspolicy)

- [Ophalen](/graph/api/b2cauthenticationmethodspolicy-get)
- [Bijwerken](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Aangepast beleid

Met de volgende bewerkingen kunt u uw Azure AD B2C Trust Framework-beleid beheren, ook wel [aangepaste beleidsregels genoemd.](custom-policy-overview.md)

- [Alle beleidsregels voor vertrouwenskaders die zijn geconfigureerd in een tenant opsnissen](/graph/api/trustframework-list-trustframeworkpolicies)
- [Vertrouwensraamwerkbeleid maken](/graph/api/trustframework-post-trustframeworkpolicy)
- [Eigenschappen van een bestaand vertrouwensraamwerkbeleid lezen](/graph/api/trustframeworkpolicy-get)
- [Beleid voor vertrouwenskaders bijwerken of maken.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Een bestaand vertrouwensraamwerkbeleid verwijderen](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Beleidssleutels

In Identity Experience Framework worden de geheimen opgeslagen waarnaar wordt verwezen in een aangepast beleid om een vertrouwensrelatie tussen onderdelen tot stand te laten komen. Deze geheimen kunnen symmetrische of asymmetrische sleutels/waarden zijn. In de Azure Portal worden deze entiteiten weergegeven als **beleidssleutels.**

De resource op het hoogste niveau voor beleidssleutels in Microsoft Graph API is [Trusted Framework Keyset.](/graph/api/resources/trustframeworkkeyset) Elke **Keyset** bevat ten minste één **sleutel**. Als u een sleutel wilt maken, maakt u eerst een lege keyset en genereert u vervolgens een sleutel in de keyset. U kunt een handmatig geheim maken, een certificaat uploaden of een PKCS12-sleutel. De sleutel kan een gegenereerd geheim, een tekenreeks (zoals het Facebook-toepassingsgeheim) of een certificaat zijn dat u uploadt. Als een keyset meerdere sleutels heeft, is slechts één van de sleutels actief.

### <a name="trust-framework-policy-keyset"></a>Trust Framework-beleidssleutelset

- [De sleutelsets van het vertrouwens framework opsnets](/graph/api/trustframework-list-keysets)
- [Sleutelsets voor vertrouwens framework maken](/graph/api/trustframework-post-keysets)
- [Een keyset op halen](/graph/api/trustframeworkkeyset-get)
- [Sleutelsets voor een vertrouwens framework bijwerken](/graph/api/trustframeworkkeyset-update)
- [Sleutelsets voor een vertrouwensraamwerk verwijderen](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Vertrouwens framework-beleidssleutel

- [Momenteel actieve sleutel in de sleutelset op halen](/graph/api/trustframeworkkeyset-getactivekey)
- [Een sleutel genereren in keyset](/graph/api/trustframeworkkeyset-generatekey)
- [Een geheim op basis van een tekenreeks uploaden](/graph/api/trustframeworkkeyset-uploadsecret)
- [Een X.509-certificaat uploaden](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Een PKCS12-indelingscertificaat uploaden](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Toepassingen

- [Toepassingen weergeven](/graph/api/application-list)
- [Een app maken](/graph/api/resources/application)
- [Toepassing bijwerken](/graph/api/application-update)
- [ServicePrincipal maken](/graph/api/resources/serviceprincipal)
- [Oauth2Permission Grant maken](/graph/api/resources/oauth2permissiongrant)
- [Toepassing verwijderen](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Eigenschappen van toepassingsextensie

- [Extensie-eigenschappen weergeven](/graph/api/application-list-extensionproperty)

Azure AD B2C biedt een directory die 100 aangepaste kenmerken per gebruiker kan bevatten. Voor gebruikersstromen worden deze extensie-eigenschappen [beheerd met behulp van de Azure Portal](user-flow-custom-attributes.md). Voor aangepaste beleidsregels maakt Azure AD B2C eigenschap voor u, de eerste keer dat het beleid een waarde naar de extensie-eigenschap schrijft.

## <a name="audit-logs"></a>Auditlogboeken

- [Auditlogboeken opslijsten](/graph/api/directoryaudit-list)

Zie Accessing Azure AD B2C audit logs (Toegang Azure AD B2C auditlogboeken) voor meer informatie over het openen [Azure AD B2C auditlogboeken.](view-audit-logs.md)

## <a name="conditional-access"></a>Voorwaardelijke toegang

- [Alle beleidsregels voor voorwaardelijke toegang opsnissen](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [Eigenschappen en relaties van een beleid voor voorwaardelijke toegang lezen](/graph/api/conditionalaccesspolicy-get)
- [Nieuw beleid voor voorwaardelijke toegang maken](/graph/api/resources/application)
- [Een beleid voor voorwaardelijke toegang bijwerken](/graph/api/conditionalaccesspolicy-update)
- [Een beleid voor voorwaardelijke toegang verwijderen](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Codevoorbeeld: Gebruikersaccounts programmatisch beheren

Dit codevoorbeeld is een .NET Core-consoletoepassing die gebruikmaakt van [de Microsoft Graph SDK](/graph/sdks/sdks-overview) voor interactie met Microsoft Graph API. De code laat zien hoe u de API aanroept om gebruikers in een tenant programmatisch Azure AD B2C beheren.
U kunt [het voorbeeldarchief](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip) downloaden, door de [opslagplaats op](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) GitHub bladeren of de opslagplaats klonen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Nadat u het codevoorbeeld hebt verkregen, configureert u het voor uw omgeving en bouwt u het project:

1. Open het project in [Visual Studio](https://visualstudio.microsoft.com) of [Visual Studio Code](https://code.visualstudio.com).
1. Open `src/appsettings.json`.
1. Vervang in de sectie door de naam van uw tenant en en door de waarden voor de registratie van uw `appSettings` `your-b2c-tenant` `Application (client) ID` `Client secret` beheertoepassing. Zie Register [a Microsoft Graph Application voor meer informatie.](microsoft-graph-get-started.md)
1. Open een consolevenster in de lokale kloon van de repo, ga naar de `src` map en bouw het project:

    ```console
    cd src
    dotnet build
    ```
    
1. De toepassing uitvoeren met de opdracht `dotnet`:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

De toepassing geeft een lijst weer met opdrachten die u kunt uitvoeren. Haal bijvoorbeeld alle gebruikers op, haal één gebruiker op, verwijder een gebruiker, werk het wachtwoord van een gebruiker bij en importeer bulksgewijs.

### <a name="code-discussion"></a>Codediscussie

De voorbeeldcode maakt gebruik [van de Microsoft Graph SDK](/graph/sdks/sdks-overview), die is ontworpen om het bouwen van hoogwaardige, efficiënte en robuuste toepassingen die toegang hebben tot Microsoft Graph.

Voor elke aanvraag voor Microsoft Graph API is een toegangs token vereist voor verificatie. De oplossing maakt gebruik van het [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet-pakket dat een wrapper op basis van een verificatiescenario van de Microsoft Authentication Library (MSAL) biedt voor gebruik met de Microsoft Graph SDK.

De `RunAsync` methode in het bestand _Program.cs:_

1. Leest toepassingsinstellingen uit hetappsettings.js _on-file_
1. Initialiseert de auth-provider met behulp van de stroom voor het verlenen van [OAuth 2.0-clientreferenties.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Met de stroom voor het verlenen van clientreferenties kan de app een toegangs token krijgen om de api Microsoft Graph aan te roepen.
1. Hiermee stelt u de Microsoft Graph-serviceclient in met de auth-provider:

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

De initialiseerde *GraphServiceClient wordt* vervolgens gebruikt in _UserService.cs om_ de gebruikersbeheerbewerkingen uit te voeren. U kunt bijvoorbeeld een lijst met gebruikersaccounts in de tenant opmaken:

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

Het maken van API-aanroepen met behulp van [de Microsoft Graph-SDK's](/graph/sdks/create-requests) bevat informatie over het lezen en schrijven van gegevens uit Microsoft Graph, het gebruik van om de geretourneerde eigenschappen te bepalen, aangepaste queryparameters op te geven en de queryparameters en te `$select` `$filter` `$orderBy` gebruiken.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
