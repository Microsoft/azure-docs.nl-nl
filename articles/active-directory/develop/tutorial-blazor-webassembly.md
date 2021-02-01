---
title: 'Zelfstudie: Gebruikers aanmelden en een beveiligde API aanroepen vanuit een Blazor WebAssembly-app'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie meldt u gebruikers aan en roept u een beveiligde API aan met behulp van het Microsoft-identiteitsplatform in een Blazor WASM-app (WebAssembly).
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 30b7f1054f7bfee8dc58638791f0b8a424de92a4
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226419"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Zelfstudie: Gebruikers aanmelden en een beveiligde API aanroepen vanuit een Blazor WebAssembly-app

In deze zelfstudie leert u hoe u een Blazor WebAssembly-app kunt bouwen dit gebruikers inlogt en gegevens kunt ophalen uit Microsoft Graph, met behulp van het Microsoft-identiteitsplatform en door de app te registreren in Azure AD (Azure Active Directory). 

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
>
> * Een nieuwe Blazor WebAssembly-app maken die is geconfigureerd om Azure AD (Active Directory) te gebruiken voor [verificatie en autorisatie](authentication-vs-authorization.md), met behulp van het Microsoft-identiteitsplatform
> * Gegevens ophalen uit een beveiligde web-API, in dit geval [Microsoft Graph](/graph/overview)

In deze zelfstudie wordt .NET Core 3.1 gebruikt. De .NET-documentatie bevat instructies over [hoe u een Blazor WebAssembly-app kunt beveiligen](/aspnet/core/blazor/security/webassembly/graph-api) met behulp van ASP.NET Core 5.0. 

We hebben ook een [zelfstudie voor Blazor Server](tutorial-blazor-server.md). 

## <a name="prerequisites"></a>Vereisten

* [NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Een Azure AD-tenant waarmee u een app kunt registreren. Als u geen toegang hebt tot een Azure AD-tenant, kunt u er een maken door u te registreren bij het [Microsoft 365-programma voor ontwikkelaars](https://developer.microsoft.com/microsoft-365/dev-program) of door een [gratis Azure-account](https://azure.microsoft.com/free) te maken.

## <a name="register-the-app-in-the-azure-portal"></a>De app registreren in het Azure-portaal

Elke app die gebruikmaakt van Azure Active Directory (Azure AD) voor verificatie moet geregistreerd zijn bij Azure AD. Volg de instructies in [Een toepassing registreren](quickstart-register-app.md) met de volgende specificaties:

- Bij **Ondersteunde accounttypen** selecteert u **Enkel accounts in deze organisatieadreslijst**.
- Kies in de vervolgkeuzelijst **Omleidings-URI** voor **Web** en voer `https://localhost:5001/authentication/login-callback` in. De standaardpoort voor een app die wordt uitgevoerd op Kestrel is 5001. Als de app beschikbaar is op een andere poort, geeft u dat poortnummer op in plaats van `5001`.

Na registratie selecteert u onder **beheren** de optie  >  **impliciete toekenning van verificatie en hybride stromen**. Selecteer **toegangs tokens** en **id-tokens** en selecteer vervolgens **Opslaan**.

## <a name="create-the-app-using-the-net-core-cli"></a>De app maken met behulp van de .NET Core CLI

Als u de app wilt maken, hebt u de nieuwste Blazor-sjablonen nodig. U kunt deze met de volgende opdracht installeren voor de .NET Core CLI:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.1
```

Voer vervolgens de volgende opdracht uit om de toepassing te maken. Vervang de tijdelijke aanduidingen in de opdracht door de juiste informatie op de overzichtspagina van uw app en voer de opdracht uit in een opdrachtshell. De uitvoerlocatie die is opgegeven met de optie `-o|--output` maakt een projectmap als deze nog niet bestaat, en gaat deel uitmaken van de naam van de app.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Tijdelijke aanduiding   | Azure-portaalnaam       | Voorbeeld                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | (Client-)id van de app | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | (Tenant-)id van de map   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>De app testen

U kunt de app nu bouwen en uitvoeren. Wanneer u deze sjabloon-app uitvoert, moet u het Framework opgeven dat moet worden uitgevoerd met behulp van --framework. In deze zelfstudie wordt gebruikgemaakt van .NET Standard 2.1, maar de sjabloon biedt ook ondersteuning voor andere frameworks.

```dotnetcli
dotnet run --framework netstandard2.1
```

Ga in uw browser naar `https://localhost:5001`. Meld u aan met een Azure AD-gebruikersaccount om te zien hoe de app wordt uitgevoerd en gebruikers worden aangemeld via het Microsoft-identiteitsplatform.

De onderdelen van deze sjabloon die aanmeldingen met Azure AD mogelijk maken via het Microsoft-identiteitsplatform, worden uitgelegd in het [ASP.NET.doc over dit onderwerp](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package).

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>Gegevens worden opgehaald uit een beveiligde API (Microsoft Graph)

[Microsoft Graph](/graph/overview) bevat API's die toegang bieden tot Microsoft 365-gegevens voor uw gebruikers. Het ondersteunt ook de tokens die zijn uitgegeven door het Microsoft Identity-platform. Hierdoor is het een goede beveiligde API om als voorbeeld te gebruiken. In deze sectie voegt u code toe om Microsoft Graph aan te roepen en de e-mails van ingelogde gebruikers weer te geven op de pagina 'Gegevens ophalen' van de toepassing.

Deze sectie is geschreven met behulp van een algemene benadering voor het aanroepen van een beveiligde API met behulp van een benoemde client. Dezelfde methode kan worden gebruikt voor andere beveiligde API's die u wilt aanroepen. Als u echter van plan bent om Microsoft Graph aan te roepen vanuit uw toepassing, kunt u de Graph SDK gebruiken om de standaard te verminderen. De .NET-documentatie bevat instructies over [het gebruik van de Graph SDK](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0&preserve-view=true).

Voordat u begint, moet u zich afmelden bij uw app, aangezien u wijzigingen aanbrengt in de vereiste machtigingen en uw huidige token daarvoor niet werkt. Als u dat nog niet hebt gedaan, voert u de app opnieuw uit en selecteert u **Afmelden** voordat u de onderstaande code bijwerkt.

Nu gaat u de registratie en code van de app bijwerken om de e-mailberichten van een gebruiker op te halen, en de berichten weer te geven in de app.

Voeg eerst de `Mail.Read` API-machtiging toe aan de app-registratie, zodat in Azure AD duidelijk is dat via de app toegang gaat worden aangevraagd tot de e-mail van de bijbehorende gebruikers.

1. Selecteer uw app onder **App-registraties** in het Azure-portaal.
1. Selecteer onder **Beheren** de optie **API-machtigingen**.
1. Selecteer **Een machtiging toevoegen** > **Microsoft Graph**.
1. Selecteer **Gedelegeerde machtigingen**, zoek en selecteer vervolgens de machtiging **Mail.Read**.
1. Selecteer **Machtigingen toevoegen**.

Voeg vervolgens het volgende toe aan het *.csproj*-bestand van uw project in de **ItemGroup** netstandard2.1. Hierdoor kunt u in de volgende stap de aangepaste HttpClient maken.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Wijzig vervolgens de code zoals opgegeven in de volgende stappen. Met deze wijzigingen worden [toegangstokens](access-tokens.md) toegevoegd aan de uitgaande aanvragen die zijn verzonden naar de Microsoft Graph API. Dit patroon wordt gedetailleerder besproken in [Extra beveiligingsscenario’s voor ASP.NET Core Blazor WebAssembly](/aspnet/core/blazor/security/webassembly/additional-scenarios).

Maak eerst een nieuw bestand met de naam *GraphAuthorizationMessageHandler.cs* met de volgende code. Deze handler wordt gebruikt om een toegangstoken toe te voegen voor de bereiken `User.Read` en `Mail.Read` voor uitgaande aanvragen naar de Microsoft Graph API.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Vervang vervolgens de inhoud van de methode `Main` in *Program.cs* door de volgende code. De code maakt gebruik van de nieuwe `GraphAPIAuthorizationMessageHandler`, en `User.Read` en `Mail.Read` worden toegevoegd als standaardbereiken die worden aangevraagd via de app wanneer de gebruiker zich de eerste keer aanmeldt.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Vervang ten slotte de inhoud van de pagina *FetchData.razor* door de volgende code. Met deze code worden e-mailgegevens van gebruikers opgehaald uit de Microsoft Graph API en weergegeven als een lijst. De nieuwe `HttpClient` die gebruikmaakt van het juiste toegangstoken, wordt gemaakt in `OnInitializedAsync`, en wordt gebruikt om de aanvraag te verzenden naar de Microsoft Graph API.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Start de app nu opnieuw. U ziet dat u wordt gevraagd de app toestemming te geven om uw e-mail te lezen. Dit wordt verwacht wanneer via een app het bereik `Mail.Read` wordt aangevraagd.

Nadat u toestemming hebt gegeven, gaat u naar de pagina "Gegevens ophalen" om een e-mailbericht te lezen.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Schermafbeelding van de uiteindelijke app. Het bevat een kop met de tekst &quot;Hello Nicholas&quot; en er wordt een lijst met e-mailberichten van Nicholas weergegeven.":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Best practices en aanbevelingen voor het Microsoft-identiteitsplatform](./identity-platform-integration-checklist.md)