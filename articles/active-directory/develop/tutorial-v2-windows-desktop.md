---
title: 'Zelfstudie: Een Windows Presentation Foundation-app (WPF) maken die gebruikmaakt van het Microsoft-identiteitsplatform voor verificatie | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie bouwt u een WPF-app die gebruikmaakt van het Microsoft-identiteitsplatform voor het aanmelden van gebruikers. U krijgt een toegangstoken waarmee u de Microsoft Graph API namens hen kunt aanroepen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0d759b3af097067ba0c9215b65b212d50474d571
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178344"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Zelfstudie: De Microsoft Graph API aanroepen vanuit een Windows-bureaublad-app

In deze zelfstudie bouwt u een systeemeigen Windows Desktop .NET-app (XAML) die gebruikers aanmeldt en een toegangstoken ophaalt om de Microsoft Graph-API aan te roepen. 

Wanneer u de handleiding hebt voltooid, kan uw toepassing een beveiligde API aanroepen die gebruikmaakt van persoonlijke accounts (waaronder outlook.com, live.com en andere). De toepassing maakt ook gebruik van werk- en schoolaccounts van een bedrijf of organisatie waar Azure Active Directory wordt gebruikt.

In deze zelfstudie:

> [!div class="checklist"]
> * Een *Windows Presentation Foundation*-project (WPF) maken in Visual Studio
> * Microsoft Authentication Library (MSAL) voor .NET installeren
> * De app registreren in Azure Portal
> * Code toevoegen voor de ondersteuning van het aan- en afmelden van gebruikers
> * Code toevoegen om de Microsoft Graph API aan te roepen
> * De app testen

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voorbeeld-app werkt die wordt gegenereerd in deze gids

![Er wordt getoond hoe de voorbeeld-app werkt die wordt gegenereerd in deze zelfstudie](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Met de voorbeeldtoepassing die u in deze handleiding maakt, kan een Windows-bureaubladtoepassing een query uitvoeren bij de Microsoft Graph API of een web-API die tokens accepteert van een Microsoft-identiteitsplatformeindpunt. Voor dit scenario voegt u een token toe aan HTTP-aanvragen via de autorisatie-header. Het verkrijgen en vernieuwen van tokens wordt afgehandeld via MSAL (Microsoft Authentication Library).

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Het afhandelen van tokens voor toegang tot beveiligde web-API's

Nadat de gebruiker is geverifieerd, ontvangt de voorbeeldtoepassing een token dat u kunt gebruiken voor het uitvoeren van een query op de Microsoft Graph API of een web-API die wordt beveiligd door het Microsoft-identiteitsplatform voor ontwikkelaars.

API's zoals Microsoft Graph vereisen een token om toegang tot specifieke resources toe te staan. Een token is bijvoorbeeld vereist om het profiel van een gebruiker te lezen, de agenda van een gebruiker te openen of een e-mail te verzenden. Uw toepassing kan een toegangstoken aanvragen met behulp van MSAL om toegang te krijgen tot deze resources door de API-bereiken op te geven. Deze toegangstoken wordt vervolgens toegevoegd aan de HTTP-autorisatie-header voor elke aanroep die wordt gedaan voor de beveiligde resource.

MSAL beheert de cachebewerkingen en vernieuwt toegangstokens voor u, zodat uw toepassing dat niet hoeft te doen.

## <a name="nuget-packages"></a>NuGet-pakketten

In deze handleiding wordt gebruikgemaakt van de volgende NuGet-pakketten:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>Uw project instellen

In deze sectie maakt u een nieuw project om te laten zien hoe u een Windows-bureaublad .NET-toepassing (XAML) met *aanmelding bij Microsoft* integreert, zodat de toepassing query's kan uitvoeren op web-API's waarvoor een token is vereist.

De toepassing die u met behulp van deze handleiding maakt, toont een knop die wordt gebruikt voor het aanroepen van een grafiek, een gebied om de resultaten op het scherm weer te geven en een knop om af te melden.

> [!NOTE]
> Wilt u liever het Visual Studio-project uit dit voorbeeld downloaden? [Download een project](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip) en ga verder met de [configuratiestap](#register-your-application) om het codevoorbeeld te configureren voordat u het uitvoert.
>

Ga als volgt te werk om uw toepassing te maken:

1. Selecteer in Visual Studio **Bestand** > **Nieuw** > **Project**.
2. Selecteer bij **Sjablonen** de optie **Visual C#** .
3. Selecteer **WPF-app (.NET Framework)** , afhankelijk van de versie van Visual Studio die u gebruikt.

## <a name="add-msal-to-your-project"></a>MSAL toevoegen aan uw project

1. Selecteer in Visual Studio **Tools** > **NuGet Package Manager**> **Package Manager Console**.
2. Plak de volgende Azure PowerShell-opdracht in het venster Pakketbeheerconsole:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > Met deze opdracht wordt Microsoft Authentication Library geïnstalleerd. MSAL zorgt ervoor dat gebruikerstokens worden verkregen, in de cache worden opgeslagen en vernieuwd. Deze gebruikerstokens worden gebruikt om toegang te krijgen tot de API's die worden beveiligd door Azure Active Directory v2.0
    >

## <a name="register-your-application"></a>Uw toepassing registreren

U kunt uw toepassing op twee manieren registreren.

### <a name="option-1-express-mode"></a>Optie 1: Snelle modus

U kunt uw toepassing snel registreren door het volgende te doen:
1. Ga naar de quickstart <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure Portal - App-registraties<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Voer een naam in voor de toepassing en selecteer **Registreren**.
1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.

### <a name="option-2-advanced-mode"></a>Optie 2: Geavanceerde modus

Ga als volgt te werk om de toepassing te registreren en de registratiegegevens van de toepassing toe te voegen aan uw oplossing:
1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in een organisatiemap (alle Azure AD-mappen - meerdere tenants) en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox).**
1. Selecteer **Registreren**.
1. Selecteer onder **Beheren** achtereenvolgens **Verificatie** > **Een platform toevoegen**.
1. Selecteer **Mobiele toepassingen en desktoptoepassingen**.
1. Selecteer **https://login.microsoftonline.com/common/oauth2/nativeclient** in de sectie **Omleidings-URI's**.
1. Selecteer **Configureren**.
1. Ga naar Visual Studio, open het bestand *App.xaml.cs* en vervang `Enter_the_Application_Id_here` in het codefragment hieronder door de toepassings-id u zojuist hebt geregistreerd en gekopieerd.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>De code toevoegen om MSAL te initialiseren

In deze stap maakt u een klasse voor het afhandelen van interactie met MSAL, zoals het afhandelen van tokens.

1. Open het bestand *App.xaml.cs* en voeg vervolgens de verwijzing voor MSAL toe aan de klasse:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Werk de app-klasse bij naar het volgende:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>De gebruikersinterface van de toepassing maken

In deze sectie wordt uitgelegd hoe een toepassing een query kan uitvoeren op een beveiligde back-endserver, zoals Microsoft Graph.

Er wordt automatisch een bestand *MainWindow.xaml* gemaakt als onderdeel van de projectsjabloon. Open dit bestand en vervang het knooppunt *\<Grid>* van uw toepassing door de volgende code:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Met behulp van MSAL een token ophalen voor de Microsoft Graph API

In deze sectie gebruikt u MSAL om een token op te halen voor de Microsoft Graph API.

1. Open het bestand *MainWindow.xaml.cs* en voeg de verwijzing voor MSAL toe aan de klasse:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Vervang de klassecode `MainWindow` door het volgende:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
        }
    ```

### <a name="more-information"></a>Meer informatie

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

De methode `AcquireTokenInteractive` aanroepen, resulteert in een venster waarin de gebruiker wordt gevraagd zich aan te melden. De eerste keer moeten gebruikers zich in toepassingen doorgaans interactief aanmelden om toegang te krijgen tot een beveiligde resource. Ze moeten zich mogelijk ook aanmelden wanneer een stille bewerking voor het verkrijgen van een token mislukt. (Bijvoorbeeld als het wachtwoord van een gebruiker is verlopen.)

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de methode `AcquireTokenSilent` wordt het verkrijgen en vernieuwen van tokens verwerkt zonder tussenkomst van de gebruiker. Nadat `AcquireTokenInteractive` voor de eerste keer is uitgevoerd, is `AcquireTokenSilent` de gebruikelijke methode voor het verkrijgen van tokens die toegang hebben tot beveiligde resources voor volgende aanroepen, omdat aanroepen voor het aanvragen of vernieuwen van tokens op de achtergrond worden uitgevoerd.

Uiteindelijk mislukt de methode `AcquireTokenSilent`. De mislukking kan het gevolg zijn van een gebruiker die zich heeft afgemeld of die het wachtwoord op een ander apparaat heeft gewijzigd. Wanneer MSAL detecteert dat het probleem kan worden opgelost door een interactieve actie te vereisen, wordt een uitzondering `MsalUiRequiredException` gestart. Uw toepassing kan deze uitzondering op twee manieren afhandelen:

* De toepassing kan onmiddellijk een aanroep voor `AcquireTokenInteractive` uitvoeren. Met deze aanroep wordt de gebruiker gevraagd zich aan te melden. Normaal gesproken wordt dit patroon gebruikt voor onlinetoepassingen waarbij er geen offline-inhoud beschikbaar is voor de gebruiker. Het voorbeeld dat door deze begeleide installatie wordt gegenereerd, volgt dit patroon. U kunt dit voorbeeld in actie zien wanneer u het voor het eerst uitvoert.

* Omdat geen enkele gebruiker de toepassing heeft gebruikt, bevat `PublicClientApp.Users.FirstOrDefault()` een null-waarde en wordt een uitzondering `MsalUiRequiredException` gegenereerd.

* De code in het voorbeeld handelt de uitzondering vervolgens af door `AcquireTokenInteractive` aan te roepen, waardoor de gebruiker wordt gevraagd zich aan te melden.

* Er kan in plaats daarvan een visuele indicatie aan gebruikers worden gegeven die aangeeft dat een interactieve aanmelding is vereist, zodat ze het juiste tijdstip kunnen selecteren om zich aan te melden. Of de toepassing kan later opnieuw proberen om `AcquireTokenSilent` uit te voeren. Dit patroon wordt vaak gebruikt wanneer gebruikers andere functionaliteit van de toepassing zonder onderbreking kunnen gebruiken, bijvoorbeeld wanneer offline-inhoud beschikbaar is in de toepassing. In dat geval kan de gebruiker beslissen wanneer hij of zij zich wil aanmelden om toegang te krijgen tot de beveiligde resource, of om de verouderde informatie te vernieuwen. De toepassing kan er ook voor kiezen om `AcquireTokenSilent` opnieuw uit te voeren wanneer het netwerk wordt hersteld nadat het tijdelijk niet beschikbaar was.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>De Microsoft Graph API aanroepen met behulp van het token dat u zojuist hebt verkregen

Voeg de volgende nieuwe methode toe aan uw `MainWindow.xaml.cs`. De methode wordt gebruikt om een `GET`-aanvraag uit te voeren voor de Graph API met behulp van een autorisatie-header:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep voor een beveiligde API

In deze voorbeeldtoepassing gaat u de methode `GetHttpContentWithToken` gebruiken om een HTTP `GET`-aanvraag uit te voeren voor een beveiligde bron waarvoor een token is vereist en de inhoud vervolgens naar de aanvrager wordt geretourneerd. Met deze methode wordt het verkregen token toegevoegd in de HTTP-autorisatie-header. In dit voorbeeld is de resource het Microsoft Graph API-eindpunt *me*, waarmee de profielgegevens van de gebruiker worden weergegeven.

## <a name="add-a-method-to-sign-out-a-user"></a>Een methode voor het afmelden van een gebruiker toevoegen

Als u de gebruiker wilt afmelden, voegt u de volgende methode toe aan het bestand `MainWindow.xaml.cs`:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>Meer informatie over het afmelden van gebruikers

Met de methode `SignOutButton_Click` worden gebruikers uit de MSAL-gebruikerscache verwijderd, waardoor MSAL in feite de instructie krijgt om de huidige gebruiker te vergeten, zodat een toekomstige aanvraag om een token te verkrijgen alleen lukt als deze is gemaakt om interactief te zijn.

Hoewel de toepassing in dit voorbeeld afzonderlijke gebruikers ondersteunt, ondersteunt MSAL scenario's waarbij meerdere accounts tegelijkertijd kunnen worden aangemeld. Een voorbeeld hiervan is een e-mailtoepassing waarbij een gebruiker meerdere accounts heeft.

## <a name="display-basic-token-information"></a>Basisinformatie over het token weergeven

Voeg de volgende methode toe aan het bestand *MainWindow.xaml.cs* om basisinformatie over het token weer te geven:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>Meer informatie

Naast het toegangstoken dat wordt gebruikt om de Microsoft Graph API aan te roepen, wordt met MSAL ook een id-token opgehaald nadat de gebruiker zich heeft aangemeld. Dit token bevat een kleine subset van informatie die relevant is voor gebruikers. Met de methode `DisplayBasicTokenInfo` wordt de basis informatie weergegeven die in het token is opgenomen. Zo worden bijvoorbeeld de weergavenaam en de id van de gebruiker weergegeven, evenals de vervaldatum van het token en de tekenreeks die het toegangstoken zelf vertegenwoordigt. U kunt de knop *Microsoft Graph API aanroepen* selecteren, en zien dat hetzelfde token opnieuw is gebruikt voor latere aanvragen. U kunt ook zien tot wanneer de vervaldatum is verlengd wanneer in Microsoft Authentication Library wordt vastgesteld dat het token moet worden vernieuwd.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bouwen van bureaublad-apps die beveiligde web-API's aanroepen in deze reeks met meerdere scenario's:

> [!div class="nextstepaction"]
> [Scenario: Bureaublad-app die web-API's aanroept](scenario-desktop-overview.md)
