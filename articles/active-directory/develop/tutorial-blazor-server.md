---
title: Zelfstudie - Een Blazor Server-app maken die gebruikmaakt van het Microsoft-identiteitsplatform voor verificatie | Azure
titleSuffix: Microsoft identity platform
description: In deze zelfstudie stelt u verificatie in met behulp van het Microsoft-identiteitsplatform in een Blazor Server-app.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: d499a0e7d3ca7933632b52d5edbd8094a29dbcaa
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979892"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Zelfstudie: Een Blazor Server-app maken die gebruikmaakt van het Microsoft-identiteitsplatform voor verificatie

In deze zelfstudie leert u hoe u een Blazor Server-app kunt bouwen dit gebruikers inlogt en gegevens kunt ophalen uit Microsoft Graph, met behulp van het Microsoft-identiteitsplatform en door de app te registreren in Azure AD (Azure Active Directory).

We hebben ook een zelfstudie voor [Blazor WASM](tutorial-blazor-webassembly.md).

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Een nieuwe Blazor Server-app maken die is geconfigureerd voor het gebruik van Azure Active Directory (Azure AD) voor verificatie
> * Verificatie en autorisatie afhandelen met Microsoft.Identity.Web
> * Gegevens ophalen uit een beveiligde web-API, Microsoft Graph

## <a name="prerequisites"></a>Vereisten

- [NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Een Azure AD-tenant waarmee u een app kunt registreren. Als u geen toegang hebt tot een Azure AD-tenant, kunt u er een maken door u te registreren bij het [Microsoft 365 Developer Program](https://developer.microsoft.com/microsoft-365/dev-program) of door een [gratis Azure-account](https://azure.microsoft.com/free) te maken.

## <a name="register-the-app-in-the-azure-portal"></a>De app registreren in het Azure-portaal

Elke app die gebruikmaakt van Azure Active Directory (Azure AD) voor verificatie moet geregistreerd zijn bij Azure AD. Volg de instructies in [Een toepassing registreren](quickstart-register-app.md) met de volgende toevoegingen:

- Bij **Ondersteunde accounttypen** selecteert u **Enkel accounts in deze organisatieadreslijst**.
- Kies in de vervolgkeuzelijst **Omleidings-URI** voor **Web** en voer `https://localhost:5001/signin-oidc` in. De standaardpoort voor een app die wordt uitgevoerd op Kestrel is 5001. Als de app beschikbaar is op een andere poort, geeft u dat poortnummer op in plaats van `5001`.

Selecteer onder **Verificatie** > **Impliciete toekenning** de selectievakjes voor **Toegangstokens** en **ID-tokens** en selecteer de knop **Opslaan**.

Aangezien de app een beschermde API aanroept (in dit geval Microsoft Graph), heeft deze een clientgeheim nodig om de identiteit te controleren wanneer een toegangstoken wordt aangevraagd om die API aan te roepen.

1. Selecteer in dezelfde appregistratie onder **Beheren** **Certificaten & geheimen**.
2. Maak een **Nieuw clientgeheim** dat nooit verloopt.
3. Noteer de **Waarde** aangezien u deze gaat gebruiken in de volgende stap. U heeft hier geen toegang meer toe wanneer u dit venster sluit. U kunt het echter opnieuw maken indien nodig.

## <a name="create-the-app-using-the-net-cli"></a>De app maken met behulp van de .NET CLI

Voer de volgende opdracht uit om de sjablonen voor Microsoft.Identity.Web te downloaden, die we in deze zelfstudie gaan gebruiken.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Voer vervolgens de volgende opdracht uit om de toepassing te maken. Vervang de tijdelijke aanduidingen in de opdracht door de juiste informatie op de overzichtspagina van uw app en voer de opdracht uit in een opdrachtshell. De uitvoerlocatie die is opgegeven met de optie `-o|--output` maakt een projectmap als deze nog niet bestaat, en gaat deel uitmaken van de naam van de app.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Tijdelijke aanduiding   | Azure-portaalnaam       | Voorbeeld                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | (Client-)id van de app | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | (Tenant-)id van de map   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Ga nu naar uw nieuwe Blazor-app in uw editor en voeg het clientgeheim toe aan het bestand *appsettings.json* en vervang de tekst "secret-from-app-registration".

```json
"ClientSecret": "xkAlNiG70000000_UI~d.OS4Dl.-Cy-1m3",
```

## <a name="test-the-app"></a>De app testen

U kunt de app nu bouwen en uitvoeren. Wanneer u deze sjabloon-app uitvoert, moet u het Framework opgeven dat moet worden uitgevoerd met behulp van --framework. Deze zelfstudie maakt gebruik van de .NET Core 3.1 SDK.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

Ga in uw browser naar `https://localhost:5001` en meld u aan met een Azure AD-gebruikersaccount om te zien hoe de app wordt uitgevoerd.

## <a name="retrieving-data-from-microsoft-graph"></a>Gegevens ophalen uit Microsoft Graph

[Microsoft Graph](/graph/overview) biedt een reeks API's die toegang bieden tot de Microsoft 365-gegevens van uw gebruikers. Door gebruik te maken van het Microsoft-identiteitsplatform als identiteitsprovider voor uw app krijgt u eenvoudig toegang tot deze gegevens, omdat Microsoft Graph rechtstreekse ondersteuning biedt voor de tokens die zijn uitgegeven door het Microsoft-identiteitsplatform. In deze sectie voegt u code toe om de e-mails van ingelogde gebruikers weer te geven op de pagina 'gegevens ophalen' van de toepassing.

Voordat u begint, moet u zich afmelden bij uw app, aangezien u wijzigingen aanbrengt in de vereiste machtigingen en uw huidige token daarvoor niet werkt. Als u dat nog niet hebt gedaan, voert u de app opnieuw uit en selecteert u **Afmelden** voordat u de onderstaande code bijwerkt.

Nu gaat u de registratie en code van uw app bijwerken om het e-mailadres van een gebruiker op te halen en de berichten in de app weer te geven. Om dit te doen, moet u eerst de machtigingen voor ap-registratie in Azure AD uitbreiden om toegang tot de e-mailgegevens in te schakelen. Vervolgens voegt u code toe aan de Blazor-app om deze gegevens op te halen en weer te geven op één van de pagina's.

1. Selecteer uw app onder **App-registraties** in het Azure-portaal.
1. Selecteer onder **Beheren** de optie **API-machtigingen**.
1. Selecteer **Een machtiging toevoegen** > **Microsoft Graph**.
1. Selecteer **Gedelegeerde machtigingen**, zoek en selecteer vervolgens de machtiging **Mail.Read**.
1. Selecteer **Machtigingen toevoegen**.

Werk in het bestand *appsettings.json* uw code bij, zodat de juiste token wordt opgehaald met de juiste machtigingen. Voeg "mail.read" toe na het bereik "user.read" onder "DownstreamAPI". Hiermee wordt opgegeven tot welke bereiken (of machtigingen) de app toegang vraagt.

```json
"Scopes": "user.read mail.read"
```

Werk vervolgens de code bij in het bestand *FetchData.razor* om e-mailgegevens op te halen in plaats van de standaard (willekeurige) weergegevens. Vervang de code in het bestand door de volgende code:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

De app starten. U wordt gevraagd om de zojuist toegevoegde machtigingen op te geven, wat aangeeft dat alles werkt zoals verwacht. Nu vraagt de app, naast standaard gebruikersprofielgegevens, toegang tot e-mailgegevens.

Nadat u toestemming hebt gegeven, gaat u naar de pagina "Gegevens ophalen" om een e-mailbericht te lezen.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Schermafbeelding van de uiteindelijke app. Het bevat een kop met de tekst &quot;Hello Nicholas&quot; en er wordt een lijst met e-mailberichten van Nicholas weergegeven.":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanroepen van de compilatie van web-apps waarmee gebruikers worden aangemeld in onze meerdelige scenarioreeks:

> [!div class="nextstepaction"]
> [Scenario: Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)
